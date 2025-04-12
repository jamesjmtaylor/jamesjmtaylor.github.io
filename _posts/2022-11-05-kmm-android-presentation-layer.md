---
title: KMP Android Presentation layer
date: '2022-11-05T08:45:21-07:00'
tags: kmp android kotlin
---
![Stormy beach](/assets/compose.jpg)

This is the second to last entry in a multi-part series on Kotlin Multiplatform.  This entry will cover implementation of a Jetpack Compose UI for the Android app.  I'm using the Army's brand new [ODIN API](https://odin.tradoc.army.mil/WEG) for a reboot of my WEG iOS and Android applications.  The ODIN API provides in-depth information about a wide array of military equipment.  

The first step in implementing the compose UI was creating the Jetpack ViewModel for the view to integrate with the KMP shared module.  The ViewModel extends the AndroidViewModel superclass so that it has access to the application context necessary to instantiate the SQLDelight database driver. 

```
class EquipmentViewModel(app: Application): AndroidViewModel(app) {
    private val sdk = EquipmentSDK(DatabaseDriverFactory(app))

    private val _landFlow: Flow<PagingData<SearchResult>> = Pager(PagingConfig(pageSize = PAGE_SIZE)) {
        SearchResultSource(EquipmentType.LAND, sdk)
    }.flow.cachedIn(viewModelScope)
    private val _airFlow: Flow<PagingData<SearchResult>> = Pager(PagingConfig(pageSize = PAGE_SIZE)) {
        SearchResultSource(EquipmentType.AIR, sdk)
    }.flow.cachedIn(viewModelScope)
    private val _seaFlow: Flow<PagingData<SearchResult>> = Pager(PagingConfig(pageSize = PAGE_SIZE)) {
        SearchResultSource(EquipmentType.SEA, sdk)
    }.flow.cachedIn(viewModelScope)
    var equipmentFlow = EquipmentFlow(_landFlow, _airFlow, _seaFlow)

    data class EquipmentFlow(
        val land: Flow<PagingData<SearchResult>>,
        val air: Flow<PagingData<SearchResult>>,
        val sea: Flow<PagingData<SearchResult>>
    )
}
```

 From there it injects that SDK into the three equipment type SearchResultSource objects, which use the Jetpack paging library to seamlessly support infinite scroll:

```
class SearchResultSource(val equipmentType: EquipmentType,
                      val equipmentSDK: EquipmentSDK) : PagingSource<Int, SearchResult>() {
    override fun getRefreshKey(state: PagingState<Int, SearchResult>): Int? {
        return state.anchorPosition
    }
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, SearchResult> {
        val nextPage = params.key ?: 0
        val searchResultList = equipmentSDK.getEquipment(equipmentType, nextPage) ?: emptyList()
        return LoadResult.Page(
            data = searchResultList,
            prevKey = if (nextPage == 0) null else nextPage - 1,
            nextKey = if (searchResultList.isEmpty()) null else nextPage + 1
        )
    }
}
```

With the data retrieval and pagination implemented, we can now add the UI to display the data.  The `EquipmentActivity` forms the primary lifecycle object that the user interacts with and contains the Jetpack Compose equipment screen:

```
class EquipmentActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val equipmentViewModel = ViewModelProvider(this).get(EquipmentViewModel::class.java)
        setContent { EquipmentScreen(equipmentViewModel.equipmentFlow) }
    }
}

@Composable
fun EquipmentScreen(equipment: EquipmentViewModel.EquipmentFlow) {
    WorldwideEquipmentGuideTheme {// A surface container using the 'background' color from the theme
        val navController = rememberNavController()
        val screens = listOf(Screen.Land,Screen.Air, Screen.Sea)
        Scaffold(//ensures proper layout strategy between topBar, bottomBar, fab, bottomSheet, content, etc.
            //See: https://developer.android.com/jetpack/compose/navigation#bottom-nav
            bottomBar = { BottomNavigation(backgroundColor = MaterialTheme.colors.background) {
                val navBackStackEntry by navController.currentBackStackEntryAsState()
                val currentDestination = navBackStackEntry?.destination
                screens.forEach { screen ->
                    BottomNavigationItem(
                        icon = { Icon(ImageVector.vectorResource(screen.drawableId), stringResource(screen.stringId)) },
                        label = { Text(stringResource(screen.stringId)) },
                        selected = currentDestination?.hierarchy?.any { it.route == screen.route } == true,
                        onClick = { navController.navigate(screen.route) {
                            // avoid building up a large stack of destinations on the back stack as users select items
                            popUpTo(navController.graph.findStartDestination().id) { saveState = true }
                            launchSingleTop = true // Avoid multiple copies of the same destination when reselecting the same item
                            restoreState = true // Restore state when reselecting a previously selected item
                        }}
                    )
                }
            }}
        ) { padding ->
            NavHost(navController, startDestination = Screen.Land.route) {
                composable(Screen.Land.route) { EquipmentColumn(equipment.land, padding) }
                composable(Screen.Air.route) { EquipmentColumn(equipment.air, padding) }
                composable(Screen.Sea.route) { EquipmentColumn(equipment.sea, padding) }
            }
        }
    }
}
```

The bottomBar has a separate BottomNavigationItem for each equipment type: land, air, and sea.  The onClick listener selects its respective route, which is then instantiated by the Navigation host.  

The `EquipmentColumn` consists of a column containing a Searchbar and an EquipmentLazyVerticalGrid.  It's passed its equipment type specific paging data by the equipment screen, which in turn is injected with a generic equipment flow by the Activity.  This is done because the equipment viewmodel requires an application context in order to be instantiated, which isn't available in composable previews.  So if we want to be able to preview our Composable layouts we need to keep the EquipmentViewModel out of them and just pass a reference to properties of the viewmodel instead.

The EquipmentLazyVerticalGrid itemizes the search result paging list

```
@Composable
fun EquipmentLazyVerticalGrid(
    searchResultFlow: Flow<PagingData<SearchResult>>,
    modifier: Modifier = Modifier
) {
    val searchResultListItems: LazyPagingItems<SearchResult> = searchResultFlow.collectAsLazyPagingItems()
    LazyVerticalGrid(
        columns = GridCells.Adaptive(150.dp),
        contentPadding = PaddingValues(horizontal = 0.dp),
        horizontalArrangement = Arrangement.spacedBy(0.dp),
        verticalArrangement = Arrangement.spacedBy(0.dp),
        modifier = modifier.fillMaxHeight()
    ) {
        items(searchResultListItems.itemCount) { index ->
            val text = searchResultListItems[index]?.title
            EquipmentCard(
                imgUrl = searchResultListItems[index]?.images?.firstOrNull()?.url,
                text = text,
                modifier = Modifier.height(150.dp)
            )
        }
}}
```

 and creates a separate Equipment Card for each one:

```
@Composable
fun EquipmentCard(
    imgUrl: String?,
    text: String?,
    modifier: Modifier = Modifier
) {
    Box(
        modifier = modifier,
    ) {
        AsyncImage(
            model = imgUrl,
            contentDescription = text ?: stringResource(R.string.placeholder_name),
            placeholder = painterResource(id = R.drawable.placeholder),
            contentScale = ContentScale.FillBounds,
            modifier = Modifier.fillMaxSize()
        )
        Text(
            color = MaterialTheme.colors.onBackground,
            text = text ?: stringResource(R.string.placeholder_name),
            style = MaterialTheme.typography.caption,
            modifier = Modifier
                .align(Alignment.BottomCenter)
                .background(MaterialTheme.colors.background)

        )
    }
}
```
![Android weg app](/assets/land.png)

If you want to see the full source code it's available \\\[here](https://github.com/jamesjmtaylor/weg-kmm).
