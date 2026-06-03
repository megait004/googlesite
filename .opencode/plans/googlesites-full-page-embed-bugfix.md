# Google Sites Full Page Embed Layout Bugfix

## Metadata

- Mode: bugfix
- Workflow: bugfix-analysis
- Phase: bugfix-analysis
- Status: needs-review

## Current Behavior

- Trang hiện tại render như một card/email được căn giữa trong viewport bằng `body.min-h-screen + flex + justify-center` tại `index.html:10`.
- Hero card có chiều cao tối thiểu cố định `min-h-[499px] sm:min-h-[508px]` tại `index.html:12`.
- Khi dán URL vào Google Sites theo kiểu embed thường, nội dung bị nhét vào container có scroll nội bộ; user report ảnh chụp cho thấy card bị crop và xuất hiện scrollbar.

## Expected Behavior

- Khi dùng **Google Sites Full page embed**, nội dung chính phải bám theo chiều rộng/chiều cao page embed, không còn cảm giác bị nhét vào card ở giữa với khoảng trắng lớn dưới nội dung.
- Không có crop lớn ở hero đầu; nếu còn scroll thì phải là scroll của page cha hoặc chỉ còn ở mức tối thiểu, không phải do fixed/min viewport pattern của trang nguồn.

## Unchanged Behavior

- Giữ nguyên mục tiêu clone 1 landing page tĩnh theo mẫu `https://megait004.github.io/googlesite/`.
- Giữ stack hiện tại: static `index.html` + Tailwind CDN (`index.html:8`).
- Không mở rộng sang multi-page, framework, backend, hay logic form thật.

## Reproduction / Evidence

- `index.html:10` dùng `min-h-screen flex items-start justify-center` trên `body`; đây là pattern dễ lệ thuộc viewport khi bị render trong embed container.
- `index.html:11-12` giới hạn wrapper `max-w-[359px] sm:max-w-[600px]` và card `min-h-[499px] sm:min-h-[508px]`, làm khung email-like khó co giãn theo container embed.
- Google Sites Help xác nhận có hai mode nhúng: `Insert > Embed` và `Pages > Full page embed`; full page embed là mode phù hợp cho cả website page, nhưng vẫn là một lớp container của Sites: <https://support.google.com/sites/answer/90569?hl=en>.
- External evidence: các case tương tự trên Stack Overflow mô tả Google Sites embed không auto-resize height, dễ sinh scrollbar nội bộ khi trang nguồn có layout/height không phù hợp: <https://stackoverflow.com/questions/76462022/google-sites-container-with-embedded-url-is-not-responsive>, <https://stackoverflow.com/questions/63844137/properly-embed-google-apps-script-url-into-google-site>.

## Root Cause Boundary

- Trong scope repo: layout hiện tại tối ưu cho mở trực tiếp trong browser như một card độc lập, chưa tối ưu cho **embedded container**.
- Ngoài scope repo: Google Sites không cho kiểm soát hoàn toàn iframe/container sizing; không thể đảm bảo loại bỏ mọi hạn chế do nền tảng.

## Risk Lane

- Lane: bugfix
- Risk: trung bình; thay đổi layout có thể làm trang khác nhẹ so với mẫu gốc nếu giảm ràng buộc viewport/card.

## Validation Notes

- Proof local: inspect `index.html` sau sửa để bảo đảm bỏ các pattern height/viewport gây crop không cần thiết.
- Proof thực tế: publish lên URL nguồn, nhúng bằng **Pages > Full page embed** trong Google Sites, rồi preview desktop/mobile.
- Reject nếu vẫn còn crop lớn ở hero đầu hoặc còn khoảng trắng vô lý do layout trung tâm toàn màn hình.

## Proof Matrix

| Bugfix truth | Evidence now | Required proof after build |
| --- | --- | --- |
| Layout hiện tại phụ thuộc viewport/card | `index.html:10-12` | body/card không còn chốt theo viewport height không cần thiết |
| Full page embed là mode đúng hơn block embed | Google Sites Help URL | User preview Full page embed không còn khung nhỏ như ảnh |
| Vấn đề không thể giải quyết hoàn toàn từ phía Sites | Stack Overflow refs | Plan giới hạn kỳ vọng rõ ràng, không hứa bỏ mọi giới hạn nền tảng |

## Friction / Lessons To Preserve

- Situation: clone page email-like nhưng deploy vào Google Sites embed.
- Root cause: layout browser-direct bị reuse trong embedded container với sizing khác.
- Future rule: với page dự kiến nhúng Google Sites, tránh phụ thuộc `vh`, fixed/min-height lớn, và center-the-whole-screen nếu không bắt buộc.
