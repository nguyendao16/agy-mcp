# Đề xuất xử lý bridge MCP

MCP transport, doctor và tác vụ chỉ đọc đã đạt; chưa chứng minh quyền sửa file. R16 attempt 3 failed exit 3, không sửa code/tests. Dừng các lượt triển khai R16 theo giới hạn AGENTS.md; đây là chẩn đoán tích hợp riêng, không tự mở lượt thứ tư bằng cách đổi tên task.

Code đã cài của `agy-mcp` 0.1.9 có điểm cần sửa/kiểm chứng: `adapters/agy.py::build_command` không truyền `request.model` vào argv. Metadata lại có thể phản ánh model yêu cầu, tạo ấn tượng đã chọn model. Log attempt 3 ghi model trống và đúng conversation cũ. Chưa biết model thực dùng khi resume. `--mode=accept-edits` cũng không được thêm cho execute; cần kiểm khả năng sửa file theo scope sau khi sửa mapping. Sync và async cùng gọi adapter này, nên chưa có cơ sở kết luận đổi sang sync sẽ giải quyết lỗi.

Phương án đề xuất, chưa thực hiện:

1. Lập patch bridge tối thiểu và unit tests: truyền model tường minh bằng fused argv `--model=<model>`; ánh xạ execute+allow_write sang chế độ file edits được CLI 1.4.0 hỗ trợ, không cấp shell rộng hoặc tắt sandbox. Read-only không nhận quyền sửa.
2. Review patch/tests trước khi áp dụng vào môi trường MCP đã cài hoặc đổi cấu hình server sang bản đã review. Không cài mã tải mới, không sửa settings/allowlist Antigravity toàn cục. Phần ngoài workspace cần quyền môi trường và sự duyệt rõ ràng của người dùng.
3. Chạy lại doctor, read-only; sau đó một kiểm tra ghi file tổng hợp trong thư mục tạm, không ghi production/data. Kiểm log/argv thực chứng minh Gemini Flash High; không chỉ dựa metadata.
4. Nếu kiểm tra capability đạt, xin/ghi nhận chỉ đạo tiếp tục R16 sau giới hạn ba lượt; chỉ sửa hai file đã được duyệt, rồi chạy regression suite và scope check.

Không khẳng định việc sửa flag sẽ giải quyết quota error: nguyên nhân exit 3 chưa được chứng minh. Thông báo reset quota trùng nguyên văn lỗi cũ trong conversation, trong khi tác vụ đọc trước đó thành công. Nếu vẫn lỗi sau kiểm chứng, báo nguyên nhân/evidence mới, không lặp request mù quáng hoặc đổi model/conversation tự động.
