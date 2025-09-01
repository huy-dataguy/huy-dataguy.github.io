
---
title: "Cài đặt và Tạo Shortcut cho Anki trên Linux"
date: 2025-09-01
categories: [Ubuntu, Anki]
tags: [anki, shortcut]

---

Trong bài viết này, mình sẽ hướng dẫn cách **cài đặt Anki trên Linux**, sau đó **tạo shortcut để hiển thị trong menu ứng dụng**. Mình thực hiện trên Ubuntu, nhưng các bản phân phối Linux khác cũng tương tự.

## 1. Cài đặt các thư viện cần thiết

Trước tiên, hãy cài các gói phụ thuộc cần cho Anki:

```bash
sudo apt install libxcb-xinerama0 libxcb-cursor0 libnss3 zstd mpv
```

---

## 2. Tải Anki

Truy cập trang chính thức để tải bản mới nhất:

👉 [Download Anki](https://apps.ankiweb.net/)

Sau khi tải, file sẽ nằm trong thư mục `~/Downloads`.

---

## 3. Giải nén và di chuyển Anki

```bash
cd ~/Downloads
tar xaf anki-2XXX-linux-qt6.tar.zst
mkdir -p ~/Applications
mv anki-2XXX-linux-qt6 ~/Applications/anki
```

Ở đây `2XXX` là số version của bạn (ví dụ: `anki-25.07.5-linux-qt6`).

---

## 4. Tạo shortcut để Anki hiển thị trong menu

Mở file `.desktop` trong thư mục ứng dụng cá nhân:

```bash
vim ~/.local/share/applications/anki.desktop
```

Thêm nội dung sau (nhớ chỉnh lại đường dẫn đúng với máy bạn):

```ini
[Desktop Entry]
Name=Anki
Exec=/home/dataguy/Applications/anki/anki
Icon=/home/dataguy/Applications/anki/anki.png
Type=Application
Categories=Education;
Terminal=false
```

---

## 5. Cấp quyền và cập nhật database

```bash
chmod +x ~/.local/share/applications/anki.desktop
update-desktop-database ~/.local/share/applications
```

Giờ bạn có thể tìm **Anki** trực tiếp trong menu ứng dụng Linux.
