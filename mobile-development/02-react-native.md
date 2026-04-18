# 02 — React Native

---

## الأساسيات

```jsx
import { View, Text, StyleSheet, FlatList, TouchableOpacity } from 'react-native'
import { useState, useEffect } from 'react'

export default function ProductList() {
  const [products, setProducts] = useState([])

  useEffect(() => {
    fetch('https://api.example.com/products')
      .then(r => r.json())
      .then(setProducts)
  }, [])

  return (
    <FlatList
      data={products}
      keyExtractor={item => item.id.toString()}
      renderItem={({ item }) => (
        <TouchableOpacity style={styles.card} onPress={() => navigate('Detail', { id: item.id })}>
          <Text style={styles.title}>{item.name}</Text>
          <Text>{item.price} SAR</Text>
        </TouchableOpacity>
      )}
    />
  )
}

const styles = StyleSheet.create({
  card: { padding: 16, margin: 8, backgroundColor: '#fff', borderRadius: 8 },
  title: { fontSize: 18, fontWeight: 'bold' },
})
```

---

## Navigation — React Navigation

```jsx
import { NavigationContainer } from '@react-navigation/native'
import { createNativeStackNavigator } from '@react-navigation/native-stack'
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs'

const Stack = createNativeStackNavigator()
const Tab = createBottomTabNavigator()

function HomeTabs() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
    </Tab.Navigator>
  )
}

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Main" component={HomeTabs} options={{ headerShown: false }} />
        <Stack.Screen name="Detail" component={DetailScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  )
}
```

---

## State Management — Zustand

```javascript
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'
import AsyncStorage from '@react-native-async-storage/async-storage'

const useCartStore = create(
  persist(
    (set, get) => ({
      items: [],
      addItem: (product) => set(state => ({ items: [...state.items, product] })),
      removeItem: (id) => set(state => ({ items: state.items.filter(i => i.id !== id) })),
      total: () => get().items.reduce((sum, i) => sum + i.price, 0),
    }),
    { name: 'cart', storage: createJSONStorage(() => AsyncStorage) }
  )
)
```

---

## Native Modules الشائعة

```bash
# الأكثر استخدامًا
@react-native-camera/react-native-camera    # الكاميرا
react-native-maps                           # الخرائط
react-native-keychain                       # التخزين الآمن
@react-native-async-storage/async-storage   # التخزين المحلي
react-native-push-notification              # الإشعارات
react-native-image-picker                   # اختيار الصور

# Expo SDK (إذا تستخدم Expo)
expo-camera, expo-location, expo-notifications
```

---

## Expo vs Bare React Native

```
Expo Managed:
✓ بداية سريعة
✓ OTA Updates
✗ قيود على Native modules

Bare React Native:
✓ تحكم كامل
✓ جميع الـ Native modules
✗ تحتاج Xcode + Android Studio
```

---

## أمثلة عملية متقدمة

### Advanced Navigation

```jsx
// Nested navigation + auth flow
const AuthStack = createNativeStackNavigator();
const MainStack = createNativeStackNavigator();

function AuthFlow() {
  return (
    <AuthStack.Navigator>
      <AuthStack.Screen name="Login" component={LoginScreen} />
      <AuthStack.Screen name="Register" component={RegisterScreen} />
    </AuthStack.Navigator>
  );
}

function MainFlow() {
  return (
    <MainStack.Navigator>
      <MainStack.Screen name="Home" component={HomeTabs} />
      <MainStack.Screen name="Detail" component={DetailScreen} />
    </MainStack.Navigator>
  );
}

export default function App() {
  const { isAuthenticated } = useAuth();

  return (
    <NavigationContainer>
      {isAuthenticated ? <MainFlow /> : <AuthFlow />}
    </NavigationContainer>
  );
}
```

### React Query for API

```javascript
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

const queryClient = new QueryClient();

function ProductsScreen() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['products'],
    queryFn: () => fetch('/api/products').then(r => r.json()),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });

  const mutation = useMutation({
    mutationFn: newProduct => post('/api/products', newProduct),
    onSuccess: () => {
      queryClient.invalidateQueries(['products']);
    },
  });

  return (
    <FlatList
      data={data}
      renderItem={({ item }) => <ProductCard {...item} />}
    />
  );
}
```

### Animations

```javascript
import { Animated, PanResponder, Dimensions } from 'react-native';

const SWIPE_THRESHOLD = Dimensions.get('window').width * 0.25;

const SwipeableCard = ({ onSwipe }) => {
  const position = new Animated.ValueXY();

  const panResponder = PanResponder.create({
    onStartShouldSetPanResponder: () => true,
    onPanResponderMove: (_, gesture) => {
      position.setValue({ x: gesture.dx, y: gesture.dy });
    },
    onPanResponderRelease: (_, gesture) => {
      if (gesture.dx > SWIPE_THRESHOLD) {
        Animated.spring(position, {
          toValue: { x: Dimensions.get('window').width, y: gesture.dy },
        }).start(onSwipe);
      } else {
        Animated.spring(position, {
          toValue: { x: 0, y: 0 },
          useNativeDriver: true,
        }).start();
      }
    },
  });

  return (
    <Animated.View
      style={[styles.card, position.getLayout()]}
      {...panResponder.panHandlers}
    />
  );
};
```

### Performance Optimization

```javascript
// 1. React.memo for expensive components
const ProductCard = React.memo(({ product }) => (
  <View>{/* expensive render */}</View>
));

// 2. useMemo for expensive calculations
const expensiveData = useMemo(() =>
  computeExpensiveData(items), [items]);

// 3. useCallback for event handlers
const handlePress = useCallback((id) => {
  navigation.navigate('Detail', { id });
}, [navigation]);

// 4. FlatList optimizations
<FlatList
  data={products}
  windowSize={5}
  initialNumToRender={10}
  maxToRenderPerBatch={10}
  removeClippedSubviews={true}
  getItemLayout={(item, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index,
  })}
/>
```

### Testing

```javascript
// Testing with Jest + React Native Testing Library
import { render, fireEvent, waitFor } from '@testing-library/react-native';

test('adds product to cart', async () => {
  const { getByText } = render(<CartTestWrapper />);

  fireEvent.press(getByText('Add to Cart'));

  await waitFor(() => {
    expect(getByText('Total: $99')).toBeTruthy();
  });
});
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [React Native Documentation](https://reactnative.dev/docs/getting-started)
- [Expo Documentation](https://docs.expo.dev/)
- [William Candillon (YouTube)](https://www.youtube.com/@wcandillon) ← للأنيميشن المتقدم
