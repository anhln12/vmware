# vmware

Mục tiêu:
- Có 2 host ESXi (ESXi1 và ESXi2)
- Mỗi host có các VM ví dụ: vm1 trên ESXi1, vm2 trên ESXi2
- vm1 và vm2 ping được nhau qua mạng LAN

[ vm1 ] --> ESXi-1 (NIC1) <--cable--> ESXi-2 (NIC1) --> [ vm2 ]

1. Kết nối vật lý giữa 2 ESXI

Ở đây mình dùng 1 cap mạng cắm trực tiếp từ NIC1 của ESXi1 sang NIC1 của ESXi2 (có thể thông qua 1 switch layer2 khi đó cần đảm bảo port kết nối nằm trong cùng subnet/VLAN)

![image](https://github.com/user-attachments/assets/2876bdf3-95cd-49ab-9be3-97a73def522a)

Sau khi cawms cap mạng bạn vào esxi sẽ ghi nhận trạng thái vmnic1 trên 2 ESXi không còn báo Link down

![image](https://github.com/user-attachments/assets/275fac7a-f2d1-4358-a17c-d587bd54986c)


2. Thiết lập vSwitch/Port Group giống nhau trên cả 2 ESXi

- Vào Networking -> Virtual Switches
- Tạo standard virtual switch-> Đặt tên & chọn Uplink lưu ý bạn có thể thêm nhiều Uplink để dự phòng
- Vào Port groups -> tạo port group -> đặt tên port group và chọn vSwitch

Mình thực hiện tạo qua command line
```
[root@localhost:~] esxcli network nic list
[root@localhost:~] esxcli network vswitch standard add --vswitch-name=vSwitch_LAN
[root@localhost:~] esxcli network vswitch standard uplink add --uplink-name=vmnic1 --vswitch-name=vSwitch_LAN
[root@localhost:~] esxcli network vswitch standard portgroup add --portgroup-name=PG_LAN --vswitch-name=vSwitch_LAN
```

![image](https://github.com/user-attachments/assets/5709bc4c-5067-4151-aa43-076f3a2f9f8e)

![image](https://github.com/user-attachments/assets/de76cf23-4e9b-4317-a4b3-e349388a2269)

![image](https://github.com/user-attachments/assets/08118472-71d8-4615-9781-a5ac5184bf6e)

![image](https://github.com/user-attachments/assets/2f7aa63e-6f74-4bd5-ab81-61668762bbe9)

3. Gám VM vào cùng port group
Ví dụ:
- vm1 (Trên ESX1): 10.1.10.2/24

![image](https://github.com/user-attachments/assets/2897811a-aab8-47a9-a259-222a14a67ae0)


- vm2 (Trên ESX2): 10.1.10.3/24

![image](https://github.com/user-attachments/assets/b3c54db2-5312-49c3-913c-1d140e64953c)


5. Kiểm tra kết nối

Trên vm1:

<img width="388" alt="image" src="https://github.com/user-attachments/assets/d1222e3e-be8d-4c37-96d1-f634441ada12" />

