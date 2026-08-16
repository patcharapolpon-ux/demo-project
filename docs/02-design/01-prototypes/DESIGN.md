# Design System — my-coffee-store

Design system กลาง สำหรับใช้อ้างอิงตอนออกแบบและพัฒนา UI ของระบบร้านกาแฟ ต่อยอดจาก [[../../01-requirements/feature-list|feature-list.md]] และ user journey ต่าง ๆ ใน [[index|01-prototypes]] เอกสารนี้เป็น living document — แก้ไข/เพิ่มเติมได้เมื่อมี component หรือ pattern ใหม่เกิดขึ้นระหว่างออกแบบหน้าจอจริง

**วันที่สร้าง:** 2026-08-16
**สถานะ:** ร่าง (Draft)

## เอกสารที่เกี่ยวข้อง

- [[20260802-001-table-qr-ordering-journey|User Journey: สั่งกาแฟจากโต๊ะ]] — ต้นทางของสถานะออเดอร์ที่ใช้ในหมวด Status Badge ด้านล่าง
- [[20260802-002-sales-dashboard-journey|User Journey: Sales Dashboard]]
- [[../02-technical/index|02-technical]] — รับช่วงต่อเรื่อง implementation (design token → CSS/component library)

---

## 1. Brand Identity & CI

**ทิศทางแบรนด์:** Clean, modern, slightly playful — เน้นความเป็นร้านกาแฟที่ทันสมัย เข้าถึงง่าย ไม่เป็นทางการจนเกินไป เหมาะกับลูกค้าที่สั่งผ่านมือถือหน้าร้าน

- **โทนเสียง (Tone of voice):** เป็นกันเอง กระชับ ตรงประเด็น หลีกเลี่ยงศัพท์เทคนิคกับลูกค้า (เช่น ใช้ "กำลังทำ" ไม่ใช่ "Processing")
- **แนวทางภาพ (Visual direction):** Food-focused UI — ให้ภาพเมนู/เครื่องดื่มเป็นจุดเด่นของหน้าจอเสมอ ใช้ illustration ประกอบ empty state / onboarding / success state เพื่อความเป็นมิตร ลดความรู้สึกเป็นระบบราชการ
- **คู่สีหลัก:** ส้ม (Primary) คู่กับดำ (Secondary) บนพื้นขาว — ให้ความรู้สึกอบอุ่นแบบกาแฟ/ของกิน แต่ยังคงความคมชัดทันสมัยด้วยดำ-ขาว
- **การใช้โลโก้:** (ยังไม่มีโลโก้อย่างเป็นทางการ ณ วันที่เขียนเอกสารนี้ — เมื่อมีโลโก้แล้วให้เพิ่ม guideline พื้นที่ว่างรอบโลโก้ (clear space), ขนาดต่ำสุด, และพื้นหลังที่ห้ามวางโลโก้ทับไว้ในหมวดนี้)

---

## 2. Design Tokens

### 2.1 Colors

| Token | ค่า | การใช้งาน |
|---|---|---|
| `color-primary` | `#FF6B00` | ปุ่มหลัก (CTA), ลิงก์สำคัญ, highlight, ไอคอนเน้น |
| `color-secondary` | `#000000` | ข้อความเน้น, ปุ่มรอง, พื้นหลัง section เข้ม |
| `color-background` | `#FFFFFF` | พื้นหลังหลักของทุกหน้าจอ |
| `color-text-primary` | `#111111` | หัวข้อ, ข้อความเนื้อหาหลัก |
| `color-text-secondary` | `#666666` | ข้อความรอง, caption, timestamp, placeholder |

**กฎการใช้สี:**
- Primary (`#FF6B00`) ใช้กับ action ที่ต้องการให้ผู้ใช้กดมากที่สุดในหน้าจอเท่านั้น (เช่น "ยืนยันสั่ง") — ห้ามใช้ปุ่ม primary มากกว่า 1 ปุ่มต่อหน้าจอ เพื่อไม่ให้ลด priority ของ action หลัก
- ห้ามใช้ primary/secondary เป็นสีพื้นหลังของข้อความยาว (เช่น ย่อหน้า) เพราะกระทบ contrast/ความอ่านง่าย
- Text secondary (`#666666`) ห้ามใช้กับข้อความที่เป็น action หรือข้อมูลสำคัญ (เช่น ราคา, สถานะออเดอร์) ให้ใช้ text primary แทน

**สีสถานะ (Semantic / status colors)** — เพิ่มเติมจากคู่สีแบรนด์ เพื่อสื่อสารสถานะออเดอร์ตาม [[20260802-001-table-qr-ordering-journey|user journey การสั่งกาแฟ]] และเมนู "หมด" โดยไม่ทับกับความหมายของ primary:

