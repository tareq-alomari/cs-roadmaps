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

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [Flutter Documentation](https://docs.flutter.dev/)
- [Riverpod Documentation](https://riverpod.dev/)
- [Flutter & Dart - Udemy (Maximilian)](https://www.udemy.com/course/learn-flutter-dart-to-build-ios-android-apps/) ← الأفضل مدفوعًا
