# Test Plan

**วันที่จัดทำ/อัปเดตล่าสุด:** 2026-08-23
**สถานะ:** ร่าง (Draft)

## 1. ภาพรวม & วัตถุประสงค์

my-coffee-store คือระบบสนับสนุนการดำเนินงานของร้านกาแฟหน้าร้านเดียว (single-branch) มีแกนกลางเป็นระบบสั่งเครื่องดื่มจากโต๊ะผ่านการสแกน QR code ที่เชื่อมโยงลูกค้า บาริสต้า และพนักงานเสิร์ฟแบบ real-time ต่อยอดด้วยฟีเจอร์สนับสนุนอีก 3 ส่วน ได้แก่ หน้าสรุปยอดขายสำหรับผู้บริหารร้าน, การบันทึก audit log เพื่อตรวจสอบย้อนหลัง, และกลไกขอความยินยอมตาม PDPA จากพนักงาน (ดู [[../../02-design/02-technical/high-level-architecture|high-level-architecture.md]] และ [[../../02-design/02-technical/tech-stack|tech-stack.md]])

วัตถุประสงค์ของการทดสอบรอบนี้คือกำหนดกลยุทธ์และขอบเขตการทดสอบภาพรวมให้ครอบคลุมทั้ง 4 backlog item ที่มีอยู่ในปัจจุบัน (20260802-001 ถึง 20260802-004) ทั้งด้าน functional (ตาม business rule ในแต่ละ spec) และ non-functional (ตาม NFR ที่ user ยืนยันในรอบนี้ — ดูหัวข้อ 5) เพื่อเป็นฐานให้ทีมแตก test case ราย feature ต่อไปใน [[test-cases/index|test-cases]]

## 2. ขอบเขตการทดสอบ (Scope)

### สิ่งที่ทดสอบ (In scope)

- **20260802-001 — ระบบสั่งกาแฟจากโต๊ะด้วยการสแกน QR Code** (`Must have`) — flow สั่งออเดอร์ครบวงจร (สแกน QR → เลือกเมนู+ตัวเลือกเสริม → ยืนยันสั่ง → เข้าคิวบาริสต้าโดยตรง → บาริสต้ากดเสร็จ → แจ้งเตือนพนักงานเสิร์ฟอัตโนมัติ → เสิร์ฟที่โต๊ะ), การแสดงสถานะออเดอร์แบบ real-time ฝั่งลูกค้า, การเปิด/ปิดเมนูชั่วคราว (manual toggle) โดยผู้บริหารร้าน/บาริสต้า
- **20260802-002 — หน้า Dashboard ดูยอดขาย** (`Should have`) — การแสดงยอดขายรวม+จำนวนออเดอร์ตาม preset ช่วงเวลา ("วันนี้"/"สัปดาห์นี้"/"เดือนนี้"), การจำกัดสิทธิ์เข้าถึงเฉพาะบทบาทผู้บริหารร้าน
- **20260802-003 — ระบบบันทึก Log (Audit Trail)** (`Should have`) — การบันทึก log เหตุการณ์สำคัญ 4 ประเภท (สร้างออเดอร์, เปลี่ยนสถานะออเดอร์, เปิด/ปิดเมนู, เข้าถึง dashboard) พร้อม timestamp+ผู้ทำรายการ, การลบ log อัตโนมัติเมื่ออายุเกิน 90 วัน, การจำกัดสิทธิ์ดู log เฉพาะบทบาทผู้บริหารร้าน
- **20260802-004 — ขอความยินยอม (Consent) ตาม PDPA จากพนักงาน** (`Could have`) — การบล็อกการใช้งานระบบจนกว่าพนักงานจะกดยินยอมตอน onboarding, การบันทึกสถานะยินยอม+timestamp แบบถาวร, **การ anonymize ข้อมูลระบุตัวตนในความยินยอมและ audit log หลังพนักงานลาออกครบ 90 วัน** (NFR ใหม่จากรอบนี้ — ดูหัวข้อ 5.4 หมายเหตุสำคัญ)

### สิ่งที่ไม่ทดสอบ (Out of scope)

