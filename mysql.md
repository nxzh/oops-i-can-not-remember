## 事务相关

### Check AutoCommit mode

	SHOW VARIABLES LIKE 'AUTOCOMMIT';
	SET AUTOCOMMIT = 1;

### 修改隔离级别

	SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;

### 锁定

	SELECT ... LOCK IN SHARE MODE
	SELECT ... FOR UPDATE


## User Management

### create user

	CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';
	CREATE USER 'user'@'%' IDENTIFIED BY 'password';

### update password

	update mysql.user set authentication_string=password('new_password') where user='user';

### delete user

	delete from user where User='user' and Host='localhost';



### 打开 mysql general log 开关

    show variables like '%general%';
    set global general_log=on;    # 开启general log模式

### 查询表的状态

	SHOW TABLE STATUS LIKE 'table_name' \G;