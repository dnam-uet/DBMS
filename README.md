# My note of DBMS Subject in UET
## Teacher's email: hanhdp@gmail.com

## 1. View 

#### 1.1 Create View

``` mysql
CREATE VIEW view_name AS sqlcode
```

#### 1.2 Drop view

```mysql
DROP VIEW view_name;
```
#### 1.3 Use view

Write query

``` mysql
SELECT data FROM view_name
```

## 2. Procedure

### Ưu điểm

  * Tăng hiệu năng của ứng dụng. Chỉ cần khởi tạo 1 lần, được lưu trong database. 
  * Chạy nhanh hơn câu lệnh SQL thường chưa được biên dịch
  * Giảm tải cho server, có tính tái sử dụng cao
  * Bảo mật do admin có thể cho phép các ứng dụng truy cập và lấy dữ liệu thông qua procedure mà k cấp quyền truy cập cơ sở dữ liệu
  
### Nhược điểm
  * Không thể chạy debug stored procedure trong DBMS 
  * Viết và bảo trì procedure thường cần có kỹ năng thật tốt và không phải dev nào cũng có khả năng này
  
#### 2.1 Cú pháp
```mysql
# Change end query default
DELIMITER //  
CREATE PROCEDURE sp_name()

	BEGIN
	SELECT * FROM products;
	# end procedure
	END //
# Change end query default
DELIMITER ;


# Use procedure 
CALL sp_name();

```
  
#### 2.2 Biến

##### 2.2.1 Khai báo

```mysql
DECLARE variable_name datatype(size) DEFAULT default_value;
```

*Ví dụ*

```mysql
DECLARE total_count INT DEFAULT 0;
SET total_count = 10;
```

```mysql
DECLARE total_products INT DEFAULT 0;
SELECT COUNT(*) INTO total_products
FROM products;
```

##### 2.2.2 Phạm vi biến
* Khi được khai báo trong procedure thì qua hàm END sẽ bị mất
* Biến có kí tự **@** đằng trước thể hiện biến session. Nó tồn tại cho đến khi kết thúc session

##### 2.2.3 Tham số

```mysql
MODE param_name param_type(param_size);
```
Có 3 mode 
> IN: mode mặc định. IN là mode thể hiện tham số được truyền và bên trong stored procedure không thể thay đổi
> OUT: mode thể hiện tham số truyền vào có thể thay đổi. Thường được dùng để lấy giá trị ra khỏi procedure nên khi truyền thường có **@** ở trước
> INOUT: mode kết hợp giữa IN và OUT. Thường để truyền tham số có tồn tại trước đó và thay đổi giá trị qua procedure

*Ví dụ*

```mysql
#IN MODE
DELIMITER $$
CREATE PROCEDURE GetOfficeByCountry(IN countryName VARCHAR(255))
    BEGIN
      SELECT city, phone
      FROM offices
      WHERE country = countryName;
   END $$
DELIMITER ;
CALL GetOfficeByCountry('USA') 

```

```mysql
#OUT MODE
DELIMITER $$
CREATE PROCEDURE CountOrderByStatus(
             IN orderStatus VARCHAR(25), OUT total INT)
BEGIN
  SELECT count(orderNumber) INTO total
  FROM orders
  WHERE status = orderStatus;
END$$
DELIMITER ;
```
```mysql
#CALL Procedure
CALL CountOrderByStatus('in  process',@total);
SELECT @total AS  total_in_process; 
```
```mysql
#INOUT MODE
DELIMITER $$
CREATE PROCEDURE Capitalize(INOUT str VARCHAR(1024))
BEGIN
  DECLARE i INT DEFAULT 1;
  DECLARE myc, pc CHAR(1);
  DECLARE outstr VARCHAR(1000) DEFAULT str;
  WHILE i <= CHAR_LENGTH(str) DO
      SET myc = SUBSTRING(str, i, 1);
      SET pc = CASE WHEN i = 1 THEN ' '
  		 ELSE SUBSTRING(str, i - 1, 1)
  	END;
      IF pc IN (' ', '&', '''', '_', '?', ';', ':', '!', ',', '-', '/', '(', '.') THEN
           SET outstr = INSERT(outstr, i, 1, UPPER(myc));
      END IF;
      SET i = i + 1;
  END WHILE;
  SET str = outstr;
END$$
DELIMITER ; 
```
Sử dụng Capitalize stored procedure
```mysql
SET @str = 'mysql stored procedure tutorial';
CALL Capitalize(@str);
SELECT @str;
```
#### 2.3 Câu lệnh điều kiện

Câu lệnh IF
```mysql
IF expression THEN commands
[ELSEIF expression THEN commands]
[ELSE commands]
END IF;
```

