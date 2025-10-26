# BÀI TẬP VỀ NHÀ 2:
# Đề Bài:
2.1. Cài đặt Apache web server:
- Vô hiệu hoá IIS: nếu iis đang chạy thì mở cmd quyền admin để chạy lệnh: iisreset /stop
- Download apache server, giải nén ra ổ D, cấu hình các file:
  + D:\Apache24\conf\httpd.conf
  + D:Apache24\conf\extra\httpd-vhosts.conf
  để tạo website với domain: fullname.com
  code web sẽ đặt tại thư mục: `D:\Apache24\fullname` (fullname ko dấu, liền nhau)
- sử dụng file `c:\WINDOWS\SYSTEM32\Drivers\etc\hosts` để fake ip 127.0.0.1 cho domain này
  ví dụ sv tên là: `Đỗ Duy Cốp` thì tạo website với domain là fullname ko dấu, liền nhau: `doduycop.com`
- thao tác dòng lệnh trên file `D:\Apache24\bin\httpd.exe` với các tham số `-k install` và `-k start` để cài đặt và khởi động web server apache.
2.2. Cài đặt nodejs và nodered => Dùng làm backend:
- Cài đặt nodejs:
  + download file `https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi`  (đây ko phải bản mới nhất, nhưng ổn định)
  + cài đặt vào thư mục `D:\nodejs`
