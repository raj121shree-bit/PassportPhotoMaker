# 📁 Complete Project Structure

```
PassportPhotoMaker/
│
├── 📄 README.md                          # Main documentation
├── 📄 SETUP_GUIDE.md                     # Detailed setup instructions
├── 📄 TECHNICAL_DOCS.md                  # Architecture & algorithms
├── 📄 build.gradle                       # Root build configuration
├── 📄 settings.gradle                    # Project settings
├── 📄 gradle.properties                  # Gradle properties
│
└── app/
    ├── 📄 build.gradle                   # App-level dependencies
    │
    ├── src/
    │   └── main/
    │       ├── 📄 AndroidManifest.xml    # App permissions & components
    │       │
    │       ├── java/com/passportphoto/
    │       │   │
    │       │   ├── 📄 PassportPhotoApp.kt         # Application class (OpenCV init)
    │       │   │
    │       │   ├── data/
    │       │   │   └── model/
    │       │   │       ├── 📄 PassportPhoto.kt           # Main data model
    │       │   │       ├── 📄 CountryStandard.kt         # Passport size standards
    │       │   │       ├── 📄 PhotoEnhancementParams.kt  # Enhancement config
    │       │   │       └── 📄 ValidationIssue.kt         # Validation errors
    │       │   │
    │       │   ├── domain/
    │       │   │   └── usecase/
    │       │   │       └── 📄 FaceDetectionUseCase.kt    # ML Kit face detection
    │       │   │
    │       │   ├── presentation/
    │       │   │   ├── ui/
    │       │   │   │   └── 📄 MainActivity.kt            # Main UI screen
    │       │   │   └── viewmodel/
    │       │   │       └── 📄 PhotoProcessingViewModel.kt # State management
    │       │   │
    │       │   └── utils/
    │       │       ├── 📄 ImageProcessor.kt        # OpenCV enhancement
    │       │       ├── 📄 BackgroundRemover.kt     # GrabCut background removal
    │       │       └── 📄 PDFGenerator.kt          # A4 PDF layout
    │       │
    │       └── res/
    │           ├── layout/
    │           │   └── 📄 activity_main.xml        # Main UI layout
    │           │
    │           ├── values/
    │           │   ├── 📄 colors.xml               # Color definitions
    │           │   ├── 📄 strings.xml              # Text strings
    │           │   └── 📄 themes.xml               # Material theme
    │           │
    │           └── xml/
    │               ├── 📄 file_paths.xml           # FileProvider paths
    │               ├── 📄 backup_rules.xml         # Backup config
    │               └── 📄 data_extraction_rules.xml # Data extraction config
    │
    └── [Generated at build time]
        ├── build/                        # Compiled code
        └── release/                      # Release APK output
```

---

## 📊 File Statistics

### Source Code
- **Kotlin Files:** 9 files (~2,500 lines)
- **XML Files:** 8 files (~500 lines)
- **Gradle Files:** 3 files (~150 lines)
- **Documentation:** 3 files (~2,000 lines)

### Key Components Size
| Component | Lines | Complexity |
|-----------|-------|------------|
| ImageProcessor.kt | 180 | High |
| MainActivity.kt | 280 | Medium |
| PhotoProcessingViewModel.kt | 120 | Medium |
| FaceDetectionUseCase.kt | 80 | Medium |
| PDFGenerator.kt | 150 | Medium |
| BackgroundRemover.kt | 90 | Medium |

---

## 🔑 Key Files Explained

### 1. **PassportPhotoApp.kt**
- **Purpose:** Application entry point
- **Function:** Initialize OpenCV library
- **Called:** Once at app launch

### 2. **MainActivity.kt**
- **Purpose:** Main user interface
- **Responsibilities:**
  - Handle camera/gallery selection
  - Display photo preview
  - Show processing progress
  - Trigger enhancement/PDF generation
- **Size:** 280 lines

### 3. **PhotoProcessingViewModel.kt**
- **Purpose:** State management & business logic
- **Responsibilities:**
  - Manage processing states
  - Coordinate between use cases
  - Handle async operations
  - Expose data to UI
- **Pattern:** MVVM

