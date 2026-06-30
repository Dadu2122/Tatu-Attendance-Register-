# TATU 4.1 — Android App Build Guide

Yeh folder ek ready Capacitor project hai. Aapki website (`www/index.html`) already
copy ho chuki hai, aur usmein ek **native persistence bridge** add kiya gaya hai
(neeche dekhein "Data Persistence Fix" section).

## Requirements (apne computer pe install karein)
- Node.js (v18+) — https://nodejs.org
- Android Studio (isme Android SDK + Gradle automatically aa jaata hai) — https://developer.android.com/studio
- JDK 17 (Android Studio ke saath bundled aata hai)

## Build Steps (Mobile-only, no PC/Laptop needed)

Agar aapke paas sirf phone hai, GitHub Actions se free cloud build karein:

1. **GitHub account banayein** (agar nahi hai): github.com pe phone browser se signup.
2. **Naya repository banayein**: "+" icon → "New repository" → naam dein
   (e.g. `tatu-attendance`) → Public ya Private, "Create repository".
3. **Files upload karein**: repo page pe "Add file" → "Upload files" → is
   `tatu-app` folder ke andar ke saare files/folders select karke upload
   karein (`www/`, `.github/`, `capacitor.config.json`, `package.json`,
   `README.md` — saara structure same rakhein). "Commit changes" daba dein.
   (Tip: phone file manager se zip pehle extract kar lein, phir GitHub ka
   upload box mein sab files drag/select kar dein.)
4. **Actions tab kholein**: repo ke andar "Actions" tab pe jaayein → left
   side "Build Android APK" workflow dikhega → "Run workflow" button dabayein
   (agar push se automatically start na ho).
5. **Build complete hone ka wait karein** (5-10 minute lagte hain) — green
   tick ✅ aane par us run pe click karein.
6. Neeche "Artifacts" section mein **`tatu-debug-apk`** dikhega — uspar tap
   karke zip download ho jayegi, usme `app-debug.apk` hoga. Wahi APK phone
   pe install kar lein.

Is tareeke se aapko Android Studio ya Node install karne ki zaroorat nahi
padti — sab kuch GitHub ke servers pe ban jaata hai, aap sirf upload aur
download karte hain.

## Build Steps (with a PC/Laptop, optional)

```bash
cd tatu-app
npm install
npx cap add android
npx cap sync android
```

Phir ek in dono tareeke se build karein:

### Option A — Android Studio se (recommended, easy)
```bash
npx cap open android
```
Android Studio khulega → upar `Build` menu → `Build Bundle(s) / APK(s)` → `Build APK(s)`.
APK yahan milega: `android/app/build/outputs/apk/debug/app-debug.apk`

### Option B — Command line se
```bash
cd android
./gradlew assembleDebug
```
APK: `android/app/build/outputs/apk/debug/app-debug.apk`

### Release (signed) APK ke liye
Play Store ya distribution ke liye signed APK chahiye to:
```bash
./gradlew assembleRelease
```
Isse pehle `android/app/build.gradle` mein apni keystore details daalni hongi
(Android Studio "Generate Signed Bundle/APK" wizard se aasan ho jaata hai).

## Data Persistence Fix (important)

`www/index.html` ke end mein ek script add ki gayi hai jo `localStorage` ke
har read/write ko Capacitor ke native `Preferences` plugin (Android
SharedPreferences) mein bhi mirror karti hai. Iska matlab:

- Pehle: agar koi Chrome ki "Clear cookies and site data" use karta tha,
  to attendance data poora delete ho jaata tha.
- Ab: app ek alag, isolated WebView mein chalti hai (Chrome browser se
  bilkul connect nahi), aur data do jagah save hota hai — localStorage
  AND native Android storage. Agar kabhi localStorage khaali mil jaaye
  (jaise app reinstall ya WebView data clear), to app automatically
  native backup se data restore kar leti hai.

**Note:** Yeh "Clear app data / Uninstall" se bachne ka guarantee nahi
deta — agar user Android Settings se is specific app ka "Clear Data"
karta hai, dono storage clear ho jaayenge. Uske liye **cloud backup**
(Firebase/Google Drive sync) chahiye hoga — agar woh bhi chahiye to
bata dein, woh agla step kar sakte hain.

## App Identity
- App ID: `com.amarshreeyog.tatu`
- App Name: TATU 4.1 - Attendance Register

Icon/splash screen customize karne ke liye `npx cap` ke saath
`@capacitor/assets` tool use karein, ya Android Studio mein directly
`android/app/src/main/res` ke mipmap folders replace karein.
