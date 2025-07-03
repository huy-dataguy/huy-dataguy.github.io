
---
title: "Read/Write Dữ Liệu trong Apache Spark"
date: 2025-07-03
categories: [Apache Spark]
tags: [spark, datasource, csv, json, parquet, orc, jdbc, text, read/write, performance optimization]

---

Nếu bạn mới bắt đầu với **Apache Spark**, một trong những kỹ năng quan trọng nhất là hiểu cách **đọc** và **ghi** dữ liệu từ nhiều nguồn khác nhau như file CSV, JSON, Parquet, hay cơ sở dữ liệu như MySQL, SQLite. Trong bài viết này, mình sẽ hướng dẫn bạn từng bước cách làm việc với các **Data Source** trong Spark.

---

## 1. Data Source Là Gì Trong Apache Spark?

**Apache Spark** là một công cụ mạnh mẽ để xử lý dữ liệu lớn. Một trong những điểm mạnh của Spark là khả năng làm việc với nhiều loại dữ liệu, từ file văn bản đơn giản đến cơ sở dữ liệu phức tạp. Spark hỗ trợ **6 nguồn dữ liệu chính** (core data sources) mà bạn có thể sử dụng ngay:

- **CSV**: File văn bản với các giá trị phân tách bằng dấu phẩy (`,`).
- **JSON**: File định dạng JSON, thường dùng trong các ứng dụng web.
- **Parquet**: Định dạng cột tối ưu, tiết kiệm không gian và nhanh khi truy vấn.
- **ORC**: Định dạng cột tối ưu cho Hive, tương tự Parquet.
- **JDBC/ODBC**: Kết nối với cơ sở dữ liệu như MySQL, PostgreSQL, SQLite.
- **Text Files**: File văn bản thuần túy, mỗi dòng là một bản ghi.

Ngoài ra, cộng đồng Spark còn hỗ trợ nhiều nguồn khác như Cassandra, MongoDB, hay AWS Redshift. Trong bài viết này, mình sẽ tập trung vào các nguồn chính, giải thích từng bước với ví dụ dễ hiểu.

---

## 2. Cách Spark Đọc và Ghi Dữ Liệu

Spark sử dụng hai công cụ chính để đọc và ghi dữ liệu:
- **DataFrameReader**: Để đọc dữ liệu từ file hoặc cơ sở dữ liệu vào Spark.
- **DataFrameWriter**: Để ghi dữ liệu từ Spark ra file hoặc cơ sở dữ liệu.

Cả hai đều có cách dùng đơn giản, thống nhất, và dễ hiểu. Hãy cùng khám phá chi tiết nhé!

### 2.1. Đọc Dữ Liệu (DataFrameReader)

Để đọc dữ liệu, bạn dùng lệnh `spark.read` với các bước sau:

```scala
spark.read
  .format("định_dạng") // ví dụ: csv, json, parquet
  .option("tùy_chọn", "giá_trị") // cấu hình cách đọc
  .schema(mySchema) // tùy chọn: định nghĩa cấu trúc dữ liệu
  .load("đường_dẫn")
```

- **format**: Chỉ định loại dữ liệu (CSV, JSON,...). Nếu không chỉ định, Spark mặc định dùng Parquet.
- **option**: Các thiết lập như có tiêu đề cột không, xử lý lỗi ra sao.
- **schema**: Định nghĩa cấu trúc dữ liệu (nếu không muốn Spark sinh schema dựa trên tập dữ liệu).
- **load**: Đường dẫn tới file hoặc nguồn dữ liệu.

**Ví dụ đọc file CSV**:

```scala
spark.read.format("csv")
  .option("header", "true") // dòng đầu là tiêu đề
  .option("inferSchema", "true") // spark tự sinh kiểu dữ liệu
  .load("/data/flight-data/csv/2018-summary.csv")
  .show(5)
```

#### **Chế Độ Đọc (Read Modes)**

Khi đọc dữ liệu, bạn có thể gặp bản ghi bị lỗi (ví dụ: thiếu cột hoặc sai định dạng). Spark có 3 chế độ để xử lý:

| **Chế Độ**      | **Ý Nghĩa**                                                                        |
| --------------- | ---------------------------------------------------------------------------------- |
| `permissive`    | Ghi các giá trị null cho bản ghi lỗi, lưu lỗi vào cột `_corrupt_record`. Mặc định. |
| `dropMalformed` | Bỏ qua các bản ghi lỗi.                                                            |
| `failFast`      | Dừng ngay khi gặp bản ghi lỗi.                                                     |

**Ví dụ dùng `failFast`**:

```scala
spark.read.format("csv")
  .option("header", "true")
  .option("mode", "FAILFAST") // dừng nếu có lỗi
  .load("/data/flight-data/csv/2018-summary.csv")
```

