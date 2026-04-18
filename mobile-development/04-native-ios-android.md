# 04 — Native iOS & Android

---

## Swift (iOS)

```swift
// SwiftUI — الحديث
import SwiftUI

struct ProductListView: View {
    @StateObject private var viewModel = ProductViewModel()

    var body: some View {
        NavigationStack {
            List(viewModel.products) { product in
                NavigationLink(destination: ProductDetailView(product: product)) {
                    ProductRow(product: product)
                }
            }
            .navigationTitle("Products")
            .task { await viewModel.loadProducts() }
        }
    }
}

// ViewModel
@MainActor
class ProductViewModel: ObservableObject {
    @Published var products: [Product] = []

    func loadProducts() async {
        do {
            let url = URL(string: "https://api.example.com/products")!
            let (data, _) = try await URLSession.shared.data(from: url)
            products = try JSONDecoder().decode([Product].self, from: data)
        } catch {
            print("Error: \(error)")
        }
    }
}
```

---

## Kotlin (Android)

```kotlin
// Jetpack Compose — الحديث
@Composable
fun ProductListScreen(viewModel: ProductViewModel = hiltViewModel()) {
    val products by viewModel.products.collectAsState()

    LazyColumn {
        items(products) { product ->
            ProductCard(
                product = product,
                onClick = { navController.navigate("detail/${product.id}") }
            )
        }
    }
}

// ViewModel
@HiltViewModel
class ProductViewModel @Inject constructor(
    private val repository: ProductRepository
) : ViewModel() {
    val products = repository.getProducts()
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), emptyList())
}
```

---

## متى تختار Native؟

```
✓ تحتاج أعلى أداء ممكن (Games, AR/VR)
✓ تستخدم أحدث iOS/Android APIs
✓ تطبيق لمنصة واحدة فقط
✓ لديك فريق منفصل لكل منصة
```

---

## أمثلة عملية متقدمة

### SwiftUI Navigation

```swift
// NavigationSplitView (iOS 16+)
struct ContentView: View {
    @State private var columnVisibility = NavigationSplitViewVisibility.all

    var body: some View {
        NavigationSplitView(columnVisibility: $columnVisibility) {
            Sidebar()
        } detail: {
            Detail()
        }
    }
}

// NavigationStack (iOS 16+)
struct ProductsView: View {
    @StateObject private var viewModel = ProductViewModel()
    @State private var selectedProduct: Product?

    var body: some View {
        NavigationStack {
            List(selection: $selectedProduct) {
                ForEach(viewModel.products) { product in
                    NavigationLink(value: product) {
                        ProductRow(product: product)
                    }
                }
            }
            .navigationDestination(for: Product.self) { product in
                ProductDetailView(product: product)
            }
        }
    }
}
```

### Swift Concurrency

```swift
// async/await
@MainActor
class ProductViewModel: ObservableObject {
    @Published var products: [Product] = []

    func loadProducts() async throws {
        let url = URL(string: "https://api.example.com/products")!
        let (data, _) = try await URLSession.shared.data(from: url)
        products = try JSONDecoder().decode([Product].self, from: data)
    }
}

// Task
Task {
    do {
        try await viewModel.loadProducts()
    } catch {
        print(error)
    }
}

// Actor for thread safety
actor ProductCache {
    private var cache: [String: Product] = [:]

    func get(_ id: String) -> Product? {
        cache[id]
    }

    func set(_ product: Product) {
        cache[product.id] = product
    }
}
```

### Kotlin Coroutines & Flow

```kotlin
// Repository
class ProductRepository(
    private val api: ProductApi,
    private val dao: ProductDao
) {
    fun getProducts(): Flow<List<Product>> = flow {
        // Emit cached first
        emit(dao.getAll())

        // Then fetch from network
        val remote = api.getProducts()
        dao.insertAll(remote)

        // Emit updated
        emit(remote)
    }.catch { e ->
        emit(dao.getAll()) // Fallback to cache
    }.flowOn(Dispatchers.IO)
}

// ViewModel
@HiltViewModel
class ProductViewModel @Inject constructor(
    private val repository: ProductRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(ProductUiState())
    val uiState: StateFlow<ProductUiState> = _uiState.asStateFlow()

    init {
        viewModelScope.launch {
            repository.getProducts()
                .collect { products ->
                    _uiState.update { it.copy(products = products) }
                }
        }
    }
}
```

### Jetpack Compose Navigation

```kotlin
@Composable
fun NavHost(
    navController: NavHostController,
    modifier: Modifier = Modifier
) {
    NavHost(
        navController = navController,
        startDestination = "products"
    ) {
        composable("products") {
            ProductsScreen(
                onProductClick = { id ->
                    navController.navigate("product/$id")
                }
            )
        }

        composable(
            route = "product/{productId}",
            arguments = listOf(
                navArgument("productId") { type = NavType.StringType }
            )
        ) { backStackEntry ->
            val productId = backStackEntry.arguments?.getString("productId")
            ProductDetailScreen(productId = productId!!)
        }
    }
}
```

### Dependency Injection

```swift
// SwiftDI with @MainActor
@MainActor
final class ProductService {
    static let shared = ProductService()

    private init() {}

    func fetchProducts() async throws -> [Product] {
        // implementation
    }
}

// Usage
@StateObject private var viewModel = ProductService.shared.makeViewModel()

// Kotlin DI with Hilt
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {
    @Inject lateinit var repository: ProductRepository
}
```

---

## ⏱️ الوقت المقدر: 6-9 أشهر

## مصادر
- [Apple Developer Documentation](https://developer.apple.com/documentation/)
- [Android Developers](https://developer.android.com/courses)
- [Hacking with Swift](https://www.hackingwithswift.com/) ← مجاني ممتاز
