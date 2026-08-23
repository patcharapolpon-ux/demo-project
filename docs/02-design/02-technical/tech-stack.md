# Tech Stack

**วันที่จัดทำ/อัปเดตล่าสุด:** 2026-08-23
**สถานะ:** ร่าง (Draft)

> เอกสารนี้ระบุเทคโนโลยี/เฟรมเวิร์ก/ฐานข้อมูล/บริการ hosting จริงที่เลือกใช้สำหรับโปรเจกต์นี้โดยเจตนา (ต่างจาก [[high-level-architecture|high-level-architecture.md]], [[database-schema|database-schema.md]] และ [[api-spec|api-spec.md]] ซึ่งเป็นเอกสารเชิงแนวคิดที่จงใจไม่ผูกมัดกับเทคโนโลยี) เอกสารนี้คือผลของการนำเกณฑ์การเลือก (ทีม/งบ/เวลา/platform/NFR) มาจับคู่กับองค์ประกอบ/เอนทิตี/operation ที่ออกแบบไว้แล้ว

## 1. เกณฑ์การเลือก (Selection Criteria)

| มิติ | รายละเอียดที่ user ให้ไว้ |
|---|---|
| ความเชี่ยวชาญของทีม | ทีมยังไม่ถนัดภาษา/เทคโนโลยีใดเป็นพิเศษ ("ยังไม่ถนัดภาษาใดเป็นพิเศษ") — เลือกได้อิสระตามความเหมาะสม เน้นสิ่งที่เรียนรู้ง่ายที่สุด |
| งบประมาณ/ต้นทุน hosting | ต้องการ free-tier/ต้นทุนต่ำที่สุด (ยืนยันชัดเจนจาก user) |
| กรอบเวลา | ไม่เร่ง เน้นความถูกต้อง/คุณภาพของสถาปัตยกรรมมากกว่าความเร็ว (ยืนยันจาก user) |
| Platform เป้าหมาย | ฝั่งลูกค้า: web ผ่านเบราว์เซอร์เท่านั้น ไม่ต้องติดตั้งแอป (ยืนยันไว้ใน high-level-architecture.md หัวข้อ 3). ฝั่งพนักงาน (บาริสต้า/พนักงานเสิร์ฟ/ผู้บริหารร้าน): หน้าจอ POS/จอครัวเฉพาะทาง — ตีความเป็นเว็บแอปตัวเดียวกันเปิดแบบ fullscreen/kiosk mode บนแท็บเล็ตที่ติดตั้งประจำจุด ไม่ใช่ native app แยก (เพื่อให้สอดคล้องกับงบ free-tier และทีมไม่มีพื้นฐาน IT) |
| ลักษณะข้อมูล | Relational — **user มอบหมายให้ผู้สัมภาษณ์ตัดสินใจแทนเนื่องจากไม่มีความรู้ด้าน IT** ("ไม่มีความรู้ตัดสินใจให้รองรับกับคนไม่มีประสบการณ์การใช้ it") เหตุผล: database-schema.md ออกแบบเป็น FK เชื่อมกันแน่น (ออเดอร์-โต๊ะ-เมนู-ผู้ใช้/พนักงาน-บันทึกเหตุการณ์-ความยินยอม) และมี business rule ที่ต้องรักษาความถูกต้องเข้มงวด (สถานะออเดอร์ต้องไหลตามลำดับที่กำหนดเท่านั้น, price snapshot ต้องไม่เปลี่ยนย้อนหลัง, การคำนวณยอดขายต้อง join ข้อมูลแม่นยำ) — ACID/relational ตอบโจทย์ตรงที่สุด |
| ความต้องการ real-time | ลูกค้าต้องเห็นสถานะออเดอร์แบบ real-time ต่อเนื่อง และพนักงานเสิร์ฟต้องได้รับแจ้งเตือนอัตโนมัติทันที (ยืนยันจาก high-level-architecture.md/api-spec.md หัวข้อ 2.1 "ติดตามสถานะออเดอร์แบบต่อเนื่อง") |
| ความคุ้นเคยด้าน DevOps | Managed/PaaS (ไม่ต้องดูแล server เอง) — **user มอบหมายให้ผู้สัมภาษณ์ตัดสินใจแทนเนื่องจากไม่มีความรู้ด้าน IT** ("ตัดสินใจให้หน่อย ไม่มีความรู้ด้าน it") เหตุผล: งบ free-tier + ทีมไม่มีความรู้ด้าน IT + สเกลระบบเล็ก (single-branch, 15-30 โต๊ะ, ผู้ใช้พร้อมกันหลักสิบคน ตาม high-level-architecture.md หัวข้อ 8) ทำให้การดูแล infrastructure เองไม่จำเป็นและเพิ่มความเสี่ยง |
| NFR ที่เกี่ยวข้อง | ยังไม่มี `test-plan.md` ในโปรเจกต์นี้ให้ NFR อย่างเป็นทางการ (มีแค่ index.md ใน `03-testing/01-test-plan/`) แนะนำให้รัน `/test-plan` ในอนาคตเพื่อกำหนด NFR อย่างเป็นทางการ — ระหว่างนี้ใช้ข้อจำกัด/สมมติฐานจาก [[high-level-architecture|high-level-architecture.md]] หัวข้อ 8 แทน (single-branch, 15-30 โต๊ะ, ผู้ใช้พร้อมกันหลักสิบคน, offline-first บางส่วน, real-time ต่อเนื่อง) |

