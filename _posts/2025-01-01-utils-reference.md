---
title: "Reusable Utils & Code Snippets"
date: 2025-07-01
categories: [Utils]
tags: [pyspark,spark,spark-sql]
description: "Collection of reusable utility functions and code snippets for Spark, Scala, Python, and SQL."

---
## 1. Spark
#### a.Write data to console
```bash
(df.write
    .format("console")
    .option("truncate", False)
    .save())
```
## 2. Spark-SQL
#### a. Show info of table in database
```bash
spark.read.table("spark_catalog.bronze.reddit_submission").show()
```
```bash
spark.read.table("spark_catalog.bronze.reddit_submission").printSchema()
```
```bash
spark.read.table("spark_catalog.bronze.reddit_submission").count()
```
```bash
val df = spark.read.table("spark_catalog.bronze.reddit_submission")
df.columns.length
```
```bash
spark.sql("DESCRIBE FORMATTED spark_catalog.bronze.reddit_submission").show(200, false)
```
