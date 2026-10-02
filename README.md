# Subscription Manager

เว็บแอปจัดการบริการแบบสมัครสมาชิก (Netflix, Spotify, iCloud ฯลฯ) ทั้งรายเดือนและรายปี
ดูยอดรวมต่อเดือน/ต่อปี สัดส่วนตามหมวด และวันครบกำหนดชำระ

Mini Project: สร้างเว็บไซต์แบบ full-stack ด้วยการสร้าง REST API (Node.js + Express + HTML/CSS/JavaScript)

## ฟีเจอร์

* เพิ่ม / แก้ไข / ลบ บริการ (CRUD ครบผ่าน REST API)
* เปิด-ปิดสถานะ "ใช้งานอยู่" ได้ทันที
* ค้นหาตามชื่อ กรองตามรอบชำระ หมวด และสถานะ เรียงตามราคา วันชำระ หรือชื่อ
* สรุปค่าใช้จ่ายต่อเดือน/ต่อปี พร้อมแถบสัดส่วนตามหมวด
* ป้ายเตือนวันครบกำหนดชำระ (อีก N วัน / เลยกำหนด)
* อัปเดตหน้าเว็บด้วย `fetch()` โดยไม่รีเฟรชหน้า

## โครงสร้างโปรเจกต์

```
.
├── server/          # ฝั่งเซิร์ฟเวอร์ (Node.js + Express)   
│   └── server.js
│   └── data.json
├── public/          # ฝั่งไคลเอนต์
│   ├── index.html
│   ├── style.css
│   └── app.js
├── docs/
│   └── screenshots/
├── package.json
└── README.md
```

## วิธีติดตั้งและรัน

```bash
npm install
npm run dev
```

จากนั้นเปิด http://localhost:3000

## REST API

Resource: `subscriptions`

|Method|Endpoint|หน้าที่|สถานะที่ตอบกลับ|
|-|-|-|-|
|GET|`/api/subscriptions`|ดึงรายการทั้งหมด (รองรับ query string)|200|
|GET|`/api/subscriptions/:id`|ดึงรายการเดียว|200, 404 ถ้าไม่พบ|
|POST|`/api/subscriptions`|เพิ่มรายการใหม่|201, 400 ถ้าข้อมูลไม่ครบ/ไม่ถูกต้อง|
|PATCH|`/api/subscriptions/:id`|แก้ไขบางฟิลด์|200, 400, 404 ถ้าไม่พบ|
|DELETE|`/api/subscriptions/:id`|ลบรายการ|204, 404 ถ้าไม่พบ|

### Query string ของ GET /api/subscriptions

|พารามิเตอร์|ค่าที่รับ|ตัวอย่าง|
|-|-|-|
|`billing`|`monthly`, `yearly`|`?billing=yearly`|
|`category`|`entertainment`, `music`, `cloud`, `education`, `other`|`?category=music`|
|`active`|`true`, `false`|`?active=true`|
|`q`|ข้อความค้นหาในชื่อ|`?q=net`|
|`sort`, `order`|`price` / `nextPayment` / `name`, `asc` / `desc`|`?sort=price\\\&order=desc`|

### โครงสร้างข้อมูล

```json
{
  "id": 1,
  "name": "Netflix",
  "price": 419,
  "billing": "monthly",
  "category": "entertainment",
  "nextPayment": "2026-10-15",
  "active": true
}
```

### ตัวอย่างการเรียก

```bash
# เพิ่มบริการ
curl -X POST http://localhost:3000/api/subscriptions \\\\
  -H "Content-Type: application/json" \\\\
  -d '{"name":"Spotify","price":129,"billing":"monthly","category":"music"}'

# ข้อมูลไม่ครบ -> 400 พร้อมข้อความ error
curl -i -X POST http://localhost:3000/api/subscriptions \\\\
  -H "Content-Type: application/json" -d '{"name":""}'

# ไม่พบรายการ -> 404
curl -i http://localhost:3000/api/subscriptions/9999

# ลบ -> 204
curl -i -X DELETE http://localhost:3000/api/subscriptions/1
```

## ภาพหน้าจอ

|||
|-|-|
|หน้าหลักและสรุปค่าใช้จ่าย|(docs\\screenshots\\home.png)|
|ค้นหาและกรอง|(docs\\screenshots\\กรอกฟอร์มก่อนกดเพื่ม.png)|
|เพิ่มบริการ|(docs\\screenshots\\หลังกดเพิ่ม.png)|
|แก้ไขบริการ|(docs\\screenshots\\แก้ไข.png)|
|ลบบริการ|(docs\\screenshots\\ลบ.png")|
|ไม่พบผลลัพธ์|(docs\\screenshots\\id ไม่อยู่ในserver.png)|
|ทดสอบ API (404 / 400 / 204)|(docs\\screenshots\\POST ข้อมูลไม่ครบ ตอบ 400.png)(docs\\screenshots\\PATCH ID ที่ไม่พบ ตอบ 404.png")(docs\\screenshots\\กรองด้วยquery.png")(docs\\screenshots\\GET id.png")(\\docs\\screenshots\\id ไม่อยู่ในserver.png")(docs\\screenshots\\GET api.png")|

## เทคโนโลยีที่ใช้

Node.js, Express.js, HTML, CSS, JavaScript (fetch API) ไม่เรียก API ภายนอก