## 2. แนวทางที่พิจารณา (Options Considered)

### 2.1 JS/TS + Supabase + Vercel (แนะนำ)

- Client: Next.js (React) — หน้าเดียวกันทั้งฝั่งลูกค้าและจอ POS พนักงาน (เปิด fullscreen บนแท็บเล็ต)
- Backend + Database: Supabase (PostgreSQL relational แบบ managed, free tier, มี realtime subscription ในตัวสำหรับ sync สถานะออเดอร์แบบต่อเนื่อง, มี auth ในตัวสำหรับบัญชีพนักงาน)
- Hosting: Vercel (frontend, free tier)
- **ข้อดี:** เรียนภาษาเดียว (JS/TS) จบทั้ง stack เหมาะกับทีมไม่มีพื้นฐาน IT, ไม่ต้องดูแล server เลย (fully managed), free tier ครอบคลุมสเกล 15-30 โต๊ะได้สบาย, tutorial/ตัวอย่างเยอะมาก, relational DB ตรงกับกฎธุรกิจที่ต้องรักษาความถูกต้อง, realtime subscription ตรงกับ operation "ติดตามสถานะออเดอร์แบบต่อเนื่อง" ใน api-spec.md โดยตรง
- **ข้อเสีย:** ผูกกับ vendor (Supabase/Vercel) หากอนาคตต้องย้าย host จะต้อง migrate, ต้องเขียน client-side caching เพิ่มเองสำหรับ offline-first ที่ high-level-architecture.md กำหนดไว้ (Supabase ไม่ auto-handle offline-first ให้เต็มรูปแบบ)
- **สถานะ: เลือก ✅**

### 2.2 JS/TS เขียน backend เอง (Node.js + Express) + Railway/Render

- Client: React (Vite) SPA
- Backend: Node.js + Express เขียนเองทั้งหมด
- Database: PostgreSQL บน Railway/Render free tier
- Realtime: Socket.io เขียนเอง
- **ข้อดี:** ควบคุม business logic ได้เต็มที่กว่า ไม่ผูก vendor SDK เท่าแนวทาง 2.1
- **ข้อเสีย:** ทีมต้องเขียน backend/realtime/auth เองทั้งหมด ใช้เวลา/ความรู้มากกว่าแนวทาง 2.1 ชัดเจน ไม่เหมาะกับทีมไม่มีพื้นฐาน IT
- **สถานะ: ไม่เลือก ❌**

### 2.3 แยก service เต็มรูปแบบ + Container (NestJS + Docker + VPS)

