# 01 — أساسيات تطوير الموبايل

---

## مفاهيم مشتركة بين جميع المنصات

### دورة حياة التطبيق

```
iOS:              Android:
viewDidLoad       onCreate
viewWillAppear    onStart
viewDidAppear     onResume
viewWillDisappear onPause
viewDidDisappear  onStop
deinit            onDestroy
```

### إدارة الحالة

```
المشكلة الأساسية في تطوير الموبايل:
UI State ← يتغير بتفاعل المستخدم
App State ← بيانات التطبيق
Server State ← بيانات من الـ API

المبدأ: Single Source of Truth
```

### الشبكة

```
- REST API calls
- WebSockets للـ Real-time
- Offline-first: تخزين محلي + Sync
- Certificate Pinning للأمان
```

### الأداء

```
- 60fps = 16ms لكل frame
- Lazy loading للصور
- تقليل Re-renders
- Memory leaks (Retain cycles في iOS)
- Battery & Network optimization
```

### التخزين المحلي

```
iOS:             Android:           Cross-platform:
UserDefaults     SharedPreferences  AsyncStorage
CoreData         Room               SQLite
Keychain         EncryptedSharedPref SecureStore
```

### الأذونات (Permissions)

```
يجب طلبها في وقت الاستخدام:
- Camera, Microphone
- Location
- Notifications
- Contacts
- Storage (Android)
```

---

## App Store & Play Store

```
iOS App Store:
- مراجعة يدوية (1-3 أيام)
- $99/سنة للـ Developer Account
- متطلبات صارمة للـ Privacy

Google Play Store:
- مراجعة سريعة (ساعات)
- $25 مرة واحدة
- أكثر مرونة
```

---

## Push Notifications

```
Firebase Cloud Messaging (FCM) ← Android + iOS
APNs (Apple Push Notification Service) ← iOS فقط

الـ Flow:
1. التطبيق يطلب إذن من المستخدم
2. يحصل على Device Token
3. يرسله للـ Server
4. السيرفر يرسل Notification عبر FCM/APNs
```

---

## Deep Linking

```
Universal Links / App Links:
https://app.example.com/product/123
→ يفتح التطبيق مباشرة إذا مثبت
→ يفتح الموقع إذا غير مثبت

Custom URL Schemes:
myapp://product/123
```

---

## أمثلة عملية متقدمة

###Local Storage Comparison

```javascript
// SharedPreferences (Android) - مفتاح-قيمة
const prefs = await SharedPreferences.getInstance();
await prefs.setString('token', 'abc123');
const token = await prefs.getString('token');

// UserDefaults (iOS) - Same API
UserDefaults.standard.set('token', 'abc123');
const token = UserDefaults.standard.string(forKey: 'token');

// AsyncStorage (Cross-platform)
import AsyncStorage from '@react-native-async-storage/async-storage';
await AsyncStorage.setItem('token', 'abc123');
const token = await AsyncStorage.getItem('token');

// Encrypted Storage (أفضل للبيانات الحساسة)
import * as SecureStore from 'expo-secure-store';
await SecureStore.setItemAsync('token', 'abc123');
const token = await SecureStore.getItemAsync('token');
```

### Network Layer Design

```javascript
// API Client مع interceptors
class ApiClient {
  constructor(baseUrl) {
    this.client = axios.create({ baseURL: baseUrl });
    this.client.interceptors.request.use(config => {
      const token = getToken();
      config.headers.Authorization = `Bearer ${token}`;
      return config;
    });
  }

  async get(path, params) {
    try {
      const response = await this.client.get(path, { params });
      return response.data;
    } catch (error) {
      if (error.response?.status === 401) {
        await refreshToken();
        return this.get(path, params);
      }
      throw error;
    }
  }
}

// Offline-First Strategy
class OfflineManager {
  async sync(localChanges, remoteUrl) {
    // 1. Save locally first
    await AsyncStorage.setItem('pending_sync', JSON.stringify(localChanges));

    // 2. Try to sync when online
    if (await this.isOnline()) {
      try {
        await fetch(remoteUrl, localChanges);
        await AsyncStorage.removeItem('pending_sync');
      } catch (e) {
        // Keep for later retry
      }
    }
  }

  async pendingChanges() {
    const data = await AsyncStorage.getItem('pending_sync');
    return data ? JSON.parse(data) : [];
  }
}
```

###Push Notifications Flow

```javascript
//1. Request permission
import * as Notifications from 'expo-notifications';

const { status } = await Notifications.requestPermissionsAsync();

// 2. Get device token
const token = await Notifications.getDevicePushTokenAsync();

// 3. Send to your server
await api.registerPushToken(token);

// 4. Handle notifications
Notifications.addNotificationReceivedListener(notification => {
  const { title, body, data } = notification.request.content;
  // Handle notification
});

// 5. Handle tap (deep link)
Notifications.addNotificationResponseReceivedListener(response => {
  const url = response.notification.request.content.data.url;
  navigation.navigate(url);
});
```

### App Lifecycle Handling

```javascript
import { AppState } from 'react-native';

useEffect(() => {
  const subscription = AppState.addEventListener('change', handleAppStateChange);

  return () => subscription.remove();
}, []);

function handleAppStateChange(nextAppState) {
  if (nextAppState === 'active') {
    // App came to foreground
    refreshData();
  } else if (nextAppState === 'background') {
    // App went to background
    saveState();
  }
}

// For iOS Background Tasks
import * as BackgroundFetch from 'expo-background-fetch';

BackgroundFetch.registerTaskAsync('sync', {
  minimumInterval: 15 * 60, // 15 minutes
});
```

---

## ⏱️ الوقت المقدر: 2-3 أسابيع (للمفاهيم)
