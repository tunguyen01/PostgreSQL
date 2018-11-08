## Tìm hiểu về  PostgreSQL replication

> Tài liệu: Viết tài liệu với PostgreSQL replication

Master server: 10.2.9.115

Slave server: 10.2.9.125

================

### Mục lục

#### 1. Cài đặt PostgreSQL

Sau khi đã cài đặt xong PostgreSQL, PostgreSQL tạo ra một người dùng là "postgres" để xử lý cơ sở dữ liệu ban đầu của nó. Ta sẽ cấu hình truy cập ssh giữa các máy chủ của mình để làm cho chuyển giao tập tin dễ dàng hơn.

Chúng ta sẽ cần phải đặt mật khẩu cho người dùng postgres để chúng ta có thể chuyển các tệp chính ban đầu(thực hiện trên cả 2 máy chủ). Nếu bạn mong muốn, bạn có thể gỡ bỏ mật khẩu sau một thời gian:
```
sudo passwd postgres
```
Tiếp đó, các cài đặt tiếp theo ta sẽ thực hiện dưới quyền của người dùng postgres. Chuyển sang người dùng postgres sử dụng lệnh:
```
sudo su - postgres
```
Tạo khóa SSH cho người dùng postgres:
```
ssh-keygen
```
Nhấn ```ENTER``` cho tất cả các lựa chọn tiếp theo.

Chuyển khóa này đến các máy chủ khác bằng cách gõ:
```
ssh-copy-id [IP_address_of_server]
```
Bây giờ bạn có thể tự do SSH tự do giữa 2 máy như là người dùng postgres.

#### 2. Cấu hình Master server

Chúng ta bắt đầu cấu hình Master server, tất cả các lệnh này nên đượ thực thi dưới quyền người dùng postgres.

Đầu tiên, ta sẽ tạo một người dùng tên là ```rep```, người dùng này có thể được sử dụng duy nhất của replication.
```
psql -c "CREATE USER rep REPLICATION LOGIN CONNECTION LIMIT 1 ENCRYPTED PASSWORD 'yourpassword';"
```
Giải thích: Chúng ta sử dụng công cụ psql để thực hiện các hành động tới máy chủ với tham sô  ```-c```.
* ```CREATE USER``` tạo một người dùng với tên "rep"
* ```LOGIN``` có thể truy cập
* ```REPLICATION``` có quyền thực thi replication
* ```CONNECTION LIMIT 1``` chỉ được 1 kết nối đồng thời
* ```ENCRYPTED``` mật khẩu được mã hóa. Thay đổi bất cứ mật khẩu nào mà bạn muốn.

Tiếp theo, chúng ta di chuyển đến thư mục cấu hình của postgres.
```
cd /etc/postgresql/version_of_postgresql/main
```
Đường dẫn phù thuộc vào phiển bản PostgreSQL mà bạn sử dụng.

Tiếp đó, ta sẽ thực hiện sửa đổi tệp tin truy cập với người dùng mà ta vừa tạo ở trên.

```
vi pg_hba.conf
```
Tại bất kỳ vị trí nào mà không phải là cuối dòng, thêm dòng dưới đây để người dùng mới có quyền truy cập vào máy chủ:

```
host    replication     rep     10.2.9.125/32   md5
```

Lưu lại và thoát khỏi tệp tin.

Sau đó, chúng ta sẽ thực hiện mở tệp tin cấu hình chính của PostgreSQL.

```
vi postgresql.conf
```

Tìm các tham số bên dưới, bỏ dấu # cho chúng nếu các tham số bị đánh dấu là chú thích và sửa đổi các tham số theo giá trị mà tôi để bên dưới:

```
listen_addresses = 'localhost,10.2.9.115'
wal_level = 'hot_standby'
archive_mode = on
archive_command = 'cd .'
max_wal_senders = 1
hot_standby = on
```

Lưu và thoát khỏi tệp tin.

Khởi động lại Master server để thực hiện các thay đổi.

```
service postgresql restart
```

#### 3. Cấu hình Slave server

Bắt đầu trên máy chủ slave bằng việc tắt phần mềm postgresql với lệnh:

