# Sales Performance — Tableau Extension

Dashboard extension สำหรับดูภาพรวมยอดขายของ Vendor รายเดียว — KPI รวม (Net Sales / Sales Qty / YoY Growth / Top Brand), Sales Trend Monthly (CY vs LY), Sales by Sales Office, Sales by Channel, Sales by Brand, Sales by MCH2, Top 10 Best-Selling Products (สลับ rank ได้ระหว่าง Net Sales กับ Sales Qty) พร้อม Filter ในตัว (Sales Office / MCH2 / Month) และปุ่ม Download รายละเอียดทั้งหมดเป็นไฟล์ Excel หลาย sheet

## โครงสร้างไฟล์

```
vend-sales-performance/
  index.html                    ไฟล์หลักของ extension (HTML + CSS + JS ในไฟล์เดียว)
  VendSalesPerformance.trex     ไฟล์ manifest สำหรับให้ Tableau รู้จัก extension นี้
tableau.extensions.1.latest.js  Tableau Extensions API (index.html เรียกใช้ไฟล์นี้)
```

> **หมายเหตุ:** ไฟล์ข้อมูลจริง (`.xlsx` / `.xls` / `.csv`) และโฟลเดอร์ `preview/` ถูกกันไว้ใน `.gitignore` ไม่ได้ขึ้น GitHub เพราะเป็นข้อมูลภายในและไม่จำเป็นต่อการรันตัว extension

> **`index.html` ไม่มีข้อมูลตัวอย่าง/mock data ฝังอยู่เลย** — ไฟล์นี้จะแสดงผลได้ก็ต่อเมื่อรันอยู่ภายใน Tableau dashboard จริงเท่านั้น (ดึงข้อมูลสดจาก worksheet ตามสเปกในข้อ 3) ถ้าเปิดไฟล์ตรงๆด้วยเบราว์เซอร์จะเห็นแค่กรอบ layout เปล่าๆ กับข้อความแจ้งว่าต้องเปิดผ่าน Tableau — ใช้เช็คแค่โครงสร้าง/การจัดวางเท่านั้น ไม่มีตัวเลขให้ดู

---

## 1) เช็คโครงสร้าง Layout (ยังไม่ต้องต่อ Tableau)

เปิดไฟล์ `vend-sales-performance/index.html` ตรงๆ ด้วยเบราว์เซอร์ (ดับเบิลคลิก หรือลากไฟล์เข้าเบราว์เซอร์) — จะเห็นข้อความ "This extension only renders inside a Tableau dashboard." เพื่อยืนยันว่าไฟล์โหลดไม่มี error

ถ้าต้องการดูข้อมูลจริงต้องเปิดผ่าน Tableau ตามข้อ 3

---

## 2) Deploy ขึ้น GitHub Pages

ขั้นตอนนี้ทำครั้งเดียวเพื่อให้ Tableau (ซึ่งต้องโหลด extension จาก URL แบบ `https://`) เข้าถึงไฟล์ `index.html` ได้

1. เข้า repo บน GitHub: `https://github.com/warinda-nor/vend_sales_performance`
2. ไปที่ **Settings → Pages**
3. ที่ **Source** เลือก **Deploy from a branch**
4. เลือก Branch เป็น **main** และ Folder เป็น **/ (root)** แล้วกด **Save**
5. รอ 1–2 นาที ให้ GitHub Pages build เสร็จ แล้วเข้าไปเช็คที่:
   ```
   https://warinda-nor.github.io/vend_sales_performance/vend-sales-performance/index.html
   ```
   ถ้าเห็นข้อความ "This extension only renders inside a Tableau dashboard." แปลว่า deploy สำเร็จ

> URL ด้านบนต้องตรงกับค่าที่อยู่ใน `vend-sales-performance/VendSalesPerformance.trex` (แท็ก `<source-location><url>`) เป๊ะๆ — ถ้าเปลี่ยนชื่อ repo หรือ path ต้องแก้ในไฟล์ `.trex` ให้ตรงกันด้วย

---

## 3) ติดตั้งใช้งานใน Tableau Desktop

1. เปิด Tableau Desktop แล้วเปิด Dashboard ที่ต้องการใส่ extension
2. ลาก object **Extension** จากแผง Objects มาวางในตำแหน่งที่ต้องการ
3. เลือก **My Extensions → Access Local Extensions** แล้วเลือกไฟล์ `vend-sales-performance/VendSalesPerformance.trex`
   (หรือถ้า deploy ผ่าน GitHub Pages แล้ว จะสามารถแชร์ไฟล์ `.trex` นี้ให้คนอื่นใช้ได้เลยโดยไม่ต้องมีไฟล์ index.html อยู่ในเครื่อง เพราะ extension จะไปโหลดจาก URL บน GitHub Pages โดยตรง)
