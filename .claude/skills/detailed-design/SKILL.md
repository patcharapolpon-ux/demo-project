---
name: detailed-design
description: สร้างหรืออัปเดตไฟล์ Detailed Design เชิงแนวคิด (docs/02-design/02-technical/detailed-design/{feature-slug}.md) ต่อ feature/backlog item — เอกสารออกแบบละเอียดที่ยังไม่ผูกมัดกับเทคโนโลยี/เฟรมเวิร์ก/protocol ใด ๆ ประกอบด้วย sequence flow diagram เป็นอย่างน้อย พร้อมการตรวจสอบ/กฎธุรกิจ (validation) และกรณีผิดพลาด (error handling) ต่อขั้นตอน โดยอ้างอิง Backlog, Feature List, Spec, User Journey, high-level-architecture.md, database-schema.md และ api-spec.md (ถ้ามี) ถ้ามี tech-stack.md อยู่แล้ว จะเพิ่มหัวข้อท้ายไฟล์ต่อ feature "Technical Mapping" ที่ map step ใน sequence flow ไปยังกลไกจริงด้วย ทำได้ทั้งครบทุก feature หรือระบุเจาะจงเฉพาะบางรายการ ใช้ skill นี้เมื่อ user ขอให้ "สร้าง detailed design", "ทำ sequence diagram แบบละเอียด", "ออกแบบ low-level design", "ทำ interaction flow ระหว่าง component" หรือเรียก /detailed-design ตรง ๆ
---

# Detailed Design — สร้าง/อัปเดต Detailed Design เชิงแนวคิด (sequence flow) ต่อ feature

Skill นี้ทำงานใน main conversation ก่อน (สำรวจแหล่งข้อมูล, กำหนดขอบเขต, เคลียร์ประเด็นคลุมเครือ) แล้วค่อยส่งงานเขียนไฟล์ให้ subagent `detailed-design-writer` ทำในขั้นตอนสุดท้ายเท่านั้น ต่างจาก `architecture`/`database-schema`/`api-spec` (living document เดียวต่อโปรเจกต์) ไฟล์นี้แยกเป็น **1 ไฟล์ต่อ feature/backlog item** เก็บใน `docs/02-design/02-technical/detailed-design/{feature-slug}.md` เหมือนแนวทางของ `test-case` เพราะ detailed design ควรทำทีละ feature ได้อิสระจากกัน

**กติกาที่ต้องคุมตลอด skill นี้:** เอกสารผลลัพธ์ต้องเป็นการออกแบบเชิงแนวคิด (conceptual) เท่านั้น ห้ามมีชื่อเทคโนโลยี เฟรมเวิร์ก ไลบรารี ฐานข้อมูล หรือ protocol/รูปแบบการสื่อสารเชิงเทคนิคใด ๆ ปรากฏในเนื้อหาหรือใน sequence diagram (เช่น ชื่อ method/endpoint จริง) ถ้า user พยายามระบุรายละเอียดเชิงเทคนิคมาระหว่างทาง ให้แจ้งว่าเอกสารนี้จงใจไม่ผูกกับ tech stack และเสนอให้เก็บรายละเอียดนั้นไว้ทำเป็นเอกสารแยกภายหลังแทน

## ขั้นตอน

### 1. สำรวจข้อมูลต้นทาง

- Read `docs/01-requirements/backlog.md` และ `docs/01-requirements/feature-list.md` (ถ้ามี)
- Glob `docs/01-requirements/01-spec/*.md` (ยกเว้น `index.md`)
- Glob `docs/02-design/01-prototypes/*-journey.md`
- Read `docs/02-design/02-technical/high-level-architecture.md` (ถ้ามี) — ใช้องค์ประกอบเชิงแนวคิดเป็น participant ใน sequence diagram
- Read `docs/02-design/02-technical/database-schema.md` (ถ้ามี) — ใช้ชื่อเอนทิตี/attribute อ้างอิงข้อมูลที่ไหลระหว่าง step
- Read `docs/02-design/02-technical/api-spec.md` (ถ้ามี) — ใช้ operation ที่นิยามไว้แล้วเป็นฐานแตก sequence ต่อ feature ที่มี operation ตรงกัน
- Glob `docs/02-design/02-technical/detailed-design/*.md` (ยกเว้น `index.md`) เพื่อดูว่า feature ไหนมี detailed design แล้วบ้าง
- Glob `docs/02-design/02-technical/tech-stack.md` — ถ้ามี ให้ Read เพื่อเตรียมส่งต่อ subagent สำหรับเขียนหัวข้อ "Technical Mapping" ท้ายไฟล์ต่อ feature (ไม่ต้องถาม user อะไรเพิ่มสำหรับส่วนนี้ เพราะเป็นการอ้างอิงการตัดสินใจที่มีอยู่แล้ว ไม่ใช่ประเด็นคลุมเครือใหม่)

