---
title: Algorithmic Selection
date: '2026-06-01T12:00:00-08:00'
tags: algorithms life
---
![Fitness](/assets/fitness.jpg)

Sorry for missing last month! May was incredibly busy. I had four climbs scheduled, Mother's day for my wife, a camping trip with the family, our fifteenth wedding anniversary, a birthday for our oldest, a final project for my Human Computer Interaction (HCI) course, and a slew of contracting work.  And I'm not out of the woods yet.  This month I have two more climbs (Middle Sister and Baker), Father's Day, and a friend's wedding in Orlando.  Fortunately, finishing my HCI course has given me a little breathing room, part of which I'm able to use for this post.

This month's musing is on algorithmic selection.  All the climb leaders with the Mazamas who I've assisted so far have asked that I help select the climb team.  This is a laborious process.  The leadership team needs to review each climb applicant and place them on an "order of merit" list to determine who to accept on the climb, who to waitlist, and who to reject. It's remarkably like screening applicants on the job market.  Many of those who apply include a list of their certifications, their general fitness plan, and even a cover letter and climb resume.  The first time I reviewed an applicant list, it took me about 90 minutes from start to finish to winnow a list of three dozen climbers down to the ten slots that we had available.  To make matters worse, new climbers are continuously applying, meaning the list is constantly updated right until the application cutoff.  Unfortunately I couldn't wait until the last minute to do the review because most climb leaders want periodic updates on how the applicant list is developing.

It didn't take me long to determine that I needed to find a better way.  The Mazamas website doesn't have a REST API, so I did the next best thing: I built a website scraper in Python that uses playwright to automatically login with my credentials, retrieve climber information for a given activity, and then tabulate it.  The login script is below:

