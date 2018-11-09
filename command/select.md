
## PostgreSQL ```SELECT```

Một trong những nhiệm vụ phổ biến nhất, khi bạn làm việc với PostgreSQL, là truy vấn dữ liệu từ các bảng bằng cách sử dụng ```SELECT``` . Câu ```SELECT``` là một trong những câu lệnh phức tạp nhất trong PostgreSQL. Nó có nhiều mệnh đề mà bạn có thể kết hợp để tạo thành một truy vấn mạnh mẽ.

Do tính phức tạp của nó, chúng tôi chia hướng dẫn ```SELECT``` PostgreSQL thành nhiều hướng dẫn ngắn hơn để bạn có thể tìm hiểu từng mệnh đề của ```SELECT``` dễ dàng hơn.

- Sau đây là các mệnh đề xuất hiện trong ```SELECT``` :

  - Chọn các hàng riêng biệt bằng cách sử dụng toán tử ```DISTINCT``` .
  - Lọc các hàng bằng cách sử dụng ```WHERE``` .
  - Sắp xếp các hàng bằng cách sử dụng mệnh đề ```ORDER BY``` .
  - Chọn các hàng dựa trên các toán tử khác nhau như ```BETWEEN``` , ```IN``` và ```LIKE``` .
  - Nhóm các hàng thành các nhóm sử dụng mệnh đề GROUP BY
  - Áp dụng các điều kiện cho các nhóm sử dụng mệnh đề HAVING .
  -Tham gia một bảng với các bảng khác bằng cách sử dụng các mệnh đề ```INNER JOIN``` , ```LEFT JOIN``` , ```FULL OUTER JOIN``` , ```CROSS JOIN``` .

Trong hướng dẫn này, bạn sẽ tập trung vào các mệnh đề ```SELECT``` và ```FROM```.

Cú pháp ```SELECT``` PostgreSQL:

Hãy bắt đầu với một biểu mẫu cơ bản của ```SELECT``` để lấy dữ liệu từ một bảng duy nhất.
Phần sau minh họa cú pháp của ```SELECT``` :
```
SELECT
column_1,
column_2,
...
FROM
table_name;
```
Hãy xem xét ```SELECT``` chi tiết hơn:

Đầu tiên, bạn chỉ định cột của bảng mà bạn muốn truy vấn dữ liệu trong mệnh đề ```SELECT``` . Nếu bạn truy xuất dữ liệu từ nhiều cột, bạn sử dụng dấu phẩy để tách hai cột. Trong trường hợp bạn muốn truy vấn dữ liệu từ tất cả các cột, bạn có thể sử dụng dấu hoa thị (``*``) làm cách viết tắt.

Thứ hai, bạn chỉ ra tên bảng sau từ khóa ```FROM``` .

### PostgreSQL ```SELECT DISTINCT```

Mệnh đề ```DISTINCT``` được sử dụng trong câu lệnh SELECT để loại bỏ các hàng trùng lặp khỏi tập kết quả. Mệnh đề ```DISTINCT``` giữ một hàng cho mỗi nhóm bản sao. Mệnh đề ```DISTINCT``` có thể được sử dụng trên một hoặc nhiều cột của bảng.

Phần sau minh họa cú pháp của mệnh đề ```DISTINCT```:

```
SELECT
 DISTINCT column_1
FROM
 table_name;
```

Trong tuyên bố này, các giá trị trong cột ```column_1``` được sử dụng để đánh giá trùng lặp.

Nếu bạn chỉ định nhiều cột, mệnh đề ```DISTINCT``` sẽ đánh giá bản sao dựa trên sự kết hợp của các giá trị của các cột này.

```
SELECT
 DISTINCT column_1,
 column_2
FROM
 tbl_name;
```

Trong trường hợp này, kết hợp cả hai column_1 và column_2 sẽ được sử dụng để đánh giá trùng lặp.

PostgreSQL cũng cung cấp ```DISTINCT ON``` (biểu thức) để giữ hàng “đầu tiên” của mỗi nhóm các bản sao bằng cách sử dụng cú pháp sau:

```
SELECT
 DISTINCT ON
 (column_1),
 column_2
FROM
 tbl_name
ORDER BY
 column_1,
 column_2;
```

Thứ tự của các hàng được trả về từ câu lệnh ```SELECT``` là không thể đoán trước do đó hàng “đầu tiên” của mỗi nhóm bản sao cũng không thể đoán trước được. Thực hành tốt là luôn sử dụng mệnh đề ```ORDER BY``` với dấu ```DISTINCT ON``` (biểu thức) để làm cho tập kết quả hiển thị rõ ràng.

Lưu ý rằng biểu thức ```DISTINCT ON``` phải khớp với biểu thức ngoài cùng bên trái trong mệnh đề ```ORDER BY``` .

### PostgreSQL ```WHERE```

Mệnh đề ```WHERE``` PostgreSQL dùng để lọc các hàng được trả về từ câu lệnh ```SELECT```.

Cú pháp của mệnh đề ```WHERE``` PostgreSQL như sau:

```
SELECT column_1, column_2 … column_n
FROM table_name
WHERE conditions;
```

