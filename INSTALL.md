# Tích hợp AAA vào CLI agent

## Dành cho agent được yêu cầu cài AAA

1. Xác định đường dẫn tuyệt đối của repo AAA và đọc `aaa/VERSION` cùng `aaa/SKILL.md`. Chỉ cài khi người dùng yêu cầu. Xác định CLI, phiên bản nếu có thể đọc, môi trường đang chạy và phạm vi cài. Nếu người dùng chưa chỉ phạm vi, chọn cấp người dùng để tránh tạo thay đổi trong repo dự án.
2. Chọn đường dẫn theo bảng. Xác nhận gốc dự án và thư mục người dùng của chính môi trường CLI. Windows, WSL và container có thể có thư mục người dùng riêng. Trong WSL hoặc container, chỉ dùng đường dẫn mà CLI trong môi trường đó truy cập được.
3. Sao chép nguyên thư mục `aaa/` của gói vào thư mục đích với tên `aaa`. Đảm bảo `SKILL.md` nằm trực tiếp trong thư mục `aaa` đích. So sánh nội dung nếu đích đã tồn tại. Nếu giống hoàn toàn, chuyển sang xác minh. Nếu khác, chỉ cập nhật bản đã nhận diện và khi người dùng yêu cầu cập nhật. Bảo toàn cấu hình và các skill khác. Kiểm tra đích và các thư mục cha để tránh ghi qua symlink ngoài phạm vi.
4. Kiểm tra bản sao có cùng bộ file và nội dung với nguồn. Trong CLI đích, xác nhận tên `aaa` được nhận diện và gọi `help` để đối chiếu phiên bản và danh sách thao tác. Nếu phiên hiện tại chưa tải lại skill, dùng lệnh tải lại phù hợp hoặc xác minh trong một phiên mới. Nếu agent chỉ xác minh được file, báo đúng mức đó và đưa lệnh cho người dùng tự kiểm tra discovery.
5. Với CLI khác, chỉ đăng ký vào cơ chế skill hoặc prompt nếu có thể xác minh cơ chế của phiên bản đích. Nếu chỉ có quyền đọc file, dùng chế độ đọc trực tiếp bên dưới và báo rằng skill đang được nạp theo yêu cầu trong phiên.

| CLI | Cấp dự án | Cấp người dùng | Cách thử |
| --- | --- | --- | --- |
| Codex CLI | `<project-root>/.agents/skills/aaa/` | `~/.agents/skills/aaa/` | Chọn `aaa` qua `/skills` hoặc gọi `$aaa help` |
| Antigravity CLI | `<project-root>/.agents/skills/aaa/` | `~/.gemini/config/skills/aaa/` | Gọi `/aaa help` trong giao diện tương tác. Dùng `/skills reload` nếu vừa thêm skill |
| CLI khác có quyền đọc file | Đọc `<aaa_tool>/aaa/SKILL.md` bằng đường dẫn tuyệt đối | Tương tự | `Đọc SKILL.md tại <đường-dẫn> và thực hiện aaa help.` |

Cài cấp dự án một lần có thể phục vụ cả Codex và Antigravity nếu hai CLI thấy cùng thư mục. Ở cấp người dùng, hai CLI dùng các đích riêng. Chọn một phạm vi cho mỗi CLI để tránh hiện nhiều bản AAA cùng tên.

Nếu cài cấp dự án và muốn lưu bản nháp ngay, đưa `.agents/skills/aaa/` vào một commit của dự án hoặc thêm đúng thư mục đó vào file loại trừ Git cục bộ do `git rev-parse --git-path info/exclude` trả về. Khi thư mục cài đặt còn hiện là chưa theo dõi, AAA vẫn chạy được ở chế độ xem trước. Agent chỉ sửa danh sách loại trừ hoặc tạo commit khi được yêu cầu riêng.

## Cài thủ công

Trong hai ví dụ, chạy từ gốc dự án và thay đường dẫn nguồn. Mỗi ví dụ dừng nếu đích đã tồn tại. Ở cấp người dùng thay thư mục cha bằng thư mục tương ứng trong bảng.

**PowerShell**

```powershell
$source = 'D:\AI-Tools\aaa_tool\aaa'
$parent = Join-Path (Get-Location) '.agents/skills'
$destination = Join-Path $parent 'aaa'
if (-not (Test-Path -LiteralPath (Join-Path $source 'SKILL.md') -PathType Leaf)) {
    throw "Khong tim thay SKILL.md tai $source"
}
if (Test-Path -LiteralPath $destination) { throw "AAA da ton tai tai $destination" }
New-Item -ItemType Directory -Path $parent -Force -ErrorAction Stop | Out-Null
Copy-Item -LiteralPath $source -Destination $destination -Recurse -ErrorAction Stop
```

**Bash trên Linux, macOS hoặc WSL**

```bash
aaa_source="/absolute/path/to/aaa_tool/aaa"
aaa_parent="$PWD/.agents/skills"
aaa_destination="$aaa_parent/aaa"
if [ -f "$aaa_source/SKILL.md" ] && [ ! -e "$aaa_destination" ] && [ ! -L "$aaa_destination" ]; then
    mkdir -p "$aaa_parent" && cp -R "$aaa_source" "$aaa_destination"
else
    printf '%s\n' "Kiem tra lai nguon hoac ban AAA dang ton tai tai $aaa_destination" >&2
fi
```

Sau khi sao chép, gọi `help` theo bảng. Khi dùng chế độ đọc trực tiếp, giữ nguyên thư mục `aaa/` để agent đọc được các tham chiếu cạnh `SKILL.md`.

## Cập nhật hoặc gỡ

Cập nhật checkout AAA bằng `git pull --ff-only` từ nguồn đã chọn. Dùng phiên bản hoặc commit đã xem xét để tích hợp.

- **Cập nhật** — xác nhận đích hiện tại là AAA, kiểm tra khác biệt và giữ bản dự phòng ngoài các thư mục được CLI quét. Sao chép đầy đủ phiên bản mới vào vị trí tạm trên cùng filesystem, đối chiếu nội dung rồi thay thư mục đích. Khôi phục bản dự phòng nếu sao chép, thay thế hoặc kiểm tra thất bại. Nếu có sửa riêng trong bản cài, giữ bản đó và xử lý khác biệt trước khi thay.
- **Quay lại** — đưa thư mục AAA đã lưu trở lại đúng vị trí đích và xác minh bằng `help`.
- **Gỡ** — di chuyển riêng thư mục `aaa` đã xác minh ra ngoài vùng CLI quét. Tài liệu kiến trúc trong các dự án có vòng đời riêng.
