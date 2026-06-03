# Design Document

## Overview

Mục tiêu là chuyển landing page hiện tại sang cấu trúc **embed-safe cho Google Sites Full page embed**: ưu tiên content-flow theo chiều dọc, giảm phụ thuộc viewport height, giữ visual gần mẫu hiện tại.

## Current Map

- Entry duy nhất: `index.html`.
- Tailwind qua CDN tại `index.html:8`.
- `body` đang vừa làm nền gradient vừa làm layout container (`index.html:10`).
- Wrapper/card hiện tại cố định khá mạnh về chiều rộng và chiều cao (`index.html:11-12`).
- Footer legal tách dưới card ở `index.html:24-27`.

## Design

- Tách vai trò layout thành 3 lớp logic ngay trong HTML hiện có:
  1. **Page shell**: full-width, top-aligned, padding an toàn cho embed.
  2. **Content wrapper**: max-width mềm, không ép khung quá hẹp trên desktop.
  3. **Hero card**: giữ gradient + bo góc + shadow, nhưng bỏ phụ thuộc `min-h-*` lớn nếu không cần.
- Loại bỏ hoặc giảm các pattern gây lỗi trong embed:
  - `min-h-screen`
  - center theo toàn viewport nếu không cần
  - `min-h-[499px]/[508px]`
- CTA và text vẫn giữ hierarchy hiện tại; chỉ chỉnh spacing/width để tránh crop trong container lạ.

## Architecture

- Không đổi stack, không thêm JS framework.
- Dùng lại `index.html` như single source.
- Asset hiện có (`metalogo.svg`, `metefavicon.ico`) được giữ nguyên.

## Interfaces

- External interface duy nhất: URL được Google Sites nhúng qua **Full page embed** theo tài liệu Google Sites Help.
- No runtime API, no JS messaging với parent iframe.

## Data Models

- Không có data model mới.

## Testing Strategy

- Static inspection: xác minh đã bỏ viewport-height coupling tại các node layout chính.
- Browser check: mở trực tiếp URL để bảo đảm visual không lệch mạnh khỏi mẫu.
- Integration check: nhúng vào Google Sites bằng **Pages > Full page embed**, preview desktop/mobile, quan sát crop/scroll/spacing.

## Proof Matrix

| Design claim | Build proof |
| --- | --- |
| Page shell không còn phụ thuộc `min-h-screen` | Diff `index.html` tại layout root |
| Hero card tự cao theo content thay vì min-height cứng | Diff `index.html` tại section classes |
| Full page embed vẫn giữ visual gần mẫu | User preview/screenshot sau publish |

## Risks and Assumptions

- Assumption: user sẽ dùng đúng **Pages > Full page embed**, không quay lại block embed.
- Assumption: mục tiêu là “không vỡ/crop khó chịu”, không phải identical pixel-perfect trong mọi viewport của Google Sites.
- Risk: nếu Google Sites vẫn áp một container height bất thường, cần thêm một pass giảm spacing/font/card density.

## Validation Gate

- Pass khi:
  - layout root không còn phụ thuộc viewport height;
  - hero không còn min-height lớn gây khoảng trắng/crop;
  - user xác nhận Full page embed preview ổn hơn ảnh lỗi ban đầu.
- Fail nếu chỉ đổi class nhỏ nhưng vẫn giữ nguyên cơ chế layout cũ.
