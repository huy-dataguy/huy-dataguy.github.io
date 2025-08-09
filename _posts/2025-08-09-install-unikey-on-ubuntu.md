---
title: "Cài đặt bộ gõ Tiếng Việt trên Ubuntu"
date: 2025-08-09
categories: [Ubuntu, Unikey]  
tags: [fcitx5, unikey, ubuntu]  
---
## Fcitx5-Unikey là gì?

Fcitx5 là một **hệ thống quản lý phương thức nhập liệu** (input method framework) trên Linux, hỗ trợ nhiều ngôn ngữ khác nhau. Unikey là một **input engine** (công cụ gõ) được tích hợp vào Fcitx5, chuyên xử lý việc nhập liệu tiếng Việt. Khi được cấu hình, Fcitx5-Unikey cho phép bạn chuyển đổi giữa tiếng Anh và tiếng Việt, đồng thời áp dụng các quy tắc gõ (như Telex hoặc VNI) để tạo ra các ký tự tiếng Việt có dấu.
## Cài Đặt và Sử Dụng Fcitx5-Unikey trên Ubuntu
#### Bước 1: Cài đặt Fcitx5 và Unikey
Mở **Terminal** và chạy lệnh sau để cài đặt các gói cần thiết:

```bash
sudo apt install fcitx5 fcitx5-unikey fcitx5-config-qt fcitx5-frontend-gtk3 fcitx5-frontend-gtk4
```

Lệnh này sẽ cài đặt Fcitx5 cùng với bộ gõ Unikey và các công cụ cấu hình giao diện.

---
#### Bước 2: Đặt Fcitx5 làm bộ gõ mặc định
Để đảm bảo Fcitx5 được sử dụng làm bộ gõ mặc định, chạy lệnh:

```bash
im-config -n fcitx5
```

Sau đó, **đăng xuất và đăng nhập lại** ubunut để áp dụng thay đổi.

---
#### Bước 3: Thêm bộ gõ tiếng Việt Unikey
Mở công cụ cấu hình Fcitx5 bằng lệnh:

```bash
fcitx5-configtool
```

Trong cửa sổ cấu hình:
1. Chuyển sang tab **Input Method**.
2. Nhấn nút **“+”** để thêm bộ gõ (giữ nguyên Keyboard-English(US).
3. Tìm kiếm **Unikey** hoặc **Vietnamese** trong danh sách.
4. Chọn **Unikey** và nhấn **OK**.

---
#### Bước 4: Cấu hình kiểu gõ

1. Trong danh sách bộ gõ, chọn **Unikey** và nhấn biểu tượng **bánh răng ⚙** để mở cài đặt.
2. Chọn kiểu gõ phù hợp:
    - **Telex**:
    - **VNI**:
Nhấn **OK** để lưu cấu hình.

---
#### Bước 5: Chuyển đổi giữa các bộ gõ
Phím tắt mặc định:

- **Ctrl + Space**: Chuyển qua lại giữa Unikey và bàn phím tiếng Anh.
- |*Nếu phím tắt không hoạt động, bạn có thể thay đổi trong công cụ cấu hình:*
1. Vào tab **Global Options** trong `fcitx5-configtool`.
2. Trong mục **Trigger Input Method**:
    - Nhấn **“-”** để xóa phím tắt hiện tại (Ctrl + Space).
    - Nhấn **“+”** và chọn phím mới, ví dụ: **Super + Space**.
3. Nhấn **OK** để lưu.

- Lưu ý: Nếu phím chuyển ngôn ngữ của **fcitx5-configtool** trùng với keyboard shortcuts của Ubuntu thì bạn sẽ không chuyển được, nên cần phải tắt keyboard shortcuts trước.
- Bạn cũng có thể nhấp vào biểu tượng **V** (tiếng Việt) hoặc **E** (tiếng Anh) ở góc màn hình để chuyển đổi nhanh.

---
#### Không thấy bộ gõ Unikey?

Chạy lại lệnh cài đặt:

```bash
sudo apt install fcitx5-unikey
```
#### Fcitx5 không hoạt động?

Khởi động lại Fcitx5 bằng lệnh:

```bash
fcitx5 -r
```
