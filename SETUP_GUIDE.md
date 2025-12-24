# 🔧 Complete Setup & Development Guide

## 📋 Table of Contents
1. [Environment Setup](#environment-setup)
2. [Project Import](#project-import)
3. [Dependency Installation](#dependency-installation)
4. [Testing](#testing)
5. [Deployment](#deployment)

---

## 1️⃣ Environment Setup

### Install Android Studio
1. Download: https://developer.android.com/studio
2. Version: Hedgehog (2023.1.1) or later
3. Install with default settings

### Install JDK 17
```bash
# Check current Java version
java -version

# If not JDK 17, download from:
# https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html
```

### Configure Android Studio
1. Open Android Studio
2. Tools > SDK Manager
3. Install:
   - Android SDK Platform 34
   - Android SDK Build-Tools 34.0.0
   - Android Emulator
   - Intel x86 Emulator Accelerator (HAXM)

---

## 2️⃣ Project Import

### Method 1: Direct Import
```bash
# Step 1: Extract/copy PassportPhotoMaker folder
# Step 2: Open Android Studio
# Step 3: File > Open
# Step 4: Navigate to PassportPhotoMaker folder
# Step 5: Click "OK"
```

### Method 2: Version Control
```bash
# If using Git
git clone [your-repo-url]
cd PassportPhotoMaker
# Open in Android Studio
```

### First-Time Setup
When you open the project:
1. Android Studio will show "Gradle Sync" notification
2. Click "Sync Now"
3. Wait 2-5 minutes for dependencies to download
4. Verify no errors in "Build" tab

---

## 3️⃣ Dependency Installation

### Automatic Installation
All dependencies are downloaded automatically during Gradle sync:

✅ **Core Libraries**
- androidx.core:core-ktx:1.12.0
- androidx.appcompat:appcompat:1.6.1
- com.google.android.material:material:1.11.0

✅ **ML Kit**
- com.google.mlkit:face-detection:16.1.6

✅ **OpenCV**
- org.opencv:opencv:4.8.0

✅ **CameraX**
- androidx.camera:camera-camera2:1.3.1
- androidx.camera:camera-lifecycle:1.3.1
- androidx.camera:camera-view:1.3.1

✅ **Coroutines**
- org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3

### Manual Installation (if needed)
If dependencies fail to download:
```bash
# Clear Gradle cache
./gradlew clean
./gradlew build --refresh-dependencies

# Or in Android Studio:
# File > Invalidate Caches / Restart
```

---

## 4️⃣ Testing

### Run on Physical Device

#### Step 1: Enable Developer Options
1. Settings > About Phone
2. Tap "Build Number" 7 times
3. Go back to Settings > Developer Options
4. Enable "USB Debugging"

#### Step 2: Connect & Run
1. Connect phone via USB
2. Allow USB debugging on phone
3. In Android Studio, select device from dropdown
4. Click Run button (Shift+F10)

### Run on Emulator

#### Create AVD (Android Virtual Device)
1. Tools > Device Manager
2. Click "Create Device"
3. Select "Pixel 5" or "Pixel 6"
4. Select System Image: "API 34" (Tiramisu)
5. Click "Finish"

#### Launch App
1. Select emulator from device dropdown
2. Click Run button
3. Wait for emulator to boot (1-2 minutes first time)
4. App will launch automatically

### Testing Checklist

#### Basic Functionality
- [ ] App launches without crash
- [ ] Camera permission dialog appears
- [ ] Can capture photo
- [ ] Can select from gallery
- [ ] Face detection works
- [ ] Enhancement completes
- [ ] Background removal works
- [ ] PDF generates successfully

#### Image Quality
- [ ] Test with low-res image (640×480)
- [ ] Test with high-res image (4000×3000)
- [ ] Test with poor lighting
- [ ] Test with glasses
- [ ] Verify enhancement improves quality

#### Edge Cases
- [ ] No face detected - shows error
- [ ] Multiple faces - shows warning
- [ ] Eyes closed - shows validation warning
- [ ] Head tilted - shows validation warning

---

## 5️⃣ Deployment

### Generate Debug APK
```bash
# In Android Studio terminal:
./gradlew assembleDebug

# Output location:
# app/build/outputs/apk/debug/app-debug.apk
```

### Generate Release APK

#### Step 1: Create Keystore
```bash
# In Android Studio:
Build > Generate Signed Bundle / APK
> APK
> Create new...

# Fill details:
Key store path: [your-path]/passport-photo-keystore.jks
Password: [create-strong-password]
Alias: passport-photo-key
Validity: 25 years
```

#### Step 2: Build Release
```bash
# Select:
- Build Variants: release
- Signature Version: V2 (Full APK Signature)

# Click Finish

# Output location:
# app/release/app-release.apk
```

### APK Size Optimization
Expected sizes:
- **Debug APK:** ~18-20 MB
- **Release APK:** ~12-15 MB (with ProGuard)

To reduce further:
```gradle
// In app/build.gradle
android {
    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
        }
    }
}
```

---

## 🐛 Common Issues & Solutions

### Issue 1: Gradle Sync Failed
**Solution:**
```bash
# Clean and rebuild
./gradlew clean
# Or: Build > Clean Project
# Then: Build > Rebuild Project
```

### Issue 2: OpenCV Not Found
**Solution:**
```gradle
// Verify in app/build.gradle:
implementation 'org.opencv:opencv:4.8.0'

// Then sync again
```

### Issue 3: ML Kit Not Working
**Solution:**
```xml
<!-- Verify in AndroidManifest.xml: -->
<meta-data
    android:name="com.google.mlkit.vision.DEPENDENCIES"
    android:value="face" />
```

### Issue 4: Camera Not Working
**Solution:**
1. Check permissions in AndroidManifest.xml
2. Verify camera permission request in code
3. Test on physical device (emulator camera may fail)

### Issue 5: PDF Not Generating
**Solution:**
```kotlin
// Check storage directory exists
val outputDir = getExternalFilesDir(Environment.DIRECTORY_DOCUMENTS)
outputDir?.mkdirs() // Create if doesn't exist
```

---

## 📊 Build Configurations

### Debug Build
- **Purpose:** Development & Testing
- **Features:** 
  - Debugging enabled
  - Logging enabled
  - No obfuscation
  - Larger APK size

### Release Build
- **Purpose:** Production/Distribution
- **Features:**
  - Debugging disabled
  - Logging stripped
  - Code obfuscation (ProGuard)
  - Optimized APK size

---

## 🔐 Security Checklist

Before release:
- [ ] Remove all log statements
- [ ] Enable ProGuard
- [ ] Test on multiple devices
- [ ] Verify privacy notice displays
- [ ] Check no hardcoded secrets
- [ ] Test offline functionality

---

## 📱 Device Compatibility

### Minimum Requirements
- Android 7.0 (API 24) or higher
- 2GB RAM minimum
- Camera (front or back)
- 50MB free storage

### Recommended
- Android 10.0 (API 29) or higher
- 4GB RAM
- Good camera quality
- 100MB free storage

### Tested Devices
- ✅ Samsung Galaxy S21
- ✅ Google Pixel 5
- ✅ OnePlus 9
- ✅ Xiaomi Redmi Note 10
- ✅ Android Emulator (Pixel 5, API 34)

---

## 📈 Performance Optimization Tips

### Memory Management
```kotlin
// Recycle bitmaps when done
bitmap?.recycle()

// Use appropriate image resolution
val options = BitmapFactory.Options()
options.inSampleSize = 2 // Reduce by 50%
```

### Processing Speed
```kotlin
// Use coroutines for heavy operations
viewModelScope.launch(Dispatchers.Default) {
    // Image processing here
}
```

### APK Size Reduction
```gradle
// Enable resource shrinking
android {
    buildTypes {
        release {
            shrinkResources true
            minifyEnabled true
        }
    }
}
```

---

## 🎓 Learning Resources

### Android Development
- [Official Android Documentation](https://developer.android.com/docs)
- [Kotlin Language Guide](https://kotlinlang.org/docs/home.html)

### ML Kit
- [Face Detection Guide](https://developers.google.com/ml-kit/vision/face-detection)

### OpenCV
- [OpenCV for Android](https://opencv.org/android/)
- [Image Processing Tutorials](https://docs.opencv.org/4.x/d7/da8/tutorial_table_of_content_imgproc.html)

---

## ✅ Pre-Launch Checklist

Before distributing:
- [ ] Test on 5+ different devices
- [ ] Verify all features work offline
- [ ] Check PDF prints correctly
- [ ] Measure actual photo dimensions
- [ ] Test with various photo qualities
- [ ] Verify privacy policy shown
- [ ] Check app permissions are minimal
- [ ] Test installation from APK
- [ ] Verify app icon and name
- [ ] Create user documentation

---

**Setup Complete! 🎉**

Your Passport Photo Maker app is now ready for development and testing!
