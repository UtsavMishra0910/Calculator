# DATASET REFERENCES & DATA SPECIFICATIONS

## 1. External Public Datasets

### 1.1 BDD100K (Berkeley DeepDrive)

**Overview:**
- Large-scale driving video dataset
- 100K+ high-quality video clips
- Urban, highway, and residential environments

**Key Features:**
- Resolution: 1280×720 at 30 FPS
- Over 1,100 hours of video
- Multiple weather conditions, times of day
- Extensive object annotations

**Contents:**
- Vehicles: cars, trucks, buses, motorcycles
- Road users: pedestrians, cyclists, riders
- Scene types: urban, highway, parking

**Access:** https://www.bdd100k.com/

**License:** Custom research license (check permissions for commercial use)

**Relevant for:** Vehicle detection, road user detection, general traffic understanding

---

### 1.2 Cityscapes Dataset

**Overview:**
- Large-scale urban scene understanding dataset
- High-quality pixel-level annotations
- 50 different cities, 5 different continents

**Key Features:**
- 5,000 finely annotated images
- 20,000 coarsely annotated images
- 19 semantic classes
- 30 instance classes

**Resolution:** 2048×1024

**Scene Types:**
- Street scenes
- Urban environments
- Various weather conditions

**Access:** https://www.cityscapes-dataset.net/

**License:** Creative Commons Attribution-NonCommercial 4.0

**Relevant for:** Lane detection, road user detection, semantic segmentation

---

### 1.3 KITTI Dataset

**Overview:**
- Autonomous driving dataset
- Real-world 3D object detection benchmark
- Collected in Karlsruhe, Germany

**Key Features:**
- 15,000+ images
- 3D bounding box annotations
- GPS/IMU data available
- Multiple viewing angles

**Object Classes:**
- Car, pedestrian, cyclist
- Van, truck, tram, train, misc

**Access:** http://www.cvlibs.net/datasets/kitti/

**License:** Creative Commons Attribution-NonCommercial 3.0

**Relevant for:** 3D object detection, vehicle classification

---

### 1.4 Mapillary Vistas

**Overview:**
- Global street-level imagery dataset
- Over 2.5 million images
- 25K diverse image types
- Street view imagery

**Key Features:**
- Worldwide coverage
- Diverse environmental conditions
- High-quality pixel annotations

**Access:** https://www.mapillary.com/dataset/vistas

**License:** CC BY-SA 4.0

**Relevant for:** Diverse environmental conditions, global traffic patterns

---

### 1.5 Wider Face Dataset

**Overview:**
- Large-scale face detection benchmark
- Diverse and challenging
- 393,703 labeled faces

**Key Features:**
- Extreme poses
- Extreme illuminations
- Occlusions and low resolution

**Relevance:** Can be repurposed for helmet detection training

**Access:** http://shuoyang1213.me/WIDERFACE/

**License:** Attribution 4.0 International

---

## 2. Indian/Bengaluru Specific Datasets

### 2.1 Indian Dataset Resources

**Note:** Limited public datasets specifically for Indian traffic. Recommended approach:

1. **Bengaluru Traffic Police Data**
   - CCTV footage from major intersections
   - Real violation examples
   - Requires partnership agreement

2. **MapMyIndia Data**
   - Traffic camera footage
   - Real-time traffic data
   - Partnership with Gridlock competition

3. **Commercial Traffic Datasets**
   - Various startups offer Indian traffic data
   - Varies in quality and coverage

---

## 3. Domain-Specific Datasets for Training

### 3.1 Helmet Detection Dataset

**Need:** 10,000+ images (5,000 with helmet, 5,000 without)

**Collection Strategy:**
1. Use BDD100K as base (motorcycle riders)
2. Collect from Bengaluru traffic cameras
3. Web scraping (traffic imagery)
4. Synthetic data generation (if needed)

**Annotation Format:**
```
helmet_status: {
  "helmet": "yes/no/unclear",
  "rider_visible": "yes/no",
  "bounding_box": [x1, y1, x2, y2],
  "confidence": 0.95
}
```

### 3.2 Seatbelt Detection Dataset

**Need:** 8,000+ images (4,000 with seatbelt, 4,000 without)

