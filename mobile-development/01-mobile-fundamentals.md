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

## ⏱️ الوقت المقدر: 2-3 أسابيع (للمفاهيم)
