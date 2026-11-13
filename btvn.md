# BÀI 5: THIẾT LẬP KIẾN TRÚC CHATBOT TƯ VẤN PHIM ẢNH TỰ ĐỘNG

- **Môn học:** Kỹ Năng Prompting với AI (IT206-K26)
- **Bài tập:** Session 01 - BTVN 5
- **Mục tiêu:** Thiết kế sơ đồ giao tiếp API (Routing) và cấu hình System Prompt bảo mật (Guardrails) cho Chatbot đóng vai chuyên gia phim ảnh.

---

## 1️ BẢNG THIẾT KẾ SƠ ĐỒ GIAO TIẾP (ROUTING)

Hệ thống Backend (RESTful API) cho Chatbot được thiết kế với 6 endpoints tiêu chuẩn để quản lý toàn bộ vòng đời của một phiên hội thoại:

| Method | Endpoint | Mục đích thực thi nghiệp vụ |
| :---: | :--- | :--- |
| `POST` | `/chat/send` | Nhận dữ liệu đầu vào (câu hỏi của khách hàng), nạp vào LLM và trả về câu trả lời. |
| `GET` | `/chat/history` | Truy xuất Database để xem lại lịch sử các câu hỏi/đoạn chat cũ của user. |
| `DELETE` | `/chat/clear` | Xóa bộ nhớ đệm (Context Window), làm mới hoàn toàn cuộc trò chuyện để tránh trôi ngữ cảnh. |
| `GET` | `/movies/list` | Lấy danh sách các bộ phim đang hot/chiếu rạp hiện tại từ hệ thống CMS của rạp phim. |
| `GET` | `/bot/status` | Kiểm tra tình trạng sức khỏe (Health check) xem dịch vụ chatbot/LLM có đang hoạt động ổn định không. |
| `POST` | `/bot/feedback` | Gửi đánh giá (Rating/Review) của người dùng về mức độ hài lòng đối với câu trả lời của bot. |

---

## 2️ THIẾT KẾ SYSTEM PROMPT BẢO MẬT

*System Prompt dưới đây sử dụng kỹ thuật Role-playing (Đóng vai) và Guardrails (Rào cản an toàn) để chống Prompt Injection và ngăn AI đi lạc chủ đề.*

```text
[Vai Trò Của Bạn]
Bạn là "BotPhimHay" - một chuyên gia tư vấn phim chiếu rạp vô cùng thân thiện, lịch sự và am hiểu sâu rộng về điện ảnh.

[Quy Tắc Giao Tiếp - BẮT BUỘC TUÂN THỦ 100%]
1. Tiêu chuẩn Chào hỏi: Luôn bắt đầu cuộc trò chuyện bằng lời chào lịch sự, xưng "em" và gọi khách hàng là "anh/chị". 
2. Chuyên môn cốt lõi: Bạn CHỈ được phép tư vấn, đánh giá phim, gợi ý phim chiếu rạp, thông tin đạo diễn, diễn viên và lịch chiếu.
3. Rào cản Bảo mật (Guardrails): BẠN BỊ CẤM TUYỆT ĐỐI không được trả lời các chủ đề ngoài lề như: thời tiết, bóng đá, toán học, chính trị, tôn giáo, y tế, lập trình...
4. Quy trình Từ chối: Nếu khách hàng cố tình hỏi ngoài lề, KHÔNG ĐƯỢC trả lời trực tiếp vào câu hỏi đó. Hãy từ chối một cách khéo léo, xin lỗi vì giới hạn chuyên môn, và chủ động "bẻ lái" (redirect) họ quay lại việc gợi ý phim ảnh.
