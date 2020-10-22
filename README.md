# DBMS Subject in UET

## 1. View 

#### 1.1 Create View

``` mysql
  CREATE VIEW view_name AS
   query
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
	CREATE PROCEDURE sp_name
	
		BEGIN
		SELECT * FROM products;
		# end procedure
		END //
	# Change end query default
	DELIMITER ;


	# Use procedure 
	CALL sp_name();

```
  