รวบรวมจากหัวข้อ "สิ่งที่ไม่ทำ (Out of scope)" ของแต่ละ spec — สิ่งเหล่านี้ไม่มีอยู่ในระบบจึงไม่มีอะไรให้ทดสอบในเฟสนี้:

- ระบบชำระเงินออนไลน์ (ลูกค้ายังจ่ายที่เคาน์เตอร์) — จาก 20260802-001
- ระบบตัดสต็อกอัตโนมัติ — จาก 20260802-001
- ระบบจองโต๊ะ — จาก 20260802-001
- สินค้าขายดี (best seller ranking), การเปรียบเทียบยอดขายระหว่างช่วงเวลา, กราฟแนวโน้มยอดขายละเอียด, การ export รายงาน (CSV/PDF), custom date range picker — จาก 20260802-002
- การ export log เป็นไฟล์, ระบบแจ้งเตือน/alert อัตโนมัติจาก log (anomaly detection), log ระดับ infrastructure/server — จาก 20260802-003
- สิทธิเจ้าของข้อมูลแบบละเอียด (ดู/แก้ไข/ลบข้อมูลด้วยตนเองผ่าน UI, data portability), กระบวนการแจ้งเหตุข้อมูลรั่วไหล (data breach notification) แบบเป็นทางการ, การแต่งตั้ง DPO หรือเอกสารนโยบายความเป็นส่วนตัวฉบับเต็ม, consent จากลูกค้า, การขอ consent ซ้ำเป็นระยะ — จาก 20260802-004
- โครงสร้างหลายสาขา/multi-tenant (ระบบยืนยันว่าโฟกัสสาขาเดียวในรอบนี้) — จาก high-level-architecture.md หัวข้อ 8
- APM/3rd-party monitoring แยกนอกเหนือจาก built-in dashboard ของ Vercel/Supabase (ดูหัวข้อ 5.11)
- CAPTCHA หรือกลไก anti-bot ระดับเข้มงวดสำหรับการสั่ง QR (ดูหัวข้อ 5.8 — ตั้งใจให้หลวมเพื่อรักษา UX)

## 3. กลยุทธ์การทดสอบ & ประเภทการทดสอบ (Test Types)

| ประเภท | ครอบคลุมอะไร | ใช้กับ feature ไหน |
|---|---|---|
| Functional Testing | ทดสอบตาม Acceptance Criteria ของแต่ละ backlog item (business rule ใน spec) | ทุก Must/Should/Could have (20260802-001 ถึง 004) |
| UI/UX Testing | ทดสอบตาม prototype/user journey ที่มีอยู่ใน `02-design/01-prototypes/` | feature ที่มี prototype แล้ว |
| Integration Testing | ทดสอบการส่งต่อข้อมูลข้ามบทบาท เช่น ลูกค้า→บาริสต้า→พนักงานเสิร์ฟ (order flow), ออเดอร์→sales dashboard, ทุก event→audit log, onboarding พนักงาน→consent gate→สิทธิ์การใช้งาน | 20260802-001 (หลัก), 002, 003, 004 |
| Regression Testing | ทดสอบซ้ำหลังแก้ไขบั๊ก/เพิ่มฟีเจอร์ โดยเฉพาะจุดตัดข้ามฟีเจอร์ (เช่น แก้ order flow ต้อง regression audit log ด้วย) | ทุก feature ที่เคย pass แล้ว |
| Non-Functional Testing | ทดสอบตามหัวข้อ 5 ทั้ง 12 ด้าน (performance, availability, backup/DR, privacy offboarding, reliability/data integrity, resilience/offline, RBAC/security, QR abuse prevention, scalability, usability/compatibility, observability, maintainability/portability) | ตามที่ NFR ระบุในแต่ละด้าน |
| Data Integrity / Business Rule Testing | ทดสอบลำดับสถานะออเดอร์ที่ต้องไหลตามลำดับที่กำหนดเท่านั้น, price snapshot ไม่เปลี่ยนย้อนหลัง, ACID compliance ระดับ transaction | 20260802-001 (หลัก), 002 (คำนวณยอดขายจากข้อมูลเดียวกัน) |
| Offline/Resilience Testing | ทดสอบจอบาริสต้าเมื่อเครือข่ายขัดข้อง และการ sync แบบ last-write-wins เมื่อเครือข่ายกลับมา | 20260802-001 (หน้าจอคิวงานบาริสต้า) |
| Access Control / Security Testing | ทดสอบ RBAC ตามบทบาท, session timeout, rate limit การสั่ง QR แบบหลวม | 20260802-002, 003 (RBAC), 20260802-001 (rate limit QR) |
| Retention/Anonymization Testing | ทดสอบการลบ audit log อัตโนมัติเมื่ออายุเกิน 90 วัน และการ anonymize consent record + audit log ของพนักงานที่ลาออกครบ 90 วัน | 20260802-003, 004 |

