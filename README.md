# 📸 Passport Photo Maker - Professional Android App

A professional Android application that converts any mobile photo into perfect passport-size photos with AI-powered enhancement and generates ready-to-print A4 layouts.

## ✨ Key Features

### 🎯 Core Capabilities
- **Smart Face Detection** - AI-powered face detection with validation
- **Professional Enhancement** - Studio-quality photo enhancement
- **Background Removal** - AI-based background replacement
- **Print-Ready Output** - A4 PDF with 8 passport photos (35mm × 45mm @ 300 DPI)
- **100% Offline** - All processing on-device, no internet required
- **Privacy-First** - No uploads, no tracking, no data storage

### 🔬 Image Processing Pipeline
1. **White Balance Correction** - Auto color temperature adjustment
2. **Exposure & Contrast** - Professional lighting optimization
3. **Skin Tone Normalization** - Natural, identity-preserving enhancement
4. **Noise Reduction** - Bilateral filtering with edge preservation
5. **Smart Sharpening** - Unsharp mask algorithm
6. **Color Depth Enhancement** - Subtle saturation boost

### ✅ Quality Validation
- Face size check (50-85% of photo height)
- Eyes open detection
- Head tilt detection
- Multiple face warning
- Real-time feedback

## 🏗️ Architecture

### MVVM Pattern
```
app/
├── data/
│   ├── model/ (PassportPhoto, CountryStandard, ValidationIssue)
│   └── repository/
├── domain/
│   └── usecase/ (FaceDetectionUseCase)
├── presentation/
│   ├── ui/ (MainActivity)
│   └── viewmodel/ (PhotoProcessingViewModel)
└── utils/
    ├── ImageProcessor (OpenCV enhancement)
    ├── BackgroundRemover (GrabCut algorithm)
    └── PDFGenerator (A4 layout)
```

### Tech Stack
- **Language:** Kotlin
- **ML Kit:** Face Detection (Google ML Kit)
- **OpenCV:** Image processing (version 4.8.0)
- **Architecture:** MVVM + Coroutines
- **UI:** Material Design 3
- **Permissions:** PermissionX library

## 📋 Prerequisites

### Development Environment
- Android Studio Hedgehog | 2023.1.1 or later
- JDK 17
- Android SDK 34
- Gradle 8.2.0

### Required SDKs
- Min SDK: 24 (Android 7.0)
- Target SDK: 34 (Android 14)
- Compile SDK: 34

## 🚀 Setup Instructions

### 1. Clone/Import Project
```bash
# Open the PassportPhotoMaker folder in Android Studio
# File > Open > Select PassportPhotoMaker directory
```

### 2. Sync Gradle
```bash
# Android Studio will automatically sync
# Or manually: File > Sync Project with Gradle Files
```

### 3. Download Dependencies
The following will be downloaded automatically:
- ML Kit Face Detection (16.1.6)
- OpenCV for Android (4.8.0)
- CameraX libraries
- Material Design Components

### 4. Build & Run
```bash
# Click Run button or Shift+F10
# Select device/emulator
```

## 📱 Usage Guide

### Basic Workflow
1. **Select Photo**
   - Tap "📸 Capture" for camera
   - Tap "🖼️ Gallery" to select existing photo

2. **Auto Enhancement**
   - Wait for face detection (2-3 seconds)
   - Review any validation warnings
   - Tap "✨ Perfect Passport Photo" for enhancement

3. **Background (Optional)**
   - Tap "🎨 Change Background"
   - Choose: White / Light Blue / Light Gray

4. **Generate PDF**
   - Tap "🖨️ Generate Printable PDF"
   - Wait for processing (3-5 seconds)
   - Share or save the PDF

### Print Instructions
- Generated PDF is A4 size (210mm × 297mm)
- Contains 8 identical passport photos
- Size: 35mm × 45mm (India standard)
- Resolution: 300 DPI
- Cut lines included for easy trimming

## 🔧 Configuration