```
service postgresql stop
```

Chúng ta sẽ thực hiện thay đổi các tệp tin postgresql tương tự với Master server, chuyển đến thư mục chứa các tệp tin cấu hình của PostgreSQL:

```
cd /etc/postgresql/version_of_postgresql/main
```

Điều chỉnh tệp truy cập để cho phép máy chủ khác kết nối tới máy chủ này. Đây là trong trường hợp chúng ta cần phải biến slave thành master sau này.

```
vi pg_hba.conf
```

Một lần nữa, thêm dòng này một nơi nào đó không phải ở phần cuối của tập tin:

```
host    replication     rep     10.2.9.115/32  md5
```

Lưu lại và thoát khỏi tệp tin.

Tiếp theo, mở tệp tin cấu hình postgresql:

```
vi postgresql.conf
```

Bạn có thể sử dụng các tùy chọn cấu hình giống như bạn đã đặt cho Master server, chỉ sửa đổi địa chỉ IP để phản ánh địa chỉ của Slave server:

```
listen_addresses = 'localhost,10.2.9.125'
wal_level = 'hot_standby'
archive_mode = on
archive_command = 'cd .'
max_wal_senders = 1
hot_standby = on
```

Lưu và thoát khỏi tệp tin.

#### 4. Sao chép cơ sở dữ liệu ban đầu

Trước khi slave có thể sao chép master, chúng ta cần phải cung cấp cho nó dữ liệu ban đầu để xây dựng. Điều này là do nó đọc các bản ghi tắt của máy chủ chủ và áp dụng các thay đổi cho cơ sở dữ liệu riêng của nó. Chúng ta cần cơ sở dữ liệu đó để phù hợp với cơ sở dữ liệu chủ.

Trên Master ser, chúng ta có thể sử dụng lệnh khởi động sao lưu nội bộ để tạo một lệnh sao lưu nhãn. Sau đó chúng ta sẽ chuyển dữ liệu cơ sở dữ liệu đến slave của chúng ta và sau đó đưa ra lệnh dừng sao lưu nội bộ để dọn sạch:

```
psql -c "select pg_start_backup('initial_backup');"
rsync -cva --inplace --exclude=*pg_xlog* /var/lib/postgresql/version_of_postgresql/main/ 10.2.9.125:/var/lib/postgresql/version_of_postgresql/main/
psql -c "select pg_stop_backup();"
```

Bây giờ chúng ta phải cấu hình một tệp khôi phục trên slave của chúng ta. Trên slave điều hướng đến thư mục dữ liệu:

```
cd /var/lib/postgresql/version_of_postgresql/main
```

Ở đây, chúng ta cần phải tạo một tập tin phục hồi được gọi là ```Recovery.conf``` :

```
vi recovery.conf
```

Điền vào các thông tin sau. Hãy chắc chắn thay đổi địa chỉ IP của Master slave của bạn và mật khẩu cho người dùng ```rep``` bạn đã tạo:

```
standby_mode = 'on'
primary_conninfo = 'host=10.2.9.115 port=5432 user=rep password=yourpassword'
trigger_file = '/tmp/postgresql.trigger.5432'
```

Dòng cuối cùng trong tệp, ```trigger_file```, là một trong những phần thú vị nhất của toàn bộ cấu hình. Nếu bạn tạo một tập tin tại vị trí đó trên Slave server của bạn, slave của bạn sẽ tự cấu hình lại để hoạt động như một Master.

Điều này sẽ phá vỡ sự sao chép hiện tại của bạn, đặc biệt nếu Master server vẫn đang chạy, nhưng đó là những gì bạn cần làm nếu Master server của bạn bị hỏng. Điều này sẽ cho phép Slave bắt đầu chấp nhận ghi. Sau đó bạn có thể sửa chữa Master server và biến nó thành Slave.

Bây giờ khởi động Slave server:

```
service postgresql start
```

Bạn sẽ muốn kiểm tra các bản ghi để xem có bất kỳ vấn đề. Chúng được đặt trên cả hai máy ở đây:

