# Quiz Arena — Trò chơi trắc nghiệm tốc độ nhiều người (Java, TCP)

> **Bản nháp do AI soạn.** Chép các mục 2–9 vào `README.md` của repo nhóm (giữ bảng Team đã có), rồi điền mọi chỗ `⟦ĐIỀN⟧` bằng thông tin thật. Không để sót chữ `⟦` khi nộp.

## 1. Team (mandatory)
| # | Full name | Student ID | Class | GitHub | Role |
|---|---|---|---|---|---|
| 1 | Hồ Mạnh Huy | B23DCCN384 | ltm nhóm 04 | @huyhm1825 | Trưởng nhóm |
| 2 | Bùi Quang Minh | B23DCCN538 | ltm nhóm 04 | @bqminh195 | Thành viên |
| 3 | Lê Duy Vinh | B23DCCN930 | ltm nhóm 04 | @vinh-OOP | Thành viên |

## 2. Problem & Idea (mandatory)
Nhiều người muốn thi trắc nghiệm nhanh chung một bài, cần một trọng tài trung lập: **ai trả lời đúng đầu tiên thì được điểm**. Server giữ câu hỏi, đồng hồ và bảng điểm; client nối bằng TCP, nhận câu hỏi cùng lúc, gửi đáp án. Thử thách mạng chính: tự quy ước khung tin trên TCP, xử lý nhiều người trả lời đúng cùng lúc (đúng một người thắng), và không sập khi client tắt đột ngột, gửi rác hoặc im lặng. Đề thuộc nhóm "Multiplayer Network Game" (mục 5 của `INSTRUCTION.md`). Xem `docs/proposal.md`.

## 3. Features
- Vào game bằng tên (`NAME`), nhận câu hỏi A/B/C/D, trả lời (`ANSWER` hoặc chỉ gõ chữ cái), xem bảng điểm (`SCORE`).
- Mỗi câu: người đúng **đầu tiên** thắng; hết giờ thì công bố đáp án; mỗi người một lần thử mỗi câu.
- Người vào giữa chừng nhận ngay câu đang diễn ra; thông báo có người vào/ra.
- Giới hạn số client (`ERR 503`), ngắt client im lặng (`ERR 408`), chặn dòng quá dài (`ERR 413`).

## 4. Architecture
Server Java: thread-per-client + một luồng trò chơi. Trạng thái dùng chung được bảo vệ bằng `ConcurrentHashMap` và `AtomicReference.compareAndSet` (không có khóa lồng nhau). Sơ đồ và lý do: `docs/architecture.md`. Giao thức: `docs/protocol-design.md`.

## 5. Quick Start
**Chạy trực tiếp bằng Java (JDK 17 trở lên):**
```
cd server
javac -encoding UTF-8 -d bin src\*.java
java -cp bin Server
```
Mở thêm terminal, mỗi người chơi một cửa sổ:
```
cd client
javac -encoding UTF-8 -d bin src\*.java
java -cp bin Client
```
Gõ `NAME an`, rồi gõ `A`, `B`, `C` hoặc `D` khi có câu hỏi. Chi tiết (NetBeans, VS Code): `HUONG_DAN.md`.

**Docker (cách thầy chấm):**
```
copy .env.example .env
docker compose up --build
docker compose run --rm client
```
⟦ĐIỀN sau khi nhóm đã chạy thành công từ bản clone sạch⟧

## 6. Demo & Test Evidence
⟦ĐIỀN: ảnh chụp / log thật từ các lần chạy của nhóm, xem bảng trong `docs/testing-guide.md`⟧

## 7. Documentation
- [`docs/proposal.md`](docs/proposal.md)
- [`docs/protocol-design.md`](docs/protocol-design.md)
- [`docs/architecture.md`](docs/architecture.md)
- [`docs/testing-guide.md`](docs/testing-guide.md)
- [`docs/ai-log.md`](docs/ai-log.md)