```
import csv
import datetime
import os
import sys
import time
import requests
from bs4 import BeautifulSoup
from playwright.sync_api import sync_playwright

EMAIL = os.getenv("MAZAMAS_EMAIL", "")
PASSWORD = os.getenv("MAZAMAS_PASSWORD", "")
CSV_FILE = "mazamas_results.csv"
LOGIN_URL = "https://mazamas.org/login/"
USER_AGENT = "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36"
HEADERS = {
	"accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
	"accept-encoding": "gzip, deflate, br, zstd",
	"accept-language": "en-US,en;q=0.9,es-US;q=0.8,es;q=0.7,fr-FR;q=0.6,fr;q=0.5,de-BE;q=0.4,de;q=0.3,fr-CA;q=0.2,nl-BQ;q=0.1,nl-BE;q=0.1,nl;q=0.1",
	"cache-control": "max-age=0",
	"cookie": "",
	"dnt": "1",
	"priority": "u=0, i",
	"referer": "https://mazamas.org/",
	"sec-ch-ua": '"Google Chrome";v="147", "Not.A/Brand";v="8", "Chromium";v="147"',
	"sec-ch-ua-mobile": "?0",
	"sec-ch-ua-platform": '"macOS"',
	"sec-fetch-dest": "document",
	"sec-fetch-mode": "navigate",
	"sec-fetch-site": "same-origin",
	"upgrade-insecure-requests": "1",
	"user-agent": USER_AGENT,
}

def login_and_set_cookies():
	print("Logging in via browser...")
	with sync_playwright() as p:
		browser = p.chromium.launch(headless=True)
		context = browser.new_context(user_agent=USER_AGENT)
		page = context.new_page()
		page.goto(LOGIN_URL, wait_until="networkidle")
		page.fill('input[name="email"]', EMAIL)
		page.fill('input[name="password"]', PASSWORD)
		page.click('input[type="submit"]')
		page.wait_for_load_state("networkidle")
		time.sleep(3)
		cookies = context.cookies("https://mazamas.org")
		browser.close()
	cookie_str = "; ".join(f"{c['name']}={c['value']}" for c in cookies)
	HEADERS["cookie"] = cookie_str
	print(f"Logged in, got {len(cookies)} cookies")


def ensure_credentials():
	if not EMAIL or not PASSWORD:
		print("Missing credentials. Set MAZAMAS_EMAIL and MAZAMAS_PASSWORD environment variables.")
		sys.exit(1)
```
The second part, retrieving the climber information, is demonstrated by the Python function `fetch_profile()`, which iterates over the participants returned for a given activity by the `fetch_participants()` function:
```
def fetch_participants(activity_instance: int) -> list[str]:
	base_url = f"https://mazamas.org/activity-instance/{activity_instance}/participants/"
	status = "Awaiting%20approval" #"Accepted" 
	url = f"{base_url}?status={status}"
	urls = []
	page = 1
	while url:
		print(f"Fetching participants page {page} ...")
		response = requests.get(url, headers=HEADERS)
		if response.status_code == 202:
			print("WAF token expired, re-logging in...")
			login_and_set_cookies()
			response = requests.get(url, headers=HEADERS)
		if response.status_code != 200:
			print(f"Failed to fetch participants: status {response.status_code}")
			break
		soup = BeautifulSoup(response.text, "html.parser")
		for a in soup.find_all("a", href=True):
			href = a["href"]
			if "/profile/" in href:
				full = f"https://mazamas.org{href}" if href.startswith("/") else href
				if full not in urls:
					urls.append(full)
		next_link = soup.find("a", class_="pagination-next")
		if next_link and next_link.get("href"):
			url = f"{base_url}{next_link['href']}"
			page += 1
		else:
			break
	print(f"Found {len(urls)} participant profiles across {page} pages")
	return urls


def fetch_profile(url: str) -> str | None:
	response = requests.get(url, headers=HEADERS)
	if response.status_code == 202:
		print(f"WAF token expired fetching {url}")
		login_and_set_cookies()
		response = requests.get(url, headers=HEADERS)
	if response.status_code != 200:
		print(f"Failed to fetch {url}: status {response.status_code}")
		return None
	return response.text
```
Once the participants are retrieved, we can parse through the HTML to retrieve the information pertinent to their ultimate score, such as their certifications in rock, snow, and first aid:
```
ROCK_LEVELS = ["Basic Rock", "Intermediate Rock", "High Angle Rock Rescue"]
SNOW_LEVELS = ["Basic Snow", "Intermediate Snow", "High Angle Snow Rescue"]
FIRST_AID_LEVELS = ["CPR", "BFA", "WFA", "Course: MFA", "WFR"]
def get_profile_field(about_section, label: str) -> str:
	label_div = about_section.find("div", class_="profile--label", string=label)
	if label_div:
		value_div = label_div.find_next_sibling("div", class_="profile--value")
		if value_div:
			return value_div.get_text(strip=True)
	return ""


def get_earned_badges(soup) -> set[str]:
	badges = set()
	earned_group = soup.find("div", class_="badges--earned-group")
	if earned_group:
		for title_div in earned_group.find_all("div", class_="badges-small--title"):
			badges.add(title_div.get_text(strip=True))
	return badges


def highest_badge(earned: set[str], levels: list[str]) -> str:
	result = ""
	for level in levels:
		if level in earned:
			result = level
	return result


def parse_profile(html: str) -> dict:
	soup = BeautifulSoup(html, "html.parser")

	about = soup.find("div", id="about")
	name = get_profile_field(about, "Full Name") if about else ""
	gender = get_profile_field(about, "Gender") if about else ""
	ethnicity = get_profile_field(about, "Ethnicity") if about else ""
	occupation = get_profile_field(about, "Occupation") if about else ""

	earned = get_earned_badges(soup)
	rock = highest_badge(earned, ROCK_LEVELS)
	snow = highest_badge(earned, SNOW_LEVELS)

	current_year = str(datetime.date.today().year)
	has_bcep_current = False
	activity_div = soup.find("div", id="activity")
	if activity_div:
		for a in activity_div.find_all("a"):
			text = a.get_text(strip=True)
			if text.startswith("BCEP") and current_year in text:
				has_bcep_current = True
				break
	if not rock and has_bcep_current:
		rock = "Basic Rock (P)"
	if not snow and has_bcep_current:
		snow = "Basic Snow (P)"

	first_aid = highest_badge(earned, FIRST_AID_LEVELS)
	crevasse_rescue = "Y" if "Crevasse Rescue" in earned else "N"
	avalanche_awareness = "Y" if "Avalanche Awareness" in earned else "N"

	return {
		"name": name,
		"gender": gender,
		"ethnicity": ethnicity,
		"occupation": occupation,
		"rock": rock,
		"snow": snow,
		"first_aid": first_aid,
		"crevasse_rescue": crevasse_rescue,
		"avalanche_awareness": avalanche_awareness,
		"url": "",
	}
```
The last part, the `write_csv()` function, creates a comma separated values spreadsheet with the applicants' name, gender, ethnicity, occupation, various skills, and profile URL.
```
def write_csv(rows: list[dict]):
	fieldnames = ["name", "gender", "ethnicity", "occupation", "rock", "snow", "first_aid", "crevasse_rescue", "avalanche_awareness", "url"]
	with open(CSV_FILE, "w", newline="") as f:
		writer = csv.DictWriter(f, fieldnames=fieldnames)
		writer.writeheader()
		writer.writerows(rows)
	print(f"Wrote {len(rows)} results to {CSV_FILE}")


if __name__ == "__main__":
	if len(sys.argv) != 2:
		print("Usage: python mazamas_scrape.py <activity-instance-id>")
		sys.exit(1)
	ensure_credentials()
	activity_instance = int(sys.argv[1])

	login_and_set_cookies()
	profile_urls = fetch_participants(activity_instance)
	if not profile_urls:
		print("No participants found")
		sys.exit(1)

	results = []
	for url in profile_urls:
		print(f"Fetching {url} ...")
		html = fetch_profile(url)
		if html is None:
			continue
		profile = parse_profile(html)
		profile["url"] = url
		for key, val in profile.items():
			print(f"  {key}: {val}")
		results.append(profile)

	write_csv(results)
```

