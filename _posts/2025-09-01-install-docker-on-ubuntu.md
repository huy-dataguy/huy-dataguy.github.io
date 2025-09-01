---
title: "Cài đặt Docker Engine và Docker Desktop trên Ubuntu"
date: 2025-09-01
categories: [Ubuntu, Docker]
tags: [docker, ubuntu]

---

Trong bài viết này, mình sẽ hướng dẫn cách **cài đặt Docker Engine và Docker Desktop trên Ubuntu**, đồng thời thiết lập **Docker Sign-In** với GPG key.  

---

## 1. Cài đặt Docker Engine từ `apt` repository

### Bước 1: Thêm Docker repository

```bash
# Cập nhật và cài đặt gói cần thiết
sudo apt-get update
sudo apt-get install ca-certificates curl

# Thêm Docker GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Thêm Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update lại apt
sudo apt-get update
````

### Bước 2: Cài đặt Docker Engine

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

## 2. Cài đặt Docker Desktop trên Ubuntu

### Bước 1: Tải Docker Desktop `.deb`

Tải gói DEB chính thức:

👉 [Download Docker Desktop for Linux](https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb)

### Bước 2: Cài đặt gói

```bash
sudo apt-get update
sudo apt-get install ~/Downloads/docker-desktop-amd64.deb
```

---

## 3. Docker Desktop - Đăng nhập (Sign In)

Docker Desktop trên Linux quản lý credential thông qua **pass** (Password Store) và **GPG key**.

### Bước 1: Tạo GPG key

```bash
gpg --generate-key
```

Nhập **tên** và **email Docker Hub** khi được hỏi. Sau khi hoàn tất, GPG sẽ tạo một key pair.

Ví dụ kết quả:

```
pub   rsa3072 2025-09-01 [SC] [expires: 2027-09-01]
      1234ABCD5678EFGH9012IJKL3456MNOP7890QRST
uid                      Your Name <you@example.com>
```

**Chuỗi in đậm chính là GPG ID**.

---

### Bước 2: Khởi tạo pass với GPG key

```bash
pass init <your_generated_gpg-id_public_key>
```

Ví dụ:

```bash
pass init 1234ABCD5678EFGH9012IJKL3456MNOP7890QRST
```

---

### Bước 3: Đăng nhập Docker Desktop

- Mở Docker Desktop
    
- Chọn **Sign In** → nhập tài khoản Docker Hub
    
- Docker sẽ lưu credential bằng `pass` với GPG key bạn vừa tạo.
