## Tìm hiểu về  PostgreSQL

> Tài liệu: Viết tài liệu với PostgreSQL

================

### Mục lục


### 1. Giới thiệu PostgreSQL

PostgreSQL là một hệ thống quản trị cơ sở dữ liệu quan hệ-đối tượng (object-relational database management system) có mục đích chung, hệ thống cơ sở dữ liệu mã nguồn mở tiên tiến nhất hiện nay.

PostgreSQL được phát triển dựa trên POSTGRES 4.2 tại phòng khoa học máy tính Berkeley, Đại học California.

PostgreSQL được thiết kế để chạy trên các nền tảng tương tự UNIX. Tuy nhiên, PostgreSQL sau đó cũng được điều chỉnh linh động để có thể chạy được trên nhiều nền tảng khác nhau như Mac OS X, Solaris và Windows.

PostgreSQL là một phần mềm mã nguồn mở miễn phí. Mã nguồn của phần mềm khả dụng theo license của PostgreSQL, một license nguồn mở tự do. Theo đó, bạn sẽ được tự do sử dụng, sửa đổi và phân phối PostgreSQL dưới mọi hình thức.

PostgreSQL không yêu cầu quá nhiều công tác bảo trì bởi có tính ổn định cao. Do đó, nếu bạn phát triển các ứng dụng dựa trên PostgreSQL, chi phí sở hữu sẽ thấp hơn so với các hệ thống quản trị dữ liệu khác.

PostgreSQL đã tự đưa ra tuyên bố là "Cơ sở dữ liệu mã nguồn mở tiên tiến nhất thế giới".


#### 2. Vì sao nên sử dụng PostgreSQL

PostgreSQL sở hữu một hệ tính năng đa dạng giúp hỗ trợ các nhà phát triển xây dựng app, các nhà quản trị bảo vệ toàn vẹn dữ liệu, và tạo ra một môi trường chịu lỗi fault-tolerant giúp bạn quản lý dữ liệu bất kể tập dữ liệu lớn hay nhỏ. Bên cạnh hệ thống nguồn mở và miễn phí, PostgreSQL cũng có khả năng mở rộng tuyệt vời. Ví dụ, bạn có thể định nghĩa các kiểu dữ liệu riêng của bạn, xây dựng các hàm tùy chỉnh, hay viết mã từ các ngôn ngữ lập trình khác nhau mà không cần biên dịch lại cơ sở dữ liệu!

PostgreSQL tuân theo tiêu chuẩn SQL nhưng không mâu thuẫn với các tính năng truyền thống hay có thể dẫn đến các quyết định kiến trúc gây hại. Nhiều tính năng theo tiêu chuẩn SQL được hỗ trợ, tuy nhiên đôi khi có thể có cú pháp hoặc hàm hơi khác một chút.

#### 3. Các yếu tố giúp PostgreSQL nổi bật

PostgreSQL là hệ thống quản lý cơ sở dữ liệu đầu tiên triển khai tính năng kiểm soát đồng thời nhiều phiên bản (MVCC) trước cả Oracle. Tính năng MVCC cũng tương tự với các snapshot riêng biệt trong Oracle.

Là hệ thống quản lý cơ sở dữ liệu quan hệ đối tượng, PostgreSQL cho phép thêm vào các tính năng tùy chỉnh được phát triển bằng các ngôn ngữ chương trình khác nhau như C/C , Java,...

Bên cạnh đó, khi bạn có bất kỳ vấn đề gì cần hỗ trợ, luôn có một cộng đồng nhiệt tình sẵn sàng để trợ giúp. Bạn luôn có thể tìm thấy câu trả lời từ cộng đồng của PostgreSQL về các vấn đề có thể gặp phải khi làm việc với PostgreSQL. Ngoài ra, cũng có rất nhiều công ty cung cấp các dịch vụ hỗ trợ trong trường hợp bạn thực sự cần đến.

Trên thị trường, có nhiều công ty đã xây dựng và cung cấp các sản phẩm, giải pháp sử dụng PostgreSQL. Một số công ty nổi bật trong số đó là Apple, Fujitsu, Red Hat, Cisco, Juniper Network, v.v.

#### 4. Cài đặt PostgreSQL

Phần này sẽ hướng dẫn cài đặt PostgreSQL trên Ubuntu 18.04. Trước khi bắt đầu cài đặt, ta thực hiện cập nhật hệ thống để hệ thống cập nhật những gói mới nhất cho hệ thống để lúc cài đặt không bị thiếu cũng như xảy ra lỗi.

Để thực hiện cập nhật, ta chạy lệnh:
``` code
sudo apt update
```
Tiếp theo, để cài đặt PostgreSQL, ta cần cài đặt các gói phần mềm cần thiết. Ta chạy lệnh sau:
``` code
sudo apt install postgresql postgresql-contrib
```
Để cấu hình PostgreSQL chạy cùng với hệ thống lúc khởi động, ta chạy lệnh sau:
``` code
sudo update-rc.d postgresql enable
```
Khởi động động PostgreSQL chạy lệnh sau:

