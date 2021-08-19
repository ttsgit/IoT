## install
### Install the repository RPM:
sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/F-34-x86_64/pgdg-fedora-repo-latest.noarch.rpm

### Install PostgreSQL:
sudo dnf install -y postgresql13-server

### Optionally initialize the database and enable automatic start:
sudo /usr/pgsql-13/bin/postgresql-13-setup initdb
sudo systemctl enable postgresql-13
sudo systemctl start postgresql-13

## psql
安装完毕后，系统会创建一个数据库超级用户 postgres，密码为空。
```
#  sudo su - postgres
```
这时使用以下命令进入 postgres，输出以下信息，说明安装成功：
```
~$ psql
psql (9.5.17)
Type "help" for help.

postgres=# 
```
输入以下命令退出 PostgreSQL 提示符：
```
\q
```

## pgadmin4 install
pgadmin4应该是基于python flask框架，这边用rpm安装会和原来的pyhon环境冲突，采用python venv虚拟环境安装

```
$ sudo mkdir /var/lib/pgadmin
$ sudo mkdir /var/log/pgadmin
$ sudo chown $USER /var/lib/pgadmin
$ sudo chown $USER /var/log/pgadmin
$ python3 -m venv pgadmin4
$ source pgadmin4/bin/activate
(pgadmin4) $ pip install pgadmin4
...
(pgadmin4) $ pgadmin4
NOTE: Configuring authentication for SERVER mode.

Enter the email address and password to use for the initial pgAdmin user account:

Email address: user@domain.com
Password: 
Retype password:
Starting pgAdmin 4. Please navigate to http://127.0.0.1:5050 in your browser.
 * Serving Flask app "pgadmin" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: off
 ```


## pgamdin4 连接本地server

pgadmin4 连接需要密码
创建本地server密码
$ psql
$ \password


## pgadmin网页创建server
host name/address   localhost
port 5443
username postgres
password 刚新建的密码
