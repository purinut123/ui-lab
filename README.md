# UI Lab

งานนี้เลือกทำ **Part 3: Robot Framework** และ **Part 4: Cypress** ตามโจทย์ Automated UI Testing โดยทดสอบ Selenium Web Form และกรณีทดสอบ QP-01 ของ QuickPizza

## Robot Framework

โฟลเดอร์ `robot` มี test suite, resource file ที่รวม locator/keyword และหลักฐานการรันสองครั้งใน `results/run1` กับ `results/run2`

```bash
cd robot
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
robot -d results/run pizza_scenario.robot
```

ต้องกำหนดตัวแปรสภาพแวดล้อม `CHROMEDRIVER` ให้ชี้ไปยัง ChromeDriver ที่ตรงกับเวอร์ชัน Chrome ก่อนรัน

## Cypress

โฟลเดอร์ `cypress` มีสเปกของ Selenium Web Form และ QP-01, custom command สำหรับล็อกอินหน้า admin รวมถึง screenshot และ video จาก full run ที่ผ่าน

```bash
cd cypress
npm install
npm test
```

หากเครื่องติดตั้ง Chromium แทน Google Chrome ให้ใช้ `--browser chromium` หรือระบุ path ของ browser แทน

