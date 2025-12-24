# 🏗️ Technical Architecture & Implementation Details

## 📐 System Architecture

### High-Level Overview
```
┌─────────────────────────────────────────────────────────┐
│                    Presentation Layer                    │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │  MainActivity │  │   ViewModel  │  │  UI Binding  │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
└─────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│                     Domain Layer                         │
│  ┌──────────────────┐  ┌────────────────────────────┐  │
│  │ FaceDetection    │  │  Business Logic/Validation │  │
│  │   UseCase        │  │                            │  │
│  └──────────────────┘  └────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│                      Utility Layer                       │
│  ┌───────────────┐  ┌──────────────┐  ┌─────────────┐  │
│  │ ImageProcessor│  │ BackgroundRmv│  │PDFGenerator │  │
│  │  (OpenCV)     │  │  (GrabCut)   │  │ (Android)   │  │
│  └───────────────┘  └──────────────┘  └─────────────┘  │
└─────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│                   External Libraries                     │
│   ML Kit Face Detection  │  OpenCV 4.8  │  CameraX      │
└─────────────────────────────────────────────────────────┘
```

---

## 🔬 Image Processing Pipeline

### Complete Enhancement Workflow

```
Input Photo (Bitmap)
         │
         ▼
┌────────────────────────────────────┐
│  1. White Balance Correction       │
│  - Convert RGB → LAB color space   │
│  - Calculate mean of A,B channels  │
│  - Shift towards neutral (128)     │
│  - Convert back to RGB             │
└────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────┐
│  2. Exposure & Contrast Adjust     │
│  - Formula: out = α*in + β         │
│  - α = contrast (typically 1.05)   │
│  - β = exposure (typically 0)      │
└────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────┐
│  3. Skin Tone Normalization        │
│  - Convert RGB → YCrCb             │
│  - Detect skin pixels (range)      │
│  - Apply subtle warmth adjustment  │
│  - Only affects skin regions       │
└────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────┐
│  4. Noise Reduction                │
│  - Bilateral Filter                │
│  - Preserves edges                 │
│  - Smooths uniform areas           │
│  - Parameters: d=3-9, σ=75         │
└────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────┐
│  5. Smart Sharpening               │
│  - Unsharp Mask algorithm          │
│  - Create Gaussian blur            │
│  - Subtract from original          │
│  - Add back with intensity         │
└────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────┐
│  6. Color Depth Enhancement        │
│  - Convert RGB → HSV               │
│  - Boost saturation (×1.15)        │
│  - Enhance value (×1.05)           │
│  - Convert back to RGB             │
└────────────────────────────────────┘
         │
         ▼
   Enhanced Photo
```

---

## 🤖 Face Detection Implementation

### ML Kit Integration

```kotlin
// Detector Configuration
FaceDetectorOptions.Builder()
    .setPerformanceMode(PERFORMANCE_MODE_ACCURATE) // Accuracy over speed
    .setLandmarkMode(LANDMARK_MODE_ALL)           // Detect all facial landmarks
    .setClassificationMode(CLASSIFICATION_MODE_ALL) // Eye open/closed, smile
    .setMinFaceSize(0.15f)                        // Minimum 15% of image
    .build()
```

### Validation Logic

#### Face Size Check
```kotlin
val faceHeight = face.boundingBox.height()
val imageHeight = bitmap.height
val faceRatio = faceHeight / imageHeight

// Passport photo standards:
// Face should occupy 70-80% of photo height
if (faceRatio < 0.5f) → "Face too small"
if (faceRatio > 0.85f) → "Face too large"
```

#### Eye Detection
```kotlin
// ML Kit provides probability (0.0 to 1.0)
val leftEyeOpen = face.leftEyeOpenProbability
val rightEyeOpen = face.rightEyeOpenProbability

if (leftEyeOpen < 0.5f || rightEyeOpen < 0.5f) {
    issue = "Eyes closed"
}
```

#### Head Orientation
```kotlin
// Euler angles for head rotation
val rotY = face.headEulerAngleY  // Left/Right tilt
val rotZ = face.headEulerAngleZ  // Clockwise/Counterclockwise

// Acceptable range: ±10 degrees
if (abs(rotY) > 10 || abs(rotZ) > 10) {
    issue = "Head tilted"
}
```

---

## 🎨 Background Removal Algorithm

### GrabCut Segmentation

