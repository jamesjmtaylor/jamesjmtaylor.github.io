---
title: KMP Database layer
date: '2022-08-08T05:49:44-07:00'
tags: kmp android ios kotlin
---
![Swedish lake](/assets/sweden.jpg)

This is the second in a multi-part series on Kotlin Multiplatform.  This entry will cover [configuring SQLDelight and implementing cache logic](https://play.kotlinlang.org/hands-on/Networking%20and%20Data%20Storage%20with%20Kotlin%20Multiplatfrom%20Mobile/05_Configuring_SQLDelight_an_implementing_cache) for the mobile iOS and Android app.  Unlike the tutorial, I'm not using the SpaceX API, which provides public access to information about SpaceX rocket launches.  Instead I'm using the Army's brand new [ODIN API](https://odin.tradoc.army.mil/WEG) for a reboot of my WEG iOS and Android applications.  The ODIN API provides in-depth information about a wide array of military equipment.  

Implementing a shared SQLDelight database & networking layer required the following dependencies:

* kotlinx.coroutines – Used for asynchronous code.
* Ktor – HTTP client for retrieving data over the internet.
* kotlinx.serialization – Deserializer for JSON responses
* SQLDelight – Kotlin code generator from SQL queries for a type-safe database API.

Once those dependencies were added to the `build.gradle.kts` files in the project root directory and the KMP module directory, we could configure the DTOs (Data Transfer Objects).  These objects were modelled after the responses from the [ODIN API](https://odin.tradoc.army.mil/WEG). Below is a sample of the data and should give you a good idea of the general structure:

```
import kotlinx.serialization.*

@Serializable
data class SearchResults (
    val batchcomplete: String? = null,
    @SerialName("continue")
    val searchResultsContinue: Continue? = null,
    val query: Query? = null
) {
    fun asList(): List<SearchResult>? {
        return query?.searchResults
    }
}
@Serializable
data class Continue (
    val sroffset: Long? = null,
    @SerialName("continue")
    val continueString: String? = null
)

@Serializable
data class Query (
    @SerialName("searchG2")
    val searchResults: List<SearchResult> = emptyList(),
    @SerialName("totalhits")
    val totalHits: Long? = null
)

@Serializable
data class SearchResult (
    val title: String? = null,
    val id: Long = 0,
    val categories: List<String> = emptyList(),
    val images: List<Image> = emptyList()
)
```

That final DTO, `SearchResult` , forms the cornerstone of what we'll display to the user. It includes: a title, so the user knows what they're looking at, categories for determining what bottom navigation tab (Land, Sea, or Air) to display the equipment on, images for showing a preview of the equipment, and an id for querying the API for additional details when the equipment icon is tapped. 

As you might have noticed, we have a couple of complex relationships that we need to store.  `Images` are their own DTOs that have a one-to-many relationship with `SearchResult`.  Categories are represented by strings, but they also need to be returned in a list format.  Finally, ODIN doesn't return `SearchResult` objects directly, but rather a `SearchResults` payload that contains a `Query` object (which has a list of our desired `SearchResult` objects) and a `Continue` object for [pagination](https://www.raywenderlich.com/12244218-paging-library-for-android-with-kotlin-creating-infinite-lists).  

This brings us to the crux of this blog entry: how do we represent these relationships in SQL?  To do this we need to create a SQLDelight `app/shared/cache/AppDatabase.sq` file with the following:

```
import kotlin.collections.List;

CREATE TABLE search_result (
equipment_id INTEGER NOT NULL PRIMARY KEY,
title TEXT,
categories TEXT
);

insertResult:
INSERT INTO search_result(title, equipment_id, categories)
VALUES(?,?,?);

selectResultByEquipmentId:
SELECT * FROM search_result
WHERE equipment_id = ?;

selectAllSearchResults:
SELECT search_result.*, image.*
FROM search_result
LEFT JOIN image ON image.equipment_id == search_result.equipment_id;

removeAllResults:
DELETE FROM search_result;

CREATE TABLE image (
equipment_id INTEGER NOT NULL,
name TEXT,
url TEXT
);

insertImage:
INSERT INTO image(equipment_id, name, url)
VALUES(?,?,?);

selectImagesByEquipmentId:
SELECT * FROM image
WHERE equipment_id = ?;

removeAllImages:
DELETE FROM image;
```

This covers all of our basic DB CRUD (Create, Read, Update, Destroy) operations for search results and images.  Images are associated with search results through a [left join](https://www.w3schools.com/sql/sql_join_left.asp). This means that we return all search results regardless of whether they have an associated image or not. 

You might have noticed that the \`SearchResults\`, \`Continue\`, and \`Query\` objects aren't represented at all in the database.  The reason for this is threefold.  First, because SQLDelight is multiplatform (iOS, Android, Desktop, etc.) the number of SQL operators available in common is a subset of those available across all those platforms.  This limits the complexity of the relationships that you can model.  [Many-to-many relationships are particularly cumbersome](https://stackoverflow.com/questions/68632268/sqldelight-relationships).  Second, even if it was straightforward model many-to-many relationships in SQLDelight, doing so would lead to inconsistent behavior.  For example, if the user was offline, but had previously searched for all equipment beginning with "T-72", they would receive results when they typed exactly "T-72", but nothing for "T-7" or "T-72A". Third, for reasons soon to be illustrated, \`SearchResults\`, \`Continue\`, and \`Query\` don't actually need to be stored database.  

These SQL queries can be invoked with the following KMP wrapper functions:

```kotlin
import com.jamesjmtaylor.weg.models.Image
import com.jamesjmtaylor.weg.models.SearchResult

/**
 * SQLDelight database for caching REST API query results.
 * "internal" accessibility modifier means the Database is only accessible from within the
 * multiplatform module
 */
internal class Database(databaseDriverFactory: DatabaseDriverFactory) {
    private val database = AppDatabase(databaseDriverFactory.createDriver())
    private val dbQuery = database.appDatabaseQueries

    internal fun clearDatabase() {
        dbQuery.transaction {
            dbQuery.removeAllResults()
            dbQuery.removeAllImages()
        }
    }

    internal fun getAllResults(): List<SearchResult> {
        return dbQuery.selectAllSearchResults(::mapSearchResultSelecting).executeAsList()
    }

    internal fun insertSearchResults(results: List<SearchResult>) {
        dbQuery.transaction {
            results.forEach { result ->
                val image = dbQuery.selectImagesByEquipmentId(result.id).executeAsOneOrNull()
                if (image == null) insertImage(result)
                insertResult(result)
            }
        }
    }

    private fun insertImage(result: SearchResult) {
        result.images.forEach { image ->
            dbQuery.insertImage(
                name = image.name,
                equipment_id = result.id,
                url = image.url
            )
        }
    }

    private fun insertResult(result: SearchResult) {
        dbQuery.insertResult(
            title = result.title,
            equipment_id = result.id,
            categories = result.categories.joinToString { it }
        )
    }

    private fun mapSearchResultSelecting(
        equipmentId: Long,
        title: String?,
        categories: String?,
        imageEquipmentId: Long?,
        imageName: String?,
        imageUrl: String?
    ) : SearchResult {
        print(imageEquipmentId)
        return SearchResult(
            title = title,
            id = equipmentId,
            categories = categories?.split(",") ?: emptyList(),
            images = listOf(Image(imageName,imageUrl))
        )
    }
}
```

These functions map the results cached in the DB to the DTOs that we defined at the beginning of this blog entry.  With the DB thus wrapped, we can invoke the DB from our client apps with the following repository pattern:

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

Using this logic we can return all the cached results, or retrieve from the network directly and update the cache as needed.  This also demonstrates why we don't need to store `SearchResults`, `Continue`, and `Query` objects in the database.  As long as the client determines whether or not to force reload from the network, the shared KMP logic can be simplified to either just returning everything that the DB has, or retrieving the data fresh.  

This wraps up the DB portion of the KMP series.  If you're curious about any of the other objects (i.e. `databaseDriverFactory`) you can access the full repository here: <https://github.com/jamesjmtaylor/weg-kmm>.  Next time we'll cover implementation of the KMP Networking layer and exactly how that `api.getEquipment()` function is implemented. Until then!

Photo by <a href="https://unsplash.com/@springsimon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Simon Spring</a> on <a href="https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