## 4. Test Environment

- **Staging environment แยกจาก production** — user ยืนยันว่าต้องการมี staging แยกก่อน go-live จริง แนวทางที่แนะนำคือสร้าง Supabase project และ Vercel project ที่สองสำหรับ staging (free tier เช่นเดียวกับ production) เพื่อไม่กระทบข้อมูลร้านจริงระหว่างทดสอบ — **ยังไม่มีการตั้งค่าจริง ณ ขณะนี้ เป็น open item ที่ต้องไปตั้งค่าจริงใน tech-stack.md/infra ต่อไป** (ดูหัวข้อ 6 Risk Management และหัวข้อ 7 Entry Criteria)
- อุปกรณ์ทดสอบ: ฝั่งลูกค้า — เบราว์เซอร์มือถือ (Chrome/Safari 2 เวอร์ชันล่าสุด ตามหัวข้อ 5.10); ฝั่งพนักงาน — แท็บเล็ตเปิดเว็บแอปแบบ fullscreen/kiosk mode (Chrome/Safari 2 เวอร์ชันล่าสุด)
- เครือข่าย: ต้องมีสภาพแวดล้อมทดสอบที่จำลองเครือข่ายขัดข้อง/หลุดชั่วคราว เพื่อทดสอบ resilience ของหน้าจอบาริสต้า (หัวข้อ 5.6)
- Hosting/Backend: Supabase (PostgreSQL, Auth, Realtime) + Vercel (frontend) ตาม [[../../02-design/02-technical/tech-stack|tech-stack.md]] — free tier ทั้ง production และ staging (ตามที่แนะนำ)

## 5. Non-Functional Requirements & เกณฑ์ทดสอบ

NFR ทั้งหมดในหัวข้อนี้ user ยืนยันแล้วในรอบนี้ (2026-08-23) ก่อนหน้านี้ spec/architecture ยังไม่มี field NFR อย่างเป็นทางการ

