---
name: feature-list
description: ตรวจสอบ backlog.md เทียบกับเอกสาร spec ทั้งหมด แล้วสร้าง/อัปเดต docs/01-requirements/feature-list.md เป็นตารางสรุปพร้อมจัดลำดับความสำคัญแบบ MoSCoW และคำอธิบายแต่ละฟีเจอร์ ใช้ skill นี้เมื่อ user ขอให้ "ตรวจสอบ backlog", "สร้าง feature list", "จัดลำดับความสำคัญฟีเจอร์" หรือเรียก /feature-list ตรง ๆ
---

# Feature List — ตรวจสอบ backlog แล้วสร้าง/อัปเดต feature list พร้อม MoSCoW

Skill นี้ทำงานใน main conversation ก่อน (เพื่อถาม user เมื่อเจอกรณีคลุมเครือ) แล้วค่อยส่งงานเขียนไฟล์ให้ subagent `feature-list-writer` ทำในขั้นตอนสุดท้าย

## ขั้นตอน

### 1. สำรวจข้อมูลต้นทาง

- Read `docs/01-requirements/backlog.md`
- Glob + Grep เอกสารใน `docs/01-requirements/01-spec/` เพื่อดูภาพรวมทุก requirement ที่มีอยู่

### 2. ตรวจสอบกรณีคลุมเครือ — ถามเมื่อจำเป็น

พิจารณา:

- มี backlog row ที่ไม่มี spec รองรับ หรือ spec ที่ไม่ถูกอ้างอิงใน backlog (orphan) หรือไม่ — ถ้าพบและไม่ชัดเจนว่าควรจัดการอย่างไร ให้ AskUserQuestion พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น "เพิ่มเข้า backlog ตอนนี้เลย", "ข้ามไปก่อน (ทำทีหลัง)", "ย้ายไป 00-archived เพราะเลิกใช้แล้ว"
- มี feature ใดที่การจัดลำดับ MoSCoW ดูก้ำกึ่งจนอาจกระทบต่อ roadmap จริง (ไม่ใช่แค่ใช้เกณฑ์ default ได้ตรง ๆ) — ถ้ามีให้ AskUserQuestion พร้อมตัวเลือก Must have / Should have / Could have / Won't have พร้อมเหตุผลของแต่ละตัวเลือกให้ user ช่วยยืนยัน

ถ้าทุกอย่างชัดเจนพอ ให้ข้ามไปขั้นตอน 3 ได้เลยโดยไม่ต้องถาม

### 3. ส่งงานให้ subagent feature-list-writer

เรียก Agent tool ด้วย `subagent_type: "feature-list-writer"` (foreground เพื่อรอผลก่อนตอบ user) พร้อมส่ง:

- วันที่ปัจจุบัน (YYYY-MM-DD)
- การตัดสินใจทั้งหมดจากขั้นตอน 2 (ถ้ามี)

### 4. รายงานผลกลับ user

สรุป path ไฟล์ feature-list.md, จำนวน feature แยกตาม MoSCoW, และปัญหาการตรวจสอบที่พบ (ถ้ามี)

## หมายเหตุ

ไฟล์ `docs/01-requirements/feature-list.md` เป็น living document — อัปเดตทับเสมอ ไม่สร้างไฟล์ใหม่ทุกครั้งที่รัน
