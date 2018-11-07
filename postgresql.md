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