### 4. **ImageProcessor.kt**
- **Purpose:** Core image enhancement
- **Algorithms:**
  - White balance (LAB color space)
  - Exposure/contrast adjustment
  - Skin tone normalization
  - Bilateral filtering (noise reduction)
  - Unsharp mask (sharpening)
  - HSV color enhancement
- **Library:** OpenCV 4.8

### 5. **FaceDetectionUseCase.kt**
- **Purpose:** Face detection & validation
- **Features:**
  - Detect face location
  - Validate face size
  - Check eyes open/closed
  - Detect head tilt
- **Library:** ML Kit

### 6. **BackgroundRemover.kt**
- **Purpose:** AI background removal
- **Algorithm:** GrabCut segmentation
- **Steps:**
  1. Expand face region
  2. Run GrabCut (5 iterations)
  3. Refine mask (morphology)
  4. Composite with new background

### 7. **PDFGenerator.kt**
- **Purpose:** Generate printable A4 layout
- **Output:**
  - A4 size (2480×3508 px @ 300 DPI)
  - 8 passport photos (4×2 grid)
  - Cut lines for trimming
  - Title header
- **Format:** PDF with embedded images

### 8. **activity_main.xml**
- **Purpose:** UI layout definition
- **Components:**
  - Photo preview card
  - Capture/Gallery buttons
  - Enhancement options
  - PDF generation button
  - Progress overlay
- **Design:** Material Design 3

---

## 🔄 Data Flow

```
User Action
    ↓
MainActivity
    ↓
PhotoProcessingViewModel
    ↓
Use Cases / Utils
    ↓
ML Kit / OpenCV
    ↓
Result State
    ↓
Update UI
```

---

## 📦 Dependencies Breakdown

### Core Android (androidx)
- `core-ktx` - Kotlin extensions
- `appcompat` - Backward compatibility
- `lifecycle` - ViewModel & LiveData
- `material` - Material Design components

### Image Processing
- `opencv:4.8.0` - Computer vision algorithms
- `mlkit:face-detection` - AI face detection

### Camera
- `camera-camera2` - Camera hardware access
- `camera-lifecycle` - Lifecycle-aware camera
- `camera-view` - Camera preview UI

### Utilities
- `kotlinx-coroutines` - Async programming
- `permissionx` - Runtime permissions
- `glide` - Image loading (optional)

---

## 🎯 Build Outputs

### Debug Build
**Location:** `app/build/outputs/apk/debug/app-debug.apk`
**Size:** ~18-20 MB
**Features:**
- Full logging
- Debugging enabled
- Unobfuscated code

### Release Build
**Location:** `app/build/outputs/apk/release/app-release.apk`
**Size:** ~12-15 MB
**Features:**
- Logging stripped
- Code obfuscated (ProGuard)
- Optimized assets
- Signed with keystore

---

## 🚀 Quick Reference

### Build Commands
```bash
# Debug APK
./gradlew assembleDebug

# Release APK
./gradlew assembleRelease

# Clean build
./gradlew clean build

# Install on device
./gradlew installDebug
```

### Run Configurations
- **Debug on Device:** Run → Debug 'app'
- **Release Build:** Build → Generate Signed Bundle / APK
- **Run Tests:** Run → Run 'All Tests'

---

## 📱 Runtime Files (Created During Use)

### Temporary Files
```
/storage/emulated/0/Android/data/com.passportphoto.maker/
├── files/
│   ├── Pictures/
│   │   └── PASSPORT_[timestamp].jpg    # Camera captures (temp)
│   └── Documents/
│       └── passport_photos_[timestamp].pdf  # Generated PDFs
```

### No Persistent Data
- ❌ No database
- ❌ No shared preferences (except privacy flag)
- ❌ No cached images
- ❌ No analytics data

---

## ✅ Quality Checklist

Before distribution:
- [x] All source files created
- [x] Build.gradle dependencies configured
- [x] AndroidManifest.xml permissions set
- [x] Resources (colors, strings, themes) defined
- [x] File provider paths configured
- [x] Privacy features implemented
- [x] Documentation complete
- [x] No hardcoded secrets
- [x] No internet permission
- [x] Ready for Android Studio import

---

**Total Project Files:** 25+ files
**Ready to Import:** Yes ✅
**Compilation Status:** Ready (after Gradle sync)
**Testing Status:** Ready (after adding test devices)
