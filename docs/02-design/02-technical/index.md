# 02 - Technical

เก็บเอกสาร **การออกแบบเชิงเทคนิค (Technical Design)** เช่น

- System architecture / โครงสร้างระบบโดยรวม — เริ่มจาก [[high-level-architecture|high-level-architecture.md]] ซึ่งเป็นสถาปัตยกรรมเชิงแนวคิด (conceptual) ยังไม่ผูกมัดกับเทคโนโลยีใด ๆ ก่อนจะแตกเป็นเอกสารเทคนิคเฉพาะด้านล่าง
- Database schema — [[database-schema|database-schema.md]] แบบจำลองข้อมูลเชิงแนวคิด (conceptual) ขยายรายละเอียดต่อจากหัวข้อแบบจำลองข้อมูลใน high-level-architecture.md ยังไม่ผูกมัดกับฐานข้อมูล/เทคโนโลยีใด ๆ
- API design / data contract — [[api-spec|api-spec.md]] สัญญาการทำงานเชิงแนวคิด (conceptual operation contract) จัดกลุ่ม operation ตามเอนทิตีที่นิยามไว้ใน database-schema.md ยังไม่ผูกมัดกับ protocol/HTTP method/status code ใด ๆ
- Detailed design / sequence flow ต่อ feature — [[detailed-design/index|detailed-design]] เอกสาร sequence flow เชิงแนวคิด (conceptual) 1 ไฟล์ต่อ feature/backlog item พร้อม validation/error handling ต่อขั้นตอน อ้างอิง high-level-architecture.md, database-schema.md และ api-spec.md ยังไม่ผูกมัดกับเทคโนโลยี/protocol ใด ๆ
- เทคโนโลยีและไลบรารีที่เลือกใช้ พร้อมเหตุผล — [[tech-stack|tech-stack.md]] เอกสารเดียวที่**ผูกมัดกับเทคโนโลยี/เฟรมเวิร์ก/ฐานข้อมูล/บริการ hosting จริงโดยเจตนา** ต่างจากเอกสารเชิงแนวคิดอื่นในโฟลเดอร์นี้ (high-level-architecture.md, database-schema.md, api-spec.md, detailed-design/) ที่จงใจไม่ระบุเทคโนโลยี พร้อม mapping กลับไปยังองค์ประกอบ/เอนทิตี/operation ที่ออกแบบไว้แล้ว

เอกสารในโฟลเดอร์นี้คือพิมพ์เขียวที่ทีมพัฒนาใช้อ้างอิงตอนลงมือเขียนโค้ด และเป็นฐานในการวางแผนทดสอบใน [[../../03-testing/01-test-plan/index|01-test-plan]]