### 2.2. Ghi Dữ Liệu (DataFrameWriter)

Để ghi dữ liệu, bạn dùng lệnh `dataFrame.write` với cấu trúc:

```scala
dataFrame.write
  .format("định_dạng") // ví dụ: csv, json, parquet
  .option("tùy_chọn", "giá_trị") // cấu hình cách ghi
  .mode("chế_độ_ghi") // ví dụ: overwrite, append
  .save("đường_dẫn")
```

- **format**: Định dạng đầu ra.
- **option**: Các thiết lập như dấu phân tách, nén dữ liệu.
- **mode**: Cách xử lý nếu dữ liệu đã tồn tại.
- **save**: Đường dẫn lưu dữ liệu.

**Ví dụ ghi file CSV**:

```scala
dataFrame.write.format("csv")
  .option("sep", "\t") // dùng tab thay vì dấu phẩy
  .mode("overwrite") // ghi đè nếu file đã tồn tại
  .save("/tmp/my-tsv-file.tsv")
```

#### **Chế Độ Ghi (Save Modes)**

Spark có 4 chế độ ghi để xử Lý dữ liệu tại đích:

| **Chế Độ**         | **Ý Nghĩa**                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| `append`            | Thêm dữ liệu mới vào file/cơ sở dữ liệu hiện có.                            |
| `overwrite`         | Ghi đè hoàn toàn dữ liệu cũ.                                                |
| `errorIfExists`     | Báo lỗi nếu dữ liệu đã tồn tại (mặc định).                                  |
| `ignore`            | Bỏ qua, không ghi nếu dữ liệu đã tồn tại.                                   |

---

## 3. Làm Việc Với Các Loại Dữ Liệu Phổ Biến

Dưới đây là hướng dẫn chi tiết cách đọc và ghi các định dạng dữ liệu phổ biến trong Spark.

### 3.1. File CSV

**CSV** là định dạng phổ biến, nhưng có thể phức tạp do các vấn đề như dấu phẩy trong giá trị hoặc dữ liệu lỗi. Spark cung cấp nhiều tùy chọn để xử lý các trường hợp này.

#### **Đọc File CSV**

**Ví dụ đơn giản**:

```scala
val csvDF = spark.read.format("csv")
  .option("header", "true")
  .option("inferSchema", "true")
  .load("/data/flight-data/csv/2018-summary.csv")
csvDF.show(5)
```

**Ví dụ
- Cách tạo schema thủ công (để kiểm soát kiểu dữ liệu):

```scala
import org.apache.spark.sql.types.{StructField, StructType, StringType, LongType}

val mySchema = StructType(Array(
  StructField("DEST_COUNTRY_NAME", StringType, true),
  StructField("ORIGIN_COUNTRY_NAME", StringType, true),
  StructField("count", LongType, false)
))

val csvDF = spark.read.format("csv")
  .option("header", "true")
  .option("mode", "FAILFAST")
  .schema(mySchema)
  .load("/data/flight-data/csv/2010-summary.csv")
csvDF.show(5)
```

**Lưu ý**: Nếu schema không khớp với dữ liệu, Spark sẽ báo lỗi khi chạy job

#### **Các Tùy Chọn CSV Quan Trọng**

| **Tùy Chọn**  | **Ý Nghĩa**                                          |
| ------------- | ---------------------------------------------------- |
| `sep`         | Ký tự phân tách (mặc định: `,`).                     |
| `header`      | Dòng đầu là tiêu đề (`true`/`false`).                |
| `inferSchema` | Tự đoán kiểu dữ liệu (`true`/`false`).               |
| `nullValue`   | Ký tự đại diện cho giá trị null (mặc định: `""`).    |
| `compression` | Nén file: `gzip`, `snappy`,... (mặc định: `none`).   |
| `multiline`   | Hỗ trợ bản ghi trải dài nhiều dòng (`true`/`false`). |

#### **Ghi File CSV**

**Ví dụ ghi file TSV (dùng tab làm phân tách)**:

```scala
csvDF.write.format("csv")
  .option("sep", "\t")
  .mode("overwrite")
  .save("/tmp/my-tsv-file.tsv")
```

**Kết quả**: Spark tạo một thư mục `/tmp/my-tsv-file.tsv` chứa nhiều file, mỗi file tương ứng với một phân vùng của DataFrame.

### 3.2. File JSON

**JSON** là định dạng phổ biến trong các ứng dụng web. Spark hỗ trợ hai loại:
- **Line-delimited JSON**: Mỗi dòng là một đối tượng JSON.
- **Multiline JSON**: Toàn bộ file là một đối tượng JSON lớn.

#### **Đọc File JSON**

```scala
val jsonDF = spark.read.format("json")
  .option("mode", "FAILFAST")
  .option("inferSchema", "true")
  .load("/data/flight-data/json/2018-summary.json")
jsonDF.show(5)
```

#### **Các Tùy Chọn JSON**

| **Tùy Chọn**       | **Ý Nghĩa**                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| `multiline`        | Đọc file như một đối tượng JSON lớn (`true`/`false`, mặc định: `false`).    |
| `compression`      | Nén file: `gzip`, `snappy`,... (mặc định: `none`).                         |
| `dateFormat`       | Định dạng ngày (mặc định: `yyyy-MM-dd`).                                    |
| `allowComments`    | Cho phép comment trong JSON (`true`/`false`).                               |

#### **Ghi File JSON**

```scala
csvDF.write.format("json")
  .mode("overwrite")
  .save("/tmp/my-json-file.json")
```

**Ưu điểm**: JSON hỗ trợ các kiểu dữ liệu phức tạp (như array, map) tốt hơn CSV.

### 3.3. File Parquet

**Parquet** là định dạng cột tối ưu, được Spark chọn làm **mặc định** vì hiệu suất cao và khả năng lưu trữ schema trong file.

#### **Đọc File Parquet**

```scala
val parquetDF = spark.read.format("parquet")
  .load("/data/flight-data/parquet/2010-summary.parquet")
parquetDF.show(5)
```

#### **Ghi File Parquet**

```scala
csvDF.write.format("parquet")
  .mode("overwrite")
  .save("/tmp/my-parquet-file.parquet")
```

#### **Tại Sao Nên Dùng Parquet?**
- **Hiệu suất cao**: Chỉ đọc cột cần thiết, tiết kiệm thời gian.
- **Nén tốt**: Tiết kiệm không gian lưu trữ.
- **Hỗ trợ kiểu dữ liệu phức tạp**: Array, map, struct.

### 3.4. File ORC

**ORC** tương tự Parquet, nhưng được tối ưu cho Hive. Nó cũng hỗ trợ nén cột và kiểu dữ liệu phức tạp.

#### **Đọc File ORC**

```scala
spark.read.format("orc")
  .load("/data/flight-data/orc/2010-summary.orc")
```

#### **Ghi File ORC**

```scala
csvDF.write.format("orc")
  .mode("overwrite")
  .save("/tmp/my-orc-file.orc")
```

### 3.5. File Text

File văn bản thuần túy được đọc dưới dạng mỗi dòng là một bản ghi. Bạn cần xử lý thêm để chuyển thành cấu trúc.

#### **Đọc File Text**

```scala
spark.read.textFile("/data/flight-data/csv/2018-summary.csv")
  .selectExpr("split(value, ',') as rows")
  .show()
```

#### **Ghi File Text**

Chỉ hỗ trợ ghi một cột kiểu string:

```scala
csvDF.select("DEST_COUNTRY_NAME")
  .write.text("/tmp/simple-text-file.txt")
```

### 3.6. Cơ Sở Dữ Liệu (JDBC)

Spark có thể kết nối với cơ sở dữ liệu như SQLite, MySQL, PostgreSQL qua **JDBC**.

#### **Đọc Từ Cơ Sở Dữ Liệu**

**Ví dụ với SQLite**:

```scala
val url = "jdbc:sqlite:/tmp/my-sqlite.db"
val tableName = "flight_info"
val driver = "org.sqlite.JDBC"

val dbDF = spark.read.format("jdbc")
  .option("url", url)
  .option("dbtable", tableName)
  .option("driver", driver)
  .load()
dbDF.show(5)
```

**Ví dụ với PostgreSQL**:

```scala
val pgDF = spark.read.format("jdbc")
  .option("driver", "org.postgresql.Driver")
  .option("url", "jdbc:postgresql://database_server")
  .option("dbtable", "schema.flight_info")
  .option("user", "username")
  .option("password", "my-secret-password")
  .load()
```

#### **Ghi Vào Cơ Sở Dữ Liệu**

```scala
val props = new java.util.Properties
props.setProperty("driver", "org.sqlite.JDBC")

csvDF.write
  .mode("overwrite")
  .jdbc("jdbc:sqlite:/tmp/my-sqlite.db", "flight_info", props)
```

#### **Tối Ưu Với Query Pushdown**

Spark có thể đẩy các bộ lọc xuống database để giảm dữ liệu tải về:

```scala
dbDF.filter("DEST_COUNTRY_NAME IN ('Anguilla', 'Sweden')").show()
```

Hoặc dùng truy vấn SQL trực tiếp:

```scala
val pushdownQuery = "(SELECT DISTINCT(DEST_COUNTRY_NAME) FROM flight_info) AS flight_info"
spark.read.format("jdbc")
  .option("url", url)
  .option("dbtable", pushdownQuery)
  .option("driver", driver)
  .load()
```

