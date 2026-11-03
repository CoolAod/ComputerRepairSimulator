# เกมร้านซ่อมคอม — หน้าเว็บบน GitHub Pages

repo นี้เก็บเฉพาะ `index.html` (หน้าเว็บ) ส่วนระบบหลังบ้าน (`Code.gs`, `Seed.gs`) อยู่ใน Apps Script
หน้าเว็บคุยกับ Apps Script ผ่านลิงก์ `/exec` ที่ตั้งไว้ในตัวแปร `API_URL`

```
นักศึกษา ──▶ https://ชื่อบัญชี.github.io/repair/   (index.html บน GitHub Pages)
                         │  fetch (POST)
                         ▼
              Apps Script เว็บแอป /exec  (Code.gs + Seed.gs)  ──▶ Google Sheets
```

## ส่วนที่ 1: Apps Script (ทำก่อน)
1. เปิดโปรเจกต์ Apps Script ที่ผูกกับ Google Sheets ของเกม
2. **ลบไฟล์ HTML ชื่อ `Index` ออก** (ถ้ามี) เพราะหน้าเว็บย้ายไป GitHub แล้ว (ถ้าไม่ลบ ลิงก์ /exec จะยังแสดงหน้าเก่า)
3. วางทับไฟล์ `Code.gs` และ `Seed.gs` ด้วยฉบับล่าสุด (ห้ามคัดลอกผ่านแอปแชต เพราะเคยทำให้ `/ | < > : ?` หาย ให้เปิดไฟล์จากเครื่องแล้วคัดลอก)
4. Project Settings > Script properties > เพิ่มคีย์ `ADMIN_PIN` พร้อมรหัสแอดมินของคุณ (ถ้ายังไม่ได้ตั้ง)
5. Deploy > **Manage deployments** > รูปดินสอ > Version: **New version** > Deploy
   (ถ้ายังไม่เคย Deploy: Deploy > New deployment > Web app > Execute as: **Me**, Who has access: **Anyone**)
6. คัดลอก **Web app URL** (ลงท้ายด้วย `/exec`)
7. ทดสอบ: เปิดลิงก์ `/exec` ในเบราว์เซอร์ ต้องเห็น `{"ok":true,"service":"Computer Repair Simulator API"}`

## ส่วนที่ 2: GitHub Pages
1. สร้าง repo ใหม่ ตั้งชื่อสั้น ๆ เช่น `repair` เลือก **Public** (บัญชีฟรีเปิด Pages ได้เฉพาะ repo Public — ใน repo นี้ไม่มีรหัสหรือข้อมูลใด ๆ)
2. อัปโหลด `index.html` (ชื่อไฟล์ต้องเป็นตัวพิมพ์เล็กทั้งหมด) และ `README.md`
3. เปิดไฟล์ `index.html` บน GitHub > กดรูปดินสอ (Edit) > ค้นหาบรรทัด
   ```
   const API_URL="";
   ```
   แล้วใส่ลิงก์ `/exec` ระหว่างเครื่องหมายคำพูด เช่น `const API_URL="https://script.google.com/macros/s/xxxx/exec";` > Commit changes
4. Settings > Pages > Source: **Deploy from a branch** > Branch `main` โฟลเดอร์ `/ (root)` > Save
5. รอ 1-2 นาที ลิงก์ที่ได้ เช่น `https://ชื่อบัญชี.github.io/repair/` คือลิงก์ที่แจกนักศึกษา

## ทดสอบหลังติดตั้ง
- หน้าแรกขึ้นแถบสถานะร้าน 🟢/🔴 (แปลว่าเชื่อมต่อ Apps Script ได้)
- สมัคร/เข้าสู่ระบบได้ เข้าแอดมินด้วยรหัส `admin` + `ADMIN_PIN`
- รับงานแล้วลองสลับแท็บ ต้องถูกหัก 50 คะแนนและมีแจ้งเตือน (ดูชีต `Events`)

## อัปเดตภายหลัง
| แก้อะไร | ทำที่ไหน |
|---|---|
| หน้าเว็บ (`index.html`) | commit ที่ GitHub รอ 1-2 นาที ลิงก์เดิมใช้ต่อได้ (ถ้ายังเห็นของเก่า กด Ctrl+F5) |
| `Code.gs` / `Seed.gs` | วางใน Apps Script แล้ว Deploy > Manage deployments > New version |
| ลิงก์ `/exec` เปลี่ยน | แก้ `API_URL` ใน `index.html` |

## แก้ปัญหา
| อาการ | วิธีแก้ |
|---|---|
| ขึ้น "ยังไม่ได้ตั้งค่า API_URL" | ยังไม่ได้ใส่ลิงก์ `/exec` ในบรรทัด `const API_URL` |
| ขึ้น "เชื่อมต่อเซิร์ฟเวอร์ไม่ได้" | ลิงก์ผิด, Deployment ไม่ได้ตั้ง Anyone/Execute as Me, หรือยังไม่ได้ New version หลังแก้ `Code.gs` ลองเปิดลิงก์ `/exec` ตรงๆ ดู |
| ล็อกอินแล้วขึ้น "ไม่รู้จักคำสั่งนี้" | `Code.gs` ใน Apps Script ยังเป็นฉบับเก่า (ไม่มี `doPost`) |
| เห็น error CORS ใน Console | Deployment ต้องเป็น Who has access: **Anyone** และตอบ JSON ได้ |
| กรอก PIN ผิดแล้วล็อก | รอ 10 นาที (ผิด 5 ครั้ง) |
