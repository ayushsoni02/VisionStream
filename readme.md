# YOLOv8 Object Detection Web Application

A real-time object detection web application powered by YOLOv8, Flask, and OpenCV. Detect objects in real-time from your webcam or analyze local video files with an intuitive web interface.

![Python](https://img.shields.io/badge/python-3.8+-blue.svg)
![Flask](https://img.shields.io/badge/flask-2.0+-green.svg)
![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-purple.svg)
![License](https://img.shields.io/badge/license-MIT-orange.svg)

## Features

- **Real-Time Webcam Detection**: Stream live video from your webcam with instant object detection
- **Video File Analysis**: Upload and analyze local video files
- **Interactive Controls**: 
  - Toggle video stream on/off
  - Flip video horizontally
  - Enable/disable object detection
  - Adjust confidence threshold (50-100%)
- **Live Detection Output**: Real-time display of detected objects with confidence scores
- **Socket.IO Integration**: Seamless real-time communication between client and server
- **Responsive UI**: Clean, modern interface with Bootstrap styling

## Technology Stack

### Backend
- **Flask**: Lightweight Python web framework
- **Flask-SocketIO**: Real-time bidirectional communication
- **YOLOv8 (Ultralytics)**: State-of-the-art object detection model
- **OpenCV**: Computer vision and video processing
- **NumPy**: Numerical computing

### Frontend
- **HTML5/CSS3**: Modern web standards
- **JavaScript/jQuery**: Dynamic client-side functionality
- **Socket.IO Client**: Real-time event handling
- **Bootstrap 5**: Responsive UI components

## Prerequisites

- Python 3.8 or higher
- Webcam (for live detection)
- macOS, Linux, or Windows

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/ayushsoni02/yolov8-Object-detection-web-application-flask.git
cd yolov8-Object-detection-web-application-flask
```

### 2. Create Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the Application

```bash
python app.py
```

The application will start on `http://127.0.0.1:8080`

> **Note for macOS users**: The app runs on port 8080 to avoid conflicts with macOS AirPlay Receiver (which uses port 5000).

## Usage

### Webcam Detection

1. Navigate to `http://127.0.0.1:8080`
2. Click the **"Start Webcam"** button
3. On the detection page:
   - Toggle **"Show Stream"** to enable webcam feed
   - Toggle **"Run Detection"** to start object detection
   - Adjust **"Confidence Threshold"** slider to filter detections

### Video File Analysis

1. Navigate to `http://127.0.0.1:8080`
2. Enter the full path to your video file (e.g., `/Users/username/videos/sample.mp4`)
3. Click **"Start from File"**
4. Use the same controls as webcam mode

### Control Panel

| Control | Description |
|---------|-------------|
| **Show Stream** | Toggle video feed on/off |
| **Flip Horizontally** | Mirror the video feed |
| **Run Detection** | Enable/disable object detection |
| **Confidence Threshold** | Adjust minimum confidence for detections (50-100%) |

## How It Works

### Architecture Overview

```
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│   Browser   │◄────────┤    Flask     │◄────────┤   YOLOv8    │
│  (Client)   │ Socket  │   Server     │  Video  │   Model     │
│             │   .IO   │              │ Frames  │             │
└─────────────┘         └──────────────┘         └─────────────┘
      ▲                        │                        │
      │                        │                        │
      │                        ▼                        ▼
      │                  ┌──────────┐            ┌──────────┐
      └──────────────────┤  OpenCV  │            │  COCO    │
         Video Stream    │ VideoIO  │            │ Dataset  │
                         └──────────┘            └──────────┘
```

### Detection Pipeline

1. **Video Capture**: OpenCV captures frames from webcam (`cv2.VideoCapture(0)`) or video file
2. **Frame Processing**: Each frame is processed by YOLOv8 model for object detection
3. **Object Detection**: YOLOv8 identifies objects with confidence scores above the threshold
4. **Annotation**: Detected objects are annotated with bounding boxes and labels
5. **Streaming**: Processed frames are streamed to the browser via Flask Response
6. **Real-Time Updates**: Detection results are sent to the client via Socket.IO
7. **Display**: Browser displays the annotated video stream and detection results

### Key Components

#### VideoStreaming Class
Manages video capture, processing, and streaming:
- Handles webcam and video file sources
- Applies horizontal flip if enabled
- Runs YOLOv8 detection on frames
- Encodes frames as JPEG for web streaming

#### Flask Routes
- `/`: Homepage with input options
- `/webcam`: Initialize webcam streaming
- `/index`: Detection interface
- `/video_feed`: Video stream endpoint
- Various toggle endpoints for controls

#### Socket.IO Events
- `connect`: Establish client-server connection
- `label`: Emit detected objects in real-time

## Project Structure

```
yolov8-Object-detection-web-application-flask/
├── app.py                    # Flask application and routes
├── camera_settings.py        # Camera configuration utilities
├── requirements.txt          # Python dependencies
├── yolov8n.pt               # YOLOv8 nano model weights
├── coco128.yaml             # COCO dataset configuration
├── static/
│   ├── style.css            # Application styles
│   └── script.js            # Client-side JavaScript
├── templates/
│   ├── hompage.html         # Landing page
│   └── index.html           # Detection interface
└── README.md                # This file
```

## Supported Objects

The application uses YOLOv8 trained on the COCO dataset, which can detect **80 different object classes** including:

- **People**: person
- **Vehicles**: car, truck, bus, motorcycle, bicycle, airplane, train, boat
- **Animals**: cat, dog, bird, horse, sheep, cow, elephant, bear, zebra, giraffe
- **Electronics**: cell phone, laptop, keyboard, mouse, remote, tv, monitor
- **Household Items**: chair, couch, bed, dining table, bottle, cup, fork, knife, spoon, bowl
- **And many more!**

## ⚙️ Configuration

### Adjust Model Confidence

The default confidence threshold is **75%**. You can adjust this via the slider in the UI or by modifying the code:

```python
# In app.py - VideoStreaming class
self._confidence = 75.0  # Change default here
```

### Change Port

To run on a different port, modify the last line in `app.py`:

```python
socketio.run(app, debug=True, port=8080)  # Change 8080 to your desired port
```

### Use Different YOLOv8 Model

Replace `yolov8n.pt` with other variants for different speed/accuracy trade-offs:

- `yolov8n.pt` - Nano (fastest, smallest)
- `yolov8s.pt` - Small
- `yolov8m.pt` - Medium
- `yolov8l.pt` - Large
- `yolov8x.pt` - Extra Large (most accurate, slowest)

## Troubleshooting

### Port Already in Use (macOS)

If port 5000 or 8080 is already in use:

```bash
# Find and kill the process
lsof -ti:8080 | xargs kill -9

# Or change the port in app.py
```

### Webcam Not Working

1. Check webcam permissions in System Preferences (macOS) or Settings (Windows)
2. Ensure no other application is using the webcam
3. Try changing the webcam index in `app.py`:
   ```python
   cap = cv2.VideoCapture(1)  # Try index 1, 2, etc.
   ```

### Stream Shows "Streaming Off"

Make sure to **click the "Show Stream" toggle** to enable the video feed after navigating to the detection page.

### Video File Not Loading

- Ensure the file path is absolute (e.g., `/Users/username/videos/sample.mp4`)
- Check that the video codec is supported by OpenCV
- Try converting the video to MP4 with H.264 encoding

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- [Ultralytics YOLOv8](https://github.com/ultralytics/ultralytics) - Object detection model
- [OpenCV](https://opencv.org/) - Computer vision library
- [Flask](https://flask.palletsprojects.com/) - Web framework
- [Socket.IO](https://socket.io/) - Real-time communication

## Contact

For questions or support, please open an issue on GitHub.

---

**Built with ❤️ using YOLOv8, Flask, and OpenCV**
