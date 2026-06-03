# Implementation Plan

## Metadata

- Slug: googlesites-full-page-embed
- Phase: tasks
- Status: approved-for-build
- Current: true
- Updated: 2026-06-03T00:00:00Z
- Related requirements: none
- Related bugfix: .opencode/plans/googlesites-full-page-embed-bugfix.md
- Related design: .opencode/plans/googlesites-full-page-embed-design.md

- [ ]   1. Probe và chỉnh layout root cho embed-safe flow
    - Xác nhận các class/layout đang tạo viewport coupling tại `index.html:10-12`; đổi `body` sang page shell top-aligned, full-width, padding-based, không phụ thuộc `min-h-screen`.
    - Giữ background gradient nhưng tách khỏi logic căn giữa toàn màn hình.
    - Verification: inspect `index.html` cho thấy root không còn `min-h-screen`/center-the-whole-screen không cần thiết.
    - Stop condition: layout root cao theo content thay vì viewport heuristic.
    - _Requirements: bugfix-current-vs-expected-1_

- [ ]   2. Nới wrapper và bỏ card min-height cứng để giảm crop/scroll
    - Điều chỉnh wrapper `max-w-*` và hero card classes tại `index.html:11-12` để phù hợp Full page embed; bỏ `min-h-[499px] sm:min-h-[508px]`, giữ visual card gần mẫu bằng spacing mềm.
    - Kiểm tra text, CTA, legal footer vẫn giữ hierarchy hiện có.
    - Verification: inspect `index.html` cho thấy section cao theo content, không còn fixed/min height lớn; browser preview không có khoảng trắng bất thường.
    - Stop condition: hero render đầy đủ mà không cần dựa vào height cứng.
    - _Requirements: bugfix-current-vs-expected-1_

- [ ]   3. Chạy pass responsive và handoff quy trình Google Sites Full page embed
    - Tinh chỉnh spacing/font/wrapper cuối cùng nếu cần để desktop/mobile đều ổn; ghi rõ bước publish + `Pages > Full page embed` để user test đúng mode.
    - Verification: proof gồm local visual check + hướng dẫn test Full page embed trên Google Sites.
    - Stop condition: có checklist xác minh crop/scroll cho desktop và mobile trong Sites.
    - _Requirements: bugfix-current-vs-expected-1_

## Parallel Waves

- Wave 1: Task 1
- Wave 2: Task 2
- Wave 3: Task 3

## Handoff

- Ready for build when: pointer `.opencode/plans/current.json` trỏ tới task này với `source: planner-agent`, `phase: tasks`, `status: approved-for-build`.
- Next action: build trực tiếp `index.html` theo hướng Google Sites Full page embed-safe rồi kiểm tra lại diff/render.
