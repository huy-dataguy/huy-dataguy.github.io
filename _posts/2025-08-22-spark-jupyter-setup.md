---
title: "Cài đặt Apache Spark và Jupyter Lab trên Ubuntu với pip"
date: 2025-08-21
categories: [Apache Spark, Setup venv]
tags: [spark, pyspark, jupyter, ubuntu, pip, miniconda]

---
Trong bài viết này mình sẽ hướng dẫn cách cài đặt **Apache Spark** trên Ubuntu và cấu hình môi trường để chạy với **Jupyter Lab** bằng `pip`. Đây là setup cơ bản giúp bạn học và thực hành Spark trong Big Data.

---
## 0. Một số khái niệm
- **PySpark** = dùng Spark bằng Python.
- **findspark** = thư viện Python hỗ trợ tìm Spark trong máy.
- **virtualenv (venv)/conda + ipykernel** = công cụ tạo môi trường ảo, tách biệt thư viện cho từng project.
  
---

## 1. Cài đặt Java JDK

Spark chạy trên JVM, nên cần Java trước.

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
java -version
````

---

## 2. Cài đặt Python và pip

Ubuntu thường có sẵn Python 3. Kiểm tra:

```bash
python3 --version
```

Nếu thiếu pip thì cài thêm:

```bash
sudo apt install python3-pip -y
```

---

## 3. Tải Apache Spark

Truy cập [Spark Download](https://dlcdn.apache.org/spark/) và lấy link bản mới nhất. Ví dụ Spark 3.5.1:

```bash
wget https://dlcdn.apache.org/spark/spark-3.5.6/spark-3.5.6-bin-hadoop3.tgz
tar -xvzf spark-3.5.6-bin-hadoop3.tgz
mv spark-3.5.6-bin-hadoop3 /opt/spark
```

---

## 4. Cấu hình biến môi trường

Mở file `~/.bashrc`:

```bash
vim ~/.bashrc
```

Thêm cuối file:

```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
export SPARK_HOME=/opt/spark
export PATH=$SPARK_HOME/bin:$PATH
```

Nạp lại:

```bash
source ~/.bashrc
```

---

## 5. Cài đặt PySpark và Jupyter Lab bằng pip

Tạo môi trường ảo:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Cài thư viện:

```bash
pip install pyspark findspark jupyterlab
```

Đăng ký kernel:

```bash
python -m ipykernel install --user --name=.venv --display-name "Python (.venv)"
```
---

## 6. Kiểm tra

Chạy thử Spark shell:

```bash
pyspark
```

Chạy Jupyter Lab:

```bash
jupyter lab
```

Trong Jupyter, tạo 1 notebook và chọn kernel **Python (.venv)** và thử code Spark
<img width="1004" height="519" alt="image" src="https://github.com/user-attachments/assets/719247d8-b8e8-4f99-865b-848884380f89" />

---

## 7. Example
a. test.txt
```text
I am a final year student of HCMUTE, I am passionate about BigData, and spark is one of my favorite tools.
spark is a very powerful tool in BigData processing
```

b. test.ipynb
<img width="1014" height="512" alt="image" src="https://github.com/user-attachments/assets/1c841219-3dde-4f4f-b89b-bb6f41fe2270" />

## 8. Hướng dẫn cài thêm cài đặt môi trường bằng miniconda
a. Xem hướng dẫn cài đặt miniconda tại:
  [miniconda install](https://www.anaconda.com/docs/getting-started/miniconda/install)

b. Tạo và activate môi trường ảo bằng miniconda

```bash
conda create -n myvenv python=3.10
```
```bash
conda activate myvenv
```

c. Cài đặt jupyter lab và đăng ký kernel
```bash
pip install jupyterlab
```
```bash
pip install pyspark==3.5.3
```
```bash
python -m ipykernel install --user --name=myvenv --display-name "Python (pyspark)"
```