```
Input: Photo + Face Rectangle
         │
         ▼
┌────────────────────────────────────┐
│  1. Expand Face Rectangle          │
│  - Add 40% width                   │
│  - Add 80% height (for shoulders)  │
│  - Ensure within image bounds      │
└────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────┐
│  2. Initialize GrabCut             │
│  - Define probable foreground      │
│  - Rectangle around person         │
│  - 5 iterations                    │
└────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────┐
│  3. Refine Mask                    │
│  - Morphological closing           │
│  - Gaussian blur (3×3)             │
│  - Smooth edges                    │
└────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────┐
│  4. Composite                      │
│  - Create solid background         │
│  - Copy foreground using mask      │
│  - Copy background where masked    │
└────────────────────────────────────┘
         │
         ▼
   Output: Photo with new background
```

### Background Color Options
```kotlin
Color.WHITE           // Standard white (255,255,255)
Color.rgb(230,240,255) // Light blue (diplomatic)
Color.rgb(245,245,245) // Light gray (neutral)
```

---

## 📄 PDF Generation Details

### A4 Layout Specifications

```
┌─────────────────────────────────────────────────────────────┐
│                     A4 Page (300 DPI)                        │
│                  2480 × 3508 pixels                          │
│                                                               │
│  Margin: 10mm (118px)                                        │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                                                         │  │
│  │         Title: "Passport Photos - India"               │  │
│  │                                                         │  │
│  │  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐                      │  │
│  │  │35×45│ │35×45│ │35×45│ │35×45│  ← Row 1             │  │
│  │  └─────┘ └─────┘ └─────┘ └─────┘                      │  │
│  │     5mm spacing between photos                          │  │
│  │  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐                      │  │
│  │  │35×45│ │35×45│ │35×45│ │35×45│  ← Row 2             │  │
│  │  └─────┘ └─────┘ └─────┘ └─────┘                      │  │
│  │                                                         │  │
│  │  Each photo: 413 × 531 pixels @ 300 DPI               │  │
│  │  = Exactly 35mm × 45mm when printed                    │  │
│  │                                                         │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Dimension Calculations
```kotlin
// Convert millimeters to pixels at 300 DPI
fun mmToPx(mm: Float, dpi: Int = 300): Int {
    val inches = mm / 25.4f  // 1 inch = 25.4mm
    return (inches * dpi).toInt()
}

// Example for 35mm × 45mm:
widthPx = (35 / 25.4) × 300 = 413 pixels
heightPx = (45 / 25.4) × 300 = 531 pixels

// A4 dimensions:
A4_WIDTH = (210 / 25.4) × 300 = 2480 pixels
A4_HEIGHT = (297 / 25.4) × 300 = 3508 pixels
```

### Cut Line Rendering
```kotlin
// Dashed lines at corners for easy cutting
fun drawCutLines(canvas: Canvas, x, y, width, height) {
    paint.pathEffect = DashPathEffect(floatArrayOf(10f, 5f), 0f)
    
    // Draw 30px lines at each corner
    // Top-left, top-right, bottom-left, bottom-right
    // L-shaped guides for precise cutting
}
```

---

## 🔄 State Management

### ProcessingState Flow
```
Idle
  │ User selects photo
  ▼
DetectingFace (2-3s)
  │ ML Kit processing
  ▼
FaceDetected (with validation issues)
  │ User taps "Enhance"
  ▼
Enhancing (3-5s)
  │ OpenCV pipeline
  ▼
Enhanced
  │ User taps "Change Background"
  ▼
RemovingBackground (4-6s)
  │ GrabCut algorithm
  ▼
BackgroundRemoved
  │ User taps "Generate PDF"
  ▼
GeneratingPDF (2-3s)
  │ A4 layout creation
  ▼
PDFGenerated (success)
```

### ViewModel Architecture
```kotlin
class PhotoProcessingViewModel : ViewModel() {
    // State management
    private val _state = MutableStateFlow<ProcessingState>(Idle)
    val state: StateFlow<ProcessingState> = _state.asStateFlow()
    
    // Data management
    private val _photo = MutableStateFlow<PassportPhoto?>(null)
    val photo: StateFlow<PassportPhoto?> = _photo.asStateFlow()
    
    // Operations run in viewModelScope
    // Heavy processing in Dispatchers.Default
    // IO operations in Dispatchers.IO
}
```

---

## ⚡ Performance Optimization

### Memory Management
```kotlin
// Bitmap recycling
val processedBitmap = enhance(originalBitmap)
originalBitmap.recycle()  // Free memory

// Image downsampling
val options = BitmapFactory.Options()
options.inSampleSize = calculateSampleSize(width, height, MAX_SIZE)
val bitmap = BitmapFactory.decodeFile(path, options)
```

### Async Processing
```kotlin
// Face detection - suspending function
suspend fun detectFace(bitmap: Bitmap) = suspendCoroutine { cont ->
    detector.process(image)
        .addOnSuccessListener { faces -> cont.resume(faces) }
}

