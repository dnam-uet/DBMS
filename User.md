## 1.User
### 1.1 Create User
*Bắt đầu với việc truy cập mysql bằng lệnh*
```mysql
mysql -u root -p password
```
*Tiếp đó*
``` mysql
CREATE USER 'username'@'Your IP' IDENTIFIED BY 'password';
```
*Lúc này username chưa có quyền làm gì. Cần phân quyền,để gán toàn quyền của db cho username, ta dùng*
```mysql
GRANT ALL PRIVILEGES ON * . * TO 'username'@'Your IP;
```
*Để thay đổi được thực hiện ngay lập tức, hãy dùng lệnh sau*
```mysql
FLUSH PRIVILEGES;
```
*user mới đã có toàn quyền như là user root*
#### Gán một số quyền nhất định cho MySQL user
```mysql
GRANT [permission type] ON [database name].[table name] TO ‘non-root’@'localhost’;
```
Các permission type
> CREATE – Cho phép user tạo databases/tables
> SELECT – Cho phép user truy xuất data
> INSERT – Cho phép user tạo thêm dòng trong bảng
> UPDATE – Cho phép user chỉnh sửa các entry trong bảng
> DELETE – Cho phép user xóa entry trong bảng
> DROP – Cho phép user xóa hoàn toàn bảng/database
> ALL PRIVILEGES – Cho phép một người dùng MySQL truy cập vào một cơ sở dữ liệu được chỉ định (hoặc nếu không có cơ sở dữ liệu nào được chọn, qua hệ thống)
> GRANT OPTION – Cho phép họ cấp hoặc xoá các đặc quyền của người dùng khác


*Ví dụ*
```mysql
CREATE USER 'newusername'@'localhost' IDENTIFIED BY 'password';
GRANT SELECT ON sakila.* TO 'newusername'@'localhost';
FLUSH PRIVILEGES;
```
*Nếu bạn muốn xóa quyền truy cập của một user có thể dùng*
```mysql
REVOKE [type of permission] ON [database name].[table name] FROM ‘[username]’@‘your_ip’;
```
*Ví dụ*
```mysql
REVOKE SELECT ON sakila.* FROM ‘newusername’@‘localhost’;
```
*Mỗi lần bạn cập nhật hoặc thay đổi sự cho phép chắc chắn sử dụng lệnh*
```mysql
Flush Privileges;
```
*Để xoá user ta dùng*
```mysql
DROP USER ‘username’@‘your_ip’;
```
*Để biết quyền gì bạn có để gán cho một MySQL*
```mysql
SHOW GRANTS FOR 'user_name'@'localhost';
```