---
## 4. Tối Ưu Hiệu Suất

Để xử lý dữ liệu lớn nhanh hơn và tiết kiệm tài nguyên, bạn cần biết cách tối ưu hóa khi đọc và ghi dữ liệu trong **Apache Spark**.
### 4.1. Phân Vùng Dữ Liệu (Partitioning)

**Phân vùng** giúp chia dữ liệu thành các nhóm nhỏ theo cột (như ngày, quốc gia), để Spark chỉ đọc phần dữ liệu cần thiết, tiết kiệm thời gian.

**Ví dụ**:
```scala
csvDF.write
  .partitionBy("DEST_COUNTRY_NAME")
  .format("parquet")
  .save("/tmp/partitioned-files.parquet")
```

**Kết quả**: Tạo các thư mục như `/DEST_COUNTRY_NAME=United States/`. Khi lọc `WHERE DEST_COUNTRY_NAME = 'United States'`, Spark chỉ đọc thư mục đó.

**Tại sao nên dùng?**
- Tăng tốc truy vấn khi lọc theo cột phân vùng.
- Giảm lượng dữ liệu cần đọc, tiết kiệm tài nguyên.

**Note**: Chỉ phân vùng theo cột có giá trị lặp lại nhiều (như quốc gia, ngày), tránh cột như ID duy nhất.

---

### 4.2. Kiểm Soát Số Lượng File

Spark tạo một file cho mỗi phân vùng khi ghi dữ liệu. Quá nhiều file nhỏ hoặc quá ít file lớn đều gây chậm. Dùng `repartition()` để kiểm soát số file.

**Ví dụ**:
```scala
csvDF.repartition(5)
  .write.format("csv")
  .save("/tmp/multiple.csv")
```

**Kết quả**: Tạo đúng 5 file `.csv`.

**Tại sao nên dùng?**
- Giảm số file nhỏ, giúp hệ thống chạy mượt hơn.
- Phù hợp với tài nguyên cụm (1-2 file mỗi CPU core).

---

### 4.3. Giới Hạn Kích Thước File

File quá lớn hoặc quá nhỏ đều không tốt. Tùy chọn `maxRecordsPerFile` giúp giới hạn số bản ghi mỗi file.

**Ví dụ**:
```scala
csvDF.write
  .option("maxRecordsPerFile", 5000)
  .format("parquet")
  .save("/tmp/output")
```

**Kết quả**: Mỗi file chứa tối đa 5000 bản ghi, tránh file quá lớn.

**Tại sao nên dùng?**
- File vừa phải giúp đọc nhanh hơn.

---

### 4.4. Sử Dụng Định Dạng Parquet

**Parquet** là định dạng mặc định của Spark, lý tưởng cho người mới vì tốc độ nhanh và tiết kiệm dung lượng.

**Ví dụ**:
```scala
csvDF.write
  .format("parquet")
  .option("compression", "snappy")
  .save("/tmp/my-parquet-file.parquet")
```

**Tại sao nên dùng?**
- **Nhanh**: Chỉ đọc cột cần thiết, không quét toàn bộ dữ liệu.
- **Nén tốt**: Giảm kích thước file (ví dụ: 1GB CSV còn 200MB Parquet).
- **Hỗ trợ phức tạp**: Lưu được array, map, struct mà CSV không làm được.

---

Bằng cách áp dụng các kỹ thuật này, bạn sẽ xử lý dữ liệu nhanh hơn, tiết kiệm tài nguyên, và dễ dàng quản lý các pipeline lớn trong Spark.

---
## 5. Tổng Kết

Hiểu cách đọc và ghi dữ liệu trong Apache Spark là bước đầu tiên để làm chủ công cụ này. Với **DataFrameReader** và **DataFrameWriter**, bạn có thể dễ dàng xử lý CSV, JSON, Parquet, hay cơ sở dữ liệu. Dưới đây là các điểm chính dành cho người mới:

- **CSV**: Phù hợp cho dữ liệu đơn giản, nhưng cần cấu hình cẩn thận.
- **JSON**: Hỗ trợ dữ liệu phức tạp, dễ dùng trong ứng dụng web.
- **Parquet**: Lựa chọn tốt nhất cho lưu trữ và xử lý dữ liệu lớn.
- **JDBC**: Kết nối với cơ sở dữ liệu, tận dụng query pushdown để tối ưu.
- **Phân vùng**: Giúp tăng tốc truy vấn bằng cách tổ chức dữ liệu thông minh.

---

**Tài Liệu Tham Khảo**:
- [Apache Spark Documentation: SQL Data Sources](https://spark.apache.org/docs/latest/sql-data-sources.html)