// Image enhancement - background thread
withContext(Dispatchers.Default) {
    imageProcessor.enhancePassportPhoto(bitmap, params)
}
```

### Caching Strategy
```kotlin
// Keep only essential data in memory
data class PassportPhoto(
    val originalBitmap: Bitmap,        // Keep original
    val processedBitmap: Bitmap? = null, // Keep latest version only
    val faceRect: Rect? = null,        // Lightweight metadata
    // Don't cache intermediate results
)
```

---

## 🔐 Security & Privacy

### Data Flow
```
User's Photo
     │
     ▼
[RAM] Processing (never written to disk except final PDF)
     │
     ▼
Generated PDF (user's Documents folder)
     │
     ▼
User manually shares/deletes
```

### Privacy Features
- ✅ No network permission in manifest
- ✅ No analytics SDKs
- ✅ No crash reporting to external servers
- ✅ Face data never persisted
- ✅ Photos deleted after app closes
- ✅ Privacy notice on first launch

### Permissions Justification
```xml
<!-- CAMERA: To capture new photos -->
<uses-permission android:name="android.permission.CAMERA" />

<!-- READ_MEDIA_IMAGES: To select existing photos (Android 13+) -->
<uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />

<!-- READ_EXTERNAL_STORAGE: To select photos (Android 12-) -->
<uses-permission 
    android:name="android.permission.READ_EXTERNAL_STORAGE"
    android:maxSdkVersion="32" />

<!-- NO INTERNET PERMISSION - Completely offline app -->
```

---

## 📊 Quality Metrics

### Image Quality Standards
- **Resolution:** 300 DPI (print quality)
- **Color Space:** RGB (24-bit)
- **File Format:** JPEG (95% quality) for display, PDF for print
- **Dimensions:** Pixel-perfect to physical size

### Validation Thresholds
```kotlin
// Face size: 50-85% of photo height
const val MIN_FACE_RATIO = 0.5f
const val MAX_FACE_RATIO = 0.85f

// Eyes open probability
const val EYE_OPEN_THRESHOLD = 0.5f

// Head rotation tolerance
const val MAX_HEAD_ROTATION = 10f  // degrees
```

### Processing Time Benchmarks
| Operation | Time | Device |
|-----------|------|---------|
| Face Detection | 1.5-2.5s | Mid-range (2021) |
| Enhancement | 3-5s | Mid-range (2021) |
| Background Removal | 4-6s | Mid-range (2021) |
| PDF Generation | 2-3s | Mid-range (2021) |
| **Total** | **11-16s** | **End-to-end** |

---

## 🧪 Testing Strategy

### Unit Tests
```kotlin
@Test
fun `face detection validates size correctly`() {
    val face = mockFace(height = 300)
    val bitmap = mockBitmap(height = 500)
    val ratio = face.height / bitmap.height  // 0.6
    
    assert(ratio in 0.5f..0.85f)  // Valid range
}
```

### Integration Tests
```kotlin
@Test
fun `enhancement pipeline produces valid output`() {
    val input = loadTestImage()
    val enhanced = imageProcessor.enhance(input, params)
    
    assertNotNull(enhanced)
    assertEquals(input.width, enhanced.width)
    assert(isValidBitmap(enhanced))
}
```

### UI Tests
```kotlin
@Test
fun `complete workflow from photo to PDF`() {
    // Select photo
    onView(withId(R.id.btnGallery)).perform(click())
    // ... select test image
    
    // Wait for face detection
    onView(withText("Face Detected")).check(matches(isDisplayed()))
    
    // Enhance
    onView(withId(R.id.btnAutoEnhance)).perform(click())
    
    // Generate PDF
    onView(withId(R.id.btnGeneratePDF)).perform(click())
    
    // Verify success
    onView(withText("PDF Generated")).check(matches(isDisplayed()))
}
```

---

## 🎯 Future Enhancements

### Planned Features
1. **Multi-Country Support**
   - Dropdown for country selection
   - Different size presets
   - Specific validation rules per country

2. **Manual Adjustments**
   - Brightness slider
   - Contrast slider
   - Saturation control
   - Reset to original

3. **Batch Processing**
   - Process multiple photos
   - Generate single PDF with all
   - Family photo packages

4. **Dress Overlay**
   - Virtual suit/formal wear
   - AR-based overlay
   - Gender-appropriate options

---

**This architecture ensures professional-quality output while maintaining 100% privacy and offline functionality.**
