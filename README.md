# Byte Survivor: The Great Memory Memory

เกม 2D Top-down ที่สอนเรื่องหน่วยความจำคอมพิวเตอร์ ผู้เล่นสวมบทบาทเป็นหุ่นยนต์ "Byte" ที่จัดการข้อมูลในเมนบอร์ดคอมพิวเตอร์

## เริ่มต้นใช้งาน

### วิธีเล่น

1. เปิดไฟล์ `byte-survivor.html` ในเบราว์เซอร์ (Chrome, Firefox, Edge)
2. กดปุ่ม "Start Game"
3. ใช้ **WASD** หรือ **Arrow Keys** เพื่อเคลื่อนที่
4. กด **E** หรือ **Space** เพื่อเก็บ/ฝากข้อมูล

### เป้าหมาย

บริหารข้อมูลให้ได้ **500 คะแนน** ภายใน **5 นาที**

## โครงสร้างโปรเจกต์

```
D:\tast Ai\
├── byte-survivor.html          # เกมต้นแบบ (เล่นได้ทันที)
├── byte-survivor-gdd.md        # Game Design Document
├── byte-survivor-recommendations.md  # คำแนะนำเพิ่มเติม
├── skill-web-edu-coach.md      # สกิลพัฒนาเว็บเพื่อการศึกษา
├── CONTEXT.md                  # Domain Model (Glossary)
├── docs/
│   ├── spec-byte-survivor.md   # เอกสาร Spec
│   └── adr/                    # Architecture Decision Records
│       ├── 0001-ram-as-volatile-memory.md
│       ├── 0002-power-off-as-teaching-mechanic.md
│       ├── 0003-construct-3-as-prototype-engine.md
│       └── 0004-pixel-art-8bit-style.md
└── README.md                   # ไฟล์นี้
```

## การพัฒนาต่อ

### ติดตั้ง Dependencies

```bash
npm install
```

### รันเซิร์ฟเวอร์สำหรับพัฒนา

```bash
npm run dev
```

### รัน Tests

```bash
npm test
```

## เทคโนโลยี

- HTML5 Canvas
- JavaScript (Vanilla)
- ไม่ต้องใช้ Library ภายนอก

## ใบอนุญาต

MIT License
