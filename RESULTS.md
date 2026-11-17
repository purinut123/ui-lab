# UI lab results

Tool parts that I chose: Part 3 - Robot Framework และ Part 4 - Cypress

## Part 0

- Step 0.1, Chrome version: Chromium 156.0.8078.79 (Official Build) (64-bit)
- Step 0.3, ChromeDriver version: ChromeDriver 156.0.8078.79
- Step 0.4, locator of Pizza, Please!: `css:button[name="pizza-please"]`
- Step 0.4, locator of Excluded tools: `css:select`
- Step 0.4, locator of Must be vegetarian: `id:default-checkbox`
- Step 0.4, locator of Custom Pizza Name: `css:input#pizza-name`
- Step 0.5, pizza name, tool and calories of the run by hand: `Lab Pizza 20261110-202027`, `Scissors`, 450 calories per slice

| Step | Actual result | Pass or fail |
|------|---------------|--------------|
| 1 | เปิดหน้า QuickPizza แล้วพบหัวข้อ `Looking to break out of your pizza routine?` | Pass |
| 2 | เปิด Advanced แล้วพบ Max Calories per Slice, Excluded tools, Must be vegetarian และ Custom Pizza Name | Pass |
| 3 | กรอก 600, เลือก Knife, ติ๊ก vegetarian และกรอกชื่อ `Lab Pizza 20261110-202027`; ค่าที่แสดงตรงกับข้อมูลทดสอบ | Pass |
| 4 | ผลลัพธ์แสดงชื่อที่กรอก ใช้เครื่องมือ Scissors ซึ่งไม่ใช่ Knife และมี 450 calories per slice ซึ่งไม่เกิน 600 | Pass |
| 5 | กด Love it! โดยยังไม่ล็อกอิน แล้วพบข้อความ `Please log in first.` | Pass |
| 6 | เปิด `/admin` และล็อกอินด้วย admin/admin แล้วพบ `Latest pizza recommendations` | Pass |
| 7 | พบ `Lab Pizza 20261110-202027` ในรายการ พร้อมเครื่องมือ Scissors ตรงกับผลจาก Step 4 | Pass |

## Part 3: Robot Framework

- `robot --version`: Robot Framework 7.5.1 (Python 3.12.14 on linux)
- Step 3.2, failure message for the wrong title: `Title should have been 'Web Form' but was 'Web form'.`
- Step 3.8, run 1, result, elapsed time and pizza name: PASS, 11.743898 วินาที, `Lab Pizza 20261110-202045`
- Step 3.8, run 2, result, elapsed time and pizza name: PASS, 11.669175 วินาที, `Lab Pizza 20261110-202102`

## Part 4: Cypress

- `node --version`, `npm --version` and `npx cypress version`: Node v24.22.1, npm 11.9.0, Cypress package/binary 16.2.1
- Step 4.3, failure message for the wrong title: `Timed out retrying after 10000ms: expected 'Web form' to equal 'Web Form'`
- Step 4.6, tool and calories in the answer of POST `/api/pizza`: run ล่าสุดได้ `Pizza cutter` และ 400 calories per slice
- Step 4.7, the line of the admin list that shows your pizza: `Lab Pizza 20261110-204719 (tool=Pizza cutter, ingredients_number=6) (newest)`
- Step 4.8, run 1, result, duration and pizza name: All specs passed (2/2), 19 วินาที, `Lab Pizza 20261110-204301`
- Step 4.8, run 2, result, duration and pizza name: All specs passed (2/2), 18 วินาที, `Lab Pizza 20261110-204719`
- Step 4.9 (optional), browser and result: ไม่ได้ทำ (optional)

## Part B

1. ช่อง Max Calories per Slice ไม่ควรถูกหาโดย `id` เพราะหน้าเว็บใช้ `id="floating_filled"` ซ้ำถึง 3 ช่อง จึงไม่สามารถระบุช่องนี้ได้แน่นอน การทดสอบจึงหา label ที่มีข้อความ `Max Calories per Slice` แล้วเลื่อนไปยัง input ที่สัมพันธ์กันแทน ควรแก้หน้าเว็บให้ช่องนี้มี id เฉพาะ เช่น `max-calories-per-slice` และกำหนด `for` ของ label ให้ตรงกัน ซึ่งจะทำให้ locator สั้น ชัดเจน และไม่เลือกผิดช่อง

