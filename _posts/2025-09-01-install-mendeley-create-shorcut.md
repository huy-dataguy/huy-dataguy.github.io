---
title: "Cài đặt và Tạo Shortcut cho Mendeley Reference Manager (AppImage) trên Linux"
date: 2025-09-01 19:15:00 +0700
categories: [Ubuntu, Mendeley]
tags: [mendeley, appimage, shortcut]

---

Trong bài viết này, mình sẽ hướng dẫn cách **cài đặt Mendeley Reference Manager dạng AppImage** trên Linux, sau đó **tạo shortcut để hiển thị trong menu ứng dụng**.  

---

## 1. Tải Mendeley Reference Manager

Truy cập trang chính thức:  

👉 [Download Mendeley Reference Manager](https://www.mendeley.com/download-reference-manager)

File tải về có dạng:  

```

mendeley-reference-manager-2.137.0-x86_64.AppImage

````

---

## 2. Di chuyển AppImage sang thư mục `Applications`

Tạo thư mục riêng để quản lý ứng dụng:

```bash
mkdir -p ~/Applications
mv ~/Downloads/mendeley-reference-manager-2.137.0-x86_64.AppImage ~/Applications/mendeley.AppImage
````

---

## 3. Cấp quyền thực thi

```bash
chmod +x ~/Applications/mendeley.AppImage
```

Cài thêm thư viện cần thiết cho AppImage:

```bash
sudo apt install libfuse2
```

---

## 4. Tạo shortcut trong menu ứng dụng

Tạo file `.desktop`:

```bash
vim ~/.local/share/applications/mendeley.desktop
```

Dán vào nội dung sau (chỉnh lại đường dẫn theo máy bạn):

```ini
[Desktop Entry]
Name=Mendeley Reference Manager
Exec=/home/dataguy/Applications/mendeley.AppImage --no-sandbox
Icon=/home/dataguy/Applications/mendeley.png
Type=Application
Categories=Office;
Terminal=false
```

> Gợi ý: bạn có thể tải icon chính thức hoặc dùng icon `.png` bất kỳ, lưu tại `~/Applications/mendeley.png`.

---

## 5. Kích hoạt shortcut

```bash
chmod +x ~/.local/share/applications/mendeley.desktop
update-desktop-database ~/.local/share/applications
```

Giờ bạn có thể mở menu ứng dụng và tìm **Mendeley Reference Manager**.
