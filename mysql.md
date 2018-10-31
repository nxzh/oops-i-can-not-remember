
## User Management

### create user
`CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';`
`CREATE USER 'user'@'%' IDENTIFIED BY 'password';`

### update password
update mysql.user set authentication_string=password('new_password') where user='user';

### delete user
delete from user where User='user' and Host='localhost';