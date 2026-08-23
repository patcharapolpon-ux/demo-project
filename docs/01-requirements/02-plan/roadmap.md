# Roadmap & Phase Plan

**วันที่จัดทำ/อัปเดตล่าสุด:** 2026-08-23
**สถานะ:** ร่าง (Draft)

> เอกสารนี้เป็น roadmap เดียวต่อโปรเจกต์ แปลงมาจาก [[../backlog|backlog.md]] + [[../feature-list|feature-list.md]] ร่วมกับช่องว่างเชิงเทคนิค/เอกสารที่ค้นพบระหว่างทำ [[../../02-design/02-technical/index|02-design/02-technical]] และ [[../../03-testing/01-test-plan/test-plan|test-plan.md]] จัดกลุ่มเป็น Phase ตามลำดับที่ควรลงมือทำ ไม่ใช่ตามลำดับ MoSCoW ของฟีเจอร์อย่างเดียว เพราะมีงานเชิงเทคนิคที่เป็น prerequisite ข้ามฟีเจอร์อยู่ด้วย

## เหตุผลการแบ่ง Phase

- **Phase 0 (Foundation)** — งานที่เป็น "ตัวบล็อกเทคนิคข้ามทุกฟีเจอร์" ต้องทำก่อนเขียนโค้ดฟีเจอร์ใดเลย โดยเฉพาะบัญชีพนักงาน/authentication ซึ่งแม้จะไม่มี priority สูงในแง่ business value โดยตรง (ไม่ใช่ user-facing flow แบบ 001) แต่ในทางเทคนิคจำเป็นสำหรับ audit log (003) และ consent gate (004) ทั้งคู่ จึงถูกจัดเป็น **Must have** ใน feature-list.md และต้องขึ้นมาก่อน
- **Phase 1** ตรงกับ 20260802-001 (Must have) — รวมงานเทคนิคที่เป็นเงื่อนไขจำเป็นของฟีเจอร์นี้โดยตรง
- **Phase 2** ตรงกับ 20260802-002/003 (Should have) — รวมกลไกเทคนิคเบื้องหลัง audit log
- **Phase 3** ตรงกับ 20260802-004 (Could have) — รวม NFR ใหม่เรื่อง PDPA offboarding ที่ผูกกับ feature นี้โดยตรง
- **Phase 4 (Pre-launch Hardening)** — ความเสี่ยง/การตัดสินใจที่ตั้งใจเลื่อนไว้ (backup/DR, scale, rate limit) ไม่ block การพัฒนาฟีเจอร์ แต่ต้องปิดก่อน go-live จริงเสมอ
- **Track คู่ขนาน** — งานเอกสารทดสอบ (acceptance-criteria, test-case) ไม่ใช่ "ฟีเจอร์" จึงไม่ผูกกับ phase ใด phase หนึ่ง แต่ควรทำคู่กันไปตลอดทุก phase
- **Backlog/Future** — รายละเอียดที่ spec ระบุไว้ชัดว่า "ไม่บังคับ" ไม่กระทบ MVP จึงเก็บไว้ทำหลังเปิดร้านจริงถ้าจำเป็น

## Phase 0 — Foundation

| งาน | สถานะ | หมายเหตุ |
|---|---|---|
| เขียน spec บัญชีพนักงาน + authentication | ✅ เขียนเสร็จ | [[../01-spec/20260823-005-staff-account-and-authentication|20260823-005]] — PIN code ต่อเครื่อง, ผู้บริหารร้านจัดการบัญชีเท่านั้น, มี operation offboarding แยก |
| Implement บัญชีพนักงาน + authentication ตาม spec 20260823-005 | ⬜ รอเริ่ม | ต้องตามด้วยการอัปเดต [[../../02-design/02-technical/high-level-architecture\|high-level-architecture.md]], [[../../02-design/02-technical/database-schema\|database-schema.md]], [[../../02-design/02-technical/api-spec\|api-spec.md]] และ [[../../02-design/02-technical/detailed-design/index\|detailed-design]] ให้ครอบคลุม spec นี้ก่อน (ยังไม่ทำ ณ วันที่จัดทำเอกสารนี้) |
| ตัดสินใจ ORM/วิธีเรียกข้อมูล | ⬜ รอตัดสินใจ | [[../../02-design/02-technical/tech-stack|tech-stack.md]] หัวข้อ 6 |
| ตั้งค่า Staging environment จริง | ⬜ รอ setup จริง | แนวคิดตัดสินใจแล้วใน test-plan.md หัวข้อ 4 (Supabase/Vercel project ที่ 2) แต่ยังไม่ตั้งค่าจริง |
| ตัดสินใจกลไก Access Control ระดับเทคนิค (Supabase RLS vs app-level) | ⬜ รอตัดสินใจ | tech-stack.md หัวข้อ 6 — กระทบการออกแบบ RBAC ของ 002/003 |

## Phase 1 — MVP Core (20260802-001, Must have)

