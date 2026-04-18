# 03 — Flutter

---

## الأساسيات

```dart
// Widget الأساسي
import 'package:flutter/material.dart';

class ProductCard extends StatelessWidget {
  final String name;
  final double price;
  final VoidCallback onTap;

  const ProductCard({
    super.key,
    required this.name,
    required this.price,
    required this.onTap,
  });

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onTap,
      child: Card(
        child: Padding(
          padding: const EdgeInsets.all(16),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              Text(name, style: Theme.of(context).textTheme.titleLarge),
              Text('\$${price.toStringAsFixed(2)}',
                  style: const TextStyle(color: Colors.green)),
            ],
          ),
        ),
      ),
    );
  }
}
```

---

## State Management — Riverpod

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

// Provider
final productsProvider = FutureProvider<List<Product>>((ref) async {
  final response = await http.get(Uri.parse('https://api.example.com/products'));
  return (jsonDecode(response.body) as List).map(Product.fromJson).toList();
});

// StateNotifier لـ Complex State
final cartProvider = StateNotifierProvider<CartNotifier, List<Product>>((ref) {
  return CartNotifier();
});

class CartNotifier extends StateNotifier<List<Product>> {
  CartNotifier() : super([]);
  void add(Product p) => state = [...state, p];
  void remove(String id) => state = state.where((p) => p.id != id).toList();
}

// في الـ Widget
class ProductScreen extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final products = ref.watch(productsProvider);
    return products.when(
      data: (list) => ListView.builder(
        itemCount: list.length,
        itemBuilder: (ctx, i) => ProductCard(
          name: list[i].name,
          price: list[i].price,
          onTap: () => ref.read(cartProvider.notifier).add(list[i]),
        ),
      ),
      loading: () => const CircularProgressIndicator(),
      error: (e, _) => Text('Error: $e'),
    );
  }
}
```

---

## Navigation — GoRouter

```dart
final router = GoRouter(
  routes: [
    GoRoute(path: '/', builder: (ctx, state) => const HomeScreen()),
    GoRoute(
      path: '/product/:id',
      builder: (ctx, state) => ProductScreen(id: state.pathParameters['id']!),
    ),
  ],
);

// التنقل
context.go('/product/123');
context.push('/cart');
context.pop();
```

---

## الأداء والأنيميشن

```dart
// Animations
AnimationController controller = AnimationController(
  vsync: this,
  duration: const Duration(milliseconds: 300),
);

Animation<double> fadeAnim = Tween(begin: 0.0, end: 1.0).animate(controller);

FadeTransition(opacity: fadeAnim, child: myWidget)

// CustomPainter للرسم المخصص
class ChartPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()..color = Colors.blue..strokeWidth = 2;
    canvas.drawLine(Offset.zero, Offset(size.width, size.height), paint);
  }
  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

---

## أمثلة عملية متقدمة

### Advanced State Management

```dart
// ChangeNotifierProvider
class CartNotifier extends ChangeNotifier {
  List<Product> _items = [];

  List<Product> get items => _items;
  double get total => _items.fold(0, (sum, p) => sum + p.price);

  void add(Product p) {
    _items.add(p);
    notifyListeners();
  }

  void remove(String id) {
    _items.removeWhere((p) => p.id == id);
    notifyListeners();
  }
}

// Usage
Consumer<CartNotifier>(
  builder: (ctx, cart, _) => Text('Total: \$${cart.total}'),
)

// Multi-provider
ProviderScope(
  overrides: [
    productsProvider.overrideWithValue(MockProductsRepo()),
  ],
  child: MyApp(),
)
```

### BLoC Pattern

```dart
// Events
abstract class ProductEvent {}

class LoadProducts extends ProductEvent {}

class AddToCart extends ProductEvent {
  final Product product;
  AddToCart(this.product);
}

// States
abstract class ProductState {}

class ProductInitial extends ProductState {}

class ProductLoading extends ProductState {}

class ProductLoaded extends ProductState {
  final List<Product> products;
  ProductLoaded(this.products);
}

// BLoC
class ProductBloc extends Bloc<ProductEvent, ProductState> {
  final ProductRepository repository;

  ProductBloc({required this.repository}) : super(ProductInitial()) {
    on<LoadProducts>((event, emit) async {
      emit(ProductLoading());
      final products = await repository.getProducts();
      emit(ProductLoaded(products));
    });
  }
}

// BlocBuilder & BlocListener
BlocBuilder<ProductBloc, ProductState>(
  builder: (ctx, state) {
    if (state is ProductLoading) return CircularProgressIndicator();
    if (state is ProductLoaded) return ListView(...);
    return SizedBox();
  },
)
```

### Localization

```dart
// flutter_localizations
import 'package:flutter_localizations/flutter_localizations.dart';

MaterialApp(
  localizationsDelegates: [
    AppLocalizations.delegate,
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
  ],
  supportedLocales: [
    Locale('en'),
    Locale('ar'),
  ],
  locale: Locale('ar'),
)

// arb files
{
  "@@locale": "ar",
  "checkout": "الدفع",
  "total": "المجموع"
}

// Usage
Text(AppLocalizations.of(context)!.checkout)
```

### Platform Channels

```dart
// MethodChannel
import 'package:flutter/services.dart';

class NativeChannel {
  static const channel = MethodChannel('com.app/native');

  static Future<String> getBatteryLevel() async {
    final level = await channel.invokeMethod('getBatteryLevel');
    return level;
  }

  static Future<void> showToast(String msg) async {
    await channel.invokeMethod('showToast', {'message': msg});
  }
}

// iOS (Swift)
@objc class AppDelegate: FlutterAppDelegate {
  override func application(...) -> Bool {
    let controller = window?.rootViewController as! FlutterViewController
    let channel = FlutterMethodChannel(
      name: "com.app/native",
      binaryMessenger: controller.binaryMessenger
    )
    channel.setMethodCallHandler { call, result in
      if call.method == "getBatteryLevel" {
        result(UIDevice.current.batteryLevel)
      }
    }
  }
}
```

### App Architecture (Clean)

```
lib/
├── core/
│   ├── errors/
│   ├── network/
│   └── usecases/
├── features/
│   └── products/
│       ├── data/
│       │   ├── datasources/
│       │   ├── models/
│       │   └── repositories/
│       ├── domain/
│       │   ├── entities/
│       │   ├── repositories/
│       │   └── usecases/
│       └── presentation/
│           ├── bloc/
│           ├── pages/
│           └── widgets/
└── injection_container.dart
```

### Testing

```dart
// Unit test
test('cart total calculation', () {
  final cart = CartNotifier();
  cart.add(Product(price: 10));
  cart.add(Product(price: 20));

  expect(cart.total, 30);
});

// Widget test
testWidgets('product card displays name', (tester) async {
  await tester.pumpWidget(
    ProductCard(product: Product(name: 'Test')),
  );

  expect(find.text('Test'), findsOneWidget);
});

// Integration test
testWidgets('full checkout flow', (tester) async {
  await app.launch();
  await tester.tap(find.byType(ProductCard));
  await tester.pumpAndSettle();
  expect(find.text('Checkout'), findsOneWidget);
});
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [Flutter Documentation](https://docs.flutter.dev/)
- [Riverpod Documentation](https://riverpod.dev/)
- [Flutter & Dart - Udemy (Maximilian)](https://www.udemy.com/course/learn-flutter-dart-to-build-ios-android-apps/) ← الأفضل مدفوعًا