| Token | ค่า | ใช้กับสถานะ |
|---|---|---|
| `color-status-pending` | `#999999` | รับออเดอร์แล้ว |
| `color-status-progress` | `#FF6B00` (primary) | กำลังทำ |
| `color-status-done` | `#2E7D32` | เสร็จแล้ว / เสิร์ฟแล้ว |
| `color-status-unavailable` | `#B00020` | เมนู "หมด" |

> หมายเหตุ: สีสถานะเป็นสีเสริมที่ยังไม่ได้ระบุมาในโจทย์ตั้งต้น กำหนดขึ้นเพื่อให้ครอบคลุม flow สถานะออเดอร์จริงตาม spec — ปรับได้เมื่อมีการทดสอบกับผู้ใช้จริง

### 2.2 Typography

| ระดับ | น้ำหนัก/ลักษณะ | การใช้งาน |
|---|---|---|
| Heading | Bold, large, modern sans-serif | หัวข้อหน้าจอ, ชื่อเมนู, ยอดขายรวมใน dashboard |
| Body | Regular, clean sans-serif | เนื้อหาทั่วไป, รายละเอียดเมนู, label ฟอร์ม |
| Button | Medium weight | ข้อความบนปุ่มทุกชนิด |

**Font family:** ยังไม่ fix ชื่อฟอนต์ — แนะนำเลือก modern sans-serif ที่รองรับภาษาไทยครบ (เช่น Noto Sans Thai, IBM Plex Sans Thai) เพราะเนื้อหาในระบบเป็นภาษาไทยเป็นหลัก และต้องอ่านง่ายบนจอมือถือขนาดเล็ก (ลูกค้าสแกน QR จากมือถือ)

**Scale แนะนำ** (อิงจาก Spacing scale ด้านล่างเพื่อความสอดคล้อง):

| ระดับ | ขนาด | น้ำหนัก | ตัวอย่างการใช้งาน |
|---|---|---|---|
| H1 | 28px | Bold | ชื่อหน้า/ยอดขายรวมใน dashboard |
| H2 | 22px | Bold | หัวข้อ section, ชื่อหมวดเมนู |
| H3 | 18px | Bold | ชื่อเมนูในการ์ด |
| Body | 16px | Regular | เนื้อหาทั่วไป |
| Caption | 14px | Regular | timestamp, หมายเหตุ, text secondary |
| Button | 16px | Medium | ข้อความบนปุ่ม |

### 2.3 Spacing

| Token | ค่า | ใช้เมื่อ |
|---|---|---|
| `space-small` | 8px | ระยะห่างภายในองค์ประกอบเดียวกัน (เช่น icon กับ label, ระหว่างบรรทัด) |
| `space-medium` | 16px | ระยะห่างระหว่างองค์ประกอบย่อยในการ์ด/ฟอร์มเดียวกัน, padding ภายในปุ่ม |
| `space-large` | 24px | ระยะห่างระหว่าง section, margin รอบการ์ด |

กฎ: ใช้เฉพาะ 3 ค่านี้เป็นฐาน (multiples ของ 8px) เพื่อรักษาความสม่ำเสมอของ layout ทั้งระบบ หลีกเลี่ยงการใส่ค่าตัวเลขที่ไม่อยู่ใน scale (เช่น 10px, 20px) ยกเว้นกรณีจำเป็นจริง ๆ เช่น 1px border

### 2.4 Border Radius

| Token | ค่า | ใช้กับ |
|---|---|---|
| `radius-button` | 12px | ปุ่มทุกชนิด |
| `radius-card` | 16px | การ์ดเมนู, การ์ดสรุปยอดขาย, modal, bottom sheet |

### 2.5 Elevation / Shadow

| Token | ค่า (ตัวอย่าง) | ใช้กับ |
|---|---|---|
| `shadow-card` | `0 2px 8px rgba(0,0,0,0.08)` (soft shadow) | การ์ด — ให้ความรู้สึกลอยเบา ๆ ไม่หนักจนดูเป็นกล่องทึบ |

---

## 3. UI Components & Patterns

### 3.1 Button

