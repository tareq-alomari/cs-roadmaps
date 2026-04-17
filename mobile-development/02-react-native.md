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

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [React Native Documentation](https://reactnative.dev/docs/getting-started)
- [Expo Documentation](https://docs.expo.dev/)
- [William Candillon (YouTube)](https://www.youtube.com/@wcandillon) ← للأنيميشن المتقدم
