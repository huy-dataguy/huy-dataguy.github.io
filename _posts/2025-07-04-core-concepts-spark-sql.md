---
title: "Khái niệm cốt lõi trong Spark SQL: Hive, Hive Metastore, Hive Warehouse, Catalog, và Managed/Unmanaged Tables"
date: 2025-07-04
categories: [Apache Spark, Spark SQL]
tags: [spark, hive, hive metastore, hive warehouse, catalog]

---

Spark SQL là một module mạnh mẽ của Apache Spark, cho phép xử lý dữ liệu lớn bằng cú pháp SQL và tích hợp với các API lập trình (Python, Scala). Tuy nhiên, các khái niệm như **Hive**, **Hive Metastore**, **Hive Warehouse**, **Catalog**, và **Managed/Unmanaged Tables** có thể gây nhầm lẫn do sự chồng chéo và tính trừu tượng của chúng. Bài viết này giải thích chi tiết từng khái niệm, vai trò của chúng, cách chúng liên quan với nhau.

---

## 1. Hive là gì?

### 1.1 Định nghĩa
**Apache Hive** là một công cụ kho dữ liệu (data warehouse) được xây dựng trên Hadoop HDFS, cho phép sử dụng **HiveQL** (một biến thể của SQL) để truy vấn dữ liệu lớn. Hive được phát triển bởi Facebook trước khi Spark ra đời và từng là công cụ chính để xử lý dữ liệu lớn trong hệ sinh thái Hadoop.

### 1.2 Vai trò trong Spark SQL
- **Tích hợp với Spark**: Spark SQL kế thừa khả năng của Hive, cho phép sử dụng các bảng đã được định nghĩa trong Hive thông qua **Hive Metastore**. Điều này giúp dễ dàng chuyển đổi Hive sang Spark mà không cần định nghĩa lại bảng. (Trước đó các doanh nghiệp dùng Hive, sau đó chuyển đổi qua Spark một cách dễ dàng).
- **Hiệu suất**: Spark SQL cải thiện hiệu suất so với Hive.
- **Không bắt buộc**: Có thể sử dụng Spark SQL mà không cần Hive, vì Spark có metastore nội bộ riêng.

###  1.3 Ví dụ
Giả sử bạn có một hệ thống Hadoop/Hive với bảng `flights` đã được định nghĩa. Spark SQL có thể truy vấn bảng này nếu được cấu hình để kết nối với Hive Metastore.

---

## 2. Hive Metastore là gì?

### 2.1 Định nghĩa

**Hive Metastore** là một cơ sở dữ liệu quan hệ (như MySQL, PostgreSQL, hoặc Derby) lưu trữ **metadata** của các bảng, cơ sở dữ liệu, phân vùng, và các đối tượng khác trong Hive hoặc Spark SQL. Metadata bao gồm:

- Cấu trúc bảng (tên cột, kiểu dữ liệu).
- Vị trí dữ liệu (đường dẫn trên HDFS, S3).
- Thông tin phân vùng (nếu có).
- Định dạng tệp (Parquet, ORC, CSV, v.v.).

### 2.2 Lưu trữ bằng gì?

- **Cơ sở dữ liệu quan hệ**:
    - **MySQL**: Phổ biến trong sản xuất do hiệu suất và độ ổn định.
    - **PostgreSQL**: Cũng được sử dụng rộng rãi.
    - **Derby**: Cơ sở dữ liệu nhúng mặc định.
- **Định dạng**: Metadata được lưu dưới dạng các bảng quan hệ, ví dụ:
    - `TBLS` (table): Lưu thông tin về bảng (tên, database).
    - `COLUMNS_V2`: Lưu thông tin cột (tên, kiểu dữ liệu).
    - `PARTITIONS`: Lưu thông tin phân vùng.

### 2.3 Vai trò trong Spark SQL

- **Quản lý metadata**: Hive Metastore lưu trữ thông tin về bảng, giúp Spark SQL biết cách truy cập dữ liệu mà không cần quét toàn bộ tệp.
- **Tích hợp với Hive**: Spark SQL có thể kết nối với Hive Metastore để sử dụng các bảng Hive hiện có, giảm việc định nghĩa lại.
- **Cải thiện hiệu suất**: Metadata giúp Spark xác định vị trí dữ liệu (như `/user/hive/warehouse/flights`) và cấu trúc bảng mà không cần liệt kê tệp.

### 2.4 Cấu hình

Để Spark SQL sử dụng Hive Metastore, cần:

- Đặt các tệp cấu hình (`hive-site.xml`, `core-site.xml`, `hdfs-site.xml`) vào thư mục `conf/` của Spark.
- Một số thuộc tính quan trọng:
    - `spark.sql.hive.metastore.version`: Phiên bản Hive Metastore.
    - `spark.sql.hive.metastore.jars`: Đường dẫn đến thư viện Hive.
    - `spark.sql.hive.metastore.sharedPrefixes`: Tiền tố lớp để giao tiếp với metastore.

