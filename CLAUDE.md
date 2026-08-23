# CLAUDE.md

ไฟล์นี้ให้คำแนะนำแก่ Claude Code (claude.ai/code) สำหรับการทำงานกับโค้ดในโปรเจกต์นี้

## โปรเจกต์นี้คืออะไร

ตอนนี้โปรเจกต์นี้เป็น **Obsidian documentation vault** ไม่ใช่ codebase ของแอปพลิเคชัน ยังไม่มี `package.json` ไม่มีซอร์สโค้ด และไม่มีเครื่องมือ build/lint/test — เนื้อหาทั้งหมดเป็นไฟล์ Markdown อยู่ใน `docs/` ส่วน `.obsidian/` เก็บการตั้งค่าแอป Obsidian (workspace layout, core plugins ที่เปิดใช้งาน) ซึ่งไม่ใช่ส่วนหนึ่งของเนื้อหาโปรเจกต์

ชื่อ vault ("my-coffee-store" / โปรเจกต์บัณฑิตพันธุ์ใหม่) บ่งบอกว่านี่คือพื้นที่วางแผนและจัดทำเอกสารสำหรับโปรเจกต์แอปพลิเคชันร้านกาแฟ ซึ่งยังไม่มีการ commit โค้ดจริง เมื่อมีการเพิ่มโค้ดแอปพลิเคชันเข้ามาในภายหลัง ควรอัปเดตไฟล์นี้ให้มีคำสั่ง build/lint/test และสถาปัตยกรรมโค้ดที่แท้จริง

## โครงสร้างเอกสาร

`docs/` จัดเรียงเป็นไปป์ไลน์ของโปรเจกต์ตามลำดับ โดยตั้งเลขนำหน้าตามลำดับที่งานไหลผ่าน แต่ละโฟลเดอร์มี `index.md` อธิบายจุดประสงค์ของตัวเอง และลิงก์ (ผ่าน syntax `[[wikilink]]` ของ Obsidian) ไปยังโฟลเดอร์ต้นน้ำ/ปลายน้ำ:

- `01-requirements/` — ต้นทาง (source of truth) ของความต้องการของโปรเจกต์
  - `backlog.md` — living document รวมรายการ requirement ทั้งหมดเรียงตามลำดับที่สร้าง อัปเดตทุกครั้งที่มี spec ใหม่
  - `feature-list.md` — living document สรุป backlog เป็นฟีเจอร์ พร้อมจัดลำดับความสำคัญแบบ MoSCoW
  - `01-spec/` — feature requirements, user stories, business rules, ขอบเขตงาน
  - `02-plan/` — roadmap, phase/milestone, ลำดับความสำคัญ (แตกมาจาก spec)
  - `03-task/` — งานย่อยพร้อมสถานะ/ผู้รับผิดชอบ/deadline (แตกมาจาก plan)
- `02-design/` — การออกแบบเพื่อทำให้ความต้องการเป็นจริง
  - `01-prototypes/` — UI/UX wireframe, mockup, user flow, design system เบื้องต้น
  - `02-technical/` — architecture, database schema, API design, การเลือกเทคโนโลยี/ไลบรารี
- `03-testing/` — การตรวจสอบ design/implementation
  - `01-test-plan/` — test case, test data, ขอบเขตการทดสอบ (แตกมาจาก spec + design)
  - `02-test-result/` — ผล pass/fail จริง และบั๊กที่พบ
- `04-retrospectives/` — บทเรียนที่ได้หลังจบแต่ละ phase/sprint/milestone อ้างอิงจากผลทดสอบและ log
- `05-log/` — changelog และ decision log แบบเรียงตามลำดับเวลา
- `00-archived/` — เอกสารที่เลิกใช้แล้ว/ถูกแทนที่; **ห้ามลบเอกสารทิ้ง ให้ย้ายมาไว้ที่นี่แทน** เพื่อรักษาประวัติการตัดสินใจ

## แนวทางการทำงาน

