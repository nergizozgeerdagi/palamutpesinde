# Palamut Peşinde — Android (Play Store) paketi

Oyunun tamamı `www/index.html` dosyasındadır. İnternet gerektirmez; ilerleme telefonda saklanır.
Bu klasör, oyunu Capacitor ile Android uygulamasına çevirmek için hazırdır.

## Android Studio kurmadan APK almak (GitHub ile)

Bu klasörde hazır bir derleme tarifi var (`.github/workflows/apk.yml`). GitHub, APK'yı senin yerine derler:

1. github.com'da ücretsiz hesap aç, yeni bir depo (repository) oluştur.
2. Bu klasörün **içindeki** her şeyi depoya yükle (`.github` klasörü dahil; "Add file > Upload files" ile sürükleyebilirsin).
3. Deponun **Actions** sekmesine gir. "APK" işi kendiliğinden başlar, 3-5 dakika sürer.
4. İş yeşil olunca üstüne tıkla, en altta **Artifacts > palamut-pesinde-apk** dosyasını indir (zip içinde `app-debug.apk`).
5. APK'yı telefona at, dokun, "bilinmeyen kaynaklardan yüklemeye izin ver" de ve kur.

Bu APK deneme (debug) sürümüdür: telefonda oynamak ve arkadaşlara göndermek için uygundur.
Play Store'a yüklemek için aşağıdaki imzalı AAB adımları gerekir.

## Gerekenler (Play Store sürümü için)

- Node.js 20 veya üstü
- Android Studio (içinde Android SDK ve JDK gelir)
- Google Play Console geliştirici hesabı (tek seferlik 25 USD)

## 1. Android projesini oluştur

```bash
npm install
npx cap add android
npm run icons        # assets/icon-only.png dosyasından uygulama ikonlarını üretir
npx cap sync android
npx cap open android # Android Studio açılır
```

`npx cap add android` öncesinde `capacitor.config.json` içindeki `appId` değerini kendi kimliğinle
değiştir (örnek: `com.seninadin.palamutpesinde`). Play Store'a yüklendikten sonra bu kimlik değiştirilemez.

## 2. Dikey ekrana kilitle

`android/app/src/main/AndroidManifest.xml` içindeki `<activity ...>` etiketine şunu ekle:

```xml
android:screenOrientation="portrait"
```

## 3. Telefonda dene

Android Studio'da telefonu USB ile bağla (Geliştirici seçenekleri > USB hata ayıklama açık) ve Run ▶ düğmesine bas.

## 4. Play Store için imzalı paket (AAB)

1. Android Studio > Build > Generate Signed App Bundle / APK > Android App Bundle
2. Yeni bir keystore oluştur. Dosyayı ve şifreyi kaybetme; güncelleme yayınlamak için gerekir.
3. `release` seç. Çıkan `app-release.aab` dosyasını Play Console'a yükle.

Her yeni sürümde `android/app/build.gradle` içindeki `versionCode` değerini 1 artır.

## 5. Play Console'da doldurulacaklar

- Mağaza görselleri: `store/play-icon-512.png` (ikon), `store/feature-1024x500.png` (öne çıkan görsel),
  ayrıca en az 2 telefon ekran görüntüsü (oyunu açıp kendin alabilirsin).
- Gizlilik politikası bağlantısı: oyun veri toplamıyor, yine de Play bir bağlantı ister.
- Veri güvenliği formu: "veri toplanmıyor / paylaşılmıyor".
- İçerik derecelendirme anketi ve hedef kitle. 13 yaş altını hedeflersen Aileler Politikası kuralları devreye girer.
- Yeni kişisel hesaplarda Google, yayına çıkmadan önce 12 test kullanıcısıyla 14 günlük kapalı test ister.

## Oyunu güncellemek

`www/index.html` değişince:

```bash
npx cap sync android
```

Ardından yeni AAB üret.

## Notlar

- Oyunun adı ve `appId` örnek değerlerdir; Play Store'da aynı adın kullanılıp kullanılmadığını kontrol et.
- `package.json` Capacitor'ın en güncel sürümünü kurar. Play'in hedef API şartı her yıl yükseldiği için bu bilinçli bir tercih.
- Android geri tuşu oyunda bir önceki ekrana döner (`@capacitor/app` eklentisi ile).