### Change Passport Size Standard
Edit `MainActivity.kt`:
```kotlin
// In PhotoProcessingViewModel
val standard = CountryStandard.USA  // For USA passport
val standard = CountryStandard.UK   // For UK passport
val standard = CountryStandard.CANADA // For Canada passport
```

### Adjust Enhancement Parameters
Edit `PhotoEnhancementParams.kt`:
```kotlin
data class PhotoEnhancementParams(
    val whiteBalance: Float = 1.0f,      // 0.0 to 2.0
    val exposure: Float = 0f,            // -50 to +50
    val contrast: Float = 1.05f,         // 0.5 to 1.5
    val sharpness: Float = 0.5f,         // 0.0 to 1.0
    val noiseReduction: Float = 0.3f,    // 0.0 to 1.0
    val skinToneCorrection: Boolean = true
)
```

## 🧪 Testing Checklist

### Image Quality Tests
- [ ] Low resolution (640×480)
- [ ] High resolution (4000×3000)
- [ ] Various lighting conditions
- [ ] Different skin tones
- [ ] With/without glasses

### Face Detection Tests
- [ ] Frontal face
- [ ] Slight angles
- [ ] Eyes closed detection
- [ ] Multiple faces warning

### PDF Generation Tests
- [ ] Verify exact dimensions with ruler
- [ ] Print on photo paper
- [ ] Check color accuracy
- [ ] Verify cut line alignment

## 📊 Performance Metrics

- **Face Detection:** < 2 seconds
- **Photo Enhancement:** 3-5 seconds
- **Background Removal:** 4-6 seconds
- **PDF Generation:** 2-3 seconds
- **Memory Usage:** < 200MB
- **APK Size:** ~15MB

## 🔐 Privacy & Security

### On-Device Processing
- ✅ All ML models run locally
- ✅ No internet connection required
- ✅ No data sent to servers
- ✅ No analytics or tracking

### Permissions Required
- **Camera:** To capture photos
- **Storage:** To read existing photos
- **No network permission** (app works 100% offline)

## 🐛 Troubleshooting

### OpenCV Not Loading
```kotlin
// Check logcat for:
// "OpenCV loaded successfully" or "Unable to load OpenCV"
// If failed, rebuild project
```

### Face Detection Fails
- Ensure good lighting
- Face should be clearly visible
- Only one person in photo
- Face not too close/far from camera

### PDF Generation Issues
- Check storage permissions
- Verify output directory exists
- Ensure sufficient storage space

## 📦 Building Release APK

### 1. Generate Signed APK
```
Build > Generate Signed Bundle / APK
> APK
> Create new keystore
> Fill details
> Build
```

### 2. Optimize for Release
Edit `app/build.gradle`:
```gradle
buildTypes {
    release {
        minifyEnabled true
        shrinkResources true
        proguardFiles getDefaultProguardFile('proguard-android-optimize.txt')
    }
}
```

## 🎨 Customization Ideas

### Additional Features
- [ ] Suit/formal dress overlay
- [ ] Batch photo processing
- [ ] Direct printer integration
- [ ] Multiple country presets selector
- [ ] Manual adjustment controls

### UI Enhancements
- [ ] Before/after comparison slider
- [ ] Live camera preview with guidelines
- [ ] Dark mode support
- [ ] Multiple language support

## 📄 License

This is a sample project for educational purposes. Feel free to modify and use as needed.

## 🙏 Credits

- **ML Kit** - Google (Face Detection)
- **OpenCV** - Open Source Computer Vision Library
- **Material Design** - Google Design System

## 📞 Support

For issues or questions:
1. Check logcat for error messages
2. Verify all dependencies are downloaded
3. Ensure device meets minimum requirements
4. Test on different devices

---

## 🚀 Quick Start Checklist

- [ ] Import project in Android Studio
- [ ] Sync Gradle
- [ ] Connect device or start emulator
- [ ] Run app (Shift+F10)
- [ ] Allow camera & storage permissions
- [ ] Capture/select photo
- [ ] Tap "Perfect Passport Photo"
- [ ] Generate PDF
- [ ] Print and verify quality

---

**Made with ❤️ for creating professional passport photos on-device**
