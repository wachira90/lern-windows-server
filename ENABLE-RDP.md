#  Remote Desktop บน Windows Server 2019

วิธีเปิดใช้งาน Remote Desktop บน Windows Server 2019 ให้ทำตามขั้นตอนการแก้ไขด่วนตามวิธีด้านล่างนี้ (หากปุ่มตั้งค่าเป็นสีเทา กดไม่ได้ แสดงว่าโดนล็อกด้วย Group Policy)
## วิธีที่ 1: เปิดผ่าน Server Manager (หน้าต่าง GUI)

   1. เปิด Server Manager
   2. คลิก Local Server ที่เมนูด้านซ้าย
   3. คลิกคำว่า Disabled ที่อยู่ข้างๆ หัวข้อ Remote Desktop
   4. ติ๊กเลือก Allow remote connections to this computer
   5. คลิก OK

## วิธีที่ 2: เปิดผ่าน PowerShell
หากวิธีแรกกดไม่ได้ ให้ใช้คำสั่งระบบสั่งเปิดโดยตรง:

   1. คลิกขวาที่ PowerShell เลือก Run as Administrator
   2. พิมพ์คำสั่งรันระบบ: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 0 แล้วกด Enter
   3. พิมพ์คำสั่งเปิดสิทธิ์: Enable-NetFirewallRule -DisplayGroup "Remote Desktop" แล้วกด Enter

## วิธีที่ 3: เปิดพอร์ตที่ Firewall (Port 3389)
ระบบต้องการพอร์ต 3389 ในการเชื่อมต่อ หากเปิดแล้วยังเข้าไม่ได้ให้เช็กไฟร์วอลล์:

   1. ไปที่ Server Manager
   2. เลือก Tools > Windows Firewall with Advanced Security
   3. คลิก Inbound Rules ที่เมนูด้านซ้าย
   4. หาหัวข้อ Remote Desktop - User Mode (TCP-In)
   5. คลิกขวาแล้วเลือก Enable Rule

## วิธีที่ 4: ปลดล็อกผ่าน Group Policy
หากระบบล็อกไม่ให้แก้ไขตั้งค่า ให้เข้าไปปลดล็อกดังนี้:

   1. กดปุ่ม Windows + R พิมพ์ gpedit.msc แล้วกด Enter
   2. ไปตามโฟลเดอร์นี้: Computer Configuration > Administrative Templates > Windows Components > Remote Desktop Services > Remote Desktop Session Host > Connections
   3. หาไฟล์ชื่อ Allow users to connect remotely by using Remote Desktop Services
   4. ดับเบิ้ลคลิก แล้วเปลี่ยนสถานะเป็น Enabled จากนั้นกด OK