- Client: Next.js
- Backend: NestJS แยกเป็น service ตาม domain
- Database: PostgreSQL บน Docker ดูแลเอง
- Hosting: VPS/cloud ผ่าน container
- **ข้อดี:** รองรับการเติบโต/หลายสาขาในอนาคตได้ดีที่สุด ควบคุม infra ได้เต็มที่
- **ข้อเสีย:** ซับซ้อนสูงสุด ต้องมีความรู้ DevOps/container ที่ทีมยืนยันว่าไม่มี ไม่จำเป็นสำหรับสเกลปัจจุบัน (single-branch, 15-30 โต๊ะ) เกินความจำเป็นเทียบกับ requirement ที่มีอยู่
- **สถานะ: ไม่เลือก ❌**

## 3. Stack ที่เลือก (Selected Stack)

| Layer | เทคโนโลยีที่เลือก | เหตุผล (อ้างอิงเกณฑ์ในหัวข้อ 1) |
|---|---|---|
| Client / Frontend | Next.js (React) — ใช้หน้าเดียวกันทั้งฝั่งลูกค้า (web browser มือถือ) และจอ POS พนักงาน (เปิด fullscreen/kiosk mode บนแท็บเล็ต) | ตอบโจทย์ platform เป้าหมายทั้ง web ลูกค้าและจอ POS พนักงานด้วย codebase เดียว, JS/TS เรียนรู้ง่าย เหมาะกับทีมไม่มีพื้นฐาน IT |
| Backend / API | Supabase (auto-generated API + Realtime subscription + Auth) | ไม่ต้องเขียน backend เอง ลดภาระทีมที่ไม่มีพื้นฐาน IT, Realtime subscription ตอบโจทย์ requirement "ติดตามสถานะออเดอร์แบบต่อเนื่อง" (api-spec.md หัวข้อ 2.1) โดยตรง, Auth ในตัวรองรับบัญชีพนักงาน/บทบาท |
| Database | Supabase — PostgreSQL (managed, relational) | ตรงกับลักษณะข้อมูล relational ที่มอบหมายให้ตัดสินใจแทน (FK เชื่อมกันแน่น + business rule ที่ต้องรักษาความถูกต้องเข้มงวดตาม database-schema.md), ACID compliance รองรับกฎลำดับสถานะออเดอร์และ price snapshot |
| Infrastructure / Hosting | Vercel (frontend, free tier) + Supabase platform (backend+db, free tier) | ตอบโจทย์งบ free-tier/ต้นทุนต่ำที่สุด และ managed/PaaS ที่มอบหมายให้ตัดสินใจแทน (ไม่ต้องดูแล server เอง เหมาะกับทีมไม่มีความรู้ด้าน IT และสเกลระบบเล็กตาม high-level-architecture.md หัวข้อ 8) |
| DevOps / CI-CD | Vercel auto-deploy จาก git push (ไม่มี pipeline แยกเพิ่มเติมที่ user ระบุมา) | สอดคล้องกับความคุ้นเคยด้าน DevOps ระดับ managed/PaaS — ไม่ต้องตั้งค่า CI/CD เองตั้งแต่ต้น |
| Third-party Integration | ไม่มี | high-level-architecture.md หัวข้อ 1 และ 3 ยืนยันว่าไม่มีการเชื่อมต่อระบบภายนอก (เช่น payment) ในเฟสนี้ |

## 4. Mapping กับเอกสารเชิงแนวคิด (Traceability)

### 4.1 องค์ประกอบ → เทคโนโลยี (จาก high-level-architecture.md)