2. การเปรียบเทียบเครื่องมือใน recommendation กับเครื่องมือของพิซซ่ารายการเดียวกันในหน้า admin เป็น consistency oracle เพราะตรวจว่าข้อมูลเดียวกันยังสอดคล้องกันข้ามหน้าจอ ใน Robot run 2 recommendation แสดง Scissors และรายการ admin ของ `Lab Pizza 20261110-202102` ก็แสดง `tool=Scissors` ตรงกัน การตรวจนี้จะจับข้อผิดพลาดที่ระบบบันทึก tool ผิดค่า หรือหน้า admin map ชื่อ tool ผิดหลังจากสร้าง recommendation

3. สามารถตรวจ vegetarian ได้โดยดักผลของ POST `/api/pizza` แล้ว assert ว่า response มีค่า `vegetarian: true` หรือส่วนผสมทุกชนิดอยู่ในรายการส่วนผสมมังสวิรัติที่เชื่อถือได้ หากตรวจจาก UI หน้า recommendation ควรแสดง badge หรือข้อความ Vegetarian เพื่อให้ test assert ได้โดยตรง วิธีตรวจจากรายชื่อส่วนผสมต้องมีแหล่งข้อมูลอ้างอิงอีกชุดว่าแต่ละส่วนผสมเป็นมังสวิรัติหรือไม่ มิฉะนั้น test จะตัดสินผลไม่ได้อย่างน่าเชื่อถือ

4. Robot Framework รอ QuickPizza ด้วย keyword ที่ระบุชัด เช่น `Wait Until Element Contains` และ `Wait Until Page Contains` ส่วน Cypress retry query/assertion ให้อัตโนมัติและใช้ `cy.wait('@pizza')` รอ API โดยตรง การเขียน QP-01 ด้วย Cypress เร็วกว่าสำหรับส่วนที่ต้องตรวจ request/response ขณะที่ Robot อ่านคล้ายภาษาธรรมชาติและแยก keyword ใน resource file ได้ดี Robot report/log แสดงแต่ละ keyword กับเวลาอย่างละเอียด ส่วน Cypress video และ screenshot ทำให้ย้อนดูพฤติกรรมทั้ง flow ได้ง่าย; รอบล่าสุดใช้ 11.669175 วินาทีใน Robot และ 18 วินาทีสำหรับ Cypress ทั้งชุด

5. สำหรับ Project 2 ฉันเลือก Cypress เหตุผลแรกคือ automatic retry และ `cy.intercept()` ช่วยลดการเขียน wait แบบ manual และตรวจทั้ง UI กับ API response ได้ใน test เดียว เหตุผลที่สองคือ Cypress สร้าง video และ screenshot ใน headless run ได้ทันที ทำให้มีหลักฐานที่เปิดดูและวิเคราะห์ failure ได้ง่าย ใน Lab นี้ full run ทั้งสองรอบผ่านครบ 2/2 specs และใช้เวลาเพียง 19 กับ 18 วินาที

6. ถ้า locator ของช่อง calories เปลี่ยน ฉันจะแก้เฉพาะตัวแปร `${MAX CALORIES}` ใน `robot/resources/quickpizza.resource` keyword ทุกตัวอ้าง locator ผ่านตัวแปรนี้ จึงไม่ต้องแก้ `pizza_scenario.robot` ที่อธิบายขั้นตอน QP-01 การรวม locator และ keyword ไว้ใน resource file ทำให้การซ่อมแซมเล็กลงและลดโอกาสแก้ไม่ครบหลายจุด

7. QP-01 ต้องอยู่ใน Cypress test เดียว เพราะ Cypress ล้าง cookies ก่อนเริ่มแต่ละ `it` และ QuickPizza ใช้ cookie เพื่อผูกผู้ใช้กับ server instance หากแยก Steps 6-7 เป็น test ที่สอง cookie เดิมจะหาย ทำให้หน้า admin อาจอ่านรายการจากคนละ instance และไม่พบพิซซ่าที่สร้างไว้ นอกจากนี้ alias `@tool` และตัวแปร `pizzaName` มีขอบเขตอยู่ใน test แรก จึงใช้เปรียบเทียบใน test ที่สองไม่ได้โดยตรง

