# ผลประเมิน YOLO กลุ่ม medium บน CCTV600

Run `cctv600-20261116T073208561699Z` · ตรวจปิดรอบเมื่อ 2026-11-16T16:13:20+07:00 (เวลาไทย)

คุณภาพครบ 3/3 โมเดล ตัวละ 600 ภาพ GT เข้าเกณฑ์ 769 คน คะแนน Box AP สูงสุดในกลุ่มคือ **yolov8m-seg.pt 61.489%**

## คุณภาพกรอบบุคคล

| Model | AP % | AP50 % | AP75 % | P % | R % | F1 % | TP | FP | FN |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 61.437 | 81.107 | 69.662 | 85.224 | 74.252 | 79.361 | 571 | 99 | 198 |
| yolo11m-seg.pt | 60.436 | 80.994 | 67.273 | 87.832 | 73.212 | 79.858 | 563 | 78 | 206 |
| yolov8m-seg.pt | 61.489 | 82.569 | 69.790 | 84.919 | 75.423 | 79.890 | 580 | 103 | 189 |

AP ใช้ IoU 0.50–0.95, 101 recall points และ maxDet 200 P/R/F1 ใช้ confidence ≥0.25 และ IoU ≥0.50 จับ valid GT ก่อนพิจารณา ignore union

## ความเร็ว

| Model | Clean rounds | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 3/3 | 29.719 | 56.126 | 17.817 | 605.934 |
| yolo11m-seg.pt | 3/3 | 30.418 | 61.257 | 16.325 | 696.762 |
| yolov8m-seg.pt | 3/3 | 30.927 | 69.127 | 14.466 | 766.317 |

เวลาหลักมาจาก 100 ภาพเดิม × 3 clean rounds หลัง warmup 10 calls ต่อรอบ Adapter รวม preprocess/H2D + forward + postprocess/native-mask transfer ส่วน input read/decode, RLE และเขียนไฟล์วัดแยก FPS = 1000 / mean adapter ms

## กราฟและภาพตัวอย่าง

![PR curve](https://raw.githubusercontent.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/main/metrics/plots/medium_pr_curve.png)

![F1-confidence curve](https://raw.githubusercontent.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/main/metrics/plots/medium_f1_curve.png)

เส้น F1 แสดงผลของ threshold ต่อ trade-off โดย main threshold คง 0.25 ภาพเปรียบเทียบ 16 ภาพเดิมเปิดในเครื่องที่ `outputs/cctv600-20261116T073208561699Z/gallery/index.html` ของ repo นี้

## แหล่งผลและข้อจำกัด

[ตารางกลุ่ม](metrics/TIER_RESULTS.csv) และ [canonical summary](metrics/RESULTS.json) มาจาก saved packets ที่ตรวจรับแล้ว Source/scientific bindings คงเดิม อ่าน [รายงานรวม](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/MASTER_RESULTS.md), [สูตรตัวชี้วัด](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md) และ [ผลตรวจปิดรอบ](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/CLOSEOUT.json)

งานนี้วัด visible bbox บน CCTV600 ของ pretrained segmentation checkpoints ส่วน native masks เก็บเป็น output diagnostics ยังไม่มี GT silhouette สำหรับ Person Mask AP ไม่มี human parsing/color/tracking score และยังไม่มี uncertainty analysis
