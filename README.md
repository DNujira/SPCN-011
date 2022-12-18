# SPCN-011
## สร้าง vm and ct on Proxmox cluster
## 1. create master vm (ubuntu-22.04)
* คลิกที่ create VM
![image](https://user-images.githubusercontent.com/117592447/208158433-d9339483-cb26-4260-9590-fbdc71d93947.png)

* ตั้งชื่อ VM
![image](https://user-images.githubusercontent.com/117592447/208159918-eaa92c8a-d685-4f27-905d-e62eee9386a7.png)

* เมื่อสร้างเสร็จจะได้ ดังรูป
![image](https://user-images.githubusercontent.com/117592447/208160225-648d238d-599e-45fd-a5fc-84eef400c646.png)

* **master VM import SSH identity from Github**
![image](https://user-images.githubusercontent.com/117592447/208159199-c84ca7be-76f7-486f-90ae-9a26a69772ad.png)

* **clone master vm to tamplate**
  * กดคลิกขวาที่ VM ที่เราสร้างไว้ และเลือก Convert to template
  
  ![image](https://user-images.githubusercontent.com/117592447/208160649-a76e9a55-1a7f-4e08-82f1-bc75ce7828e7.png)
  * จะได้ตัว VM เป็นแบบ template ดังรูป
  
  ![image](https://user-images.githubusercontent.com/117592447/208161586-96eb49f9-9032-4eea-970a-2a62d33f825a.png)
  
* **clone from template สร้าง vm ใหม่จำนวน 2 vm**
  * กดคลิกขวาที่ template และเลือก clone
  
  ![image](https://user-images.githubusercontent.com/117592447/208162140-b22ec824-0364-47c2-9c04-fbde23fd08a4.png)
  * ตั้งชื่อ VM ที่เรา clone ออกมาทั้ง 2 ตัว
  
  ![image](https://user-images.githubusercontent.com/117592447/208162345-868bbee9-feae-41b8-96f6-626760ca12cf.png)
  ![image](https://user-images.githubusercontent.com/117592447/208162555-444b4cc5-8d01-40b4-9822-9c69dee4e58b.png)
  
  * คำสั่งในการ change host name
  
        sudo hostnamectl set-hostname [ชื่อที่ต้องการเปลี่ยน]
        
  * คำสั่งในการ Set Time
  
        date                                            //เอาไว้เพื่อเช็ค local time ปัจจุบัน
        sudo timedatectl set-timezone Asia/Bangkok      //เปลี่ยน timezone ให้เป็น Bangkok
        
  * คำสั่งเกี่ยวกับ QEMU Guest Agent 
        
        sudo apt install qemu-guest-agent               //ติดตั้ง QEMU Guest Agent
        sudo systemctl start qemu-guest-agent           //เปิดใช้ QEMU Guest Agent
        sudo systemctl status qemu-guest-agent          //เช็คสถานะของ QEMU Guest Agent 
        
  * คำสั่งที่ใช้เปลี่ยน IP ของ VM ที่ clone มาทั้ง 2 ตัวเพื่อให้ IP ของ VM ไม่ซ้ำกัน
  
        sudo -i                                           ///root
        rm /var/lib/dbus/machine-id     
        nano /etc/machine-id                              //ลบข้อมูลทั้งหมดใน machine-id
        ln -s /etc/machine-id /var/lib/dbus/machine-id    //link ข้อมูลในไฟล์ machine-id
        reboot                                            //เพื่อให้ระบบ reboot
        
   * clone ตัวแรก (IP คือ 172.31.1.41)
   
   ![image](https://user-images.githubusercontent.com/117592447/208167149-41036442-a5e4-4967-b10c-8cbff4f7f3d4.png)
   
   * clone ตัวที่ 2 (IP คือ 172.31.1.94)
   
   ![image](https://user-images.githubusercontent.com/117592447/208167073-3604fed8-f00a-4313-896f-cca28b79e997.png)
   
## 2. create vm from other os 

  * คลิกที่ create VM และตั้งชื่อเป็น nuji-otherOS-180
  
  ![image](https://user-images.githubusercontent.com/117592447/208167681-b43ac352-bb58-4660-81d0-15a0da6c2125.png)
  
  * กดเลือก OS เป็นตัวอื่นที่ไม่ใช่ ubuntu-22.04 โดยในตัวอย่างจะเลือกเป็น debian-live-11.5.0-amd64-xfce.iso
  
  ![image](https://user-images.githubusercontent.com/117592447/208168116-29533914-6ccf-43c4-a1ac-ea46be01b861.png)
  
  * เปลี่ยน timezone ให้เป็น Bangkok
  
  ![image](https://user-images.githubusercontent.com/117592447/208168358-08eb146a-f6bf-4c38-b47b-1ca992ffafc9.png)
  
  * เปิดใช้งาน QEMU Guest Agent และจะได้ผลลัพธ์ดังรูป
  
  * คำสั่งในการเปิดใช้งาน Qemu Guest Agent
  
        sudo -i                                            //root
        apt update && apt -y install qemu-guest-agent      //เพื่อ update และ install QEMU Guest Agent
        systemctl enable qemu-guest-agent                  //เพื่อเปิดใช้งาน QEMU Guest Agent 
        systemctl start qemu-guest-agent                   //เพื่อเริ่มการทำงานของ QEMU Guest Agent
        systemctl status qemu-guest-agent                  //เช็คสถานะของ QEMU Guest Agent 
  
  ![image](https://user-images.githubusercontent.com/117592447/208302573-a0736731-6729-4016-b2ec-9fc03f3bae1b.png)
  
  ![image](https://user-images.githubusercontent.com/117592447/208302614-ccb0165c-3774-43c1-ae3f-f4249694a1c7.png)
  
## 3. create container template (select from CT list)

  * คลิกที่ Create CT
  
  ![image](https://user-images.githubusercontent.com/117592447/208170842-d965a121-a0db-42c1-914e-cd62993fdceb.png)
  
  * ตั้งชื่อ ใส่รหัสและ LOAD SSH Public Key File
  
  ![image](https://user-images.githubusercontent.com/117592447/208171069-bfd5c247-7a16-438a-ab88-c5e5b13eaac3.png)

  ![image](https://user-images.githubusercontent.com/117592447/208171041-abbe3e3b-a100-457b-934e-0e1d76ba717c.png)
  
  * เลือก templat เป็น ubuntu-22.04-standard
  
  ![image](https://user-images.githubusercontent.com/117592447/208171850-a7b736a4-b6c2-4e49-9bc8-5f601a5ca12b.png)

  
  * เลือก IPv4 เป็น DHCP และ IPv6 เป็น SLAAC ดังรูป
  
  ![image](https://user-images.githubusercontent.com/117592447/208171689-bdebb2f2-49ff-4651-8735-7d28db8e43a6.png)

  
  * เมื่อ Create เสร็จจะได้ผลลัพธ์ ดังรูป
  
  ![image](https://user-images.githubusercontent.com/117592447/208171458-2a097b74-38c9-4c31-8905-74210a7b019c.png)











      