Câu lệnh CASE
```mysql
WHEN expression THEN commands
...
WHEN expression THEN commands
ELSE commands
END CASE;
```
#### 2.4 Vòng lặp

While
```mysql
WHILE expression DO
	statements
END WHILE
```
Repeat
```mysql
REPEAT
statements;
UNTIL expression
END REPEAT
```

#### 2.5 Hàm

Cú pháp

```mysql
CREATE FUNCTION name([parameterlist])
RETURNS datatype[options]
AS
BEGIN
	sqlcode 
	[RETURN]
END;
```

Xóa function
```mysql
DROP FUNCTION [IF EXISTS] name;
```

Ví dụ
```mysql
# increase Number by step Function
DELIMITER $$
DROP FUNCTION IF EXISTS increase $$
CREATE FUNCTION increase(_number INT)
RETURNS INT 
BEGIN 
    DECLARE step INT DEFAULT 0;
    SET step = 2;
    SET _number = _number + step;
    RETURN _number;
END $$
DELIMITER ;

SELECT increase(5);
```

Function và Procedure

||Procedure|Function|
|----------------|-------------------------------|----------------------------------|
|Thực thi| CALL satement | SQL satement: SELECT, UPDATE, ...|
|Giá trị trả về| Có thể một hoặc nhiều giá trị| Chỉ giá trị trả về thông qua RETURN|
|Tham số| IN, OUT, INOUT| Chỉ truyền tham trị|
|Sử dụng proc/function| Một proc có thể dùng proc hay function bên trong nó| Chỉ sử dụng function bên trong nó|
## 3. User
### 3.1 Create User
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
### 3.2 Revoke privileges
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
### 3.3 Drop user
*Để xoá user ta dùng*
```mysql
DROP USER ‘username’@‘your_ip’;
```
### 3.4 Show grants
*Để biết quyền gì bạn có để gán cho một MySQL*
```mysql
SHOW GRANTS FOR 'user_name'@'localhost';
```


## 4. Triggger
Khi có một event được thực hiện thì trigger tương ứng tự động được kích hoạt. Điều này khác với Store Procedure khi ta cần call procedure bằng lệnh để kích hoạt nó

###  4.1. Create trigger

*Cú pháp*

```mysql
CREATE TRIGGER trig_name trig_time trig_event
ON table_name
FOR EACH ROW
BEGIN

END
```

Các tham số được truyền vào
1. **Trig_time**: BEFORE, AFTER
2. **Trig_event**: INSERT, UPDATE and DELETE

*Mỗi một trigger chỉ có thể xử lý 1 event, nếu muốn xử lý nhiều event phải định nghĩa nhiều trigger*

MySQL cung cấp 2 keyword **OLD** và **NEW** cho phép việc viết trigger dễ dàng và hiệu quả hơn

*OLD: trỏ đến các cột đã tồn tại trước khi thay đổi dữ liệu thông qua trigger*
*NEW: trỏ đến các cột mới sau khi thay đổi dữ liệu thông qua trigger*


## 5. Backup and restore

### 5.1 Mức bảng

#### 5.1.1 Kiết xuất file

Ví dụ: Truy vấn bảng film
```mysql
SELECT * FROM film;
```

Lưu dữ liệu ra file film.txt
```mysql
SELECT * INTO OUTFILE 'film.txt'
[
	FIELDS
		# Ngăn cách giữa các trường bằng
		TERMINATED BY ','
		# 
		ESCAPED BY '*'
		# Bao đóng trong cặp kí tự nào
		ENCLOSED BY'"'
	LINES
		# Bắt đầu một dòng bằng
		STARTING BY '<TR><TD>'
		# Kết thúc một dòng bằng
		TERMINATED BY '</TD></TR>\n'
]
FROM film
```

*File film.txt sẽ lưu trong file mysql>data>sakila( database đang dùng )*

#### 5.1.2 Import file vào CSDL

**Khi kiết xuất dữ liệu ra như thế nào thì khi thêm vào phải có trường thứ tự trùng như vậy. FILE và LINE cũng như vậy**

Ví dụ: Load file film.txt vào bảng film1
```mysql
CREATE TABLE film1 LIKE film;
LOAD DATA INFILE 'film.txt' INTO TABLE film1
[
	FIELDS
		TERMINATED BY ','
		ESCAPED BY '*'
		ENCLOSED BY '"'
	LINES
		STARTING BY '<TR><TD>'
		TERMINATED BY '</TD></TR>\n'
]
```

### 5.2 Mức Database

**mysqldump - khi muốn sao lưu 1 cơ sở dữ liệu**

**Cú pháp**

```mysql
mysqldump [options] > file.sql
```

**Kiết xuất ra console**

`xampp>mysql>bin>mysqldump.exe -uroot -p sakila`

**Kiết xuất ra 1 file**

