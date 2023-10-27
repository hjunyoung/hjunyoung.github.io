---
layout: single
title: 'Windows에서 WSL2 MySQL에 접속하기'
author_profile: true
date: 2023-10-27 00:00:00 +0900
categories: ['MySQL', 'WSL2']
published: true
toc: true
toc_label: 'Table of Contents'
toc_icon: 'list-ul'
toc_sticky: true
sidebar:
  nav: 'docs'
# sidebar:
#   - title: 'Title'
#     image: http://placehold.it/350x250
#     image_alt: 'image'
#     text: 'Some text here.'
#   - title: 'Another Title'
#     text: 'More text here.'
---

현재 WSL2를 사용하고 있어서 MySQL는 WSL2에 설치되어있다. 하지만 MySQL Workbench는 Windows host machine에 설치된 상태고, Windows에 깔린 IntelliJ에서 DB연동을 하려면 Window에서 WSL2에 설치된 MySQL에 접속할 수 있어야 했다. 

여러 방법을 찾다가 Youtube 영상을 보고 우여곡절 끝에 연결할 수 있었다. 나중에 컴퓨터 포맷하거나 다시 설정할 경우에 대비해 사용한 방법을 정리해 둔다.

<br>


# Windows에서 WSL2 MySQL에 접속하기

## 1. WSL2에 MySQL이 없다면 설치하기

**1-1. packages를 최신 상태로 업데이트**

```zsh
# packages update & upgrade
sudo apt-get update
sudo apt-get upgrade
``` 

<br>
 
**1-2. mysql-server 설치**

```shell
sudo apt-get install mysql-server
mysql --version
```


<br>
<br>

## 2. MySQL 설정하기

**2-1. mysql 시작 후 mysql server로 접속**

```zsh
sudo systemctl start mysql
sudo mysql
```

<br>
 
**2-2. root user password 설정**

  mysql을 설치할 때 설정한 경우 설정하지 않아도 된다.

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';

FLUSH PRIVILEGES;
exit;
```

<br>

**2-3. MySQL security를 높이기 위한 설정**

```sql
sudo mysql_secure_installation -p
```
위의 command 입력하고 MySQL 설치할 때 또는 2-2에서 설정한 password 입력

여러 setting을 할 수 있는 prompt가 뜨는데 아래와 같이 설정(필요에 따라 변경 가능)

```zsh
Change the password for root ? : n
Remove anonymous users? : y
Disallow root login remotely? : n
Remove test database and access to it? : n
Reload privilege tables now? : y
```

<br>
 
**2-4. Credential이 잘 동작하는지 확인**

```zsh
sudo mysql 
# ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
# 이런 ERROR가 나야 Credential이 잘 동작하는 것

sudo mysql -u root -p
# 위 command 입력 후 password 입력했을 때 server로 들어가면 잘 동작하는 것
```

<br>
<br>

## 3. MySQL의 default PORT 변경

Windows에 MySQL server가 없는 경우에는 WSL2에 있는 MySQL의 PORT를 변경하지 않아도 된다.

Host machine(Window)에도 MySQL server가 있는 경우에, 동시에 두 개의 MySQL server를 열기 위해서 WSL2 MySQL의 PORT를 바꿔주는 거다.


**3-1. my.cnf 파일을 열기**

```shell
sudo nano /etc/mysql/my.cnf
```

**3-2. 마지막 줄에서 PORT 변경**
```shell
[mysqld]
port = 33061 # 변경하고 싶은 PORT 입력 
```

서버 재시작
```zsh
sudo systemctl restart mysql

```


<br>
<br>


## 4. 연결확인 하기

**4-1. Windows Workbench에서 연결**

![image](https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/9a3fd713-c952-4d59-8936-0e875fca59fd)

<br>

**4-2. Windows IntelliJ에서 연결**

![image](https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/4938f75a-da27-4e63-a7a7-3e53da59e0e2)



<br>
<br>

# Reference
[Connect WSL's MySQL from Windows Host Machine](https://www.youtube.com/watch?v=DBsyCk2vZw4)