**Collection Strategy:**
1. Extract from BDD100K (car occupants)
2. City traffic footage
3. Synthetic car interior rendering

**Annotation Format:**
```
seatbelt_status: {
  "seatbelt": "yes/no/unclear",
  "occupant_visible": "yes/no",
  "seat_position": "driver/passenger",
  "bounding_box": [x1, y1, x2, y2]
}
```

### 3.3 License Plate Dataset

**Need:** 5,000+ images of license plates

**Collection Strategy:**
1. Synthetic generation (OpenALPR format)
2. Real plates from traffic images
3. Varied angles and lighting

**Formats:**
- Indian standard: 2 lines, 10-12 characters
- State code, district code, series, number

**Annotation:**
```
license_plate: {
  "number": "KA-01-AB-1234",
  "plate_roi": [x1, y1, x2, y2],
  "angle": 0-45,
  "occlusion": "none/partial/heavy"
}
```

---

## 4. Dataset Preparation Pipeline

### 4.1 Data Collection

```
1. Raw Footage Collection
   ├─ Traffic camera feeds (CCTV)
   ├─ Video files (local storage)
   └─ Dashcam footage (external sources)

2. Frame Extraction
   ├─ Extract frames at variable intervals
   ├─ Remove duplicates/blur
   └─ Quality check (resolution, brightness)

3. Preliminary Filtering
   ├─ Remove non-traffic scenes
   ├─ Keep relevant vehicle/rider shots
   └─ Balance violation types
```

### 4.2 Annotation Process

```
Stage 1: Coarse Annotation (Faster)
  ├─ Draw vehicle bounding boxes
  ├─ Mark violation type (if any)
  └─ Basic quality checks

Stage 2: Fine Annotation (Detailed)
  ├─ Mark specific violations
  ├─ Add helmet/seatbelt status
  ├─ Mark license plate location
  └─ Verify accuracy

Stage 3: QA/QC Review
  ├─ 20% re-annotation by different person
  ├─ Inter-rater agreement check
  ├─ Conflict resolution
  └─ Quality metrics (Cohen's Kappa > 0.85)
```

### 4.3 Data Augmentation Strategy

**During Training:**
```
Augmentation Techniques:
  ├─ Random flip (horizontal)
  ├─ Random rotation (±15°)
  ├─ Random scale (0.8x to 1.2x)
  ├─ Random brightness (±20%)
  ├─ Random contrast (±20%)
  ├─ Random hue/saturation
  ├─ Mosaic (combine 4 images)
  └─ CutMix (mix random regions)
```

---

## 5. Data Specifications

### 5.1 Input Image Specifications

```
Format:           JPEG, PNG, MP4 (for video)
Resolution:       1920x1080 (full HD) minimum
Color Space:      RGB, 8-bit
Aspect Ratio:     16:9 preferred
File Size:        <5 MB per image
Metadata:         EXIF with timestamp (preferred)
```

### 5.2 Annotation Format Standards

**COCO JSON Format (Recommended):**
```json
{
  "images": [
    {
      "id": 1,
      "file_name": "image001.jpg",
      "height": 1080,
      "width": 1920,
      "date_captured": "2024-06-15T14:30:45"
    }
  ],
  "annotations": [
    {
      "id": 1,
      "image_id": 1,
      "category_id": 1,
      "bbox": [x, y, width, height],
      "area": width * height,
      "iscrowd": 0,
      "attributes": {
        "helmet_status": "no_helmet",
        "violation_type": "helmet_non_compliance",
        "confidence": 0.95
      }
    }
  ],
  "categories": [
    {"id": 1, "name": "motorcycle"},
    {"id": 2, "name": "car"},
    {"id": 3, "name": "truck"},
    {"id": 4, "name": "bus"},
    {"id": 5, "name": "person"},
    {"id": 6, "name": "cyclist"}
  ]
}
```

**Pascal VOC Format (Alternative):**
```xml
<annotation>
  <filename>image001.jpg</filename>
  <size>
    <width>1920</width>
    <height>1080</height>
  </size>
  <object>
    <name>motorcycle</name>
    <bndbox>
      <xmin>100</xmin>
      <ymin>200</ymin>
      <xmax>500</xmax>
      <ymax>700</ymax>
    </bndbox>
    <attributes>
      <helmet_status>no_helmet</helmet_status>
      <violation_type>helmet_non_compliance</violation_type>
    </attributes>
  </object>
</annotation>
```

