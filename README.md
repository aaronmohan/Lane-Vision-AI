# Lane Vision AI 🛣️🚗
### Real-Time On-Device Lane Detection & Advanced Driver Assistance System (ADAS) for Android

[![Android](https://img.shields.io/badge/Platform-Android_API_24+-3DDC84?style=flat&logo=android&logoColor=white)](https://developer.android.com)
[![OpenCV](https://img.shields.io/badge/OpenCV-4.5.3-5C3EE8?style=flat&logo=opencv&logoColor=white)](https://opencv.org/)
[![Gradle](https://img.shields.io/badge/Gradle-8.13-02303A?style=flat&logo=gradle&logoColor=white)](https://gradle.org/)
[![Language](https://img.shields.io/badge/Language-Java_8-ED8B00?style=flat&logo=openjdk&logoColor=white)](https://www.java.com)
[![Architecture](https://img.shields.io/badge/Architecture-Edge_Computer_Vision-blue?style=flat)](#-computer-vision-pipeline)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**Lane Vision AI** is an on-device mobile computer vision application that transforms an Android smartphone into an **Advanced Driver Assistance System (ADAS)**. Powered by edge-accelerated OpenCV, it detects highway lane markings, projects a dynamic drivable corridor, computes real-time vehicle-to-boundary distances in meters, and delivers instantaneous audio-visual **Lane Departure Warnings (LDWS)**—all operating 100% locally with zero cloud dependencies or network latency.

> ### ⚡ Recruiter Quick-Scan (TL;DR)
> - **Domain:** Mobile Computer Vision, Real-Time Edge Processing, Automotive ADAS.
> - **What It Does:** Converts an Android smartphone into an on-device **Lane Departure Warning System (LDWS)** that tracks highway lane boundaries, projects a drivable corridor, and computes real-time vehicle-to-lane offsets in meters.
> - **Core Engineering Highlight:** **100% on-device edge computation** via hardware-accelerated **OpenCV 4.5.3**—zero cloud dependencies, zero network latency, and complete operational privacy.

---

## 🎥 Video Demonstration

[![Lane Vision AI Real-Time Lane Detection Demo](assets/demo.gif)](https://github.com/aaronmohan/Lane-Vision-AI/blob/main/assets/opencvdemo.mp4)

*Live screen recording of Lane Vision AI running real-time lane detection, corridor projection, and metric distance estimation on highway footage.*

▶️ **[Click here to watch the full HD 1-Minute Demonstration Video (`assets/opencvdemo.mp4`)](https://github.com/aaronmohan/Lane-Vision-AI/blob/main/assets/opencvdemo.mp4)**

---

## 📸 Visual Demos & Field Tests

| Example 1: In-App Augmented Reality HUD | Example 2: Hardware-in-the-Loop Test Bench |
| :---: | :---: |
| [![Example 1: In-App Lane Detection HUD Overlay](assets/example1.jpg)](assets/example1.jpg) | [![Example 2: Hardware-in-the-Loop Real-Time Testing](assets/example2.jpg)](assets/example2.jpg) |
| *Clean UI screenshot showing active lane boundaries, drivable corridor, and metric telemetry.* | *Physical device running real-time detection on dynamic desert highway footage.* |

### Detailed Breakdown

#### 🔹 Example 1: In-App Augmented Reality HUD & Lane Detection
The screenshot illustrates the active HUD mode running real-time detection on a curved highway:
- **Boundary Identification:** Detected left lane boundary outlined in **Red** and right lane boundary outlined in **Green** (10px line weight).
- **Drivable Corridor:** An alpha-blended semi-transparent blue/purple polygon dynamically mapped between the fitted lane trajectories to demarcate the vehicle's safe travel zone.
- **Live Telemetry HUD (Top Right):**
  - `Left Lane : 1.34 m` — Real-time distance from the vehicle/camera centerline to the left lane line.
  - `Right Lane : 1.73 m` — Real-time distance from the vehicle/camera centerline to the right lane line.
  - `Road : 3.07 m` — Total detected lane width across both boundaries.
  - `0.0035` — Editable real-time metric calibration scale ($m/\text{pixel}$).
- **Interactive Controls (Top Left):**
  - `CAMERA`: Engages live camera sensor feed with OpenCV frame listener (`onCameraFrame`).
  - `PICTURE`: Allows loading and testing detection accuracy on static benchmark photos via Android's file picker.
  - `[ ] ROI`: Developer diagnostic overlay to inspect the trapezoidal Region of Interest.
  - `[ ] LANE`: Toggles the active **Lane Assist** departure alert safety loop.
  - `🔊 / 🔇 Sound Toggle`: Instantly mutes or unmutes audible departure alert chimes.

#### 🔹 Example 2: Hardware-in-the-Loop Field Testing & Benchmarking
The second demo showcases physical hardware-in-the-loop benchmarking under dynamic road conditions:
- **Live Optical Stream Ingestion:** An Android test device processes dynamic, high-speed highway footage played on an external workstation monitor.
- **Continuous Edge Processing:** Operates seamlessly via OpenCV's `CameraBridgeViewBase`, maintaining smooth frame rates without UI stutter or memory leaks.
- **Environmental Resilience:** Successfully locks onto dashed and solid boundaries despite strong desert sunlight, background mountain contours, and high-contrast shadows.
- **Real-Time Dynamic Recalibration:** Instantaneous tracking adjustments as vehicle trajectory shifts (`Right Lane: 1.54 m`, `Left Lane: 0.86 m`, `Road: 2.20 m`).

---

## 🛠️ Tech Stack & Core Competencies

| Layer | Technology | Key Implementation |
| :--- | :--- | :--- |
| **Language** | **Java (JDK 8 / 1.8)** | Parallel Streams (`parallelStream`), Lambda expressions, Threading |
| **Computer Vision** | **OpenCV Android SDK 4.5.3** | Canny Edge, HoughLinesP, ConvexPoly, Matrix transformations |
| **Video Ingestion** | **OpenCV `CameraBridgeViewBase`** | Asynchronous frame callbacks (`onCameraFrame`) |
| **Mobile Platform** | **Android OS (API 24+)** | Min SDK: `24` (Android 7.0) • Target SDK: `31/32` • Gradle `8.13` |
| **Safety System** | **ADAS LDWS** | Directional flashing HUD indicators + audible departure alerts |
| **Architecture** | **Offline Edge CV** | Fully local pipeline, zero cloud reliance |

---

## 🔬 Computer Vision Pipeline

The image processing engine in [`LaneDetector1.java`](app/src/main/java/com/aaron/lanevision/LaneDetector1.java) executes a structured classical CV pipeline on every incoming frame:

```mermaid
flowchart LR
    A["Camera Frame (RGBA)"] --> B["Grayscale & Gaussian Blur (5x5)"]
    B --> C["Canny Edge Detection (150, 200)"]
    C --> D["Morphological Dilation & Erosion"]
    D --> E["Trapezoidal ROI Masking"]
    E --> F["Probabilistic Hough Transform"]
    F --> G["Parallel Slope Filtering & Averaging"]
    G --> H["Line Extrapolation & Polygon Fill"]
    H --> I["Metric Distance & LDWS Telemetry"]
```

1. **Color Space Conversion:** Converts `RGBA` input to single-channel Grayscale (`COLOR_RGBA2GRAY`) to eliminate chromatic variance and reduce computation overhead.
2. **Noise Suppression:** Applies a $5 \times 5$ Gaussian kernel (`GaussianBlur`) to smooth high-frequency sensor noise and road asphalt grain.
3. **Gradient Edge Detection:** Executes Canny edge detection (`Canny`) with tuned hysteresis thresholds ($150, 200$) to highlight high-contrast lane transitions.
4. **Morphological Filtering:** Employs $3 \times 3$ structuring elements with dilation and erosion (`dilate`, `erode`) to bridge broken markings and eliminate isolated noise pixels.
5. **Trapezoidal ROI Masking:** Isolates the drivable road horizon using a trapezoidal polygon mask (`fillConvexPoly` & `bitwise_and`), cutting out irrelevant sky, mountain, and roadside data:
   $$\text{Points} = \left[(0, H), (W, H), (0.60 W, 0.50 H), (0.40 W, 0.50 H)\right]$$
6. **Line Extraction:** Executes the Probabilistic Hough Transform (`HoughLinesP`) to extract discrete line segments from the masked edge map (`threshold=40`, `minLineLength=40`, `maxLineGap=70`).
7. **Slope Classification & Parallel Stream Averaging:**
   - Calculates slope $m = \frac{y_2 - y_1}{x_2 - x_1}$ for every candidate segment.
   - Categorizes lines into **Right Lane** ($m > 0.5$) and **Left Lane** ($m < -0.5$).
   - Strict slope bounds ($0.5 \le |m| \le 3.0$) filter out horizontal road seams and false artifacts.
   - Computes statistically robust slope and intercept averages using Java 8 parallel streams.
8. **Trajectory Fitting & Drivable Corridor Visualization:**
   - Extrapolates left and right boundary vectors across the road plane from $0.65 \times H$ to $H$.
   - Renders left (red) and right (green) lines with 10px thickness.
   - Blends an alpha-transparent driving corridor polygon (`addWeighted`, $\alpha=0.25$) between boundaries.
9. **Spatial Telemetry Calculation:**
   - Calculates vehicle offset relative to lane center:
     $$\text{Distance} = \left|\frac{\text{Width}}{2} - x_{\text{lane}}\right| \times \text{Factor}$$
   - Converts pixel offsets into real-world meters using the calibrated metric scale ($0.0035\text{ m/px}$).

---

## 🛡️ Lane Departure Warning System (LDWS)

Integrated directly into the camera lifecycle inside [`videoClass.java`](app/src/main/java/com/aaron/lanevision/videoClass.java):
- **Safety Proximity Threshold:** Evaluates vehicle distance against a safety boundary ($\delta = 0.70\text{ m}$).
- **Visual Alert Animation:** Automatically triggers high-visibility pulsing directional arrows (`imageViewAlertL` / `imageViewAlertR`) when an unintentional lane drift occurs.
- **Audible Alerts:** Sounds an immediate audio chime from [`res/raw/alert.mp3`](app/src/main/res/raw/alert.mp3) with a 1-second debounce timer to avoid sound spamming.
- **Runtime In-Flight Calibration:** Drivers can adjust the metric conversion factor on the fly to adapt to different vehicle dashboard heights or phone mounting angles.

---

## 💡 Engineering Highlights (Interview Talking Points)

- **Edge Computing & Zero Cloud Latency:** All matrix transformations execute locally on the mobile CPU/GPU, ensuring safety alerts operate even without cellular reception in remote areas.
- **Memory & GC Conscious:** Matrices and image buffers are reused across frames to minimize heap churn and avoid Android Garbage Collection pauses during live driving.
- **Noise Rejection Under Varied Lighting:** Combines Gaussian smoothing, morphology, and strict angular constraints to maintain line lock during shadows, direct sunlight, and worn road markings.
- **Real-World Metric Translation:** Extends beyond basic edge drawing by delivering real-world spatial measurements in meters, demonstrating practical ADAS software engineering.

---

## 🏛️ Project Structure

```
Lane Vision AI app/
├── assets/                          # Documentation demos & visual assets
│   ├── demo.gif                     # Animated real-time demo preview
│   ├── opencvdemo.mp4               # Full HD field demonstration video
│   ├── example1.jpg                 # Example 1: In-app detection HUD screenshot
│   └── example2.jpg                 # Example 2: Hardware field-test photo
├── app/
│   ├── src/main/
│   │   ├── java/com/aaron/lanevision/
│   │   │   ├── videoClass.java      # Main Launcher Activity: Fullscreen Camera, OpenCV bridge, HUD & LDWS
│   │   │   ├── LaneDetector1.java   # Core OpenCV pipeline, Hough transform, line fitting
│   │   │   ├── TextFormatter.java   # High-efficiency Spannable HUD formatting
│   │   │   └── InstructionsActivity.java # Onboarding guide with typewriter text animation & AdMob
│   │   ├── res/                     # Layouts, vector drawables, alert icons
│   │   │   ├── layout/
│   │   │   │   ├── roadvideo_land.xml # Landscape HUD overlay layout
│   │   │   │   └── activity_instructions.xml # Instructions screen layout
│   │   │   └── raw/
│   │   │       ├── alert.mp3        # LDWS audible departure alert sound
│   │   │       └── videocar.mp4     # Built-in highway test video dataset
│   │   └── AndroidManifest.xml      # Camera permissions and activity declarations
│   └── build.gradle                 # Module dependencies (OpenCV 4.5.3, Material, Play Services)
├── gradle/                          # Gradle wrapper files (Gradle 8.13)
├── build.gradle                     # Top-level Gradle configuration (AGP 8.13.2)
├── settings.gradle                  # Repository & module definitions
└── README.md                        # Project documentation
```

---

## 🚀 Getting Started & Build Instructions

1. **Prerequisites:** 
   - Android Studio (Giraffe, Hedgehog, Ladybug, or newer).
   - JDK 17 (required by modern Gradle 8+).
   - Android SDK API 24+ installed.
2. **Open Project:** 
   - In Android Studio, select **File > Open** and choose the `Lane Vision AI app` folder.
3. **Gradle Sync:** 
   - Allow Gradle to sync dependencies (`com.quickbirdstudios:opencv:4.5.3.0` will automatically configure via Maven Central).
4. **Deploy:** 
   - Connect an Android device with USB debugging enabled.
   - Grant camera permissions when prompted.
   - Press **Run (Shift + F10)**.