``` code
sudo service postgresql start
```

#### 5. PostgreSQL cơ bản

##### 5.1 Tải mẫu cơ sở dữ liệu của PostgreSQL

Đầu tiên, ta tải về một bản mẫu cơ sở dữ liệu của PostgreSQL để dùng cho việc thực hành các lệnh của PostgreSQL. Mẫu cơ sở dữ liệu bao gồm:
* 15 tables
* 1 trigger
* 7 views
* 8 functions
* 1 domain
* 13 sequences

Dưới đây là mô hình của cơ sở dữ liệu mẫu này.
<img src="https://imgur.com/WekNg0r.png">

Để tải mẫu cơ sở dữ liệu này chạy lệnh sau:
``` code
wget https://github.com/tunguyen01/PostgreSQL/raw/master/dvdrental.tar
```
Tiếp theo, ta thực hiện tạo cơ sở dữ liệu và thực hiện đấy cơ sở dữ liệu mẫu này vào. Đầu tiên, đăng nhập vào tài khoản postgres:

``` code
su - postgres
```
Sau đó, thực hiện đăng nhập vào cơ sở dữ liệu với lệnh ```psql```:
```
psql
```

Sau khi đã đăng nhập, ta tạo cơ sở dữ liệu để sau đó đẩy bản cơ sở dữ liệu mẫu PostgreSQl mà ta đã tải trước đó.

```
CREATE DATABASE dvdrental;
```

Sau khi chạy lệnh hoàn thành sẽ hiển thị như sau:

```
postgres=# CREATE DATABASE dvdrental;
CREATE DATABASE
```
Tiếp đó, ta sử dụng công cụ ```pg_restore``` để tải dữ liệu mẫu vào cơ sở dữ liệu dvdrental ta đã tạo trước đó.

```
pg_restore -U postgres -d dvdrental dvdrental.tar
```

Sau khi đã tải cơ sở dữ liệu mẫu, ta lại đăng nhập vào PostgreSQL với lệnh ```psql```. Và để có thể thực thi trên cơ sở dữ liệu ta đã tải lên trước đó,ta sử dụng lệnh: ``` \c <tên_CSDL> ```. Sau khi chạy lệnh sẽ có kết quả như sau:
```
postgres@master:~$ psql
psql (10.5 (Ubuntu 10.5-0ubuntu0.18.04))
Type "help" for help.

postgres=# \c dvdrental
You are now connected to database "dvdrental" as user "postgres".
dvdrental=#
```
##### 5.2 PostgreSQL cơ bản

###### 5.2.1 Truy vấn dữ liệu

* ```Select``` - chỉ cho bạn cách truy vấn dữ liệu từ một bảng.

* ```Sort by``` - hướng dẫn bạn cách sắp xếp tập kết quả được trả về từ truy vấn.

* ```Select Distinct``` - cung cấp cho bạn một mệnh đề loại bỏ các hàng trùng lặp trong tập hợp kết quả.

###### 5.2.2 Lọc dữ liệu

* ```Where``` - lọc các hàng dựa trên một điều kiện cụ thể.
* ```Limit``` - nhận một tập con các hàng được tạo bởi truy vấn.
* ```Fetch``` - giới hạn số hàng được truy vấn trả về.
* ```In``` - chọn dữ liệu khớp với bất kỳ giá trị nào trong danh sách giá trị.
* ```Between``` - chọn dữ liệu là một phạm vi giá trị.
* ```Like``` - lọc dữ liệu dựa trên kết hợp mẫu.

###### 5.2.3 Tham gia nhiều bảng

* ```Inner Join``` - chọn các hàng từ một bảng có các hàng tương ứng trong các bảng khác.
* ```Left Join``` - chọn các hàng từ một bảng có thể có hoặc không có các hàng tương ứng trong các bảng khác.
* ```Self-join``` - tự mình tham gia một bảng bằng cách so sánh bảng với chính nó.
* ```Full Outer Join``` - sử dụng tham gia đầy đủ để tìm một hàng trong một bảng không có hàng phù hợp trong bảng khác.
* ```Cross Join``` - tạo ra một sản phẩm Descartes của các hàng trong hai hoặc nhiều bảng.
* ```Natural Join``` - tham gia hai hoặc nhiều bảng bằng cách sử dụng điều kiện tham gia ngầm định dựa trên tên cột chung trong các bảng được nối.

###### 5.2.4 Nhóm dữ liệu

* ```Group by``` - chia các hàng thành các nhóm và áp dụng một hàm tổng hợp trên mỗi nhóm.
* ```Having``` - áp dụng điều kiện cho các nhóm.

###### 5.2.5 Thiết lập hoạt động

