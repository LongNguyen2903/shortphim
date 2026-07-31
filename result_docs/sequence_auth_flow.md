# Sơ đồ Sequence Diagram: Luồng Xem Phim Miễn Phí 100% & Đồng Bộ Tài Khoản (Free Playback Flow)

Dưới đây là sơ đồ trình tự tương tác hệ thống khi người dùng xem phim ngắn trọn bộ 100% Miễn phí và luồng đăng nhập để đồng bộ dữ liệu cá nhân.

![Sơ đồ Sequence Diagram](./sequence_auth_flow.png)

## Mã nguồn Mermaid (Dark Theme)
```mermaid
sequenceDiagram
    autonumber
    actor User as User (Khán giả)
    participant App as Mobile App (PineDrama)
    participant Player as Video Player
    participant Backend as API Server
    participant DB as User Database

    Note over User, DB: LUỒNG XEM PHIM NGẮN 100% MIỄN PHÍ & ĐỒNG BỘ CÁ NHÂN

    User->>App: 1. Chọn bất kỳ Tập phim nào (Tập 1 -> Tập 80)
    App->>Backend: 2. Request Stream URL (Tập 12)
    Backend->>Backend: 3. Kiểm tra tính khả thi của tập phim (Status: FREE)
    Backend-->>App: 4. 200 OK - Return Stream URL HLS
    App->>Player: 5. Autoplay Video Tập 12 ngay lập tức (Không khóa)

    opt Người dùng bấm Thả Tim / Lưu Tủ Phim / Đồng Bộ Lịch Sử
        alt Đã Đăng Nhập
            App->>Backend: POST /api/v1/user/watchlist (Kèm JWT Token)
            Backend->>DB: Lưu vào danh sách cá nhân
            Backend-->>App: OK - Đã lưu thành công
        else Chưa Đăng Nhập (Guest User)
            App->>App: Hiển thị Pop-up Nhắc Đăng Nhập
            User->>App: Đăng nhập bằng Google / Apple / SĐT
            App->>Backend: POST /api/v1/auth/login
            Backend-->>App: Trả về JWT AccessToken & User Info
            App->>Backend: Đồng bộ dữ liệu Lịch sử xem
        end
    end
```

## Bảng giải thích chi tiết luồng
1. **Phim mở 100% Free:** Mọi tập phim từ Tập 1 đến tập 80+ đều gọi API phát HLS video ngay lập tức không bị rào cản phân quyền hay trả phí.
2. **Trải nghiệm phát liền mạch:** Người dùng vuốt lên/xuống chuyển tập mượt mà không bị ngắt quãng bởi màn hình khóa.
3. **Đồng bộ đám mây:** Khi đăng nhập, tiến trình lịch sử xem phim và danh sách tủ phim sẽ được lưu an toàn trên Database.
