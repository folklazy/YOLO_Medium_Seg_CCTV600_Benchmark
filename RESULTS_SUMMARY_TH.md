# ผลประเมิน YOLO บน CCTV600

Run `cctv600-20261116T073208561699Z`

คะแนนหลักเป็น Person Box AP ของ segmentation checkpoints บน visible GT bbox เกณฑ์คนด้านยาว ≥50 px

| Model | Complete | Box AP % | AP50 % | AP75 % | P % | R % | F1 % |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 600/600 | 61.437 | 81.107 | 69.662 | 85.224 | 74.252 | 79.361 |
| yolo11m-seg.pt | 600/600 | 60.436 | 80.994 | 67.273 | 87.832 | 73.212 | 79.858 |
| yolov8m-seg.pt | 600/600 | 61.489 | 82.569 | 69.790 | 84.919 | 75.423 | 79.890 |

| Model | Clean rounds | Forward ms/image | Adapter ms/image | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 3/3 | 29.719 | 56.126 | 17.817 | 605.934 |
| yolo11m-seg.pt | 3/3 | 30.418 | 61.257 | 16.325 | 696.762 |
| yolov8m-seg.pt | 3/3 | 30.927 | 69.127 | 14.466 | 766.317 |

Adapter time รวม preprocessing/H2D, forward, NMS/native masks และ compact transfer ส่วน input I/O, RLE และ output writing เก็บแยก

P/R/F1 ใช้confidence≥0.25 และbboxIoU≥0.50 ส่วนAPใช้IoU0.50:0.05:0.95 และmaxDet200 โดยเก็บpredictionsเหนือconfidencefloor0.001

Timing หลักแสดงเมื่อมีสาม clean rounds ครบ งาน GPU อื่นทำให้รอบเวลาเป็น diagnostics

GT person inventory อ้างคำยืนยันตรวจ600ภาพตามกติกาทุกคน≥50px ข้อมูลบางส่วนเคยใช้พัฒนาannotation helper ส่วนผู้เข้าสอบใช้pretrained checkpointที่ตรึง

ภาพจริงและ per-image diagnostics เก็บ private ใน outputs ของ tier ส่วน results JSON/CSV เก็บสถิติรวม

ผลนี้เป็น checkpoint+application configuration comparison ของกรอบคน และยังไม่มีผล Person Mask AP หรือ tracking