- เนื้อหาเอกสารเขียนเป็น **ภาษาไทย** ให้เขียนโน้ตใหม่เป็นภาษาไทยให้สอดคล้องกับเนื้อหาเดิม เว้นแต่ผู้ใช้จะขอเป็นอย่างอื่น
- รักษาลิงก์ `[[wikilink]]` ที่เชื่อมโยงต้นน้ำ/ปลายน้ำระหว่างโฟลเดอร์ไว้เมื่อเพิ่มหรือแก้ไขโน้ต เพราะลิงก์เหล่านี้สื่อถึงลำดับงาน requirements → design → testing → retrospective ที่ตั้งใจไว้
- ห้ามลบเอกสารออกจากโฟลเดอร์ที่ใช้งานอยู่ ให้ย้ายเนื้อหาที่ถูกแทนที่ไปไว้ใน `docs/00-archived/` แทน
- โปรเจกต์นี้มี custom agent/skill ผูก workflow การเขียนเอกสารไว้แล้วใน `.claude/agents/` และ `.claude/skills/` — ใช้ skill เหล่านี้แทนการเขียนเอกสารเองโดยตรงเมื่อทำงานที่ตรงกับ workflow ของมัน:
  - `/new-requirement` (subagent `requirement-writer`) — สร้าง spec ใหม่ใน `01-spec/` พร้อมอัปเดต `backlog.md`
  - `/feature-list` (subagent `feature-list-writer`) — ตรวจสอบ backlog เทียบ spec แล้วอัปเดต `feature-list.md`
  - `/user-journey` (subagent `user-journey-writer`) — สร้าง/อัปเดต user journey diagram ใน `02-design/01-prototypes/`
  - `/audit-backlog` — รัน `feature-list` ต่อด้วย `user-journey` ให้ครบทุก requirement ในรอบเดียว
  - `/prototype` (subagent `prototype-writer`) — สร้าง/อัปเดต UI prototype แบบ HTML mockup ใน `02-design/01-prototypes/mockups/` โดยอ้างอิง requirement + journey + `DESIGN.md` ทำได้ทั้งหมดทุก requirement หรือระบุเจาะจงบางรายการ เสนอแผนให้ user ยืนยันก่อนสร้างไฟล์จริงเสมอ และถามทุกครั้งว่าจะสร้าง folder version ใหม่หรือแก้ไข version ล่าสุดเมื่อมี prototype เดิมอยู่แล้ว ถ้ายังไม่มี `DESIGN.md` จะถามข้อมูล (โทนสี/สไตล์/ภาพตัวอย่าง) เพื่อสร้างไฟล์นี้ก่อน
  - `/architecture` (subagent `architecture-writer`) — สร้าง/อัปเดต `02-design/02-technical/high-level-architecture.md` เอกสารภาพรวมสถาปัตยกรรมระบบ 1 ไฟล์ต่อโปรเจกต์แบบ**เชิงแนวคิด (conceptual) ที่ยังไม่ผูกมัดกับเทคโนโลยี/เฟรมเวิร์ก/ฐานข้อมูลใด ๆ** ครอบคลุม system context, องค์ประกอบเชิงแนวคิด, data flow ตาม user journey, และแบบจำลองข้อมูลเชิงแนวคิด โดยอ้างอิง backlog + feature-list + spec + journey ทั้งหมด ถ้ามี `tech-stack.md` อยู่แล้ว จะเพิ่มหัวข้อท้ายไฟล์ "Technical Mapping" (ข้อยกเว้นเดียวที่อนุญาตระบุเทคโนโลยีจริง) map องค์ประกอบไปยัง stack จริง
  - `/database-schema` (subagent `database-schema-writer`) — สร้าง/อัปเดต `02-design/02-technical/database-schema.md` เอกสารแบบจำลองข้อมูล 1 ไฟล์ต่อโปรเจกต์แบบ**เชิงแนวคิด ที่ยังไม่ผูกมัดกับฐานข้อมูล/เทคโนโลยีใด ๆ** ประกอบด้วยรายละเอียดแต่ละเอนทิตี/ตาราง (attribute, ชนิดข้อมูลเชิงแนวคิด, ข้อจำกัดเชิงธุรกิจ) และ ER Diagram เป็นอย่างน้อย โดยขยายรายละเอียดต่อจากหัวข้อแบบจำลองข้อมูลใน `high-level-architecture.md` (ถ้ามี — ทั้งสองไฟล์แยกกันและลิงก์ไป-กลับ ไม่ยุบรวม) ถ้ามี `tech-stack.md` อยู่แล้ว จะเพิ่มหัวข้อท้ายไฟล์ "Technical Mapping" (ข้อยกเว้นเดียวที่อนุญาตระบุ DBMS/ชนิดข้อมูลจริง) แปลง attribute เป็น column จริง
  - `/api-spec` (subagent `api-spec-writer`) — สร้าง/อัปเดต `02-design/02-technical/api-spec.md` เอกสารรายการ operation ของระบบ 1 ไฟล์ต่อโปรเจกต์แบบ**เชิงแนวคิด ที่ยังไม่ผูกมัดกับ protocol/รูปแบบการสื่อสารเชิงเทคนิคใด ๆ** (ไม่มี HTTP verb, URL path, status code) ประกอบด้วยรายชื่อ operation, ผู้เรียกใช้, ข้อมูลนำเข้า/ผลลัพธ์เชิงแนวคิด, กฎธุรกิจ/ข้อยกเว้น โดยอ้างอิง `high-level-architecture.md` และ `database-schema.md` (ถ้ามี) เพื่อให้ operation สอดคล้องกับองค์ประกอบ/เอนทิตีที่นิยามไว้แล้ว ถ้ามี `tech-stack.md` อยู่แล้ว จะเพิ่มหัวข้อท้ายไฟล์ "Technical Mapping" (ข้อยกเว้นเดียวที่อนุญาตระบุ protocol จริง) map operation ไปยังกลไกจริง
  - `/detailed-design` (subagent `detailed-design-writer`) — สร้าง/อัปเดต `02-design/02-technical/detailed-design/{feature-slug}.md` เอกสาร detailed design เชิงแนวคิด **1 ไฟล์ต่อ feature/backlog item** (ต่างจาก architecture/database-schema/api-spec ที่เป็น 1 ไฟล์ต่อโปรเจกต์) ประกอบด้วย sequence flow diagram เป็นอย่างน้อย พร้อม validation/error handling ต่อขั้นตอน โดยอ้างอิง backlog + feature-list + spec + journey + high-level-architecture.md + database-schema.md + api-spec.md (ถ้ามี) — สำหรับ feature ที่มี operation ตรงกันใน api-spec.md จะใช้ operation นั้นเป็นฐานหลัก ถ้าไม่มีจะ fallback ไปใช้ journey/spec ทำได้ทั้งครบทุก feature หรือระบุเจาะจงบางรายการ ถ้ามี `tech-stack.md` อยู่แล้ว จะเพิ่มหัวข้อท้ายไฟล์ต่อ feature "Technical Mapping" (ข้อยกเว้นเดียวที่อนุญาตระบุเทคโนโลยี/protocol จริง — ห้ามแก้ label ใน sequence diagram) map step ไปยังกลไกจริง
  - ทั้ง 4 skill ข้างต้น (architecture, database-schema, api-spec, detailed-design) ยังคงเนื้อหาหลักเป็นเชิงแนวคิดเหมือนเดิมทุกประการ — หัวข้อ "Technical Mapping" เป็นส่วนเสริมท้ายไฟล์เท่านั้น ไม่ได้เปลี่ยนธรรมชาติของเอกสารทั้งฉบับให้ผูกกับเทคโนโลยี
  - `/tech-stack` (subagent `tech-stack-writer`) — สัมภาษณ์ user แบบเข้มข้น (ความเชี่ยวชาญของทีม, งบประมาณ, กรอบเวลา, platform เป้าหมาย, ลักษณะข้อมูล, ความคุ้นเคยด้าน DevOps, NFR) ทุกมิติที่ยังไม่ชัดเจนต้องถามพร้อมตัวเลือกอย่างน้อย 3 แนวทางและข้อดี/ข้อเสีย จากนั้นสังเคราะห์เป็นอย่างน้อย 3 แนวทาง tech stack ให้ user เลือก แล้วสร้าง/อัปเดต `02-design/02-technical/tech-stack.md` เอกสารเดียวต่อโปรเจกต์ที่**ผูกมัดกับเทคโนโลยี/เฟรมเวิร์ก/ฐานข้อมูล/บริการ hosting จริงโดยเจตนา** (ตรงข้ามกับ architecture/database-schema/api-spec/detailed-design ที่ห้ามระบุเทคโนโลยี) พร้อม mapping กลับไปยังองค์ประกอบ/เอนทิตี/operation ใน `high-level-architecture.md` + `database-schema.md` + `api-spec.md` (ถ้ามี)
  - `/acceptance-criteria` (subagent `acceptance-criteria-writer`) — สร้าง/อัปเดต `03-testing/01-test-plan/acceptance-criteria.md` แบบ Given-When-Then ต่อ backlog item โดยอ้างอิง backlog + feature-list + spec + journey/prototype (ถ้ามี) ทำได้ทั้งครบทุก backlog item หรือระบุเจาะจงบางรายการ (living document เดียว แก้เฉพาะส่วนในขอบเขต)
  - `/test-plan` (subagent `test-plan-writer`) — สร้าง/อัปเดต `03-testing/01-test-plan/test-plan.md` เอกสารกลยุทธ์ทดสอบภาพรวม 1 ไฟล์ต่อโปรเจกต์ (scope, ประเภทการทดสอบ, environment, risk management, entry/exit criteria) รวบรวม non-functional requirement (NFR) จาก user ทุกครั้งที่ยังไม่มีข้อมูล เพราะ spec ปัจจุบันยังไม่มี field NFR ชัดเจน
  - `/test-case` (subagent `test-case-writer`) — สร้าง/อัปเดต `03-testing/01-test-plan/test-cases/{feature-slug}.md` เป็น test case แบบ step-by-step (test id, pre-condition, test step, expected result, test data, reference) ต่อ feature โดยอ้างอิง `acceptance-criteria.md` เป็นฐานหลัก + backlog + journey ต้องมี acceptance-criteria ของ backlog item นั้นอยู่ก่อนจึงจะทำได้
  - ทุก skill กำหนดให้ main conversation ต้องเคลียร์ประเด็นคลุมเครือกับ user ผ่าน `AskUserQuestion` ก่อนเรียก subagent เสมอ — subagent เองห้ามถามคำถามผู้ใช้
- เมื่อมีการนำโค้ดแอปพลิเคชันจริงเข้ามาในโปรเจกต์นี้ ให้อัปเดตไฟล์นี้ด้วย tech stack, คำสั่ง setup/build/lint/test และสถาปัตยกรรมโค้ดที่แท้จริง
