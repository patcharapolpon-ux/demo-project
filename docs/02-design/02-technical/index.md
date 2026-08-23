# 02 - Technical

เก็บเอกสาร **การออกแบบเชิงเทคนิค (Technical Design)** เช่น

- System architecture / โครงสร้างระบบโดยรวม — เริ่มจาก [[high-level-architecture|high-level-architecture.md]] ซึ่งเป็นสถาปัตยกรรมเชิงแนวคิด (conceptual) ยังไม่ผูกมัดกับเทคโนโลยีใด ๆ ก่อนจะแตกเป็นเอกสารเทคนิคเฉพาะด้านล่าง
- Database schema — [[database-schema|database-schema.md]] แบบจำลองข้อมูลเชิงแนวคิด (conceptual) ขยายรายละเอียดต่อจากหัวข้อแบบจำลองข้อมูลใน high-level-architecture.md ยังไม่ผูกมัดกับฐานข้อมูล/เทคโนโลยีใด ๆ
- API design / data contract — [[api-spec|api-spec.md]] สัญญาการทำงานเชิงแนวคิด (conceptual operation contract) จัดกลุ่ม operation ตามเอนทิตีที่นิยามไว้ใน database-schema.md ยังไม่ผูกมัดกับ protocol/HTTP method/status code ใด ๆ
- เทคโนโลยีและไลบรารีที่เลือกใช้ พร้อมเหตุผล

เอกสารในโฟลเดอร์นี้คือพิมพ์เขียวที่ทีมพัฒนาใช้อ้างอิงตอนลงมือเขียนโค้ด และเป็นฐานในการวางแผนทดสอบใน [[../../03-testing/01-test-plan/index|01-test-plan]]
