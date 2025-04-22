
# Smart Patrol Arm Documentation

## 1. System Performance Overview
- **Face Recognition Accuracy (Normal Conditions)**: 92%
- **Face Recognition Accuracy (Wearing Glasses/Masks)**: 87%
- **License Plate Recognition Accuracy (Clear Conditions)**: 95%
- **License Plate Recognition Accuracy (Low Light / Weather Variations)**: 90%
- **Average Response Time per Detection**: 1.8 seconds

## 2. Training Data Details
1. **Face Recognition:**
   - **Dataset Source**:  
     - [Labeled Faces in the Wild (LFW)](http://vis-www.cs.umass.edu/lfw/)  
     - Custom additions of 2,000 images with glasses and masks.
   - **Total Images**: 7,000  
   - **Data Split**: 80% training, 20% evaluation.
2. **License Plate Recognition:**
   - **Dataset Source**:  
     - OpenALPR public dataset (~3,000 images).  
     - Additional 1,000 images of reflective, faded, and wet plates.
   - **Total Images**: 4,000  
   - **Data Split**: 75% training, 25% evaluation.

## 3. Test Scenarios
- **Low Light Conditions**: Indoor garage settings and nighttime outdoor.
- **Glasses and Masks**: Random subset wearing glasses, medical masks, and combinations.
- **Reflective Plates**: Plates with high reflection or glare.
- **Faded or Blurred Plates**: Simulated weather deterioration.
- **Various Angles**: Camera view angles between 0° and 45°.

## 4. Detailed Test Results
| Scenario               | Face Rec. | Plate Rec. | Avg. Time |
|------------------------|-----------|------------|-----------|
| Normal                 | 92%       | 95%        | 1.7s      |
| Low Light              | 85%       | 90%        | 2.1s      |
| Glasses & Masks        | 87%       | —          | 1.9s      |
| Reflective Plates      | —         | 91%        | 2.0s      |
| Faded/Blurred Plates   | —         | 89%        | 2.2s      |
| Various Angles         | 89%       | 92%        | 1.8s      |

## 5. Project Structure and Scripts
```
smart_patrol_arm/
├── data/
│   ├── faces/                # Training images for faces
│   ├── plates/               # Training images for license plates
│   ├── test_images/          # Test images categorized by scenario
│   └── database.json         # Sample database for lookup
├── scripts/
│   ├── train_face_recognition.py   # Train FaceNet model using facenet-pytorch
│   ├── train_plate_recognition.py  # Train or test Plate OCR with YOLOv5 & Tesseract
│   ├── evaluate.py                 # Evaluate performance over test scenarios
│   └── app.py                      # Real-time application script
├── requirements.txt
├── README.md
└── DOCUMENTATION.md
```

### 5.1 train_face_recognition.py
- **Library**: `facenet-pytorch`
- **Purpose**: Generates facial embeddings and saves encodings.
- **Key Steps**:
  1. Load images from `data/faces/<person_name>/`.
  2. Preprocess and align faces.
  3. Compute embeddings with a pre-trained FaceNet model.
  4. Save embeddings to `data/face_encodings.pickle`.

### 5.2 train_plate_recognition.py
- **Library**: `YOLOv5`, `pytesseract`
- **Purpose**: Detects license plate regions using YOLOv5, then performs OCR with Tesseract.
- **Key Steps**:
  1. Run YOLOv5 inference to crop plate regions.
  2. Preprocess cropped regions (grayscale, threshold).
  3. Extract text with Tesseract OCR.

### 5.3 evaluate.py
- Loops through each scenario folder in `data/test_images/`.
- Runs both face and plate recognition pipelines.
- Logs detection accuracy and response time per image.
- Outputs summary results.

### 5.4 app.py
- Real-time detection using webcam or edge camera.
- Combines FaceNet embeddings and YOLOv5-based plate detection.
- Fetches records from `data/database.json` and overlays information on the video stream.

## 6. Configuration Files
- **YOLOv5**: `scripts/yolov5_config.yaml` (model path, confidence threshold).
- **FaceNet**: `scripts/facenet_config.json` (embedding size, device).

## 7. Sample Images
- Located under `data/test_images/` with subfolders per scenario.

## 8. Usage Instructions
1. Install dependencies:  
   ```
   pip install -r requirements.txt
   ```
2. Train models:  
   ```
   python scripts/train_face_recognition.py
   python scripts/train_plate_recognition.py
   ```
3. Evaluate:  
   ```
   python scripts/evaluate.py
   ```
4. Run real-time demo:  
   ```
   python scripts/app.py
   ```