| # | ด้าน | เกณฑ์ที่ต้องผ่าน |
|---|---|---|
| 5.1 | Performance | Response time ของ action สำคัญ (สั่งออเดอร์, อัปเดตสถานะออเดอร์แบบ real-time) ต้อง **< 3 วินาที** |
| 5.2 | Availability | Best-effort ตาม free-tier ของ Supabase/Vercel เท่านั้น **ไม่มี SLA ทางการ** — ยอมรับความเสี่ยง downtime โดยเจตนาเพื่อคุมต้นทุน ตามการตัดสินใจใน tech-stack.md — ทดสอบเชิงสังเกตการณ์ (monitor downtime จริง) มากกว่าการตั้งเกณฑ์ผ่าน/ไม่ผ่านที่ตายตัว |
| 5.3 | Backup/DR | ข้อมูลออเดอร์/เมนู/ยอดขาย — **ยังไม่ตัดสินใจ** (user ระบุว่ายังไม่ต้องตัดสินใจตอนนี้) default ปัจจุบันคือพึ่ง auto-backup ของ Supabase free tier ไปก่อน — บันทึกเป็น **open risk** ในหัวข้อ 6 ต้องทบทวนอีกครั้งก่อน go-live จริง (แยกจาก audit log retention 90 วัน และ consent record เก็บถาวร ซึ่งตัดสินใจไปแล้ว) |
| 5.4 | Privacy (PDPA) — Offboarding | เมื่อพนักงานลาออก ต้อง anonymize ข้อมูลระบุตัวตนใน consent record และ audit log ที่อ้างอิงถึงพนักงานคนนั้น หลังพ้นระยะเวลา **90 วัน** นับจากวันที่ลาออก (เท่ากับ audit log retention เพื่อความสอดคล้อง) — **หมายเหตุสำคัญ: เป็นการตัดสินใจใหม่จากรอบ NFR นี้ (2026-08-23) ยังไม่มีอยู่ใน spec 20260802-003/20260802-004 หรือ database-schema.md/api-spec.md ปัจจุบัน ต้องมีการอัปเดต spec/design ที่เกี่ยวข้องแยกต่างหากก่อนจึงจะเขียน test case ที่ทดสอบเรื่องนี้ได้จริง** |
| 5.5 | Reliability/Data Integrity | ACID compliance (จากการเลือก relational DB), ลำดับสถานะออเดอร์ต้องไหลตามลำดับที่กำหนดเท่านั้น (รับออเดอร์แล้ว → กำลังทำ → เสร็จแล้ว → เสิร์ฟแล้ว — ห้ามข้ามลำดับหรือย้อนกลับ), price snapshot ต้องไม่เปลี่ยนย้อนหลังแม้ราคาเมนูปัจจุบันเปลี่ยน, conflict resolution ตอน sync กลับจาก offline ใช้ last-write-wins แบบง่าย (grounded ใน high-level-architecture.md หัวข้อ 7-8) |
| 5.6 | Resilience/Offline tolerance | จอบาริสต้าต้องทนทานต่อเครือข่ายขัดข้องได้ **ไม่กำหนดตัวเลข timeout ตายตัว** — ทนได้จนกว่าเครือข่ายกลับมา (การตัดสินใจของ user) แล้ว sync ข้อมูลกลับด้วยกติกา last-write-wins |
| 5.7 | Security — Access control | RBAC ตามบทบาทที่มีอยู่ (ลูกค้าไม่ login, staff ต้อง login+บทบาท), session timeout และ password policy ระดับเบา — ใช้ค่า default ของ Supabase Auth ไม่ตั้งเข้มงวดเพิ่ม |
| 5.8 | Security — QR ordering abuse prevention | Rate limit การสั่งออเดอร์ผ่าน QR ต่อโต๊ะแบบ **หลวม** — จำกัดระดับป้องกัน bot/request รัว ๆ เท่านั้น (เช่น กันยิง request ถี่เกินปกติในเวลาสั้น ๆ) ไม่ต้องเข้มงวดถึงระดับ CAPTCHA เพราะขัดกับ UX ที่ต้องการให้ลูกค้าสั่งง่ายไม่ต้อง login |
| 5.9 | Scalability | คงสมมติฐานเดิมจาก high-level-architecture.md หัวข้อ 8 — รองรับร้านขนาดกลาง **15-30 โต๊ะ**, ผู้ใช้งานพร้อมกันระดับ**หลักสิบคน** — **ยังเป็นสมมติฐานเบื้องต้น ยังไม่ยืนยันจากเจ้าของร้านจริง** ควรตรวจสอบซ้ำก่อนออกแบบเชิงเทคนิคละเอียดเพิ่ม |
| 5.10 | Usability/Compatibility | รองรับเบราว์เซอร์มือถือ/แท็บเล็ตรุ่นปัจจุบันเท่านั้น (Chrome/Safari 2 เวอร์ชันล่าสุด) ไม่ต้องรองรับเบราว์เซอร์รุ่นเก่า |
| 5.11 | Observability | ใช้ monitoring แบบ built-in ของ Vercel/Supabase เท่านั้น (error log, usage dashboard) ไม่ตั้ง APM/3rd-party monitoring แยกในเฟสนี้ |
| 5.12 | Maintainability/Portability | เก็บ schema/business logic ให้เป็น standard PostgreSQL ให้มากที่สุด หลีกเลี่ยงผูกกับ Supabase-specific feature โดยไม่จำเป็น เพื่อลดผลกระทบจาก vendor lock-in ตาม tech-stack.md หัวข้อ 5 |

## 6. Risk Management