Once the data is tabulated I can then use a separate Python script to parse the data to assign each candidate a score.  These scores can then be used to sort the candidates into an order of merit list.  I've included the algorithm below:

```
import csv
from collections import Counter

INPUT_CSV = "mazamas_results.csv"
OUTPUT_CSV = "mazamas_results.csv"

ROCK_POINTS = {
	"Basic Rock": 1, "Basic Rock (P)": 2,
	"Intermediate Rock": 2, "High Angle Rock Rescue": 3,
}
SNOW_POINTS = {
	"Basic Snow": 1, "Basic Snow (P)": 2,
	"Intermediate Snow": 2, "High Angle Snow Rescue": 3,
}
FIRST_AID_POINTS = {"CPR": 1, "BFA": 2, "WFA": 3, "Course: MFA": 4, "WFR": 5}

with open(INPUT_CSV, newline="") as f:
	rows = list(csv.DictReader(f))

# --- Count group frequencies for diversity scoring ---
gender_counts = Counter(r["gender"] for r in rows if r["gender"])
ethnicity_counts = Counter(r["ethnicity"] for r in rows if r["ethnicity"])
total = len(rows)

for row in rows:
	score = 0.0

	# --- Skills (max 9) ---
	score += ROCK_POINTS.get(row["rock"], 0)
	score += SNOW_POINTS.get(row["snow"], 0)
	score += FIRST_AID_POINTS.get(row["first_aid"], 0)

	# --- Safety certs (1 each) ---
	if row["crevasse_rescue"] == "Y":
		score += 1
	if row["avalanche_awareness"] == "Y":
		score += 1

	# --- Gender diversity: rarer gender scores higher ---
	gender = row["gender"]
	if gender and gender_counts[gender] > 0:
		gender_score = (1 - (gender_counts[gender] / total)) * 3
		score += gender_score
		print(f"Debug: {row['name']} gender {gender} score {gender_score}")
	else:
		score += 1.5  # unknown treated as midpoint

	# --- Ethnicity diversity: rarer ethnicity scores higher ---
	eth = row["ethnicity"]
	if eth:
		ethnicity_score = (1 - (ethnicity_counts[eth] / total)) * 3
		score += ethnicity_score
		# print(f"Debug: {row['name']} ethnicity {eth} score {ethnicity_score}")
	else:
		score += 1.5  # unknown treated as midpoint

	row["score"] = round(score, 1)

rows.sort(key=lambda r: r["score"], reverse=True)

fieldnames = ["score"] + [k for k in rows[0] if k != "score"]
with open(OUTPUT_CSV, "w", newline="") as f:
	writer = csv.DictWriter(f, fieldnames=fieldnames)
	writer.writeheader()
	writer.writerows(rows)

print(f"Scored {len(rows)} profiles, sorted by priority:")
for r in rows:
	print(f"  {r['score']:5}  {r['name']}")
```

In addition to the certifications, the algorithm also factors in gender and ethnicity, supporting the Mazamas goals of diversity, equity, and inclusion in the outdoors. This was something I simply could not do when I was manually considering candidates. I was already so mentally overwhelmed with evaluating all the climber attributes that to add even more would have caused "analysis paralysis." If you've never heard of analysis paralysis, it's a condition that you may be familiar with if you've ever played "point salad" style boardgames.  In these boardgames the calculations necessary to optimize your final score are overwhelming. This can get to the point of preventing any forward progress in making a final decision on your turn.  As a result each player turn drags out indefinitely, ultimately causing players to throw up their hands and go on intuition rather than any consciously rational evaluation.

To wrap things up, I'm happy with how well the scripts work.  What used to take one to two hours now takes one to two minutes.  The three climb leaders I've assisted so far have commented that my algorithm's recommendations nearly mirror their own, with the occasional difference because they personally know the climber and have first-hand experience with them as either better, or worse, than they appear on paper.  The one admitted shortcoming of my algorithm is that it does not account for physical fitness.  Candidates can populate a "Physical Activities" section in their account, but given that it's a free-form field, I'm not exactly sure how to quantify it.  How do you assign a score to someone who says they lift weights every other day to some who kayaks on the weekends? If you have any ideas, please leave them in the comments!  I'm all ears.  


Photo by <a href="https://unsplash.com/@gabinvallet?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Gabin Vallet</a> on <a href="https://unsplash.com/photos/man-in-black-t-shirt-and-black-shorts-running-on-road-during-daytime-J154nEkpzlQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>