`xampp>mysql>bin>mysqldump.exe -uroot -p sakila > sakila.sql`
```mysql
mysqldump -u root -p database_name > database_name.sql
```

**Kiết xuất nhiều databases**

`mysqldump -u root -p [options] --databases database_name_a [database_name_b...] > databases_a_b.sql`

**Kiết xuất tất cả databases**

`mysqldump -u root -p [options] --all-databases`

*Options*
1. --no-create-info : tạo file chỉ chứa data, không chứa lệnh tạo bảng
2. --no-data : chỉ chứa các câu lệnh tạo bảng( tạo schema ), không chứa lệnh insert


### 5.3 File binlog

`
File binlog lưu lịch sử các truy vấn trong cơ sở dữ liệu
`
`
File binlog nằm trong thư mục data của mysql
`

*Kiểm tra database có dùng file binlog không*

```mysql
SHOW BINARY LOGS;
```
*Kiểm tra xem file mình đang chạy tại thời điểm này là file binlog nào*

```mysql
SHOW MASTER STATUS;
```

**Nếu chưa sử dụng mà muốn sử dụng thì:**

1. Bật file cấu hình my.ini
2. Tìm đến [mysqld]
3. Bỏ comment dòng **log-bin-mysql-bin** hoặc thêm chính nó vào file


**Khi sử dụng câu lệnh mysqldump dùng option --flush-log sẽ tạo ra một file binlog mới.
==> Khi khôi phục dữ liệu chỉ cần dùng file binlog mới + file đã export ra từ mysqldump**

**Đọc thông tin trong file bin log**

``
mysql\bin>mysqlbinlog.exe [path_of_file_bin_log] [>  name_of_file_export]
``

# 6. Con trỏ (Cursor)
*Con trỏ là một câu lệnh select, được định nghĩa trong phần khai báo trong MySQL*
Cú pháp
```mysql
DECLARE cursor_name CURSOR FOR  
Select statement;  
```

**Các tham số**
cursor_name: tên con trỏ

select_statement: Câu truy vấn
### Mở con trỏ
```mysql
Open cursor_name;  
```
### Nạp con trỏ
```mysql
FETCH [ NEXT [ FROM ] ] cursor_name INTO variable_list; 
```
**Tham số:**
cursor_name: tên con trỏ

variable_list: các biến, dấu phẩy, v.v. được lưu trữ trong một con trỏ cho tập kết quả
### Đóng con trỏ
```mysql
  Close cursor_name;  
```
*Ví dụ*
```mysql
DELIMITER $$
CREATE PROCEDURE getActor(OUT a_name VARCHAR(200) )
BEGIN
	DECLARE is_done INT DEFAULT 0;
    DECLARE act_cursor CURSOR FOR
    	SELECT first_name FROM actor;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET is_done = 1;
    OPEN act_cursor;
    get_list: LOOP
    FETCH act_cursor INTO a_name;
    IF( is_done) = 1 THEN
    LEAVE get_list;
    END IF;
    END LOOP get_list;
    CLOSE act_cursor;
END $$
DELIMITER ;
```
# 7. Handler
*Cú pháp*
```mysql
	
DECLARE <action> HANDLER FOR <condition_value> <statement>;
```
**Tham số**
action: Có thể nhận hai giá trị CONTINUE hoặc EXIT.
* CONTINUE: Nói với chương trình rằng khi lỗi xẩy ra hãy thực thi statement và tiếp tục.
* EXIT: Nói với chương trình rằng khi lỗi xẩy ra hãy thực thi statement và thoát khỏi khối lệnh cha.

condition_value: Mô tả các đặc điểm của lỗi mà khi lỗi phát sinh, bộ điều khiển này sẽ được hoạt động. Nó có thể là:
* Một mã lỗi cụ thể (là một con số), ví dụ:
  1062 là lỗi khi trèn thêm một bản ghi mà ID của nó đã tồn tại.
* Một chuỗi string có 5 ký tự (Mã SQLSTATE chuẩn), ví dụ:
  * HY000 là mã lỗi nói rằng ổ đĩa bị đầy.
  * HY001 là lỗi do tràn bộ nhớ.
* Một lớp lỗi thông dụng đã được MySQL đặt tên hoặc có thể do người dùng đặt tên, chẳng hạn:
  SQLWARNING: là các cảnh báo mà có mã chuẩn bắt đầu bởi '01'.
  NOTFOUND: là lớp các lỗi có mã chuẩn (SQLSTATE) bắt *đầu bởi '02'. Thường liên quan tới sử lý con trỏ.
  SQLEXCEPTION: Là lớp các lỗi mà có mã chuẩn không bắt đầu bởi '00', '01', '02'. Chú ý rằng mã bắt đầu bởi '00' là các thông báo thành công.