### Mysql

```bash
sudo systemctl start mysql.service

create user 'tiny_library'@'%' identified by 'Library_Tiny_240307';
create user 'cms'@'%' identified by 'CMS_System_240307';

grant all privileges on tiny_library to 'tiny_library'@'%';
grant all privileges on cms to 'cms'@'%';

FLUSH PRIVILEGES;
```

### nvm

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```