### 2.5 Ví dụ

Khi bạn chạy:

```sql
CREATE TABLE flights (DEST_COUNTRY_NAME STRING, count LONG) USING parquet
```

- **Hive Metastore** lưu metadata:
    - Tên bảng: `flights`.
    - Cột: `DEST_COUNTRY_NAME` (STRING), `count` (LONG).
    - Vị trí: `/user/hive/warehouse/flights`.
    - Định dạng: Parquet.
- Metadata được lưu trong cơ sở dữ liệu MySQL (nếu dùng MySQL làm metastore).

---

## 3. Hive Warehouse là gì?

### 3.1 Định nghĩa

**Hive Warehouse** là **thư mục** trên hệ thống tệp (thường là HDFS, nhưng có thể là S3 hoặc hệ thống tệp cục bộ) nơi Spark hoặc Hive lưu trữ **dữ liệu thực tế** của các **managed tables**. Đường dẫn mặc định là `/user/hive/warehouse`, nhưng có thể tùy chỉnh qua `spark.sql.warehouse.dir`.

### 3.2 Lưu trữ bằng gì?

- **Dữ liệu**: Lưu dưới dạng **tệp** trên hệ thống tệp phân tán (HDFS, S3, v.v.).
- **Định dạng tệp**:
    - **Parquet**: Định dạng cột hiệu quả, mặc định trong Spark.
    - **ORC**: Tối ưu hóa cho Hive.
    - **CSV**, **JSON**, **Avro**: Tùy thuộc vào cấu hình.
- **Cấu trúc thư mục**:
    - Mỗi managed table là một thư mục con trong `/user/hive/warehouse`.
    - Ví dụ: Bảng `flights` được lưu tại `/user/hive/warehouse/flights`.
    - Nếu bảng được phân vùng:
        
        ```
        /user/hive/warehouse/partitioned_flights/DEST_COUNTRY_NAME=USA/
        /user/hive/warehouse/partitioned_flights/DEST_COUNTRY_NAME=Canada/
        ```
        

### 3.3 Vai trò trong Spark SQL

- **Lưu trữ dữ liệu managed tables**: Khi bạn tạo một managed table, Spark lưu dữ liệu vào Hive Warehouse.
- **Quản lý tự động**: Spark tự động quản lý dữ liệu trong Hive Warehouse, bao gồm tạo và xóa.
- **Xóa managed table**: Khi xóa một managed table (`DROP TABLE flights`), dữ liệu trong Hive Warehouse cũng bị xóa.

### 3.4 Ví dụ

Tạo managed table:

```sql
CREATE TABLE flights (DEST_COUNTRY_NAME STRING, count LONG) USING parquet
```

- **Hive Warehouse**: Lưu dữ liệu dưới dạng tệp Parquet tại `/user/hive/warehouse/flights`.
- **Hive Metastore**: Lưu metadata (cấu trúc bảng, vị trí `/user/hive/warehouse/flights`).

---

## 4. Catalog là gì?

### 4.1 Định nghĩa

>*In Spark SQL, a **catalog** is the interface that lets you manage and interact with metadata of databases, tables, views, and functions.*
>--*phần này dịch tiếng việt khó hiểu quá:))*

**Catalog** là một **giao diện trừu tượng** (abstraction) trong Spark SQL, thuộc gói `org.apache.spark.sql.catalog.Catalog`, dùng để **truy cập** và **quản lý** metadata của các đối tượng như bảng, cơ sở dữ liệu, view, và hàm. Nó không lưu trữ metadata, mà chỉ là một lớp trung gian để tương tác với **Hive Metastore** (hoặc metastore nội bộ của Spark). 

- Nó đóng vai trò như một **cầu nối** giữa người dùng và nơi lưu trữ metadata (thường là **Hive Metastore** hoặc **metastore nội bộ** của Spark).

- Hình dung: Catalog giống như một "nhân viên lễ tân" trong thư viện. Bạn hỏi về danh sách bảng, Catalog tra cứu trong Hive Metastore và trả lời.

### 4.2 Vai trò trong Spark SQL

1. **Truy cập metadata**:
    
    - Liệt kê bảng, cơ sở dữ liệu, hàm:
        
        ```python
        spark.catalog.listTables().show()
        spark.catalog.listDatabases().show()
        spark.catalog.listFunctions().show()
        ```
        
    - Tương đương SQL:
        
        ```sql
        SHOW TABLES
        SHOW DATABASES
        SHOW FUNCTIONS
        ```
        
2. **Quản lý metadata**:
	- Kiểm tra bảng tồn tại:
	``` python
		spark.catalog.tableExists("flights")
	```
	- Cache bảng (lưu trữ dữ liệu của bảng đó vào bộ nhớ RAM):
		```python
		spark.catalog.cacheTable("flights")
		```
	        