* ```Union``` - kết hợp tập kết quả của nhiều truy vấn thành một tập hợp kết quả duy nhất.
* ```Intersect``` - kết hợp các bộ kết quả của hai hoặc nhiều truy vấn và trả về một tập kết quả có các hàng xuất hiện trong cả hai tập kết quả.
* ```Except``` - trả về các hàng trong truy vấn đầu tiên không xuất hiện trong đầu ra của truy vấn thứ hai.

###### 5.2.6 Nhóm bộ

* ```Group Sets``` - tạo ra nhiều bộ nhóm trong báo cáo.
* ```Cube``` - xác định nhiều bộ nhóm bao gồm tất cả các kết hợp thứ nguyên có thể có.
* ```Rollup``` - tạo các báo cáo có chứa tổng số một tổng số phụ.

###### 5.2.7 Truy vấn con

* ```Subquery ```- viết truy vấn được lồng trong truy vấn khác.
* ```ANY``` - truy xuất dữ liệu bằng cách so sánh giá trị với một tập hợp các giá trị được truy vấn con trả về.
* ```ALL``` - truy vấn bằng cách so sánh giá trị với danh sách các giá trị được truy vấn con trả về.
* ```EXISTS``` - kiểm tra sự tồn tại của các hàng được truy vấn con trả về.

###### 5.2.8 Sửa đổi dữ liệu

Trong phần này, bạn sẽ tìm hiểu cách chèn dữ liệu vào một bảng với câu lệnh ```INSERT```, sửa đổi dữ liệu hiện có bằng câu lệnh ```UPDATE``` và loại bỏ dữ liệu bằng câu lệnh ```DELETE```. Ngoài ra, bạn học cách sử dụng câu lệnh upsert để hợp nhất dữ liệu.

* ```Insert```  - chèn dữ liệu vào một bảng.
* ```Update```  - cập nhật dữ liệu hiện có trong bảng.
* ```Update join``` - cập nhật các giá trị trong một bảng dựa trên các giá trị trong bảng khác.
* ```Delete``` - xóa dữ liệu trong bảng.
* ```Upsert``` - chèn hoặc cập nhật dữ liệu nếu hàng mới đã tồn tại trong bảng.

###### 5.2.9 PostgreSQL nhập xuất

Bạn sẽ tìm hiểu cách nhập và xuất dữ liệu PostgreSQL từ và sang định dạng tệp CSV bằng lệnh sao chép.

* Nhập tệp CSV vào Bảng - cho bạn biết cách nhập tệp CSV vào bảng.
* Xuất Bảng PostgreSQL thành tệp CSV - cho bạn thấy cách xuất bảng vào tệp CSV.

###### 5.2.10 Quản lý bảng

Trong phần này, chúng ta bắt đầu khám phá các kiểu dữ liệu PostgreSQL và chỉ cho bạn cách sử dụng câu lệnh ```CREATE TABLE``` để tạo một bảng mới. Chúng tôi cũng sẽ bao gồm một số tính năng bổ sung, chẳng hạn như sửa đổi cấu trúc bảng và xóa bảng. Ngoài ra, bạn sẽ tìm hiểu một cách hiệu quả để xóa tất cả các hàng từ một bảng bằng cách sử dụng câu lệnh ```TRUNCATE```.

* Data types - bao gồm các kiểu dữ liệu PostgreSQL được sử dụng phổ biến nhất.
* Create table - hướng dẫn bạn cách tạo một bảng mới trong cơ sở dữ liệu.
* ```Select Into``` & ```Create table as``` - cho bạn thấy cách tạo bảng mới từ tập kết quả của truy vấn.
* Cột tăng tự động với SERIAL - sử dụng SERIAL để thêm cột tăng tự động vào bảng.
* ```Alter table``` - thay đổi cấu trúc của bảng hiện có.
* ```Rename table```- thay đổi tên của bảng thành bảng mới.
* ```Rename database``` - thay đổi tên của cơ sở dữ liệu thành một cơ sở dữ liệu mới.
* ```Add column``` - chỉ cho bạn cách sử dụng thêm một hoặc nhiều cột vào bảng hiện có.
* ```Drop column``` - minh họa cách thả một cột của bảng.
* Thay đổi kiểu dữ liệu cột - chỉ cho bạn cách thay đổi dữ liệu của một cột.
* ```Rename column``` - minh họa cách đổi tên một hoặc nhiều cột của bảng.
* ```Drop table``` - loại bỏ một bảng hiện có và tất cả các đối tượng phụ thuộc của nó.
* ```Temporary table``` - chỉ cho bạn cách sử dụng bảng tạm thời.
* ```Truncate table``` - loại bỏ tất cả dữ liệu trong một bảng lớn một cách nhanh chóng và hiệu quả.

###### 5.2.11 Các loại dữ liệu PostgreSQL chuyên sâu
###### 5.2.12 Hiểu các ràng buộc PostgreSQL
###### 5.2.13 Biểu thức và toán tử điều kiện
###### 5.2.14 Tiện ích PostgreSQL
###### 5.2.15 Phương thức PostgreSQL