- Cài đặt nodered:
  + chạy cmd, vào thư mục `D:\nodejs`, chạy lệnh `npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered"`
  + download file: https://nssm.cc/release/nssm-2.24.zip
    giải nén được file nssm.exe
    copy nssm.exe vào thư mục `D:\nodejs\nodered\`
  + tạo file "D:\nodejs\nodered\run-nodered.cmd" với nội dung (5 dòng sau):
@echo off
REM fix path
set PATH=D:\nodejs;%PATH%
REM Run Node-RED
node "D:\nodejs\nodered\node_modules\node-red\red.js" -u "D:\nodejs\nodered\work" %*
  + mở cmd, chuyển đến thư mục: `D:\nodejs\nodered`
  + cài đặt service `a1-nodered` bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"
  + chạy service `a1-nodered` bằng lệnh: `nssm start a1-nodered`
2.3. Tạo csdl tuỳ ý trên mssql (sql server 2022), nhớ các thông số kết nối: ip, port, username, password, db_name, table_name
2.4. Cài đặt thư viện trên nodered:
- truy cập giao diện nodered bằng url: http://localhost:1880
- cài đặt các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus
- Sửa file `D:\nodejs\nodered\work\settings.js` : 
  tìm đến chỗ adminAuth, bỏ comment # ở đầu dòng (8 dòng), thay chuỗi mã hoá mật khẩu bằng chuỗi mới
    adminAuth: {
        type: "credentials",
        users: [{
            username: "admin",
            password: "chuỗi_mã_hoá_mật_khẩu",
            permissions: "*"
        }]
    },   
   với mã hoá mật khẩu có thể thiết lập bằng tool: https://tms.tnut.edu.vn/pw.php
- chạy lại nodered bằng cách: mở cmd, vào thư mục `D:\nodejs\nodered` và chạy lệnh `	`
  khi đó nodered sẽ yêu cầu nhập mật khẩu mới vào được giao diện cho admin tại: http://localhost:1880
	
- tại flow1 trên nodered, sử dụng node `http in` và `http response` để tạo api
- thêm node `MSSQL` để truy vấn tới cơ sở dữ liệu
- logic flow sẽ gồm 4 node theo thứ tự sau (thứ tự nối dây): 
  1. http in  : dùng GET cho đơn giản, URL đặt tuỳ ý, ví dụ: /timkiem
  2. function : để tiền xử lý dữ liệu gửi đến
  3. MSSQL: để truy vấn dữ liệu tới CSDL, nhận tham số từ node tiền xử lý
  4. http response: để phản hồi dữ liệu về client: Status Code=200, Header add : Content-Type = application/json
  có thể thêm node `debug` để quan sát giá trị trung gian.
- test api thông qua trình duyệt, ví dụ: http://localhost:1880/timkiem?q=thị
2.6. Tạo giao diện front-end:
- html form gồm các file : index.html, fullname.js, fullname.css
  cả 3 file này đặt trong thư mục: `D:\Apache24\fullname`
  nhớ thay fullname là tên của bạn, viết liền, ko dấu, chữ thường, vd tên là Đỗ Duy Cốp thì fullname là `doduycop`
  khi đó 3 file sẽ là: index.html, doduycop.js và doduycop.css
- index.html và fullname.css: trang trí tuỳ ý, có dấu ấn cá nhân, có form nhập được thông tin.
- fullname.js: lấy dữ liệu trên form, gửi đến api nodered đã làm ở bước 2.5, nhận về json, dùng json trả về để tạo giao diện phù hợp với kết quả truy vấn của bạn.
2.7. Nhận xét bài làm của mình:
- đã hiểu quá trình cài đặt các phần mềm và các thư viện như nào?
- đã hiểu cách sử dụng nodered để tạo api back-end như nào?
- đã hiểu cách frond-end tương tác với back-end ra sao?
# __________________________________________________________________________________________________________________________________

# Bài làm: 
2.1. Cài đặt Apache web server:
 - Vô hiệu hóa IIS để dùng Apache thay cho IIS:
   <img width="1402" height="1014" alt="image" src="https://github.com/user-attachments/assets/c924bd16-177c-4b06-81e2-1f65fc08baff" />
- Download apache server qua link: https://www.apachelounge.com/download/ , giải nén ra ổ G:
  <img width="1175" height="622" alt="image" src="https://github.com/user-attachments/assets/08950488-7451-43d4-b1a4-e9209741bc71" />
  + Cấu hình file G:\Apache24\conf\httpd.conf:
    <img width="329" height="65" alt="image" src="https://github.com/user-attachments/assets/ae9488ee-4727-4f61-b790-d91192d7b54e" />
    <img width="357" height="79" alt="image" src="https://github.com/user-attachments/assets/b2ef6e9a-c98e-4f41-8d33-a213d91c6177" />
    <img width="549" height="36" alt="image" src="https://github.com/user-attachments/assets/23405392-29ff-43de-a0ed-e823e25ea450" />
    <img width="411" height="105" alt="image" src="https://github.com/user-attachments/assets/311bad6b-1b1d-46b1-893d-79dfca2dbd90" />
    <img width="727" height="195" alt="image" src="https://github.com/user-attachments/assets/99ec6d80-141e-4243-93a0-f762456142e0" />
    <img width="550" height="116" alt="image" src="https://github.com/user-attachments/assets/dacb9fe9-edf8-4729-885c-0a2d10e9d75c" />
  + Cấu hình file D:Apache24\conf\extra\httpd-vhosts.confđể tạo website với domain: lyvankien.com:
    <img width="969" height="565" alt="image" src="https://github.com/user-attachments/assets/84d0a22c-f9ed-4684-bcb3-a80c03efc62b" />

  + Code web đặt tại vị trí:
    <img width="907" height="277" alt="image" src="https://github.com/user-attachments/assets/79bda5ea-0c7c-43b5-a845-b0173d83fe65" />
  + sử dụng file `c:\WINDOWS\SYSTEM32\Drivers\etc\hosts` để fake ip 127.0.0.1 cho domain này:
    <img width="1019" height="657" alt="image" src="https://github.com/user-attachments/assets/bf6fc6bf-5622-4232-831a-921cc21c2955" />

- Cài Đặt và Khởi động web server Apache qua CMD trên D:\Apache24\bin\httpd.exe:
    <img width="909" height="152" alt="image" src="https://github.com/user-attachments/assets/757ee342-f601-495c-b50b-6229497a8d56" />
- Test domain trên trình duyệt:
<img width="862" height="443" alt="image" src="https://github.com/user-attachments/assets/0f3e5a8f-85a5-4d23-9ddb-024a42cedbab" />

2.2. Cài đặt nodejs và nodered => Dùng làm backend:

- Cài đặt nodejs vào ổ G theo link: https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi
<img width="1113" height="511" alt="image" src="https://github.com/user-attachments/assets/0c8b6437-9d74-4b38-bb51-abcd217cc0ac" />

- Cài đặt node red bằng lênh cmd: npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered"
  <img width="946" height="461" alt="image" src="https://github.com/user-attachments/assets/c7925d47-5042-40cc-bc74-256359cd1bf7" />

- Dowload và giải nén file nssm.exe vào thư mục`G:\nodejs\nodered\`
  <img width="961" height="517" alt="image" src="https://github.com/user-attachments/assets/9b4856ac-ce31-4ebf-a988-4afbb46411b0" />
- Tạo file "D:\nodejs\nodered\run-nodered.cmd" và thêm nội dung:
  <img width="1092" height="389" alt="image" src="https://github.com/user-attachments/assets/a4e99ca0-5972-401e-a28e-ee55d4e74c43" />


- Cài đặt service a1-nodered:
  <img width="835" height="127" alt="image" src="https://github.com/user-attachments/assets/3241136e-d4b1-463c-9070-66f166c14021" />

- Chạy service:
  <img width="618" height="108" alt="image" src="https://github.com/user-attachments/assets/f9921879-d17a-42d3-bb8f-d3b927a6622f" />

2.3. Tạo csdl: Quản lí thành viên trong gia đình
<img width="369" height="267" alt="image" src="https://github.com/user-attachments/assets/c80c242e-94f9-451e-a901-cfb26953682d" />


2.4. Cài đặt thư viện trên nodered:
- Truy cập: http://localhost:1880
- Sau khi vào được gia diện nodered, tải các thư viện theo các bước:
  <img width="627" height="513" alt="image" src="https://github.com/user-attachments/assets/706eb34e-ecce-452f-b2ab-ddf42f01634c" />
  + Install các thư viện:
    <img width="663" height="839" alt="image" src="https://github.com/user-attachments/assets/696e9a4e-7ba1-4cb2-a0d7-b7b241dbb787" />
- Sửa file `D:\nodejs\nodered\work\settings.js` để truy cập nodered cần đăng nhập và đăng nhập với giao diện admin:
  <img width="1113" height="729" alt="image" src="https://github.com/user-attachments/assets/ef8aa88d-fe4b-4d8d-961c-c2548f692a5d" />
  <img width="1004" height="220" alt="image" src="https://github.com/user-attachments/assets/52bb1885-e857-49f3-b459-8f53f65aebda" />

- Chạy lại nodered bằng lệnh cmd và truy cập lại url nodered nhập tài khoản và mật khẩu
  <img width="603" height="132" alt="image" src="https://github.com/user-attachments/assets/86858a58-a38d-4394-8151-1c28dcbf1596" />
<img width="1644" height="873" alt="image" src="https://github.com/user-attachments/assets/22fc6b77-758e-4c3a-9425-f8952bb453ce" />


2.5. Tạo giao diện Frontend:
<img width="1615" height="762" alt="image" src="https://github.com/user-attachments/assets/1af58fc1-9161-44fa-b651-506a45d59061" />

2.6. tạo api back-end bằng nodered:
<img width="962" height="509" alt="image" src="https://github.com/user-attachments/assets/e69754f2-ac97-4fb5-9171-2e8c49881cd1" />

2.7. Nhận xét bài làm của mình:
- Đâ hiểu các bước cài đặt và sử dụng Apache và Node red qua Nodejs
- Biết các thư viện trên nodered
-  Đã hiểu Cách frontend tương tác với backend thông qua API gửi đi từ mssql nodered và API nhân vào từ js 



  
  





  









  

