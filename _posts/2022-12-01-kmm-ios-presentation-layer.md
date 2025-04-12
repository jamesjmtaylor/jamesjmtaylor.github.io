---
title: KMP iOS Presentation layer
date: '2022-12-01T07:39:00-08:00'
tags: kmp ios kotlin
---
![Desert](/assets/desert.jpg)

This is the last entry in a multi-part series on Kotlin Multiplatform.

Today I will cover implementation of the SwiftUI user interface for the iOS app.  I'm using the Army's brand new [ODIN API ](https://odin.tradoc.army.mil/WEG) for a reboot of my WEG iOS and Android applications.  The ODIN API provides in-depth information about a wide array of military equipment.  I'm going to try and cut the code snippets down to only the most salient bits in order to keep this post's size a little more manageable.  Because of that, I'd encourage you to open the [repository](https://github.com/jamesjmtaylor/weg-kmm) in a second pane to provide context to the snippets as needed.

When I started work on implementing the KMP repository layer it was under the assumption that both Android and iOS had their own native pagination.  For Android this assumption was correct, and I was able to use the [Android Jetpack Paging Library](https://developer.android.com/topic/libraries/architecture/paging/v3-overview).  iOS, unfortunately, doesn't have a native pagination library.  As I started down the path of writing my pagination logic I very quickly got frustrated that I was creating duplicate logic across the two platforms.  So I did a little digging and found a [KMP multiplatform paging library](https://github.com/kuuuurt/multiplatform-paging).  This library was built to support the Jetpack Paging library out of the box, so very little needed to change for my pre-existing Android implementation.  For iOS I just needed to wrap the data in a CommonFlow object, as shown below:

```
    val landPager = Pager(
        clientScope = scope,
        config = PagingConfig(pageSize = PAGE_SIZE, enablePlaceholders = false), // Ignored on iOS
        initialKey = 0,
        getItems = { currentKey, _ ->
            val items = getEquipmentSearchResults(EquipmentType.LAND, currentKey) ?: emptyList()
            PagingResult(
                items = items,
                currentKey = currentKey,
                prevKey = { max(currentKey - 1,0) },
                nextKey = { currentKey + 1 }
            )
        }
    )
    val landPagingData: CommonFlow<PagingData<SearchResult>>
        get() = landPager.pagingData
            .cachedIn(scope) // cachedIn from AndroidX Paging. on iOS, this is a no-op
            .asCommonFlow() // So that iOS can watch the Flow
```

Once that was done I could create my ViewModels.  For iOS I had to create 3 separate VMs, one for each tab/equipment type.  The land equipment view model is below:

```
class LandEquipmentViewModel: EquipmentViewModel {
    override func fetchEquipment() {
        sdk.landPagingData.watch { nullablePagingData in
            guard let list = nullablePagingData?.compactMap({ $0 as? SearchResult }) else {
                return
            }
            self.equipment = list
            self.hasNextPage = self.sdk.landPager.hasNextPage
        }
    }
    override func fetchNextData() {
        sdk.landPager.loadNext()
    }
}
```

I did it this way instead of the single VM like I did for android because for iOS pagination requires the repository to provide two objects,  the `pagingData`, which the view watches, and the `pager`, which is used to load the next page.  Rather than passing both objects to each view, I decided to just wrap them in their own EquipmentType specific VM.  Once that was done I just needed to instantiate the VMs: 

```
import SwiftUI
import shared

@main
struct WegApp: App {
    let sdk = EquipmentSDK(databaseDriverFactory: DatabaseDriverFactory())
    var body: some Scene {
        WindowGroup<EquipmentView> {
            EquipmentView(
                landVm: LandEquipmentViewModel(sdk: sdk),
                airVm: AirEquipmentViewModel(sdk: sdk),
                seaVm: SeaEquipmentViewModel(sdk: sdk)
            )
        }
    }
}
```

Pass them into the main TabView:

```
struct EquipmentView: View {
    @ObservedObject private(set) var landVm: PreviewEquipmentViewModel
    @ObservedObject private(set) var airVm: PreviewEquipmentViewModel
    @ObservedObject private(set) var seaVm: PreviewEquipmentViewModel
	var body: some View {
        TabView {
            EquipmentLazyVGrid(vm: landVm)
                .tabItem{
                    Image("ic_land")
                    Text("Land")
                }
            EquipmentLazyVGrid(vm: airVm)
                .tabItem{
                    Image("ic_air")
                    Text("Air")
                }
            EquipmentLazyVGrid(vm: seaVm)
                .tabItem{
                    Image("ic_sea")
                    Text("Sea")
                }
        }
	}
}
```

And then paginate the grid:

```
struct EquipmentLazyVGrid: View {
    @Environment(\.horizontalSizeClass) var horizontalSizeClass
    @ObservedObject private(set) var vm: PreviewEquipmentViewModel
    @State private var searchText = ""
    
    let columns = [GridItem(.flexible()), GridItem(.flexible())]
    var body: some View {
        NavigationView{
            ScrollView {
                LazyVGrid(columns: getColumns(), spacing: 0) {
                    ForEach (vm.equipment, id: \.id) { equipment in
                        EquipmentCard(equipment: equipment)
                    }
                    if vm.hasNextPage {
                        nextPageView
                    }
                }
            }
        }
        .searchable(text: $searchText, prompt: "Search Equipment")
        .onAppear { vm.fetchEquipment() }
    }
    
    private var nextPageView: some View {
        HStack {
            Spacer()
            VStack {
                ProgressView()
                Text("Loading next page...")
            }
            Spacer()
        }
        .onAppear(perform: {
            vm.fetchNextData()
        })
    }

    func getColumns() -> [GridItem] {
        var columns = [GridItem]()
    
        let numColumns = horizontalSizeClass == .compact ? 2 : 4
        for _ in 1...numColumns {
            columns.append(GridItem(.flexible(),spacing: 0))
        }
        return columns
    }
}
```

![iOS WEG App](/assets/iosweg.png)

By now I'm sure you've noticed a lot of similarities between the components in Jetpack Compose and SwiftUI.  They both even have a Lazy Vertical Grid, with LazyVerticalGrid for Android and LazyVGrid for iOS!  That being said, I did find it marginally easier to reason about SwiftUI. Swift simply doesn't have to deal with all the backstack baggage that Android does.  Creating the routes, destinations, NavHosts, and NavControllers that I did in Jetpack Compose wasn't necessary in SwiftUI.  Instead, view relationships were handled implicitly and elegantly.  I'm sure there are instances when you might want the extra flexibility afforded by Android explicit implementation, but as far as this project is concerned, it's just more stuff to potentially screw up.

I learned a lot during the creation of this app.  KMP as a framework has advanced by leaps and bounds since I first started playing with it at Kotlin Conf 2018.  While the project [was still in Beta as of this writing](https://blog.jetbrains.com/kotlin/2022/10/kmm-beta/), I'm confident it won't be long before the final stable version is released.

Photo by <a href="https://unsplash.com/@yuli_superson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Juli Kosolapova</a> on <a href="https://unsplash.com/s/photos/desert?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
