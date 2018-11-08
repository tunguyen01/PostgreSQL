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

``` ```
service postgresql stop
```

Chúng ta sẽ thực hiện thay đổi các tệp tin postgresql tương tự với Master server, chuyển đến thư mục chứa các tệp tin cấu hình của PostgreSQL:
``` ```
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
rsync -cva --inplace --exclude=*pg_xlog* /var/lib/postgresql/version_of_postgresql/main/ slave_IP_address:/var/lib/postgresql/version_of_postgresql/main/
psql -c "select pg_stop_backup();"
```

Bây giờ chúng ta phải cấu hình một tệp khôi phục trên slave của chúng ta. Trên slave điều hướng đến thư mục dữ liệu:

``` ```
cd /var/lib/postgresql/version_of_postgresql/main
```

Ở đây, chúng ta cần phải tạo một tập tin phục hồi được gọi là ```Recovery.conf```:

``` 
vi recovery.conf
```

Điền vào các thông tin sau. Hãy chắc chắn thay đổi địa chỉ IP của Master slave của bạn và mật khẩu cho người dùng Rep bạn đã tạo:
