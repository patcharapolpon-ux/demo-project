---
name: user-journey
description: สร้างหรืออัปเดตไฟล์ user journey (Mermaid diagram) ใน docs/02-design/01-prototypes/ สำหรับ requirement ที่ระบุ หรือสำหรับทุก backlog item ที่ยังไม่มี journey ใช้ skill นี้เมื่อ user ขอให้ "สร้าง user journey", "วาด flow ผู้ใช้งาน" หรือเรียก /user-journey ตรง ๆ
---

# User Journey — สร้าง/อัปเดต journey diagram ผูกกับ requirement

Skill นี้ทำงานใน main conversation ก่อน (กำหนดขอบเขตและถาม user เมื่อจำเป็น) แล้วค่อยส่งงานเขียนไฟล์ให้ subagent `user-journey-writer` ทีละ 1 requirement

## ขั้นตอน

### 1. หา requirement เป้าหมาย

- ถ้า user ระบุ requirement/spec มาแล้ว ใช้ตามนั้น
- ถ้า user ไม่ได้ระบุ ให้ Read `docs/01-requirements/backlog.md` แล้ว Glob `docs/02-design/01-prototypes/*-journey.md` เทียบ running number เพื่อหา backlog row ที่ยังไม่มี journey — เสนอ list นี้ให้ user เลือกว่าจะสร้างให้ข้อไหนบ้าง (ถ้ามีมากกว่า 1 รายการ)

### 2. กำหนดขอบเขต — ถามเมื่อไม่แน่ใจ

สำหรับแต่ละ requirement ที่จะทำ journey พิจารณา: ควรแสดงบทบาทไหนเป็นหลัก (ถ้า spec มีหลายบทบาท), ต้องรวม edge case/error path ด้วยหรือไม่, ต้องการ diagram ประเภทไหนเป็นพิเศษหรือไม่

ถ้าไม่ชัดเจน ให้ AskUserQuestion พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น (ตัวอย่างกรณี spec มีหลายบทบาท):

1. วาด journey แบบ end-to-end รวมทุกบทบาทใน diagram เดียว (sequenceDiagram)
2. วาดแยกเป็นคนละ diagram ต่อบทบาทในไฟล์เดียวกัน
3. โฟกัสเฉพาะบทบาทหลัก (เช่น ลูกค้า) แล้วกล่าวถึงบทบาทอื่นแบบย่อ

### 3. ส่งงานให้ subagent user-journey-writer (ทีละ requirement)

เรียก Agent tool ด้วย `subagent_type: "user-journey-writer"` พร้อมส่ง path spec, วันที่ปัจจุบัน, และขอบเขตที่ตกลงในขั้นตอน 2 ถ้ามีหลาย requirement ให้เรียกทีละตัวตามลำดับ (ไม่ parallel เพราะทุกไฟล์ต่อท้าย log ไฟล์เดียวกัน — เรียกพร้อมกันเสี่ยง race condition)

### 4. รายงานผลกลับ user

สรุป path ไฟล์ journey ที่สร้าง/อัปเดตทั้งหมด พร้อมประเภท diagram ที่ใช้ในแต่ละไฟล์

## หมายเหตุ

- ห้ามลบไฟล์ journey เดิมเด็ดขาด ถ้าต้องแทนที่ทั้งหมดให้เสนอย้ายไป `docs/00-archived/` แทน (อย่าย้ายเองโดยไม่ถาม)
- ชื่อไฟล์ journey ผูกกับ running number เดียวกับ spec ต้นทางเสมอ (1:1) เพื่อให้ mapping ระหว่าง requirement กับ journey ชัดเจนที่สุด