Mệnh đề ```WHERE``` xuất hiện ngay sau mệnh đề FROM của câu lệnh ```SELECT```. Các điều kiện được sử dụng để lọc các hàng được trả về từ câu lệnh ```SELECT```. PostgreSQL cung cấp cho bạn các toán tử chuẩn khác nhau để xây dựng các điều kiện.

Bảng sau đây minh họa các toán tử so sánh tiêu chuẩn.

|Toán tử|Mô tả|
|-|-|
|=| Bằng|
|>| Lớn hơn|
|<| Nhỏ hơn|
|>=| Lớn hơn hoặc bằng|
|<=| Nhỏ hơn hoặc bằng|
|<> hoặc !=| Không bằng nhau|
|AND| Toán tử logic AND|
|HOẶC| Toán tử logic OR|

Hãy thực hành với một số ví dụ về việc sử dụng mệnh đề ```WHERE``` với các điều kiện.

Nếu bạn muốn có được tất cả các khách hàng có tên đầu tiên là ```Jamie```, bạn có thể sử dụng mệnh đề ```WHERE``` với toán tử bằng (=) như sau:

```
SELECT last_name, first_name
FROM customer
WHERE first_name = 'Jamie';
```

### PostgreSQL ```ORDER BY```

Khi bạn truy vấn dữ liệu từ một bảng, PostgreSQL trả về các hàng theo thứ tự mà chúng được chèn vào bảng. Để sắp xếp tập hợp kết quả, bạn sử dụng mệnh đề ```ORDER BY``` trong câu lệnh ```SELECT```.

Mệnh đề ```ORDER BY``` cho phép bạn sắp xếp các hàng được trả về từ câu lệnh ```SELECT``` theo thứ tự tăng dần hoặc giảm dần dựa trên các tiêu chí được chỉ định.

Phần sau minh họa cú pháp của mệnh đề ```ORDER BY```:

```
SELECT
 column_1,
 column_2
FROM
 tbl_name
ORDER BY
 column_1 ASC,
 column_2 DESC;
```

Hãy xem xét cú pháp của mệnh đề ORDER BY chi tiết hơn:

* Đầu tiên, chỉ định cột mà bạn muốn sắp xếp theo mệnh đề ORDER BY. Nếu bạn sắp xếp tập hợp kết quả dựa trên nhiều cột, hãy sử dụng dấu phẩy để tách biệt giữa hai cột.
* Thứ hai, sử dụng ```ASC``` để sắp xếp tập kết quả theo thứ tự tăng dần và ```DESC``` để sắp xếp tập hợp kết quả theo thứ tự giảm dần. Nếu bạn để trống, mệnh đề ```ORDER BY``` sẽ sử dụng ```ASC``` theo mặc định.

### PostgreSQL ```BETWEEN```

Sử dụng toán tử ```BETWEEN``` để khớp với một giá trị so với một dải giá trị. Phần sau minh họa cú pháp của toán tử ```BETWEEN```:

```
value BETWEEN low AND high;
```

Nếu giá trị lớn hơn hoặc bằng giá trị thấp và nhỏ hơn hoặc bằng giá trị cao, biểu thức trả về true, ngược lại, giá trị trả về false.

Bạn có thể viết lại toán tử ```BETWEEN``` bằng cách sử dụng toán tử lớn hơn hoặc bằng (> =) hoặc nhỏ hơn hoặc bằng (<=) như câu lệnh sau:

```
value >= low and value <= high
```

Nếu bạn muốn kiểm tra nếu một giá trị nằm ngoài phạm vi, bạn kết hợp toán tử NOT với toán tử ```BETWEEN``` như sau:

```
value NOT BETWEEN low AND high;
```

Biểu thức sau đây tương đương với biểu thức sử dụng toán tử ```NOT``` và `BETWEEN`:

```
value < low OR value > high
```

Truy vấn sau chọn thanh toán có số tiền từ 8 đến 9 (USD):

```
SELECT
 customer_id,
 payment_id,
 amount
FROM
 payment
WHERE
 amount BETWEEN 8
AND 9;
```

### PostgreSQL ```IN```

Bạn sử dụng toán tử IN trong mệnh đề WHERE để kiểm tra xem một giá trị có khớp với bất kỳ giá trị nào trong một danh sách các giá trị hay không. Cú pháp của toán tử IN như sau:

```
value IN (value1,value2,...)
```

Biểu thức trả về true nếu giá trị khớp với bất kỳ giá trị nào trong danh sách tức là, value1 và value2. Danh sách các giá trị có thể là danh sách các số hoặc chuỗi hoặc tập kết quả của câu lệnh SELECT như được hiển thị trong truy vấn sau:

```
value IN (SELECT value FROM tbl_name);
```

Câu lệnh bên trong dấu ngoặc đơn được gọi là truy vấn con là truy vấn được lồng trong truy vấn khác.

Giả sử bạn muốn biết thông tin cho thuê của id khách hàng 1 và 2, bạn có thể sử dụng toán tử IN trong mệnh đề WHERE như sau:

```
SELECT
 customer_id,
 rental_id,
 return_date
FROM
 rental
WHERE
 customer_id IN (1, 2)
ORDER BY
 return_date DESC;
```
