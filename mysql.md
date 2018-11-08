
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