4. Dashboard ต้องมี Worksheet ทั้งหมด **2 ตัว** ตามสเปกด้านล่าง — **ตั้งชื่อ Worksheet เป็นอะไรก็ได้ตามใจ** เพราะ extension จะดูจาก **field ที่มีอยู่ใน worksheet นั้นๆ** เพื่อแยกว่าอันไหนคือ Detail อันไหนคือ Trend (ไม่ได้ดูจากชื่อ worksheet):
   - มี field **`Article Id`** → ถือเป็น **Detail**
   - มี field **`Month, Year of Time Date`** แต่ **ไม่มี** `Article Id` → ถือเป็น **Trend**
5. ถ้า field ที่ต้องใช้ขาดไปฝั่งใดฝั่งหนึ่ง extension จะโชว์ banner สีแดงบอกชื่อ field ที่ขาดแบบเจาะจง ไม่ใช่หน้าจอเปล่าๆ — ให้แก้ชื่อ field ใน Tableau (หรือแก้ค่าคงที่ `DETAIL_FIELDS`/`TREND_FIELDS` ใน `index.html`) ให้ตรงกัน

### Calculated Field ที่ต้องสร้างใน Tableau (CY/LY คำนวณสำเร็จรูปมาให้ extension เลย)

Net Inc Tax และ Sale Qty ทุกตัวต้อง split เป็นคอลัมน์ CY (Current Year) กับ LY (ปีก่อน ช่วงเดียวกัน) แยกกัน โดยขับด้วย Parameter `Start Date` / `End Date` บน Dashboard แล้ว extension จะ sum แต่ละคอลัมน์ตรงๆ ไม่มีการคำนวณช่วงวันที่เองอีกต่อไป — **ช่วงวันที่/ปีที่แสดงบนหน้าจอ extension อ่านตรงจาก Parameter `Start Date`/`End Date` ผ่าน Parameters API** (`dashboard.getParametersAsync()`), เปลี่ยน Parameter แล้ว extension จะ refresh label ให้เองอัตโนมัติ:

| Field ที่ต้องสร้าง | แนวคิดสูตร (ตัวอย่าง) |
|---|---|
| `Net Inc Tax - CY` | `IF [Time Date] >= [Start Date] AND [Time Date] <= [End Date] THEN [Net Inc Tax] END` |
| `Net Inc Tax - LY` | เหมือนกันแต่ใช้ `DATEADD('year', -1, [Start Date])` / `DATEADD('year', -1, [End Date])` |
| `Sale Qty - CY` / `Sale Qty - LY` | สูตรแบบเดียวกัน ใช้ `[Sale Qty]` |

**`Month, Year of Time Date`** — ใส่ใน worksheet "Trend" เท่านั้น เป็น date field (เช่น Month/Year date-part หรือ `DATETRUNC('month', [Time Date])`) ใช้เป็นแกนเวลาของกราฟ Sales Trend Monthly — worksheet "Detail" **ไม่ต้องมี field วันที่เลย**

> extension parse ค่าฟิลด์นี้แบบ timezone-safe (อ่านตัวเลข YYYY-MM ตรงจาก string ที่ Tableau ส่งมา ไม่ผ่าน `new Date()` ที่อาจเพี้ยนข้ามเดือนตาม timezone เครื่อง) — ถ้า data source ส่งมาเป็น text ที่ format ต่างจากนี้ (เช่น `"January 2026"`) extension จะลอง parse แบบสำรองให้ แต่ถ้า parse ไม่ผ่านเลยสักแถว จะโชว์ error banner พร้อมตัวอย่างค่าจริงที่ได้รับมา ไม่ปล่อยให้ตัวเลขกลายเป็น 0 เงียบๆ

### สเปก field ที่แต่ละ Worksheet ต้องมี

**Worksheet "Detail"** — grain รายบทความ (ต่อ Article Id) ไม่มีวันที่

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `Vendor Id`, `Vendor Name` | แสดงในการ์ด Vendor (มุมซ้ายของแถว KPI) |
| `Article Id` | นับจำนวน SKU / ใช้แยกว่านี่คือ worksheet Detail |
| `Article Name Th` | ชื่อสินค้าในตาราง Top 10 Best-Selling Products |
| `Sales Office` | Sales by Sales Office + ตัวเลือกใน Filter "Sales Office" |
| `Sales Channel` | Sales by Channel (Web/Website/Anyhome(Online) จะถูกรวมเป็น "Online" อัตโนมัติ) |
| `Brand` | Sales by Brand |
| `MC_Desc ` (มีช่องว่างต่อท้าย) | คอลัมน์ "Mch Desc" ในตาราง Top 10 Best-Selling Products |
| `MCH2_Desc ` (มีช่องว่างต่อท้าย) | Sales by MCH2 + ตัวเลือกใน Filter "MCH2" |
| `Net Inc Tax - CY`, `Net Inc Tax - LY` | ยอดขาย ปีปัจจุบัน/ปีก่อน |
| `Sale Qty - CY`, `Sale Qty - LY` | จำนวนขาย ปีปัจจุบัน/ปีก่อน |