### 2. แจ้ง user ถ้ายังไม่มี high-level-architecture.md

ถ้ายังไม่มีไฟล์นี้ ให้แจ้ง user ว่าปกติควรมีองค์ประกอบเชิงแนวคิดจาก `architecture` ก่อน เพื่อให้ sequence diagram ใช้ชื่อ participant ที่สอดคล้องกับเอกสารอื่น และเสนอทางเลือกว่าจะรัน skill `architecture` ให้ก่อนหรือไม่ (ไม่บังคับ — ถ้า user อยากทำต่อเลยก็ทำได้ โดยจะใช้บทบาท/คำอธิบายจาก journey หรือ spec แทนชื่อ component ที่เป็นทางการ)

### 3. กำหนดขอบเขต (scope) — ถามเมื่อ user ไม่ได้ระบุเจาะจงมา

ถ้า user ไม่ได้ระบุ feature/backlog item เจาะจงมา ให้ AskUserQuestion พร้อมตัวเลือกอย่างน้อย 3 แบบ พร้อมข้อดี/ข้อเสีย เช่น:

1. **ทำให้ครบทุก feature ที่มี requirement แล้ว** — ข้อดี: ได้ detailed design ครอบคลุมทั้งระบบในรอบเดียว; ข้อเสีย: ใช้เวลานานถ้ามี feature จำนวนมาก และบาง feature อาจยังไม่พร้อม (ไม่มี journey/api-spec)
2. **เลือกเฉพาะ feature บางรายการ** (ให้ user เลือกจาก list) — ข้อดี: โฟกัสเฉพาะส่วนที่ทีมกำลังจะลงมือพัฒนาก่อน; ข้อเสีย: ส่วนอื่นยังไม่มี detailed design ให้ทีมอื่นอ้างอิง
3. **ทำเฉพาะ feature ที่ยังไม่มี detailed design เลย** — ข้อดี: ไม่ทำงานซ้ำ ครอบคลุมส่วนที่ขาดให้ครบ; ข้อเสีย: feature ที่ spec/journey/api-spec เพิ่งอัปเดตแต่มี detailed design เดิมอยู่แล้วจะไม่ถูกรีเฟรชตามในรอบนี้

### 4. ตรวจสอบความพร้อมของแหล่งอ้างอิงต่อ feature ในขอบเขต

ต่อ feature แต่ละรายการในขอบเขต ตรวจว่า:

- มี operation ที่เกี่ยวข้องใน `api-spec.md` หรือไม่ → ถ้ามี ใช้เป็นฐานหลักในการแตก sequence (สอดคล้องกับ operation ที่นิยามไว้แล้ว)
- ถ้าไม่มี ให้ใช้ journey (ถ้ามี) หรือ spec เป็นฐานแทน

ไม่ต้องถาม user ในขั้นตอนนี้ (เป็นการเลือกแหล่งอ้างอิงอัตโนมัติตามความพร้อมของแต่ละ feature) แต่ให้จดบันทึกไว้ส่งต่อ subagent และรายงานให้ user ทราบท้ายงานว่า feature ไหนขาด api-spec/journey (แม่นยำน้อยกว่า)

### 5. เคลียร์ประเด็นที่กระทบการออกแบบ sequence — ถามเมื่อจำเป็น

ตรวจสอบว่า feature ในขอบเขตมีเอนทิตีที่มีสถานะ (status) เปลี่ยนแปลงชัดเจนตาม flow หรือไม่ (เช่น สถานะออเดอร์, สถานะการชำระเงิน) ถ้ามี ให้ AskUserQuestion ถามพร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น:

