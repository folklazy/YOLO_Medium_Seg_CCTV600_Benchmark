# สรุปผล Medium (M) บน CCTV600

## สรุปใน 1 นาที

- ทดสอบ 3 pretrained checkpoints บน 600 ภาพชุดเดียวกันและ GT เข้าเกณฑ์ 769 คน
- Input 640×640, batch 1, FP32 บน Tesla T4 ไม่มี fine-tuning ใน benchmark รอบนี้
- คุณภาพครบ 600 IDs ต่อโมเดล และเวลาครบ 3 clean rounds ต่อโมเดล
- Box AP สูงสุดเป็น yolov8m-seg.pt ที่ 61.489%
- Adapter time ต่ำสุดเป็น yolo26m-seg.pt ที่ 56.126 ms/ภาพ
- Allocated VRAM ต่ำสุดเป็น yolo26m-seg.pt ที่ 605.934 MiB

## ผลลัพธ์หลัก

| Model | Box AP % | AP75 % | Recall % | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 61.437 | 69.662 | 74.252 | 29.719 | 56.126 | 17.817 | 605.934 |
| yolo11m-seg.pt | 60.436 | 67.273 | 73.212 | 30.418 | 61.257 | 16.325 | 696.762 |
| yolov8m-seg.pt | 61.489 | 69.790 | 75.423 | 30.927 | 69.127 | 14.466 | 766.317 |

Box AP/P/R/F1 แสดงเป็นเปอร์เซ็นต์ Forward และ adapter เป็น ms/ภาพ, FPS = 1000 / mean adapter ms และ VRAM เป็น peak allocated MiB

## ผู้ชนะของแต่ละด้าน

| ด้าน | โมเดล | ค่าที่วัด |
| --- | --- | --- |
| Box AP | yolov8m-seg.pt | 61.489 % |
| AP75 | yolov8m-seg.pt | 69.790 % |
| Recall | yolov8m-seg.pt | 75.423 % |
| Forward time | yolo26m-seg.pt | 29.719 ms/ภาพ |
| Adapter time | yolo26m-seg.pt | 56.126 ms/ภาพ |
| Adapter FPS | yolo26m-seg.pt | 17.817 ภาพ/วินาที |
| Allocated VRAM | yolo26m-seg.pt | 605.934 MiB |

## สิ่งที่ตัวเลขบอกเรา

- yolov8m-seg.pt มี AP สูงกว่า yolo26m-seg.pt 0.052 จุดเปอร์เซ็นต์
- ตัวนำ AP มี TP 580, FP 103 และ FN 189 ที่ confidence ≥0.25 / bbox IoU ≥0.50
- Adapter ของ yolo26m-seg.pt เร็วกว่าตัวนำ AP 13.000 ms/ภาพ
- ตัวนำ AP ใช้ allocated VRAM มากกว่าค่าต่ำสุด 160.384 MiB

## บทบาทของแต่ละโมเดล

| โมเดล | จุดเด่นที่วัดได้ | ข้อแลกเปลี่ยน | เงื่อนไขการเลือก |
| --- | --- | --- | --- |
| yolo26m-seg.pt | forward เร็วสุดในกลุ่ม, adapter เร็วสุดในกลุ่ม, allocated VRAM ต่ำสุดในกลุ่ม | AP ห่างตัวนำ 0.052 pp, Recall ห่างตัวนำ 1.170 pp | จำกัดเวลา adapter, จำกัด allocated VRAM |
| yolo11m-seg.pt | Precision สูงสุด | AP ห่างตัวนำ 1.053 pp, Recall ห่างตัวนำ 2.211 pp, adapter เพิ่ม 5.131 ms | ลดสัดส่วนกรอบเกิน |
| yolov8m-seg.pt | Box AP สูงสุดในกลุ่ม, Recall สูงสุด | adapter เพิ่ม 13.000 ms, VRAM เพิ่ม 160.384 MiB | เน้น Box AP, เก็บคนให้ครบ |

## ข้อแลกเปลี่ยนหลัก

### คุณภาพกับความเร็ว

ตัวนำ AP มี AP 61.489% ส่วนตัว adapter เร็วสุดมี AP 61.437% ทั้งสองเป้าหมายอาจได้ checkpoint เดียวกัน ให้พิจารณา forward กับ adapter แยกกัน เพราะ native mask postprocessing มีต้นทุนอยู่หลัง model forward

### คุณภาพกับหน่วยความจำ

ตัว allocated VRAM ต่ำสุดมี AP 61.437% และใช้ 605.934 MiB ค่า allocator peak นี้ครอบคลุม resident model ใน benchmark หลัง warmup ส่วน memory รวมในระบบให้ตรวจ CUDA context และบริการที่รันร่วมด้วย

## ข้อควรระวังในการตีความ

GT ใช้ visible bbox ของคนด้านยาว ≥50 px รวม 769 คน การรับรอง person inventory อิงการตรวจรับของผู้จัดทำ dataset ยังไม่มี independent inventory review ครบชุด ขนาดโมเดลและข้อมูล pretraining ต่างกัน การเปรียบเทียบนี้ยังไม่แยกสาเหตุทาง architecture ไม่มี uncertainty analysis และ Person Mask AP ต้องมี GT silhouette เพิ่ม Adapter time ครอบคลุม preprocessing/H2D, forward และ postprocessing/native-mask transfer โดย input I/O, RLE, writing, model loading และ queue time มีขอบเขตแยก

## อ่านต่อ

[รายงานเทคนิค](REPORT.md) · [การวิเคราะห์ภาพและข้อผิดพลาด](PRESENTATION_SUMMARY_TH.md) · [ตารางเต็ม](metrics/TIER_RESULTS.csv) · [สูตรตัวชี้วัด](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md)