| องค์ประกอบเชิงแนวคิด | เทคโนโลยีที่ implement จริง |
|---|---|
| หน้าจอสั่งอาหารของลูกค้า | Next.js (React) page |
| หน้าจอคิวงานบาริสต้า | Next.js (React) page — เปิด fullscreen/kiosk mode บนแท็บเล็ตประจำจุด |
| หน้าจอแจ้งเตือนพนักงานเสิร์ฟ | Next.js (React) page รับข้อมูลผ่าน Supabase Realtime subscription |
| หน้าจัดการเมนูของผู้บริหารร้าน | Next.js (React) page |
| หน้า Sales Dashboard | Next.js (React) page |
| หน้าดู Audit Log | Next.js (React) page |
| หน้าจอ Consent Onboarding | Next.js (React) page |
| การจัดการออเดอร์และคิวบาริสต้า | Supabase (PostgreSQL + auto-generated API) |
| การแจ้งเตือน/ซิงก์สถานะแบบ real-time | Supabase Realtime subscription |
| การจัดการเมนู | Supabase (PostgreSQL + auto-generated API) |
| การคำนวณสรุปยอดขาย | Supabase (PostgreSQL query ผ่าน auto-generated API) |
| การควบคุมสิทธิ์การเข้าถึงตามบทบาท (Access Control) | Supabase Auth (บัญชีพนักงาน + บทบาท) — กลไกตรวจสอบสิทธิ์ระดับ policy/application ยังไม่ระบุ ดูหัวข้อ 6 |
| การบันทึกเหตุการณ์ (Audit Logging) | Supabase (PostgreSQL + auto-generated API) |
| การจัดการความยินยอม (Consent Gate) | Supabase (PostgreSQL + auto-generated API) |
| คลังข้อมูลออเดอร์และเมนู | Supabase PostgreSQL |
| คลังข้อมูลผู้ใช้และบทบาท | Supabase PostgreSQL + Supabase Auth |
| คลังข้อมูล Audit Log | Supabase PostgreSQL |
| คลังข้อมูลความยินยอม (Consent) | Supabase PostgreSQL |
| QR Code ประจำโต๊ะ | ไม่ใช่องค์ประกอบซอฟต์แวร์ (physical trigger) — เครื่องมือ/ไลบรารีที่ใช้สร้าง QR code ยังไม่ระบุ ดูหัวข้อ 6 |
| อุปกรณ์ปลายทางของผู้ใช้งาน (มือถือ/แท็บเล็ต) | เว็บเบราว์เซอร์ทั่วไปบนอุปกรณ์ของลูกค้า/พนักงาน (ไม่ต้องติดตั้งแอป) |

### 4.2 เอนทิตี → ฐานข้อมูลจริง (จาก database-schema.md)

| เอนทิตี | จัดเก็บด้วย |
|---|---|
| ออเดอร์ | Supabase PostgreSQL (table) |
| รายการสินค้าในออเดอร์ | Supabase PostgreSQL (table) |
| โต๊ะ | Supabase PostgreSQL (table) |
| เมนู | Supabase PostgreSQL (table) |
| ผู้ใช้/พนักงาน | Supabase PostgreSQL (table) + Supabase Auth |
| บันทึกเหตุการณ์ (Audit Log Record) | Supabase PostgreSQL (table) |
| ความยินยอม (Consent Record) | Supabase PostgreSQL (table) |

> หมายเหตุ: ชื่อ table จริง, naming convention และการใช้ ORM เฉพาะเจาะจง (ถ้ามี) ยังไม่ถูกกำหนด — ดูหัวข้อ 6

### 4.3 กลุ่ม Operation → Protocol/Framework จริง (จาก api-spec.md)

