---
name: acceptance-criteria
description: สร้างหรืออัปเดตไฟล์ Acceptance Criteria (docs/03-testing/01-test-plan/acceptance-criteria.md) แบบ Given-When-Then ต่อ backlog item โดยอ้างอิง Backlog, Feature List, Spec, และ User Journey/Prototype (ถ้ามี) ทำได้ทั้งครบทุก backlog item หรือระบุเจาะจงเฉพาะบางรายการ ใช้ skill นี้เมื่อ user ขอให้ "สร้าง acceptance criteria", "เขียน AC แบบ Given-When-Then", "ทำเกณฑ์การยอมรับ" หรือเรียก /acceptance-criteria ตรง ๆ
---

# Acceptance Criteria — สร้าง/อัปเดต Given-When-Then ต่อ Backlog Item

Skill นี้ทำงานใน main conversation ก่อน (กำหนดขอบเขต, ถามเมื่อคลุมเครือ) แล้วค่อยส่งงานเขียนไฟล์ให้ subagent `acceptance-criteria-writer` ทำในขั้นตอนสุดท้าย

## ขั้นตอน

### 1. สำรวจข้อมูลต้นทาง

- Read `docs/01-requirements/backlog.md` และ `docs/01-requirements/feature-list.md` (ถ้ามี)
- Glob `docs/01-requirements/01-spec/*.md` (ยกเว้น `index.md`)
- Glob `docs/03-testing/01-test-plan/acceptance-criteria.md` เพื่อดูว่ามีไฟล์เดิมอยู่แล้วหรือไม่ และ backlog item ไหนมี AC แล้วบ้าง (Read ไฟล์นี้ถ้ามีอยู่)

### 2. กำหนดขอบเขต (scope) — ถามเมื่อ user ไม่ได้ระบุเจาะจงมา

- ถ้า user ระบุ backlog item/feature เจาะจงมาแล้วในคำขอ (เช่น รหัส RUNNING_NO หรือชื่อ feature จาก feature-list.md) ใช้ตามนั้นได้เลย ไม่ต้องถามซ้ำ
- ถ้า user ไม่ได้ระบุ ให้ AskUserQuestion พร้อมตัวเลือกอย่างน้อย 3 แบบ พร้อมข้อดี/ข้อเสีย เช่น:
  1. **ทำให้ครบทุก backlog item** — ข้อดี: ได้ AC ครอบคลุมทั้งระบบในรอบเดียว; ข้อเสีย: ใช้เวลานานถ้า backlog มีจำนวนมาก
  2. **เลือกเฉพาะ backlog item/feature บางรายการ** (ให้ user เลือกจาก list) — ข้อดี: โฟกัสเฉพาะส่วนที่ต้องการตอนนี้ เร็วกว่า; ข้อเสีย: ส่วนอื่นยังไม่มี AC ต้องกลับมาทำทีหลัง
  3. **ทำเฉพาะ backlog item ที่ยังไม่มี AC เลย** (ส่วนที่มีอยู่แล้วข้ามไป) — ข้อดี: ไม่ทำงานซ้ำ; ข้อเสีย: ถ้า spec เดิมมีการแก้ไข AC เก่าจะไม่ถูกอัปเดตตามในรอบนี้

### 3. ตรวจสอบ edge case ที่คลุมเครือ (ถ้ามี)

ถ้า spec ของ backlog item ในขอบเขตมีกฎทางธุรกิจที่ตีความ edge case ได้หลายแบบ (เช่น ไม่ชัดว่าเงื่อนไขขอบเขตควรนับเป็น AC แยกหรือรวมกับ AC หลัก) ให้ AskUserQuestion ถามพร้อมตัวเลือกอย่างน้อย 3 แบบ มิฉะนั้นข้ามไปขั้นตอน 4 ได้เลย

### 4. ส่งงานให้ subagent `acceptance-criteria-writer`

เรียก Agent tool ด้วย `subagent_type: "acceptance-criteria-writer"` (foreground เพื่อรอผลก่อนตอบ user) พร้อมส่ง:

- วันที่ปัจจุบัน (YYYY-MM-DD)
- ขอบเขต backlog item (รายชื่อ RUNNING_NO หรือ "ทั้งหมด") ที่ยืนยันแล้วในขั้นตอน 2
- การตัดสินใจเรื่อง edge case จากขั้นตอน 3 (ถ้ามี)

### 5. รายงานผลกลับ user

สรุป path ไฟล์ acceptance-criteria.md, รายชื่อ backlog item ที่เขียน/แก้ไข พร้อมจำนวน AC ต่อรายการ

## หมายเหตุ

- ไฟล์ `docs/03-testing/01-test-plan/acceptance-criteria.md` เป็น living document เดียวของทั้งโปรเจกต์ — เขียน/แก้ไขเฉพาะส่วนของ backlog item ที่อยู่ในขอบเขตแต่ละรอบ ไม่เขียนทับทั้งไฟล์
- เอกสารนี้เป็นแหล่งอ้างอิงหลักของ skill `test-case` (ทุก test case ต้องอ้างอิงกลับมาที่ AC ในไฟล์นี้)
