# 01 — Mô Hình Quy Trình Nghiệp Vụ: Luyện Tập Quiz & Flashcard

> Module: `quiz-flashcard` | Mã: `BV-04` | Múi giờ: `Asia/Ho_Chi_Minh` | Quota Free: `20` lượt AI/ngày (dùng chung ledger với BV-03)

## 1. Mục đích
- Mô tả vòng đời sinh Quiz trắc nghiệm + Flashcard từ `Document` bằng AI và làm bài `DRAFT → IN_PROGRESS → SUBMITTED → SCORED`.
- Làm căn cứ cho Use Case, Business Rule, State Model và kiểm thử.
- Kiểm soát quota sinh đề, hallucination đáp án, retry sinh dở dang, concurrent nộp bài.

## 2. Thuật ngữ và định danh
| Thuật ngữ | Identifier | Ghi chú |
|---|---|---|
| Bộ đề | `QuizSet` | Gồm N `QuizQuestion`, có `difficulty`, `questionCount` |
| Lượt làm bài | `QuizAttempt` | Trạng thái DRAFT→IN_PROGRESS→SUBMITTED→SCORED |
| Câu hỏi | `QuizQuestion` | Trắc nghiệm 4 lựa chọn + `explanation` |
| Thẻ ghi nhớ | `Flashcard` / `FlashcardDeck` | Mặt trước/sau + `SM2` ôn tập |
| Lượt AI | `AiQuotaLedger` | Sinh đề/flashcard tốn quota; làm bài không tốn |
| Bản sinh dở | `GenerationJob` | Job sinh AI bất đồng bộ, retry được |

## 3. Quy trình L0 — Toàn cảnh

```mermaid
flowchart TB
    U["👤 Học viên User"] -->|"1. Chọn tài liệu + cấu hình N câu/độ khó"| G["📝 QuizSet / FlashcardDeck"]
    G -->|"2. Kiểm tra quota AiQuotaLedger"| Q{"❓ Còn quota AI?"}
    Q -->|"3A — Còn quota: sinh bằng AI"| A["🤖 AIGenerate Quiz + Flashcard"]
    Q -->|"3B — Hết quota: chặn sinh"| B["⛔ QuotaExceeded + Paywall"]
    A -->|"4. Làm bài IN_PROGRESS"| P["✍️ QuizAttempt SUBMITTED"]
    P -->|"5. Chấm điểm + giải thích"| S["📊 SCORED + Explanation"]
    S -->|"6. Ôn flashcard + làm lại"| U
```

## 4. Quy trình L1 — Sinh đề và làm bài

```mermaid
flowchart TB
    C1["📄 Chọn Document + số câu 5/10/20 + độ khó EASY/MEDIUM/HARD"] -->|"1. Validate cấu hình"| C2["💳 Hold 1 quota AI"]
    C2 -->|"2. Còn quota: truy hồi RAG"| C3["📚 Retrieval chunk làm ngữ liệu"]
    C3 -->|"3. Gọi LLM sinh JSON Quiz/Flashcard"| C4["🤖 GenerationJob RUNNING"]
    C4 -->|"4. Validate JSON + đáp án duy nhất"| C5{"❓ Đạt chuẩn?"}
    C5 -->|"5A — Đạt: lưu DRAFT"| C6["💾 QuizSet DRAFT sẵn sàng"]
    C5 -->|"5B — Lỗi: retry 1 lần"| C4
    C6 -->|"6. Bắt đầu làm bài"| C7["✍️ Attempt IN_PROGRESS → SUBMITTED"]
    C7 -->|"7. Chấm local + hiện giải thích"| C8["📊 SCORED + Review + History"]
```

## 5. Quy trình L2 — Kỹ thuật sâu (idempotency, partial, concurrent)

```mermaid
flowchart TB
    D1["📥 POST /quiz/generate + Idempotency-Key"] -->|"1. Dedupe key 24h"| D2["🔍 Atomic hold quota per user"]
    D2 -->|"2. Hold thành công: tạo GenerationJob"| D3["🧮 RAG + LLM JSON timeout 45s"]
    D3 -->|"3. LLM trả JSON từng phần"| D4{"❓ Timeout nhưng đã lưu nháp?"}
    D4 -->|"4A — Local success: trả cached DRAFT"| D5["✅ Trả QuizSet DRAFT đã lưu"]
    D4 -->|"4B — Partial/fail: retry 1 lần"| D6["↩️ Retry cùng key, tái dùng hold"]
    D5 -->|"5. Start attempt IN_PROGRESS"| D7["✍️ Nộp SUBMITTED chấm local"]
    D7 -->|"6. Chấm + lưu history"| D8["📊 SCORED + AttemptHistory"]
    D6 -->|"7. Fail cuối: rollback quota"| D9["⛔ Fallback + hoàn quota + thử lại"]
```

## 6. Ma trận RACI
| Hoạt động | User | QuizService | LlmProvider | Admin |
|---|---|---|---|---|
| Cấu hình + sinh đề | R | A | R | — |
| Kiểm tra/trừ quota | — | R/A | — | — |
| Làm bài + nộp | R | A | — | — |
| Chấm + giải thích | — | R/A | — | — |
| Duyệt câu sai | — | — | — | R/A |

## 7. Ghi chú kiểm soát
- Sinh đề/flashcard tốn 1 quota AI/lần (không tính theo số câu); làm bài, xem giải thích, làm lại MIỄN PHÍ.
- Nộp bài dùng `attemptVersion` chống double-submit concurrent.