| งาน | สถานะ | หมายเหตุ |
|---|---|---|
| หาเครื่องมือ/ไลบรารีสร้าง QR code ประจำโต๊ะ | ⬜ รอตัดสินใจ | tech-stack.md หัวข้อ 6 — ต้องมี QR จริงก่อน launch |
| ตั้งค่า Kiosk mode ของแท็บเล็ตพนักงาน | ⬜ รอตัดสินใจ | tech-stack.md หัวข้อ 6 |
| เครื่องมือ/ไลบรารี client-side caching (offline-first บางส่วน) | ⬜ รอตัดสินใจ | tech-stack.md หัวข้อ 6 — รองรับ business rule offline-first ของ 001 |
| ชี้แจงว่าต้องมี "การจัดการโต๊ะ" เป็นองค์ประกอบแยกหรือไม่ | ⬜ รอตัดสินใจ | [[../../02-design/02-technical/detailed-design/table-qr-ordering|detailed-design/table-qr-ordering.md]] หัวข้อ 5 |

## Phase 2 — Supporting Features (20260802-002/003, Should have)

| งาน | สถานะ | หมายเหตุ |
|---|---|---|
| กลไก auto-capture + auto-delete ของ audit log (trigger/scheduled job) | ⬜ รอตัดสินใจ | tech-stack.md หัวข้อ 6 — เบื้องหลัง business rule เก็บ 90 วันของ 003 |

## Phase 3 — Compliance (20260802-004, Could have)

| งาน | สถานะ | หมายเหตุ |
|---|---|---|
| อัปเดต spec 003/004 ให้รวม PDPA offboarding anonymization (90 วัน) | ✅ อัปเดตเสร็จ | แก้ไข [[../01-spec/20260802-003-audit-log|20260802-003]] และ [[../01-spec/20260802-004-pdpa-consent|20260802-004]] แล้ว |
| อัปเดต database-schema.md/api-spec.md ให้รองรับกลไก anonymize จริง | ⬜ รอเริ่ม | ต้องตามหลัง spec 003/004 ที่แก้ไปแล้ว — ยังไม่มีรายละเอียดเชิงแนวคิดของกลไก anonymize ใน database-schema.md/api-spec.md ปัจจุบัน |

## Phase 4 — Pre-launch Hardening (Cross-cutting)

| งาน | สถานะ | หมายเหตุ |
|---|---|---|
| ตัดสินใจ Backup/DR policy ของข้อมูลออเดอร์/เมนู/ยอดขาย | ⬜ ยังไม่ตัดสินใจ (เจตนา) | test-plan.md หัวข้อ 5.3 — user ยืนยันว่ายังไม่ต้องตัดสินใจตอนนี้ แต่ต้องปิดก่อน go-live จริง |
| ยืนยันสมมติฐาน scale (15-30 โต๊ะ, ผู้ใช้พร้อมกันหลักสิบคน) กับเจ้าของร้านจริง | ⬜ รอยืนยัน | high-level-architecture.md หัวข้อ 8, test-plan.md หัวข้อ 5.9 |
| Implement rate limit การสั่ง QR แบบหลวม | ⬜ รอเริ่ม | test-plan.md หัวข้อ 5.8 |

## Track คู่ขนาน (ไม่ผูก Phase)

| งาน | สถานะ | หมายเหตุ |
|---|---|---|
| สร้าง `acceptance-criteria.md` | ⬜ รอเริ่ม | prerequisite ของ test-case ทุก feature — ควรทำคู่กับ Phase 1 ทันที ไม่ต้องรอ |
| สร้าง test-case/test-result ราย feature | ⬜ รอเริ่ม | ตามหลัง acceptance-criteria.md เสร็จในแต่ละ feature |

## Backlog / Future (Nice-to-have — ไม่บล็อก MVP)

| งาน | หมายเหตุ |
|---|---|
| ตัวกรอง (filter) ของหน้าดู Audit Log ย้อนหลัง (ช่วงเวลา/ประเภท/ผู้ทำรายการ) | api-spec.md หัวข้อ 5, detailed-design/audit-log.md หัวข้อ 4 — spec 003 ระบุว่า "ไม่บังคับ" |
| ตัวกรองสถานะของหน้าคิวบาริสต้า | api-spec.md หัวข้อ 5 — spec 001 ไม่ได้ระบุรายละเอียด ไม่ block core flow |

## เอกสารที่เกี่ยวข้อง

- [[../backlog|backlog.md]]
- [[../feature-list|feature-list.md]]
- [[../../02-design/02-technical/tech-stack|tech-stack.md]]
- [[../../02-design/02-technical/high-level-architecture|high-level-architecture.md]]
- [[../../03-testing/01-test-plan/test-plan|test-plan.md]]
- [[../03-task/index|03-task]] — งานย่อยที่จะแตกออกจาก roadmap นี้

## ประวัติการแก้ไข

- 2026-08-23: สร้างไฟล์ใหม่ทั้งหมด จัดกลุ่มรายการที่พบระหว่างทำเอกสารเชิงเทคนิค (tech-stack.md, api-spec.md, detailed-design, test-plan.md) เป็น Phase 0-4 + Track คู่ขนาน + Backlog/Future ตามที่ user ยืนยันในบทสนทนา รวมสถานะล่าสุดของ Phase 0 (เขียน spec 20260823-005 เสร็จแล้ว) และ Phase 3 (แก้ spec 003/004 เสร็จแล้ว)