## 8. AI Disclosure (mandatory)
> Policy: [`INSTRUCTION.md` §7](INSTRUCTION.md#7-ai-usage-policy). Disclosing AI use never lowers your score — hiding it does.

### 8.1 Summary
| Tool / model | Used by | Used for | Files / modules | Level |
|---|---|---|---|---|
| Claude (Sonnet 5.5) | ⟦ĐIỀN⟧ | So sánh và chọn đề tài, soạn proposal | `docs/proposal.md` | Generated, nhóm đã đọc và chỉnh ⟦xác nhận⟧ |
| Claude (Sonnet 5.5) | ⟦ĐIỀN⟧ | Viết server và client Java, script kiểm thử | `server/`, `client/`, `scripts/test_quiz.py` | Generated, nhóm đã đọc và chạy ⟦xác nhận⟧ |
| Claude (Sonnet 5.5) | ⟦ĐIỀN⟧ | Thiết kế giao thức, kiến trúc, tài liệu test | `docs/*.md`, `README.md` | Generated, nhóm đã đối chiếu với code ⟦xác nhận⟧ |

**Levels:** **Assist** — explanations, suggestions, review; we wrote the code. **Co-write** — AI drafted parts, we substantially rewrote. **Generated** — AI wrote most of it; we reviewed, tested and can explain it.

> ⚠️ Chỉ ghi "Generated, nhóm đã đọc và chạy" nếu đúng là cả nhóm đã đọc và chạy. Phần nào không giải thích được khi vấn đáp sẽ không được tính điểm, dù chạy đúng.

### 8.2 Decisions we made ourselves
⟦ĐIỀN bằng lời của nhóm. Gợi ý các quyết định thật của dự án; chỉ giữ những cái nhóm thực sự đồng ý và hiểu lý do.⟧
- Chọn game Quiz (đề số 3 của thầy) vì dễ hiểu, có một trạng thái dùng chung chính, tránh phần ghép cặp/lượt đi của Caro.
- Khung tin bằng ký tự `\n` thay vì tiền tố độ dài, vì giao thức là văn bản; giới hạn 1024 ký tự chống dòng vô tận.
- Thread-per-client có giới hạn `MAX_CLIENTS` thay vì `java.nio`, vì đơn giản hơn nhiều; hiểu giới hạn khi có hàng nghìn client.
- Mỗi người chỉ thử một lần mỗi câu để không thể vét cạn A/B/C/D.
- Không làm mật khẩu, lưu điểm lâu dài, giao diện đồ họa để giữ phạm vi vừa sức.

### 8.3 Where AI was wrong — and how we found out
- Câu hỏi đến trước `OK welcome` do thứ tự đăng ký/phát tin; test phát hiện, sửa bằng `synchronized (out)`. (`docs/ai-log-entry.md` N1)
- Bài test đua chỉ có ý nghĩa vì đã thử với một server sai cố tình (check-then-set), test báo nhiều người thắng. (N2)
- Lỗi trong chính script test (đọc bỏ dòng, điểm cộng dồn theo tên). (N3)
- ⟦ĐIỀN thêm ít nhất 1 ví dụ do chính nhóm phát hiện⟧

### 8.4 Full log
See [`docs/ai-log.md`](docs/ai-log.md).

---

## 9. Contribution (mandatory)
| Member | Owns (modules / documents) | Key PRs / commits | AI-assisted parts | Contribution % |
|--------|---------------------------|-------------------|-------------------|:--------------:|
| Hồ Mạnh Huy (@huyhm1825) | ⟦NHÓM TỰ ĐIỀN⟧ | ⟦số PR/commit thật⟧ | ⟦ĐIỀN⟧ | ⟦ĐIỀN⟧ |
| Bùi Quang Minh (@bqminh195)| ⟦NHÓM TỰ ĐIỀN⟧ | ⟦số PR/commit thật⟧ | ⟦ĐIỀN⟧ | ⟦ĐIỀN⟧ |
| Lê Duy Vinh (@vinh-OOP)| ⟦NHÓM TỰ ĐIỀN⟧ | ⟦số PR/commit thật⟧ | ⟦ĐIỀN⟧ | ⟦ĐIỀN⟧ |

> Theo `AGENTS.md` của thầy, AI không điền bảng Contribution hay khẳng định ai làm gì. Bảng này **do nhóm tự điền** dựa trên lịch sử git thật (commit/PR từ tài khoản của từng người).

We confirm the table above is accurate and agreed by all members:
- [x] Hồ Mạnh Huy (@huyhm1825)
- [x] Bùi Quang Minh (@bqminh195)
- [x] Lê Duy Vinh (@vinh-OOP)
