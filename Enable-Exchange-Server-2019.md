# Microsoft Exchange Server 2019 บน Windows Server 2019

การติดตั้งและเปิดใช้งาน Microsoft Exchange Server 2019 บน Windows Server 2019 ต้องเริ่มต้นด้วยการนำเซิร์ฟเวอร์เข้าร่วมโดเมน Active Directory (ระบบจัดการผู้ใช้และคอมพิวเตอร์ในเครือข่าย) จากนั้นให้เมานต์ (Mount) ไฟล์ ISO ของ Exchange ติดตั้งฟีเจอร์พื้นฐานของ Windows และรันตัวติดตั้งเพื่อเปิดใช้งานบทบาท Mailbox role
ขั้นตอนการเตรียมระบบและการติดตั้ง มีดังนี้:
## 1. เตรียมเซิร์ฟเวอร์
เซิร์ฟเวอร์ของคุณต้องเข้าร่วม (Join) โดเมนภายในของคุณเรียบร้อยแล้ว จากนั้นให้ลงชื่อเข้าใช้งานด้วยบัญชีที่เป็นสมาชิกของกลุ่ม Schema Admins, Enterprise Admins และ Organization Management
## 2. ติดตั้งโปรแกรมจำเป็น (Prerequisites)
Exchange จำเป็นต้องใช้ฟีเจอร์บางอย่างของ Windows เช่น .NET Framework และ Internet Information Services (IIS)
ให้เปิด Windows PowerShell ด้วยสิทธิ์ผู้ดูแลระบบ (Run as Administrator) แล้วรันคำสั่งนี้เพื่อติดตั้งฟีเจอร์ที่จำเป็น:

```powershell
Install-WindowsFeature Server-Media-Foundation, RSAT-ADDS
```

## 3. เมานต์ไฟล์และติดตั้ง Exchange

   1. คลิกขวาที่ไฟล์ ISO ของ Exchange Server 2019 ที่คุณดาวน์โหลดมา แล้วเลือก Mount
   2. เปิดไดรฟ์ที่เมานต์ขึ้นมา แล้วดับเบิลคลิกไฟล์ Setup.exe
   3. ในหน้าจอ Check for Updates ให้เลือกว่าต้องการดาวน์โหลดตัวอัปเดตล่าสุดหรือไม่
   4. ในหน้าจอ Server Role Selection ให้ทำเครื่องหมายถูกที่ช่อง Mailbox role และทำเครื่องหมายถูกที่ช่อง Automatically install Windows Server roles and features that are required ด้วย
   5. กดทำตามขั้นตอนในตัวช่วยติดตั้งไปจนจบ แล้วคลิก Install

## 4. การตั้งค่าหลังติดตั้ง
เมื่อติดตั้งเสร็จสิ้น คุณต้องกำหนดเส้นทางการรับส่งอีเมล (Mail Flow) ดังนี้:

   1. เปิดเว็บเบราว์เซอร์แล้วเข้าไปที่ Exchange Admin Center (EAC) โดยพิมพ์ URL: https://localhost/ecp
   2. ลงชื่อเข้าใช้งานด้วยบัญชีผู้ดูแลระบบ (Administrator)
   3. ไปที่เมนู mail flow > send connectors เพื่อสร้างตัวเชื่อมต่อ (Connector) ที่อนุญาตให้เซิร์ฟเวอร์ของคุณสามารถส่งอีเมลออกไปยังอินเทอร์เน็ตได้

คุณสามารถศึกษาข้อกำหนดเครือข่ายและฮาร์ดแวร์อย่างละเอียดเพิ่มเติมได้ที่คู่มือ [Exchange Server 2019 System Requirements](https://learn.microsoft.com/en-us/exchange/plan-and-deploy/system-requirements) บนแพลตฟอร์ม [Microsoft Learn](https://learn.microsoft.com/)
หากคุณต้องการตั้งค่าเพิ่มเติม สามารถแจ้งข้อมูลเครือข่ายของคุณเพื่อให้ผมช่วยแนะนำในเรื่องเหล่านี้ได้:

* การตั้งค่า Send and Receive Connectors (ตัวเชื่อมต่อรับ-ส่งอีเมล)
* การติดตั้ง SSL Certificates เพื่อความปลอดภัยในการเชื่อมต่อ
* การย้ายข้อมูลผู้ใช้จาก Exchange เวอร์ชันเก่า

# Enable User

หากต้องการเปิดใช้งานกล่องจดหมาย (Mailbox) บนระบบ Microsoft Exchange Server คุณต้องมีบัญชี Active Directory ของผู้ใช้รายนั้นก่อน จากนั้นจึงทำการกำหนดสิทธิ์กล่องจดหมาย โดยสามารถทำได้ 2 วิธีหลักๆ ดังนี้
## วิธีที่ 1: ใช้ Exchange Management Shell (PowerShell)

   1. เปิด Exchange Management Shell ด้วยสิทธิ์ผู้ดูแลระบบ (Administrator)
   2. สำหรับผู้ใช้เดิมที่มีอยู่แล้ว ให้รันคำสั่ง:
   Enable-Mailbox -Identity "Username" -Alias "useralias"
   3. สำหรับสร้างผู้ใช้ใหม่พร้อมกล่องจดหมาย ให้รันคำสั่ง:
   New-Mailbox -UserPrincipalName user@domain.com -Alias "useralias" -Database "Mailbox Database"

## วิธีที่ 2: ใช้ Exchange Admin Center (EAC) ผ่านเว็บเบราว์เซอร์

   1. เปิดเว็บเบราว์เซอร์แล้วไปที่ URL ของ Exchange Admin Center (เช่น https://ชื่อเซิร์ฟเวอร์ของคุณ/ecp)
   2. เข้าสู่ระบบด้วยบัญชีผู้ดูแลระบบ (Administrator)
   3. ไปที่เมนูด้านซ้าย คลิก recipients (ผู้รับ) แล้วเลือก mailboxes (กล่องจดหมาย)
   4. คลิกไอคอน + (บวก) แล้วเลือก User mailbox
   5. เลือก Existing user (หากผู้ใช้มีบัญชี Active Directory อยู่แล้ว) หรือเลือก New user (หากต้องการสร้างใหม่)
   6. กรอกข้อมูลให้ครบถ้วนแล้วคลิก Save (บันทึก)

หลังจากเปิดใช้งานกล่องจดหมายแล้ว อย่าลืมตรวจสอบการตั้งค่า DNS records ภายนอก (เช่น MX Record และ Autodiscover) ให้ชี้มาที่ Public IP ของ Exchange Server เพื่อให้ระบบอีเมลภายนอกสามารถส่งข้อมูลหาคุณได้ถูกต้อง
หากต้องการให้แนะนำขั้นตอนอย่างเจาะจง สามารถบอกข้อมูลเพิ่มเติมได้:

* คุณกำลังใช้ Exchange Server (ติดตั้งในองค์กร) หรือ Exchange Online (Microsoft 365)?
* คุณกำลังตั้งค่าให้ ผู้ใช้ใหม่ หรือ ผู้ใช้เดิมที่มีบัญชีอยู่แล้ว?
* พบปัญหาเกี่ยวกับ การรับส่งเมลภายใน หรือ การเชื่อมต่อจากภายนอก (DNS)?

ผมสามารถให้คำสั่งหรือขั้นตอนที่ละเอียดตรงกับหน้างานของคุณได้ทันที


