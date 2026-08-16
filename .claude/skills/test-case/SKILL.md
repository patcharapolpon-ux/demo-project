---
name: test-case
description: สร้างหรืออัปเดตไฟล์ Test Case แบบ step-by-step ต่อ feature/backlog item (docs/03-testing/01-test-plan/test-cases/{feature-slug}.md) พร้อม test id, pre-condition, test step, expected result, test data และ reference กลับไปยัง requirement/acceptance criteria โดยอ้างอิง Acceptance Criteria, Backlog, และ User Journey ทำได้ทั้งครบทุก feature หรือระบุเจาะจงเฉพาะบางรายการ ใช้ skill นี้เมื่อ user ขอให้ "สร้าง test case", "เขียน test case แบบละเอียด", "ทำ test script" หรือเรียก /test-case ตรง ๆ
---

# Test Case — สร้าง/อัปเดต test case แบบ step-by-step ต่อ feature

Skill นี้ทำงานใน main conversation ก่อน (กำหนดขอบเขต, ตรวจสอบว่ามี acceptance criteria รองรับแล้ว, ถามเมื่อคลุมเครือ) แล้วค่อยส่งงานเขียนไฟล์ให้ subagent `test-case-writer` ทำในขั้นตอนสุดท้าย

## ขั้นตอน

### 1. สำรวจข้อมูลต้นทาง

- Read `docs/03-testing/01-test-plan/acceptance-criteria.md` — ถ้ายังไม่มีไฟล์นี้เลย ให้แจ้ง user ทันทีว่าต้องรัน skill `acceptance-criteria` ก่อน (ยังไม่มีฐานสำหรับแตก test case) แล้วหยุดรอ user ตัดสินใจว่าจะรัน `acceptance-criteria` ก่อนหรือไม่
- Read `docs/01-requirements/backlog.md` และ `docs/01-requirements/feature-list.md` (ถ้ามี)
- Glob `docs/03-testing/01-test-plan/test-cases/*.md` (ยกเว้น `index.md`) เพื่อดูว่า feature ไหนมี test case แล้วบ้าง

### 2. กำหนดขอบเขต (scope) — ถามเมื่อ user ไม่ได้ระบุเจาะจงมา

- ถ้า user ระบุ feature/backlog item เจาะจงมาแล้ว ใช้ตามนั้นได้เลย
- ถ้า backlog item ที่เลือก (หรือทั้งหมด) มีบางรายการที่ยังไม่มี AC ใน acceptance-criteria.md ให้แจ้ง user ว่ารายการไหนจะถูกข้ามไปก่อน และถามว่าต้องการรัน `acceptance-criteria` ให้ก่อนไหม (AskUserQuestion พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น "รัน acceptance-criteria ให้ก่อนแล้วค่อยทำ test case ต่อ", "ข้าม backlog item นั้นไปก่อน ทำเฉพาะที่มี AC แล้ว", "ยกเลิกก่อน")
- ถ้า user ไม่ได้ระบุขอบเขต ให้ AskUserQuestion พร้อมตัวเลือกอย่างน้อย 3 แบบ พร้อมข้อดี/ข้อเสีย เช่น:
  1. **ทำให้ครบทุก feature ที่มี AC แล้ว** — ข้อดี: ได้ test case ครอบคลุมทั้งระบบในรอบเดียว; ข้อเสีย: ใช้เวลานานถ้ามี feature จำนวนมาก
  2. **เลือกเฉพาะ feature บางรายการ** (ให้ user เลือกจาก list) — ข้อดี: โฟกัสเฉพาะส่วนที่ต้องการตอนนี้; ข้อเสีย: ส่วนอื่นยังไม่มี test case
  3. **ทำเฉพาะ feature ที่ยังไม่มี test case เลย** — ข้อดี: ไม่ทำงานซ้ำ; ข้อเสีย: feature ที่ AC เพิ่งอัปเดตแต่มี test case เดิมอยู่แล้วจะไม่ถูกรีเฟรชตามในรอบนี้

### 3. ตัดสินใจความละเอียดของ test case — ถามเมื่อไม่ชัดเจน

ถ้า user ไม่ได้ระบุมา ให้ AskUserQuestion ถามว่าต้องการความละเอียดระดับไหน พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น:
1. **Positive case ตาม AC เท่านั้น** — ข้อดี: เร็ว ครอบคลุม happy path ครบ; ข้อเสีย: ไม่มี negative/edge test case ตรวจ validation
2. **Positive + Negative/Edge case สำคัญ** (แนะนำ) — ข้อดี: ครอบคลุมกรณีข้อมูลผิด/ขอบเขตด้วย; ข้อเสีย: ใช้เวลาเขียน/รีวิวมากขึ้น
3. **ครอบคลุมทุกกรณีที่เป็นไปได้อย่างละเอียด** — ข้อดี: ครบถ้วนที่สุด; ข้อเสีย: ไฟล์ยาว อาจมี test case ซ้ำซ้อนบางส่วน

### 4. ส่งงานให้ subagent `test-case-writer`

เรียก Agent tool ด้วย `subagent_type: "test-case-writer"` (foreground เพื่อรอผลก่อนตอบ user) พร้อมส่ง:

- วันที่ปัจจุบัน (YYYY-MM-DD)
- ขอบเขต feature/backlog item (รายชื่อ RUNNING_NO หรือ "ทั้งหมดที่มี AC แล้ว") ที่ยืนยันแล้วในขั้นตอน 2
- ระดับความละเอียดที่ตัดสินใจในขั้นตอน 3

### 5. รายงานผลกลับ user

สรุป path ไฟล์ test case ที่สร้าง/แก้ไข, จำนวน test case ต่อไฟล์, และ backlog item ที่ถูกข้ามเพราะยังไม่มี AC (ถ้ามี)

## หมายเหตุ

- Path มาตรฐาน: `docs/03-testing/01-test-plan/test-cases/{feature-slug}.md` โดย `{feature-slug}` ใช้ slug เดียวกับชื่อไฟล์ spec ต้นทาง (เช่น spec `20260802-001-table-qr-ordering.md` → ไฟล์ test case `table-qr-ordering.md`)
- ห้ามข้ามการตรวจสอบ acceptance-criteria.md ก่อนเสมอ (ขั้นตอน 1) — test case ที่ไม่มี AC รองรับจะไม่มี reference ที่ถูกต้อง