| กลุ่ม Operation | Protocol/Framework ที่ใช้ implement |
|---|---|
| ออเดอร์ — สั่งออเดอร์ / ดูคิวออเดอร์สำหรับบาริสต้า / เปลี่ยนสถานะออเดอร์ / ดูสรุปยอดขายตามช่วงเวลา | Supabase auto-generated API (REST ผ่าน PostgREST) เรียกผ่าน Supabase client library จาก Next.js |
| ออเดอร์ — ติดตามสถานะออเดอร์แบบต่อเนื่อง (Subscription เชิงแนวคิด) | Supabase Realtime subscription (WebSocket-based) |
| โต๊ะ — เปิดหน้าสั่งอาหารของโต๊ะจากการสแกน QR | Supabase auto-generated API (REST ผ่าน PostgREST) |
| เมนู — ดูรายการเมนูปัจจุบัน / เปิด-ปิดเมนูชั่วคราว | Supabase auto-generated API (REST ผ่าน PostgREST) |
| ผู้ใช้/พนักงาน — ตรวจสอบสิทธิ์การเข้าถึงตามบทบาท | Supabase Auth ร่วมกับ Supabase API — กลไกตรวจสอบระดับ policy (เช่น Row Level Security) หรือ application-level ยังไม่ระบุ ดูหัวข้อ 6 |
| บันทึกเหตุการณ์ (Audit Log Record) — บันทึกเหตุการณ์ / ดู Audit Log ย้อนหลัง / ลบบันทึกเหตุการณ์ที่เกินอายุอัตโนมัติ | Supabase auto-generated API (REST ผ่าน PostgREST); กลไกการดักจับเหตุการณ์อัตโนมัติและการลบตามอายุยังไม่ระบุเครื่องมือเฉพาะ (เช่น database trigger/scheduled job) ดูหัวข้อ 6 |
| ความยินยอม (Consent Record) — ขอความยินยอม PDPA ตอน onboarding / ตรวจสอบสถานะยินยอมก่อนอนุญาตใช้งาน | Supabase auto-generated API (REST ผ่าน PostgREST) |

## 5. ความเสี่ยงและข้อจำกัดของ Stack ที่เลือก (Risks & Limitations)

- **Vendor lock-in:** ผูกกับ Supabase/Vercel โดยตรง หากอนาคตต้องย้าย host หรือสเกลใหญ่ขึ้นมากจะต้อง migrate ฐานข้อมูล/auth/realtime ทั้งหมด (ระบุไว้ในแนวทาง 2.1 ที่เลือก) — แนวทางบรรเทา: เก็บ schema/business logic ให้เป็น standard PostgreSQL ให้มากที่สุด หลีกเลี่ยงการผูกกับ Supabase-specific feature โดยไม่จำเป็น
- **ข้อจำกัดของ free tier:** Supabase/Vercel free tier มีเพดานการใช้งาน (เช่น จำนวน connection, storage, การ pause โปรเจกต์เมื่อไม่มีการใช้งานนาน ๆ) ซึ่งอาจกระทบความพร้อมใช้งาน (availability) ถ้าระบบโตเกินสเกลปัจจุบัน (15-30 โต๊ะ) — แนวทางบรรเทา: ติดตามการใช้งานเป็นระยะ และเตรียมอัปเกรดเป็น paid tier เมื่อจำเป็น
- **Offline-first ไม่ได้ auto-handle:** high-level-architecture.md หัวข้อ 7-8 กำหนดให้บาริสต้าต้องทำงานต่อได้ชั่วคราวเมื่อเครือข่ายขัดข้อง แต่ Supabase ไม่มีกลไก offline-first ให้ในตัวแบบเต็มรูปแบบ ทีมต้องเขียน client-side caching/sync เพิ่มเอง — ยังไม่มีรายละเอียดเครื่องมือ/ไลบรารีที่จะใช้ (ดูหัวข้อ 6)
- **Real-time ขึ้นกับคุณภาพเครือข่ายหน้าร้าน:** การแจ้งเตือน/สถานะ real-time ผ่าน Supabase Realtime ต้องพึ่งพา WiFi/เครือข่ายที่ร้านติดตั้งไว้ หากเครือข่ายไม่เสถียรอาจกระทบ user experience ของทั้งลูกค้าและพนักงาน
- **Learning curve เบื้องต้น:** แม้ Next.js/Supabase เรียนรู้ง่ายกว่าแนวทางอื่นที่พิจารณา แต่ทีมยังต้องใช้เวลาทำความเข้าใจ React/Next.js และแนวคิดของ Supabase (auth, realtime, RLS ถ้าเลือกใช้) ตั้งแต่ต้น เนื่องจากทีมยังไม่ถนัดภาษา/เทคโนโลยีใดเป็นพิเศษ

## 6. ประเด็นที่ยังไม่ชัดเจน / รอการตัดสินใจ (Open Questions)