| ความเสี่ยง | โอกาสเกิด | ผลกระทบ | แนวทางลด/รับมือ |
|---|---|---|---|
| Order flow หลัก (20260802-001, Must have) ทำงานผิดลำดับสถานะ หรือออเดอร์ไม่เข้าคิวบาริสต้า | กลาง | สูง | Functional + data integrity testing ครอบคลุมทุก transition ของสถานะออเดอร์ (5.5), ทดสอบ end-to-end จริงกับหลายอุปกรณ์พร้อมกัน |
| Real-time sync/แจ้งเตือนล่าช้าเกิน 3 วินาที (20260802-001, Must have) | กลาง | สูง | Performance testing วัด response time ของ action สั่งออเดอร์/อัปเดตสถานะ (5.1) บนเครือข่ายจริงของร้าน |
| เครือข่ายขัดข้องที่จอบาริสต้าทำให้ข้อมูลขัดแย้งกันตอน sync กลับ (offline conflict, 20260802-001) | กลาง | สูง | Offline/Resilience testing จำลองเครือข่ายขาด-กลับ ตรวจสอบผลลัพธ์ last-write-wins ตรงตามกติกา (5.5, 5.6) |
| Free-tier ของ Supabase/Vercel pause หรือ downtime กระทบการใช้งานจริงหน้าร้าน (Availability, ทุก feature) | กลาง | สูง | ยอมรับความเสี่ยงตามที่ user ตัดสินใจ (5.2) — เฝ้าสังเกตการณ์ downtime จริงระหว่าง staging/UAT และแจ้ง user ก่อน go-live |
| **ยังไม่มีแผน Backup/DR ของข้อมูลออเดอร์/เมนู/ยอดขาย (open risk, 5.3)** | สูง (ยังไม่ตัดสินใจ) | สูง (ถ้าข้อมูลหายจะกระทบยอดขาย/ประวัติร้านทั้งหมด) | บันทึกเป็น open risk ต้องทบทวนแผน Backup/DR อีกครั้งก่อน go-live จริง — ระหว่างนี้พึ่ง auto-backup ของ Supabase free tier เป็น default |
| Sales dashboard/Audit log เข้าถึงได้โดยบทบาทที่ไม่มีสิทธิ์ (20260802-002, 003 — RBAC) | ต่ำ-กลาง | สูง (ข้อมูลยอดขาย/log รั่วไหลไปยังบทบาทที่ไม่ควรเห็น) | Access control testing ทดสอบทุก endpoint/หน้าจอกับทุกบทบาทที่ไม่มีสิทธิ์ (5.7) |
| Audit log ไม่ถูกลบอัตโนมัติหลัง 90 วัน (data minimization violation, 20260802-003) | ต่ำ-กลาง | กลาง | Retention testing ทดสอบ scheduled job/trigger การลบ log ที่อายุเกิน 90 วัน |
| Consent gate ไม่บล็อกการใช้งานจริงเมื่อพนักงานไม่กดยินยอม (20260802-004, Could have) | ต่ำ | กลาง | Integration testing ทดสอบทุก entry point ของระบบว่าถูก gate ครบตามบทบาท |
| **NFR offboarding anonymization (5.4) ยังไม่มี spec/design รองรับ** — ทดสอบไม่ได้จนกว่า spec/database-schema/api-spec อัปเดต | สูง (ยังไม่ implement) | กลาง | บันทึกเป็น open item — ต้องอัปเดต spec 20260802-003/004 และ design ที่เกี่ยวข้องก่อน จึงเขียน acceptance-criteria/test-case สำหรับเรื่องนี้ได้ |
| Rate limit QR หลวมเกินไปจนถูก bot ยิง order รัว ๆ กระทบคิวบาริสต้าจริง (20260802-001, 5.8) | ต่ำ-กลาง | กลาง | Security testing ทดสอบเฉพาะกรณี request ถี่ผิดปกติในเวลาสั้น ๆ ตามเกณฑ์หลวมที่กำหนด ไม่ทดสอบระดับ CAPTCHA |
| สมมติฐาน scale 15-30 โต๊ะ/ผู้ใช้พร้อมกันหลักสิบคน ไม่ตรงกับร้านจริง (5.9) | กลาง | กลาง | Load testing เบื้องต้นตามสมมติฐานปัจจุบัน และ flag ให้ตรวจสอบซ้ำกับเจ้าของร้านจริงก่อน go-live |