### 5.3 Dataset Split

```
Total Dataset: 50,000 images

Training Set: 35,000 images (70%)
  ├─ General traffic: 25,000 images
  ├─ Helmet violations: 4,000 images
  ├─ Seatbelt violations: 3,000 images
  ├─ Triple riding: 2,000 images
  └─ Other violations: 1,000 images

Validation Set: 7,500 images (15%)
  └─ Same distribution as training

Test Set: 7,500 images (15%)
  ├─ Unseen variations
  ├─ Real-world conditions
  └─ Corner cases
```

### 5.4 Class Distribution

```
Vehicles:
  ├─ Car: 35%
  ├─ Motorcycle/Bike: 30%
  ├─ Auto-rickshaw: 15%
  ├─ Bus: 10%
  ├─ Truck: 8%
  └─ Other: 2%

Violations (in violation images):
  ├─ Helmet non-compliance: 35%
  ├─ Seatbelt non-compliance: 25%
  ├─ Triple riding: 15%
  ├─ Stop-line violation: 10%
  ├─ Red-light violation: 10%
  ├─ Wrong-side driving: 3%
  └─ Illegal parking: 2%

Clean Images (no violations): 20%
```

---

## 6. Data Quality Metrics

### 6.1 Annotation Quality

| Metric | Target |
|--------|--------|
| Inter-rater Agreement (Cohen's Kappa) | >0.85 |
| Missing Annotations | <1% |
| False Annotations | <2% |
| Bounding Box Accuracy | >95% IoU overlap |

### 6.2 Image Quality

| Metric | Target |
|--------|--------|
| Resolution < 1920x1080 | <5% of dataset |
| Blur Score (Laplacian var) | >100 |
| Brightness (0-255 range) | 50-200 |
| Exposure Level | Properly exposed |

---

## 7. Data Management Plan

### 7.1 Data Storage

```
Training Phase:
  ├─ Local SSD: 500 GB (fast training access)
  └─ Backup NAS: 1 TB (redundancy)

Production Phase:
  ├─ Hot Storage (recent 30 days): SSD/NVMe
  ├─ Warm Storage (30-365 days): HDD
  └─ Cold Storage (>1 year): AWS S3 Glacier ($0.004/GB/month)
```

### 7.2 Data Privacy & Security

```
PII Handling:
  ├─ Blur driver/occupant faces
  ├─ Encrypt license plate data
  ├─ Anonymize GPS coordinates
  └─ Remove EXIF metadata

Access Control:
  ├─ Role-based access (admin, researcher, engineer)
  ├─ Audit logging of all access
  ├─ Data encryption at rest (AES-256)
  └─ TLS 1.3 for data in transit
```

### 7.3 Data Versioning

```
Dataset Versions:
  ├─ v1.0: Initial 50K dataset
  ├─ v1.1: Data cleaning and fixes
  ├─ v2.0: Expanded to 100K with new sources
  └─ v2.1: Additional weather/night conditions

Changelog:
  ├─ Date added/removed
  ├─ Reason for change
  ├─ Impact on model metrics
  └─ Updated by (researcher name)
```

---

## 8. Recommended Dataset Timeline

```
Week 1-2:     Identify data sources
Week 3-6:     Collect raw footage (20,000 images)
Week 7-10:    Initial annotation (coarse pass)
Week 11-12:   Fine annotation + QA/QC
Week 13-14:   Data augmentation and preprocessing
Week 15-16:   Final dataset ready for training
```

---

## 9. Data Acquisition Contacts

### Traffic Authority Partnerships
- **Bengaluru Traffic Police (ASTrAM):** Traffic data, violation examples
- **MapMyIndia:** Traffic datasets, localized data

### Potential Data Sources
- City CCTV systems
- Dashcam footage repositories
- Traffic management companies
- Academic institutions

---

**Document Status:** Data Specifications Complete  
**Next Step:** Begin data collection and annotation
