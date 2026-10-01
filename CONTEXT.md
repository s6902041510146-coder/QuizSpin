# Byte Survivor: The Great Memory Memory

เกม 2D Top-down Pixel Survival / Management Sim ที่สอนเรื่องหน่วยความจำคอมพิวเตอร์ ผู้เล่นสวมบทบาทเป็นหุ่นยนต์ "Byte" ที่จัดการข้อมูลในเมนบอร์ดคอมพิวเตอร์

## Language

### ตัวละคร (Characters)

**Byte**:
หุ่นยนต์ซ่อมแซมและจัดเก็บข้อมูลตัวจิ๋ว ผู้เล่นควบคุม Byte ให้เก็บข้อมูลจากสายพานแล้วนำไปฝากในตู้เก็บความจำ
_Avoid_: Player, character, hero

**CPU Master**:
หัวหน้า CPU ผู้คอยสั่งงานให้ Byte เป็น NPC ที่ให้คำแนะนำและตำหนิเมื่อเกิดปัญหา
_Avoid_: Boss, manager, supervisor

### วัตถุ (Objects)

**Data Card**:
ข้อมูลที่ลอยเข้ามาตามสายพาน มี 4 ประเภท: เพลง, รูปภาพ, เอกสาร, รหัสผ่านสำคัญ
_Avoid_: Item, object, thing

**Memory Box**:
ตู้เก็บความจำ 2 ประเภทบนแผนที่: RAM (เร็ว, เล็ก, Volatile) และ SSD (ช้า, ใหญ่, Non-volatile)
_Avoid_: Storage, container, box

**สายพาน (Conveyor Belt)**:
ระบบที่ส่ง Data Card เข้ามาในเกมอย่างต่อเนื่อง
_Avoid_: Spawner, generator

### ระบบ (Systems)

**Production Phase**:
ช่วงเวลาปกติที่ Data Card ลอยเข้ามาตามสายพาน ผู้เล่นเก็บและฝากข้อมูล
_Avoid_: Normal phase, build phase

**Crisis Phase**:
ช่วงเวลาที่เกิดสถานการณ์ขัดข้อง เช่น ไฟดับ หรือความจำเต็ม
_Avoid_: Emergency, disaster

**Power Off**:
สถานการณ์ที่ไฟดับ 5 วินาที ทำให้ข้อมูลใน RAM หายทั้งหมด แต่ข้อมูลใน SSD ยังอยู่
_Avoid_: Blackout, shutdown

**Memory Full**:
สถานการณ์ที่ RAM เต็มและผู้เล่นไม่ย้ายข้อมูลออก ทำให้ระบบช้าลงและเสียคะแนน
_Avoid_: Overflow, crash

### แนวคิดทางการศึกษา (Educational Concepts)

**Volatile Memory**:
หน่วยความจำที่ข้อมูลหายไปเมื่อไฟดับ ในเกมคือ RAM
_Avoid_: Temporary, volatile

**Non-volatile Memory**:
หน่วยความจำที่ข้อมูลยังอยู่แม้ไฟดับ ในเกมคือ SSD
_Avoid_: Permanent, non-volatile

**Memory Hierarchy**:
การจัดลำดับหน่วยความจำตามความเร็วและขนาด ในเกมคือ RAM (เร็ว/เล็ก) → SSD (ช้า/ใหญ่)
_Avoid_: Memory pyramid, storage hierarchy

## Rules

- **Be opinionated.** ใช้คำที่เฉพาะเจาะจงที่สุดเสมอ
- **Keep definitions tight.** ประโยคเดียวถึงสองประโยค
- **Only include terms specific to this project's context.** ไม่ใส่แนวคิดทั่วไป
- **Group terms under subheadings** เมื่อมีความเกี่ยวข้องกัน