(Feature ที่จัดลำดับ Must have — 20260802-001 — ได้รับการประเมิน risk เป็นลำดับแรกตามที่กำหนด รองลงมาคือ Should have — 20260802-002, 003 — และ Could have — 20260802-004)

## 7. Entry Criteria

- Spec ของ backlog item นั้นต้องมีสถานะ "ร่าง (Draft)" หรือสูงกว่า และผ่านการตรวจสอบความสอดคล้องกับ backlog/feature-list แล้ว (ตามผลการ audit ล่าสุดใน feature-list.md)
- `acceptance-criteria.md` ของ backlog item นั้นต้องเขียนเสร็จแล้ว (ปัจจุบันยังไม่มีไฟล์นี้ในโปรเจกต์ — ต้องรัน `/acceptance-criteria` ก่อนจึงจะเริ่มแตก test case ได้)
- Test case ราย feature ใน `test-cases/` ต้องถูกออกแบบและ review แล้วก่อนเริ่มทดสอบจริง
- Staging environment ต้องถูกตั้งค่าจริงแล้ว (ปัจจุบันยังไม่มีการตั้งค่า — ดูหัวข้อ 4 และ 6)
- สำหรับ NFR ที่ยังไม่มี spec/design รองรับ (เช่น offboarding anonymization ในหัวข้อ 5.4) ต้องอัปเดต spec/design ที่เกี่ยวข้องให้ครบก่อน จึงจะเข้าเงื่อนไข entry criteria ของหัวข้อนั้นได้

## 8. Exit Criteria

- Test case ทั้งหมดของ backlog item ที่อยู่ใน scope รอบนั้นถูกรันครบและผ่านตามเกณฑ์ที่กำหนดใน acceptance-criteria.md
- ไม่มีบั๊กระดับ Critical/High ค้างอยู่ในทุก feature ที่อยู่ใน scope
- NFR ทั้ง 12 ด้านในหัวข้อ 5 ที่มีเกณฑ์ชัดเจนแล้ว (ยกเว้น 5.3 Backup/DR ที่ยังเป็น open risk โดยเจตนา) ผ่านการทดสอบตามเกณฑ์ที่ระบุ
- Open risk ในหัวข้อ 6 (โดยเฉพาะ Backup/DR และ offboarding anonymization ที่ยังไม่มี spec รองรับ) ได้รับการตัดสินใจ/ทบทวนจาก user อย่างชัดเจนก่อน go-live จริง แม้จะยังไม่บล็อกการทดสอบฟีเจอร์อื่นในรอบนี้
- Staging environment ผ่านการทดสอบ smoke test เบื้องต้นก่อนนำไปใช้ทดสอบจริงเต็มรูปแบบ

## เอกสารที่เกี่ยวข้อง

- [[../../01-requirements/backlog|backlog.md]]
- [[../../01-requirements/feature-list|feature-list.md]]
- [[../../02-design/02-technical/high-level-architecture|high-level-architecture.md]]
- [[../../02-design/02-technical/database-schema|database-schema.md]]
- [[../../02-design/02-technical/api-spec|api-spec.md]]
- [[../../02-design/02-technical/tech-stack|tech-stack.md]]
- [[acceptance-criteria|acceptance-criteria.md]]
- [[test-cases/index|test-cases]]
- [[../02-test-result/index|02-test-result]]

## ประวัติการแก้ไข

- 2026-08-23: สร้างไฟล์ใหม่ทั้งหมด ครอบคลุมทั้ง 4 backlog item (20260802-001 table-qr-ordering, 20260802-002 sales-dashboard, 20260802-003 audit-log, 20260802-004 pdpa-consent) อ้างอิง backlog, feature-list, spec ทั้ง 4 ฉบับ, high-level-architecture.md, database-schema.md, api-spec.md, tech-stack.md กำหนด scope (in/out), ประเภทการทดสอบ, test environment (รวม staging ที่ยังไม่ตั้งค่าจริง), NFR ครบทั้ง 12 ด้านตามที่ user ยืนยันในรอบนี้ (รวม NFR ใหม่เรื่อง PDPA offboarding anonymization ที่ยังไม่มี spec รองรับ), risk management (รวม Backup/DR เป็น open risk โดยเจตนา), และ entry/exit criteria
