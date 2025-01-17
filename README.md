### Inference Speed Results, System Architecture, and Optimization Strategies

#### 1. **Inference Speed Results**

- **Video Information:**
  - Total Frames: 660
  - Frame Rate: ~24 FPS
  - Resolution: 640x480 (varies by input frame size)

- **Inference Performance:**
  - **Preprocessing Time per Frame:** ~4-8 ms
  - **Inference Time per Frame:** ~90-175 ms
  - **Postprocessing Time per Frame:** ~1-3 ms

- **Overall Speed:**
  - Average Frames Processed per Second (FPS): ~5-8 FPS (on CPU)
  - Total Video Processing Time: ~82 seconds for 660 frames.

#### 2. **System Architecture**

The system is designed to detect objects and their associated sub-objects in video streams and save results in a hierarchical JSON format. The architecture consists of the following components:

- **Object Detection:**
  - Main objects (e.g., "Car," "Person") are detected using a YOLOv8 model.
  - Detection results include bounding boxes, labels, and confidence scores.

- **Sub-Object Detection:**
  - Regions of interest (ROIs) are cropped from the frame based on main object bounding boxes.
  - A separate YOLOv8 model processes these ROIs to detect sub-objects (e.g., "Helmet," "Tire").

- **Data Storage and Output:**
  - Detected objects and sub-objects are saved in a JSON format:
    ```json
    {
      "object": "Car",
      "id": 1,
      "bbox": [x1, y1, x2, y2],
      "subobject": {
        "object": "Tire",
        "id": 1,
        "bbox": [x1, y1, x2, y2]
      }
    }
    ```
  - Cropped images of sub-objects are stored for retrieval.

- **Pipeline Workflow:**
  1. Read video frames using OpenCV.
  2. Detect main objects in each frame.
  3. Crop ROIs based on detected bounding boxes.
  4. Detect sub-objects within ROIs.
  5. Save results in JSON format and sub-object images.

#### 3. **Optimization Strategies**

To improve inference speed and performance on CPU:

- **Model Selection:**
  - Utilized YOLOv8n (nano) models, optimized for CPU with smaller weights and faster inference times.

- **Batch Processing:**
  - Processed frames sequentially to reduce memory overhead, avoiding batch processing which is resource-intensive on CPUs.

- **Resolution Adjustment:**
  - Downscaled input frames to balance detection accuracy and speed.
  - ROIs for sub-object detection were cropped directly from the original resolution to preserve detail.

- **Efficient Data Handling:**
  - Used efficient data structures (e.g., dictionaries for JSON serialization).
  - Minimized redundant computations by reusing processed data when possible.

- **Parallelism (Future Scope):**
  - Leverage multi-threading or multiprocessing to parallelize frame reading and inference for real-time performance.

#### 4. **Observations and Challenges**

- **Challenges:**
  - High variance in inference times due to frame content complexity (e.g., crowded scenes).
  - Lower FPS on CPU compared to GPU, requiring further optimization for real-time video processing.

- **Observations:**
  - The system performs well for offline video processing.
  - Results are accurate and organized, meeting the hierarchical JSON format requirements.

#### 5. **Future Enhancements**

- **Hardware Optimization:**
  - Deploy on edge devices with dedicated accelerators (e.g., NVIDIA Jetson) for improved performance.

- **Algorithmic Improvements:**
  - Implement non-max suppression (NMS) optimization for overlapping detections.
  - Fine-tune YOLO models for specific object-subobject relationships.

- **Real-Time Processing:**
  - Explore lightweight detection models or frameworks for achieving >10 FPS on CPU.
