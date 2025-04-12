---
title: KMP Networking layer
date: '2022-09-01T07:07:02-07:00'
tags: kmp android ios kotlin
---
![Clouds](/assets/clouds.jpg)

This is the third in a multi-part series on Kotlin Multiplatform.  The source code for the final project is available [here](https://github.com/jamesjmtaylor/weg-kmm). This entry will cover [configuring Ktor and executing networking requests](https://play.kotlinlang.org/hands-on/Networking%20and%20Data%20Storage%20with%20Kotlin%20Multiplatfrom%20Mobile/06_Implementing_an_API_service) for the mobile iOS and Android apps.  Unlike the tutorial, I'm not using the SpaceX API, which provides public access to information about SpaceX rocket launches.  Instead I'm using the Army's [ODIN API](https://odin.tradoc.army.mil/WEG) for a reboot of my WEG iOS and Android applications.  The ODIN API provides in-depth information about a wide array of military equipment.  

With the Data Transfer Objects (DTOs) already created in the previous sections, we can jump straight into the API implementation below:

```
class Api {
    private val httpClient = HttpClient {
        install(JsonFeature) {
            val json = Json { ignoreUnknownKeys = true }
            serializer = KotlinxSerializer(json)
        }
    }

    suspend fun getEquipment(): SearchResults {
        val equipmentURl = API_URL + "?format=json&" +
                "action=query&" +
                "list=searchG2&" +
                "srimages=1&" +
                "srsearch=incategory:Land&" +
                "srlimit=20" +
                "&sroffset=0"
        return httpClient.get(equipmentURl)
    }

    companion object {
        const val BASE_URL = "https://odin.tradoc.army.mil"
        const val API_URL = "$BASE_URL/mediawiki/api.php"
    }
}
```

The `API` class contains a private Ktor HttpClient. Instead of accessing the client directly, our client apps will invoke networking functions and receive results from the API. If you're wondering where all those url query parameters came from, it wasn't easy.  Since the [API documentation for ODIN](https://odin.tradoc.army.mil/mediawiki/api.php) is rather obtuse, I found it easier to open the [Chrome DevTools](https://developer.chrome.com/docs/devtools/) and just click around the ODIN website, making notes of the requests that my web client was making to the server.  Then I'd input them into [Postman](https://www.postman.com/) and tweak the parameters to figure out exactly what worked and what didn't.  

The `getEquipment()` function invocation was briefly touched on in the KMP Database layer blog entry.  Here it is again for reference:

```
class EquipmentSDK(databaseDriverFactory: DatabaseDriverFactory) {
    private val db = Database(databaseDriverFactory)
    private val api = Api()

    @Throws(Exception::class)
    suspend fun getEquipment(forceReload: Boolean): List<SearchResult>? {
        val cachedEquipment = db.getAllResults()
        return if (cachedEquipment.isNotEmpty() && !forceReload) {
            cachedEquipment
        } else {
            api.getEquipment().asList().also { results ->
                results?.let {
                    db.clearDatabase()
                    db.insertSearchResults(it)
                }
            }
        }
    }
}
```

This `EquipmentSDK` class creates a repository pattern that our client apps can use to abstract away caching.  In this way they don't need to worry about when to fetch fresh data from the server or to use cached data, the shared KMP SDK handles it all for them.  

That concludes the API section of the KMP series. The next KMP entry will cover creating our Jetpack Compose views.  Before we get to that though we'll take a break from the unmitigated stream of technical discourse for a critique of stoicism.   

Photo by <a href="https://unsplash.com/@michaelbweidner?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Michael & Diane Weidner</a> on <a href="https://unsplash.com/s/photos/clouds?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
