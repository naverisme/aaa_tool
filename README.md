# AAA · Adaptive Architecture Assistant

AAA là một gói hướng dẫn để CLI agent khảo sát, thiết kế và duy trì kiến trúc phần mềm theo cùng quy trình. Agent đang dùng cung cấp mô hình suy luận, Git và quyền đọc hoặc ghi file. Gói AAA chạy trong phiên agent với các file Markdown và Git. Có thể dùng chung quy trình cho nhiều dự án.

## Dùng thử trên dự án đầu tiên

Clone gói ở cạnh repo dự án.

```sh
git clone https://github.com/naverisme/aaa_tool.git
```

Mở Codex CLI hoặc Antigravity CLI từ thư mục dự án. Dán vào phiên agent, thay đường dẫn bằng vị trí đã clone.

```text
Đọc <đường-dẫn-tuyệt-đối>/aaa_tool/INSTALL.md.
Cài AAA ở cấp người dùng trong CLI hiện tại và kiểm tra bằng thao tác help.
```

Cấp người dùng cho phép gọi AAA từ nhiều repo trong cùng môi trường. Chọn **cấp dự án** nếu muốn chia sẻ gói với cả nhóm hoặc dùng chung một bản cài cho Codex và Antigravity trong cùng repo. Xem [INSTALL.md](INSTALL.md) để cài thủ công, cập nhật hoặc gỡ cài đặt.

| Agent | Gọi trong phiên agent |
| --- | --- |
| Codex CLI | `$aaa help`, rồi `$aaa audit` |
| Antigravity CLI (`agy`) | `/aaa help`, rồi `/aaa audit` trong giao diện tương tác |
| Agent khác đọc được file | `Đọc <đường-dẫn>/aaa_tool/aaa/SKILL.md và thực hiện aaa audit cho dự án hiện tại.` |

Mỗi CLI áp dụng cơ chế gọi skill của chính nó. `audit`, `design` và các từ bên dưới là thao tác bên trong AAA.

## Các thao tác

| Thao tác | Dùng khi | Kết quả |
| --- | --- | --- |
| `help` | Bắt đầu hoặc quên cú pháp | Phiên bản, cách gọi và danh sách thao tác |
| `init` | Vào repo mới | Phạm vi, mốc Git, kế hoạch đọc và câu hỏi còn thiếu |
| `audit` | Cần biết kiến trúc đang chạy theo mã nguồn | Luồng hiện tại, bằng chứng, vấn đề và giới hạn khảo sát |
| `design [mục tiêu]` | Cần thiết kế mới hoặc thay đổi có thể triển khai | Thành phần, giao diện, luồng lỗi, quyết định và thứ tự làm |
| `explain [chủ đề]` | Cần hiểu một luồng hoặc quyết định | Giải thích có dẫn chiếu đến code và tài liệu |
| `check [quy tắc]` | Cần kiểm tra quy tắc kiến trúc | Đạt, vi phạm hoặc chưa xác minh, kèm bằng chứng |
| `sync` | Code hoặc tài liệu đã thay đổi | So sánh mốc đã duyệt với hiện trạng và đề xuất cập nhật |
| `apply <draft-id>` | Đã xem và muốn xuất bản đúng bản nháp | Ghi nội dung đã duyệt vào một file kiến trúc |

Ví dụ trong Codex CLI, nếu đang làm trên nhánh dự án muốn khảo sát.

```text
$aaa init
$aaa audit Chỉ khảo sát Workspace và quyền thành viên.
$aaa design Thiết kế các chế độ xem công việc và lịch, làm rõ từng nút và luồng thao tác.
$aaa design --draft Thiết kế các chế độ xem công việc và lịch theo các ràng buộc vừa xác nhận.
$aaa apply <draft-id-do-AAA-trả-về>
$aaa sync
```

`design` và `sync` mặc định trả bản xem trước. Thêm `--draft` để lưu bản nháp có thể duyệt. Với dự án mới, AAA đề xuất `docs/architecture/overview.md` và lưu nháp ở `docs/architecture/overview.draft.md`. Dự án đã có tài liệu chính thức có thể dùng đường dẫn hiện có.

AAA tự đọc nguồn liên quan trước khi hỏi. Nếu thiếu dữ kiện làm thay đổi phạm vi, quyền, dữ liệu hoặc quyết định khó đảo ngược, AAA hỏi đúng phần đó và tiếp tục phân tích phần đã rõ. Bản nháp và xuất bản cần mốc Git đã commit và cây làm việc sạch ngoài hai file tài liệu đã chọn. Repo chưa có commit hoặc đang sửa code vẫn dùng được chế độ xem trước. Khi xuất bản, AAA kiểm tra lại mốc Git, bản nháp và file đích. Mã nguồn và trạng thái Git do người dùng quản lý.

## Nội dung gói

| Đường dẫn | Vai trò |
| --- | --- |
| [INSTALL.md](INSTALL.md) | Hướng dẫn dành cho agent và người dùng khi tích hợp |
| [aaa/SKILL.md](aaa/SKILL.md) | Điểm vào và hợp đồng thao tác |
| [aaa/references/](aaa/references/) | Quy tắc khảo sát, thiết kế, xuất bản và đồng bộ |
| [aaa/assets/architecture.md](aaa/assets/architecture.md) | Mẫu tài liệu kiến trúc |
| [aaa/VERSION](aaa/VERSION) | Phiên bản để kiểm tra bản đang nạp |

AAA dựa trên khả năng của agent chủ. Quyền, sandbox và cách gửi dữ liệu cho mô hình do CLI đang dùng kiểm soát. Hỗ trợ nạp được mô tả theo [tài liệu OpenAI](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills) và [tài liệu Google Antigravity](https://antigravity.google/docs/skills/#cli-skill-locations). CLI khác có thể dùng chế độ đọc file trực tiếp.