- ยังไม่ระบุ ORM หรือวิธีเรียกข้อมูลเฉพาะ (เช่น ใช้ Supabase JS client เรียกตรง, หรือใช้ ORM เพิ่ม เช่น Prisma/Drizzle ครอบอีกชั้น) — caller ไม่ได้ระบุมา
- ยังไม่ระบุ CI/CD pipeline เพิ่มเติมนอกจาก Vercel auto-deploy จาก git push (เช่น automated test pipeline, staging environment แยกจาก production) — caller ไม่ได้ระบุมา
- ยังไม่ระบุรายละเอียดการตั้งค่า kiosk mode ของแท็บเล็ตพนักงานแบบละเอียด (เช่น รุ่น/OS ของแท็บเล็ต, วิธี lock browser เป็น fullscreen kiosk mode) — ระบุไว้เพียงระดับแนวทาง stack ว่าเป็นเว็บแอปเดียวกันเปิด fullscreen บนแท็บเล็ต
- ยังไม่ระบุกลไกตรวจสอบสิทธิ์การเข้าถึงตามบทบาทที่เป็นรูปธรรม (เช่น ใช้ Supabase Row Level Security policy หรือตรวจสอบที่ชั้น application/Next.js) — caller ระบุเพียงว่า Supabase มี auth ในตัว
- ยังไม่ระบุเครื่องมือ/กลไกสำหรับดักจับเหตุการณ์เพื่อบันทึก audit log โดยอัตโนมัติ และการลบ log ที่เกินอายุ 90 วันโดยอัตโนมัติ (เช่น database trigger, scheduled function) — caller ไม่ได้ระบุมา
- ยังไม่ระบุเครื่องมือ/ไลบรารีสำหรับ client-side caching และ sync เพื่อรองรับ offline-first บางส่วนของหน้าจอบาริสต้า — caller ยืนยันว่า Supabase ไม่ auto-handle ให้ แต่ยังไม่ระบุวิธีแก้ที่เจาะจง
- ยังไม่ระบุเครื่องมือ/ไลบรารีสำหรับสร้าง QR code ประจำโต๊ะ — caller ไม่ได้ระบุมา
- ยังไม่มี `docs/03-testing/01-test-plan/test-plan.md` ให้ NFR อย่างเป็นทางการ แนะนำให้รัน `/test-plan` เพื่อรวบรวม NFR (performance, security, availability) อย่างเป็นทางการ แล้วนำมาตรวจสอบ/ปรับ stack นี้อีกครั้งหากจำเป็น

## เอกสารที่เกี่ยวข้อง

- [[../../01-requirements/backlog|backlog.md]]
- [[../../01-requirements/feature-list|feature-list.md]]
- [[high-level-architecture|high-level-architecture.md]]
- [[database-schema|database-schema.md]]
- [[api-spec|api-spec.md]]
- [[index|02-technical]]
- [[../../03-testing/01-test-plan/test-plan|test-plan.md]]

## ประวัติการแก้ไข

- 2026-08-23: สร้างไฟล์ใหม่ทั้งหมด หลังการสัมภาษณ์ user เรื่องความเชี่ยวชาญของทีม, งบประมาณ, กรอบเวลา, platform เป้าหมาย, ลักษณะข้อมูล (มอบหมายให้ผู้สัมภาษณ์ตัดสินใจแทน), ความคุ้นเคยด้าน DevOps (มอบหมายให้ผู้สัมภาษณ์ตัดสินใจแทน) เสนอ 3 แนวทาง (JS/TS+Supabase+Vercel, JS/TS+Node/Express เขียนเอง, NestJS+Docker+VPS) — user เลือกแนวทาง JS/TS + Supabase + Vercel ระบุ stack ครบทุก layer ยกเว้นรายละเอียดปลีกย่อย (ORM เฉพาะ, CI/CD pipeline เพิ่มเติม, kiosk mode configuration, กลไก RLS/audit trigger, offline sync library, QR generator) ที่บันทึกไว้ในหัวข้อ 6 เนื่องจาก caller ไม่ได้ระบุมา