- **Primary button:** พื้นหลัง `color-primary` (#FF6B00), ข้อความสีขาว, มุมโค้ง `radius-button` (12px), น้ำหนักตัวอักษรระดับ Button (Medium)
  - ใช้กับ action หลักของหน้าจอเท่านั้น (เช่น "ยืนยันสั่ง", "บันทึก")
  - Disabled state: ลด opacity ปุ่มลง (เช่น 40%) และห้ามใช้สีเทาแทน primary เพื่อคงเอกลักษณ์สี
- **Secondary button:** ขอบ (outline) สี `color-secondary` หรือ `color-primary`, พื้นหลังโปร่งใส/ขาว, ข้อความสีเดียวกับขอบ — ใช้กับ action รอง เช่น "ยกเลิก", "แก้ไข"
- **ขนาดพื้นที่กดขั้นต่ำ:** 44x44px (touch target) เพราะลูกค้าส่วนใหญ่ใช้งานผ่านมือถือหน้าจอเล็ก

### 3.2 Card

- พื้นหลังสีขาว (`color-background`), มุมโค้ง `radius-card` (16px), เงานุ่ม (`shadow-card`)
- Padding ภายใน: `space-medium` (16px) เป็นค่าเริ่มต้น, การ์ดที่มีเนื้อหาเยอะ (เช่นการ์ดสรุปยอดขายใน dashboard) ใช้ `space-large` (24px)
- ใช้สำหรับ: การ์ดเมนู (รูป + ชื่อ + ราคา + ปุ่มเพิ่ม), การ์ด KPI ใน dashboard, การ์ดสรุปออเดอร์

### 3.3 Status Badge

ใช้สื่อสารสถานะออเดอร์แบบ real-time ตาม [[20260802-001-table-qr-ordering-journey|user journey การสั่งกาแฟ]] — pill shape, มุมโค้งเต็ม (fully rounded), พื้นหลังสีอ่อนของสีสถานะ (opacity ~15%) + ข้อความ/จุดนำหน้าเป็นสีสถานะเข้ม เพื่อคง contrast และอ่านง่ายบนพื้นขาว

| สถานะ | สีอ้างอิง |
|---|---|
| รับออเดอร์แล้ว | `color-status-pending` |
| กำลังทำ | `color-status-progress` |
| เสร็จแล้ว / เสิร์ฟแล้ว | `color-status-done` |
| หมด | `color-status-unavailable` |

### 3.4 Form Input

- ขอบบาง (1px) สีเทาอ่อน, มุมโค้ง 8px (เล็กกว่าปุ่ม/การ์ดเพื่อแยกลำดับชั้นภาพ), padding แนวตั้ง/นอน ตาม `space-small`/`space-medium`
- Placeholder ใช้ `color-text-secondary`, ข้อความที่พิมพ์แล้วใช้ `color-text-primary`
- Focus state: ขอบเปลี่ยนเป็น `color-primary`

### 3.5 Illustration & Empty State

- ใช้ illustration แนว playful ประกอบหน้าที่ยังไม่มีข้อมูล (เช่น ยังไม่มีออเดอร์, ยังไม่มียอดขายในช่วงที่เลือก) แทนข้อความเปล่า ๆ เพื่อรักษาโทน "Clean, modern, slightly playful" ตามที่กำหนดในหมวด Style

---

## 4. UX Guidelines & Rules

- **Mobile-first เสมอ:** ลูกค้าสั่งอาหารผ่านการสแกน QR จากมือถือเป็นหลัก ทุก component/pattern ต้องออกแบบและทดสอบบนหน้าจอมือถือก่อน แล้วค่อยขยายไปจอที่ใหญ่กว่า (เช่นจอ dashboard ของผู้จัดการร้าน)
- **Real-time status ต้องเห็นได้ทันที:** ตาม business rule ใน spec การสั่งกาแฟ ห้ามซ่อนสถานะออเดอร์ไว้ในหน้าที่ต้องกดเข้าไปดูเพิ่ม ต้องแสดงบน primary view เสมอ
- **Contrast ต้องผ่านเกณฑ์อ่านง่าย:** คู่สีข้อความบนพื้นหลัง (โดยเฉพาะ text บน primary orange) ต้องตรวจสอบ contrast ratio ให้อ่านง่ายในที่แสงจ้า (หน้าร้าน/กลางแจ้ง) — แนะนำอย่างน้อย WCAG AA (4.5:1 สำหรับ body text)
- **จำกัดจำนวน primary action ต่อหน้าจอ:** 1 ปุ่ม primary ต่อหน้าจอ เพื่อไม่ให้ผู้ใช้สับสนว่าต้องกดอะไรก่อน (สอดคล้องกับกฎการใช้สีในหมวด Colors)
- **ภาษา:** UI ที่ลูกค้า/พนักงานเห็นใช้ภาษาไทยเป็นหลัก หลีกเลี่ยงศัพท์เทคนิคภาษาอังกฤษที่ไม่จำเป็น (เช่น แสดง "กำลังทำ" ไม่ใช่ "In Progress") ยกเว้นคำที่ผู้ใช้งานคุ้นเคยอยู่แล้ว (เช่น "Dashboard")
- **Consistency กับ spacing/radius scale:** ห้ามใช้ค่า spacing/border-radius นอกเหนือจาก token ที่กำหนดไว้ในหมวด Design Tokens เพื่อรักษาความสม่ำเสมอเมื่อระบบขยายหน้าจอเพิ่มในอนาคต

---

## ประวัติการแก้ไข

- 2026-08-16: สร้างเอกสารฉบับแรก กำหนด color palette, typography, spacing, border radius ตามที่ user ระบุ พร้อมขยายเป็น component patterns (Button, Card, Status Badge, Form Input) และ UX guideline โดยอ้างอิงสถานะออเดอร์จริงจาก [[20260802-001-table-qr-ordering-journey|user journey การสั่งกาแฟ]]