```
less /var/log/postgresql/postgresql-[version_of_postgresql]-main.log
```

Bạn sẽ thấy rằng nó được kết nối thành công với Master server.

#### 5. Kiểm tra Replication

Chúng ta sẽ thấy đầu tay nếu các máy chủ của chúng ta đang sao chép chính xác bằng cách thực hiện một số thay đổi trên Master server và sau đó truy vấn slave.

Trên Master server, là người dùng postgres, hãy đăng nhập vào hệ thống postgres bằng cách gõ:

```
psql
```

Lời nhắc của bạn sẽ thay đổi để cho biết rằng bạn hiện đang giao tiếp với phần mềm cơ sở dữ liệu giống như sau:

```
postgres@slave:~$ psql
psql (10.5 (Ubuntu 10.5-0ubuntu0.18.04))
Type "help" for help.

postgres=#
```

Chúng tôi sẽ tạo một bảng thử nghiệm để tạo ra một số thay đổi. Đầu tiên tạo một Database để tạo bảng trên đó:

```
postgres=#postgres=# \c test_replication
You are now connected to database "test_replication" as user "postgres".CREATE DATABASE test_replication;
CREATE DATABASE
```

Sau đó, kết nối tới cơ sở dữ liệu đã tạo bằng cách sử dụng tham số ```\c database_name``` để tạo một bảng thử nghiệm để tạo ra một số thay đổi:

```
postgres=# \c test_replication
You are now connected to database "test_replication" as user "postgres".
test_replication=# CREATE TABLE rep_test (test varchar(40));
CREATE TABLE
```

Bây giờ, ta có thể chèn một số giá trị vào bảng với các lệnh sau đây:

```
INSERT INTO rep_test VALUES ('data one');
INSERT INTO rep_test VALUES ('some more words');
INSERT INTO rep_test VALUES ('lalala');
INSERT INTO rep_test VALUES ('hello there');
INSERT INTO rep_test VALUES ('blahblah');
```

Bây giờ bạn có thể thoát ra khỏi giao diện này bằng cách gõ:

```
\q
```

Bây giờ, trên Slave server, vào giao diện cơ sở dữ liệu bằng cùng một cách:

```
psql
```

Bây giờ ta sẽ kiểm tra xem cơ sở dữ liệu đã tạo bên Master server có tồn tại hay không với lệnh ```\l``` :  

```
postgres=# \l
                                     List of databases
       Name       |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
------------------+----------+----------+-------------+-------------+-----------------------
 haha             | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 postgres         | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 template0        | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                  |          |          |             |             | postgres=CTc/postgres
 template1        | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                  |          |          |             |             | postgres=CTc/postgres
 test_replication | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
(5 rows)
```

Ta có thể thấy đã có database mà ta đã tạo bên master. Tiếp theo kết nối tới database để kiểm tra xem bảng cũng như các thông tin đã được thêm vào bên master. Để kiểm tra danh sách các bảng trong databse, ta sử dụng tham số ```\dt```.

```
postgres=# \dt
Did not find any relations.
postgres=# \c test_replication
You are now connected to database "test_replication" as user "postgres".
test_replication=# \dt
          List of relations
 Schema |   Name   | Type  |  Owner   
--------+----------+-------+----------
 public | rep_test | table | postgres
(1 row)

test_replication=# SELECT * FROM rep_test;
      test       
-----------------
 data one
 some more words
 lalala
 hello there
 blahblah
(5 rows)

test_replication=#
```

Tuyệt vời! Dữ liệu của chúng ta đã được viết cho cả hai máy chủ Master và Slave.

Hãy xem nếu chúng ta có thể chèn thêm dữ liệu vào bảng trên Slave của chúng ta:

```
test_replication=# INSERT INTO rep_test VALUES ('oops');
ERROR:  cannot execute INSERT in a read-only transaction
```

Như bạn thấy, chúng ta không thể chèn dữ liệu vào nô lệ. Điều này là do dữ liệu chỉ được chuyển giao theo một hướng. Để giữ cho các cơ sở dữ liệu phù hợp, postgres phải làm cho slave chỉ đọc.