**Worksheet "Trend"** — grain รายเดือน ไม่มี Article Id

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `Month, Year of Time Date` | แกนเวลาของกราฟ Sales Trend Monthly + ตัวเลือกใน Filter "Month" / ใช้แยกว่านี่คือ worksheet Trend |
| `Sales Office` | ใช้กรองยอดตาม Filter "Sales Office" ในกราฟ Trend |
| `Brand` | ใช้หา Top Brand ของ KPI card แล้วดึงเส้นแนวโน้มรายเดือนของแบรนด์นั้นมาทำ sparkline |
| `MCH2_Desc ` (มีช่องว่างต่อท้าย) | ใช้กรองยอดตาม Filter "MCH2" ในกราฟ Trend |
| `Net Inc Tax - CY`, `Net Inc Tax - LY` | KPI ยอดขาย + กราฟ Trend รายเดือน |
| `Sale Qty - CY`, `Sale Qty - LY` | KPI จำนวนขาย + กราฟ Trend รายเดือน |

> ชื่อ field ต้องตรงกับในตาราง **เป๊ะๆ รวมช่องว่างต่อท้าย** (ตรงตามค่าคงที่ `DETAIL_FIELDS` / `TREND_FIELDS` ท้ายไฟล์ `index.html`) ถ้าใน data source ใช้ชื่อคอลัมน์ต่างจากนี้ (เช่นไม่มีช่องว่างต่อท้าย `MCH2_Desc`/`MC_Desc`) ให้แก้ค่าในตัวแปรเหล่านั้น (และจุดที่เรียก `r['...']` ในฟังก์ชัน `buildDashboardData`) ให้ตรงกับ data source จริง

---

## Filter ในตัว extension

หัวมุมขวาบนของ extension มี Filter 3 ตัว อ่านตัวเลือกจากค่าจริงที่ Tableau ส่งมาทุกครั้งที่ข้อมูลรีเฟรช (ไม่ใช่ค่าคงที่):

- **Sales Office** และ **MCH2** — มีอยู่ทั้งในสอง worksheet จึงกรองได้ทุกการ์ดบนแดชบอร์ด
- **Month** — มีอยู่แค่ใน worksheet "Trend" (Detail ไม่มี field วันที่เลย) จึงกรองแคบได้แค่กราฟ **Sales Trend Monthly** เท่านั้น การ์ด KPI/ตารางอื่นๆ ที่มาจาก Detail จะไม่เปลี่ยนตาม Filter นี้ — เป็นข้อจำกัดของโครงสร้างข้อมูล ไม่ใช่บั๊ก

Filter ทั้ง 3 ตัวจะ **reset กลับเป็น "เลือกทั้งหมด" ทุกครั้งที่ Tableau ส่งข้อมูลใหม่มา** (เปลี่ยน Quick Filter / Parameter / worksheet refresh บน Tableau เอง) เพื่อไม่ให้ค้างอ้างอิงค่าที่อาจไม่มีอยู่แล้วในชุดข้อมูลใหม่

---

## ข้อจำกัดที่ควรรู้

- ต้องมี Parameter ชื่อตรงตัว **`Start Date`** และ **`End Date`** (type: Date) อยู่บน Dashboard จริง — extension อ่านค่าทั้งสองตัวนี้ตรงๆผ่าน Parameters API เพื่อเอามาโชว์เป็น period/ปี label ที่หัวหน้าจอ ถ้าไม่พบ Parameter ชื่อนี้ label จะไม่อัปเดต (ไม่ error แต่จะค้างที่ค่า default)
- field ที่เป็นตัวเลข (measure) ถ้าถูกลากขึ้น shelf แบบ aggregate จะได้ fieldName กลับมาเป็น `AGG(ชื่อ field)` ไม่ใช่ชื่อ field เพียวๆ — extension ตัดคำห่อนี้ให้อัตโนมัติแล้ว (`normalizeFieldName`) ไม่ต้องแก้อะไรฝั่ง Tableau
- ทุกครั้งที่แก้ `vend-sales-performance/index.html` แล้ว push ขึ้น GitHub ต้องรอ GitHub Pages build ใหม่ (ปกติ 1–2 นาที) ก่อนที่ Tableau จะเห็นเวอร์ชันล่าสุด — ถ้าไม่เห็นการเปลี่ยนแปลง ให้ลอง hard refresh หรือปิด-เปิด dashboard ใหม่
- อ่าน Detail กับ Trend **ทีละ worksheet** (ไม่อ่านพร้อมกัน) เพราะเปิด `DataTableReader` สอง session ซ้อนกันเจอ `internal-error` ใน Tableau บางเวอร์ชัน
- ปุ่ม "Download all detail" (มุมขวาบนของ header) สร้างไฟล์ `.xlsx` จริงแยก sheet ตามการ์ด ผ่าน SheetJS (โหลดจาก CDN `cdn.jsdelivr.net`) — ต้องมีอินเทอร์เน็ตให้เครื่องที่รัน Tableau โหลด CDN นี้ได้
