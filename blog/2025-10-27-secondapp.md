---
slug: secondapp
title: Firebase Multiple App For React Native
authors: [yakupdurmus]
tags: [firebase,android,native,notification]
---

# Firebase Multiple App For RN! 👋

Merhaba, bir uygulamadaya 2 farklı firebase bağlayıp, notifikasyon almayla ilgili bir konudan bahsedeceğim. 

Şimdi, buna neden ihtiyaç duyduğumdan ve nasıl çözdüğümden size bahsetmek istiyorum. Resimdeki gibi 2 farklı uygulamam var. Bu uygulamalarıma daha önce firebase entegrasyonu yapmıştım, Analytics, Crash vs özelliklerini kullanıyorum. Play store da yayınlamıştım. 

<img src="/img/second-app-flow-1.png" alt="Resim 1" style={{ width: "60%" }} />


İlk uygulamayı chat servisine bağladığımda güzel bir şekilde notifiaskyonları alıyorum. Fakat aynı zamanda ikinci uygulamamıda bağlamak istiyorum. Fakat servis account dosyasını ikinci uygulama service account dosyasıyla güncellediğimde ilk uygulamanın notifikasyonu haliyle bozuluyor.

Her iki uygulamayı tek chat servisine bağlamak istiyorum. Bu durumda nasıl bir çözüm yolu izlediğimi sizinle paylaşmak istiyorum.

Var olan firebase accountlarını silemem, farklı yerlerde kullanılıyor. Bahsettiğim gibi analitik gibi farklı entegrsayonları var. Bu durumda yeni bir Firebase projesi oluşturdum. Birlikte aşağıdaki resmi inceleyelim.

<img src="/img/second-app-flow-2.png" alt="Resim 2" style={{width: "60%"}} />

Oluşturduğum yeni firebase dosyasını iki dosyaya bağladım. Bu şekilde ortak bir firebase projesi altında birinci ve ikinci uygulamam yer alıyor. Daha sonra bu firebase projemden ürettiğim google servis account'u chat servisine yükledim. 

Artık iki uygulama Firebase C ye yani ortak bir firebase projesine de bağlı. 

Bundan sonra Chat Servisini proje içerisinde intial ederken Firebase C için üretilen FCM token'i Chat servisine göndermek kalıyor.



Örnek olarak ikinci Firebase projesini intial etme kodu 

```kotlin

private static String firebaseAppName = "multiple-firebase-app";
FirebaseOptions options = new FirebaseOptions.Builder()
.setApplicationId("1:XXX:android:YYYY")
.setApiKey("your-api-key")
.setProjectId("your-project-id")
.setGcmSenderId("your-sender-id")
.build();

FirebaseApp.initializeApp(context, options, firebaseAppName);

```



daha sonra token leri set ediyoruz

```kotlin
FirebaseMessaging.getInstance().getToken().addOnSuccessListener(new OnSuccessListener<String>() {
    @Override
    public void onSuccess(String s) {
        Log.d("MultipleFirebase","Multiple FCM Token: "+s);
    }
})
.addOnFailureListener(new OnFailureListener() {
    @Override
    public void onFailure(@NonNull Exception e) {
        Log.e("MultipleFirebase","Failed to get multiple FCM token: "+e.getMessage());
    }
});


```


```kotlin

FirebaseApp secondApp = FirebaseApp.getInstance(firebaseAppName);
FirebaseMessaging firebaseMessaging = secondApp.get(FirebaseMessaging.class);
firebaseMessaging.getToken().addOnSuccessListener(new OnSuccessListener<String>() {
    @Override
    public void onSuccess(String token) {
        Log.d("MultipleFirebase", "Alternative FCM Token: " + token);
        // YourChatSDK.setPushRegistrationToken(token);
    }
})
.addOnFailureListener(new OnFailureListener() {
    @Override
    public void onFailure(@NonNull Exception e) {
        Log.e("MultipleFirebase", "Failed to get alternative FCM token: " + e.getMessage());
    }
});
```



