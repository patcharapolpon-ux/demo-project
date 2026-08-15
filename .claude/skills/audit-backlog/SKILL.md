---
name: audit-backlog
description: ตรวจสอบ backlog.md ทั้งหมด สร้าง/อัปเดต feature list (MoSCoW) และสร้าง/อัปเดต user journey ที่เกี่ยวข้องให้ครบทุก requirement ในรอบเดียว ใช้ skill นี้เมื่อ user ขอให้ "ตรวจสอบ backlog แล้วสร้าง feature list กับ user journey ให้หน่อย" หรือเรียก /audit-backlog ตรง ๆ ถ้า user ต้องการแค่ feature list อย่างเดียวหรือ user journey อย่างเดียว ให้ใช้ skill feature-list หรือ user-journey แยกแทน
---

# Audit Backlog — ภาพรวม feature list + user journey จาก backlog

Skill นี้ chain 2 ขั้นตอนต่อกัน: ทำตามขั้นตอนของ skill `feature-list` ก่อน แล้วตามด้วยขั้นตอนของ skill `user-journey`

## ขั้นตอน

### 1. รัน feature list flow

ทำตามขั้นตอนทั้งหมดของ skill `feature-list` (สำรวจ backlog+spec, ตรวจสอบกรณีคลุมเครือด้วย AskUserQuestion เมื่อจำเป็น, เรียก subagent `feature-list-writer`, รายงานผล)

### 2. หา requirement ที่ยังไม่มี user journey

Glob `docs/02-design/01-prototypes/*-journey.md` เทียบ running number กับ `docs/01-requirements/backlog.md` — ถ้ามี requirement ที่ยังไม่มี journey ให้ AskUserQuestion ถาม user ว่าต้องการให้สร้าง journey เลยหรือไม่ พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น:

1. สร้าง journey ให้ทุกรายการที่ยังไม่มีตอนนี้เลย
2. เลือกสร้างเฉพาะบางรายการ (ให้ user เลือกทีละรายการ)
3. ข้ามขั้นตอนนี้ไปก่อน (ทำ feature list อย่างเดียวพอสำหรับตอนนี้)

### 3. รัน user journey flow ตามที่ user เลือก

ทำตามขั้นตอนของ skill `user-journey` (ขั้นตอนกำหนดขอบเขต + เรียก subagent `user-journey-writer`) สำหรับ requirement ที่ user เลือกไว้ในขั้นตอน 2

### 4. สรุปผลรวม

รายงาน path ไฟล์ feature-list.md และไฟล์ journey ทั้งหมดที่สร้าง/อัปเดตในรอบนี้ให้ user ทราบแบบกระชับ
