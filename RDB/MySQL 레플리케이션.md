# MySQL Replication 구성

## Master 및 Slave 설정

1. Master 설정

```yaml
[mysqld]
server-id = 1
log-bin = mysql-bin # 바이너리 로그의 경로 혹은 파일명을 지정 
```

* 바이너리 로그란
    * DB에 가해지는 변경사항(쓰기,수정,삭제) 기록이 별도로 저장되는 파일
    * master에만 생성하도록 하는게 일반적(slave에는 릴레이 로그로 저장)

* 릴레이 로그
    * slave가 생성하는 파일로 바이너리 로그를 통해 생성
    * 릴레이 로그를 사용해 slave는 master와 sync를 맞추게 된다. 
    
2. Slave 설정

```yaml
[mysqld]
server-id = 2
replicate-do-db='repl_db'
```

* server-id의 경우 master 혹은 다른 slave들과 중첩되지 않도록 한다.
* Slave는 2개의 thread로 동작하게 된다.
    1. IO thread : Master의 binary log를 읽어들여, Slave에 relay log를 작성하는 thread
    2. SQL thread : Relay log를 읽어서 DB에 저장하는 thread
    
## Replication(Slave) 권한 추가

1. 마스터 DB에 대한 Slave 들의 권한을 설정해 주어야 한다.

```sql
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'192.168.100.0' IDENTIFIED BY '비밀번호';
```
2. Master로부터 dump 떠서 적용
    1. 마스터 DB 중단
```sql
FLUSH TABLES WITH READ LOCK; # 글로벌 락
```
        
* 글로벌 락
    * MySQL에서 제공하는 락 중 가장 범위가 크다. 
    * SELECT를 제외한 DDL 혹은 DML은 대기상태로 남는다.
    * UNLOCK TABLES를 통해 락을 해제하며, 이후에 모든 쿼리들이 반영된다. 

2. 덤프 생성
```sql
mysqldump -u root -p --all-databases > dump.db
```    

3. Slave에 덤프 반영
```sql
mysql -u root -p < dump.db
```

4. 글로벌 락 해제
```sql
UNLOCK TABLES;	# 이후에 pending되었던 쿼리들이 반영된다.
```

## Master 지정(Slave에서)

```sql
CHANGE MASTER TO
master_host = '192.168.100.52',
master_user = 'repl',
master_password = 'test',
master_log_file = 'mysql-bin.000001',
master_log_pos = 730; # 로그 파일 내 읽을 위치, SHOW MASTER STATUS 명령을 통해 확인
```

이후에

```sql
START SLAVE # 중지 할 때는 STOP SLAVE
```

## Slave 상태 확인

```sql
SHOW SLAVE STATUS #마스터의 경우 SHOW MASTER STATUS
```

## 주의 사항
* Master보다 Slave가 높은 버전일 것(혹은 같은버전)
* Master, Slave 순으로 기동시킬 것