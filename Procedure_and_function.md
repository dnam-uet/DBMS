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
