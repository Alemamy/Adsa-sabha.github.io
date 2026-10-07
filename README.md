# عدسة سبها - Flutter + Firebase (المرحلة 2)

هذه النسخة تنقل المشروع من Prototype محلي إلى بنية جاهزة لـ Firebase.

## تم تنفيذ

- Firebase Core.
- Firebase Authentication بالبريد وكلمة المرور.
- Firestore للمستخدمين والإعلانات والعروض والحجوزات.
- Cloud Storage لرفع صور الإعلانات.
- Firebase Cloud Messaging (FCM) كبنية للإشعارات.
- لوحة مستخدم رئيسي Admin.
- تعديل الأسعار من قاعدة البيانات.
- إضافة/تعديل الإعلانات.
- رفع صورة الإعلان.
- إضافة العروض والخصومات.
- تشغيل/إيقاف الإعلانات والعروض.
- الحجوزات وتغيير حالتها من لوحة الإدارة.
- قواعد Firestore وStorage مبدئية للحماية.
- Cloud Functions لإرسال إشعار عند إنشاء حجز أو تغيير حالته.
- وضع Demo يعمل بدون Firebase حتى يتم إعداد المشروع.

## مهم جداً: إعداد Firebase

لا يمكن وضع إعدادات Firebase الحقيقية داخل المشروع بدون مشروع Firebase تابع لك.

1. أنشئ مشروعاً في Firebase Console.
2. ثبّت Firebase CLI وFlutterFire CLI.
3. داخل مجلد المشروع نفّذ:

firebase login
dart pub global activate flutterfire_cli
flutterfire configure

اختر Android وiOS.

بعدها سيُنشأ `lib/firebase_options.dart` الحقيقي.

ثم غيّر في:
`lib/app_config.dart`

من:
`static const bool useFirebase = false;`

إلى:
`static const bool useFirebase = true;`

## تفعيل تسجيل الدخول

في Firebase Console:
Authentication -> Sign-in method -> Email/Password -> Enable

## Firestore

أنشئ Firestore Database ثم انشر القواعد:

firebase deploy --only firestore:rules

## Storage

فعّل Storage ثم:

firebase deploy --only storage

ملاحظة: Firebase يطلب حالياً خطة Blaze لاستخدام Cloud Storage وفق متطلبات Firebase الحالية.

## إنشاء المستخدم الرئيسي

أنشئ حساباً عادياً من التطبيق.
بعدها افتح Firestore:
users -> UID الخاص بالحساب

غيّر:
role: "user"

إلى:
role: "admin"

لا تسمح للمستخدم العادي بتعديل role من التطبيق؛ قواعد Firestore تمنع تغيير دوره بنفسه.

## الإشعارات

ثبّت إعدادات FCM على Android وiOS.

لـ iOS تحتاج إعداد Push Notifications وAPNs في Xcode.

ثم داخل functions:

cd functions
npm install

ثم من جذر المشروع:

firebase deploy --only functions

## الحجز

الحجز في هذه النسخة ينشئ سجل Firestore بحالة `pending`.
طرق الدفع الموجودة في الواجهة:
- cash
- bank
- card (واجهة فقط حالياً)

بوابة دفع حقيقية تحتاج اختيار مزود الدفع وإضافة مفاتيح API الخاصة بحسابك.

## تشغيل نسخة Demo

اترك:
useFirebase = false

ثم:
flutter pub get
flutter run

حساب المدير التجريبي:
admin / 1234

## تشغيل Firebase

بعد flutterfire configure:
useFirebase = true

ثم:
flutter pub get
flutter run

## بناء Android

flutter build apk --release
أو
flutter build appbundle --release

## بناء iOS

flutter build ipa --release

## ملاحظات مهمة

- لم أضع أي مفاتيح Firebase حقيقية.
- لم أضع مفاتيح بطاقة أو بوابة دفع.
- Cloud Storage محمي بقواعد تسمح بالرفع للـ Admin فقط.
- قواعد Firestore تمنع المستخدم العادي من تعديل أسعار الإعلانات أو إنشاء/تعديل العروض.
- يفضل لاحقاً إضافة App Check وCrashlytics قبل النشر التجاري.


## المرحلة الثانية — الحجز والإشعارات

تم ربط شاشة الحجز بقاعدة Firestore: عند تأكيد المستخدم يتم إنشاء مستند داخل `bookings` بحالة `pending`. كما يتم حفظ FCM token داخل `users/{uid}.fcmTokens` عند تسجيل الدخول، تمهيداً لإرسال إشعارات تأكيد/تغيير حالة الحجز.

### تشغيل Firebase
1. نفّذ `firebase login`.
2. ثبّت FlutterFire CLI: `dart pub global activate flutterfire_cli`.
3. من مجلد المشروع نفّذ `flutterfire configure`.
4. غيّر `AppConfig.useFirebase` في `lib/app_config.dart` إلى `true`.
5. فعّل Email/Password من Firebase Authentication.
6. انشر القواعد: `firebase deploy --only firestore:rules,storage`.
7. لتشغيل Cloud Functions: `cd functions && npm install && cd .. && firebase deploy --only functions`.

> بوابة الدفع الحقيقية غير مفعّلة بعد؛ خيار البطاقة موجود كواجهة فقط ويحتاج بيانات مزود دفع مناسب لليبيا.