1. **มี sequence diagram + validation/error handling ต่อ step เท่านั้น** (มาตรฐานของ skill นี้) — ข้อดี: กระชับ พอเพียงสำหรับ flow ที่ไม่ซับซ้อน; ข้อเสีย: ไม่เห็นภาพรวมว่าสถานะของเอนทิตีเปลี่ยนไปอย่างไรบ้างตลอด flow
2. **เพิ่มตารางการเปลี่ยนสถานะเอนทิตีต่อ step ด้วย** (แนะนำเมื่อมี status ที่ซับซ้อน) — ข้อดี: เห็นชัดว่าแต่ละ step กระทบสถานะเอนทิตีอย่างไร ลด bug จาก state ที่ไม่ถูกจัดการ; ข้อเสีย: เอกสารยาวขึ้น ต้องดูแลให้ตรงกับ database-schema.md ตลอด
3. **แยกเป็นเอกสาร state diagram ต่างหากภายหลัง ไม่ใส่ในไฟล์นี้ตอนนี้** — ข้อดี: ไฟล์นี้โฟกัสเฉพาะ sequence flow ตามที่ตั้งใจไว้; ข้อเสีย: ต้องมาเชื่อมโยง 2 เอกสารเองภายหลัง

ถ้าไม่มีเอนทิตีที่มีสถานะซับซ้อนชัดเจนในขอบเขตนี้ ให้ข้ามขั้นตอนนี้ไปได้เลย (ใช้ค่ามาตรฐานคือ sequence diagram + validation/error handling ต่อ step เสมอ ไม่ต้องถาม)

### 6. ส่งงานให้ subagent `detailed-design-writer`

เรียก Agent tool ด้วย `subagent_type: "detailed-design-writer"` (foreground เพื่อรอผลก่อนตอบ user) พร้อมส่ง:

- วันที่ปัจจุบัน (YYYY-MM-DD)
- ขอบเขต feature/backlog item (รายชื่อ RUNNING_NO หรือ "ทั้งหมด") ที่ยืนยันแล้วในขั้นตอน 3
- ต่อ feature ในขอบเขต: อิงจาก api-spec operation หรือ journey/spec (จากขั้นตอน 4)
- คำตอบเรื่องตารางการเปลี่ยนสถานะเอนทิตี (ถ้าถามในขั้นตอน 5) หรือระบุว่า "ใช้ค่ามาตรฐาน (sequence + validation/error handling) ทุก feature"
- หมายเหตุว่ามี/ไม่มี `high-level-architecture.md`, `database-schema.md`, `api-spec.md` ให้อ้างอิง
- หมายเหตุว่ามี/ไม่มี `docs/02-design/02-technical/tech-stack.md` ให้อ้างอิง ถ้ามีให้ส่งสรุป stack ต่อ layer ไปด้วย (สำหรับเขียนหัวข้อ "Technical Mapping" ท้ายไฟล์ต่อ feature)

### 7. รายงานผลกลับ user

สรุปรายชื่อไฟล์ detailed design ที่สร้าง/แก้ไข, feature ที่ขาด api-spec/journey อ้างอิง (ถ้ามี, แม่นยำน้อยกว่า), และประเด็นที่ยังค้างเป็น open question (ถ้ามี) พร้อมชวน user เพิ่มข้อมูลได้ภายหลังด้วยการรัน `/detailed-design` ซ้ำเมื่อมี requirement/api-spec ใหม่

## หมายเหตุ

- Path มาตรฐาน: `docs/02-design/02-technical/detailed-design/{feature-slug}.md` โดย `{feature-slug}` ใช้ slug เดียวกับชื่อไฟล์ spec ต้นทาง (เช่น spec `20260802-001-table-qr-ordering.md` → ไฟล์ `table-qr-ordering.md`) เหมือนแนวทางของ `test-case`
- เมื่อทีมเลือกเทคโนโลยี/protocol จริงแล้ว (มี `tech-stack.md`) ให้เพิ่มได้แค่หัวข้อ "Technical Mapping" สรุป step → กลไกจริงท้ายไฟล์ต่อ feature (ตามที่ระบุในขั้นตอน 6) ห้ามแก้ label ใน sequence diagram ให้เป็นชื่อ method/endpoint จริงไม่ว่ากรณีใด
