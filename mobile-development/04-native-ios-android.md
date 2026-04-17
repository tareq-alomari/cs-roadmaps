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

## ⏱️ الوقت المقدر: 6-9 أشهر

## مصادر
- [Apple Developer Documentation](https://developer.apple.com/documentation/)
- [Android Developers](https://developer.android.com/courses)
- [Hacking with Swift](https://www.hackingwithswift.com/) ← مجاني ممتاز