3. **Tích hợp với Hive**:
    - Nếu Spark được cấu hình với Hive Metastore (qua `hive-site.xml`), Catalog gửi yêu cầu đến Hive Metastore để lấy hoặc cập nhật metadata.
    - Nếu không dùng Hive, Catalog làm việc với metastore nội bộ (thường là Derby).
4. **Hỗ trợ cả SQL và lập trình**:
    
    - Catalog cho phép sử dụng lệnh SQL (`SHOW TABLES`) hoặc API lập trình (`spark.catalog.listTables()`)

### 4.3 Ví dụ

Tạo bảng:

```sql
CREATE TABLE flights (DEST_COUNTRY_NAME STRING, count LONG) USING parquet
```

- **Catalog**: Gửi yêu cầu để lưu metadata vào Hive Metastore.
- **Hive Metastore**: Lưu metadata (tên bảng, cột, vị trí `/user/hive/warehouse/flights`).
- **Hive Warehouse**: Lưu dữ liệu Parquet tại `/user/hive/warehouse/flights`.

Truy vấn:

```sql
SHOW TABLES
```

- **Catalog**: Hỏi Hive Metastore về danh sách bảng, trả về `flights`.

---

## 5. Managed vs. Unmanaged Tables

### 5.1 Managed Tables

- **Định nghĩa**: Spark quản lý cả **dữ liệu** (trong Hive Warehouse) và **metadata** (trong Hive Metastore).
- **Tạo**:
    
    ```sql
    CREATE TABLE flights (DEST_COUNTRY_NAME STRING, count LONG) USING parquet
    ```
    
    Hoặc:
    
    ```python
    df.saveAsTable("flights")
    ```
    
- **Lưu trữ**:
    - Dữ liệu: Trong `/user/hive/warehouse/flights` (Parquet).
    - Metadata: Trong Hive Metastore (MySQL, PostgreSQL).
- **Xóa**:
    
    ```sql
    DROP TABLE flights
    ```
    
    - Xóa cả dữ liệu (trong Hive Warehouse) và metadata (trong Hive Metastore).

### 5.2 Unmanaged Tables

- **Định nghĩa**: Spark chỉ quản lý **metadata** (trong Hive Metastore), dữ liệu nằm ở vị trí bên ngoài (như HDFS, S3).
- **Tạo**:
    
    ```sql
    CREATE EXTERNAL TABLE external_flights (DEST_COUNTRY_NAME STRING, count LONG)
    LOCATION '/data/external/flights'
    ```
    
- **Lưu trữ**:
    - Dữ liệu: Trong `/data/external/flights` (không thuộc Hive Warehouse).
    - Metadata: Trong Hive Metastore.
- **Xóa**:
    
    ```sql
    DROP TABLE external_flights
    ```
    
    - Chỉ xóa metadata, dữ liệu ở `/data/external/flights` vẫn còn.

### 5.3 Ví dụ

- **Managed Table**:
    
    ```sql
    CREATE TABLE flights (DEST_COUNTRY_NAME STRING, count LONG) USING parquet
    INSERT INTO flights VALUES ('USA', 100)
    ```
    
    - Dữ liệu lưu tại `/user/hive/warehouse/flights`.
    - Metadata lưu trong Hive Metastore.
    - `DROP TABLE flights`: Xóa cả dữ liệu và metadata.
- **Unmanaged Table**:
    
    ```sql
    CREATE EXTERNAL TABLE external_flights (DEST_COUNTRY_NAME STRING, count LONG)
    LOCATION '/data/external/flights'
    ```
    
    - Dữ liệu ở `/data/external/flights`.
    - Metadata trong Hive Metastore.
    - `DROP TABLE external_flights`: Chỉ xóa metadata.

---

## 6. Tổng kết

- **Hive**: Công cụ kho dữ liệu, cung cấp HiveQL và Hive Metastore để quản lý metadata. Spark SQL tích hợp với Hive để tái sử dụng bảng.
- **Hive Metastore**: Lưu **metadata** trong cơ sở dữ liệu quan hệ (MySQL, PostgreSQL), giúp Spark biết cấu trúc bảng và vị trí dữ liệu.
- **Hive Warehouse**: Lưu **dữ liệu** của managed tables trong hệ thống tệp (HDFS, `/user/hive/warehouse`).
- **Catalog**: lớp giao tiếp trừu tượng (abstraction), là cầu nối, giúp làm việc với metadata dễ dàng gửi yêu cầu đến Hive Metastore để truy cập hoặc quản lý metadata.
- **Managed Tables**: Sử dụng cả Hive Metastore (metadata) và Hive Warehouse (dữ liệu).
- **Unmanaged Tables**: Chỉ sử dụng Hive Metastore (metadata), dữ liệu ở vị trí bên ngoài.