# 07 — Ảnh Hưởng Dữ Liệu Và Tác Động: Theo Dõi Tiến Độ Và Báo Cáo

> Thực thể: `LearningEvent`, `DailyProgress`, `WeeklyReport`, `Goal`, `Badge`.

## 7.1 Mô hình dữ liệu (rút gọn)
- `LearningEvent(eventId UNIQUE, userId, type AI_ASKED/QUIZ_COMPLETED/LOGIN_STUDY, occurredAt, durationSec, score, metaJson)`.
- `DailyProgress(userId, date UNIQUE(userId+date), timeSpentSec, aiCount, quizCount, avgScore, activeDay BOOL, streak)`.
- `WeeklyReport(reportId, userId, weekStart, version, status DRAFT/FINAL/PARTIAL/REVISED, totalMin, avgScore, streakBest, vsLastWeekPct)`.
- `Goal(goalId, userId, weekStart, targetMin, progressMin)` — P2 nhưng đặt bảng trước.
- `Badge(badgeId, userId, code, earnedAt)` — MVP 3 mã cơ bản.

## 7.2 Tác động theo use case
| Use case | Tạo | Cập nhật | Đọc |
| :--- | :--- | :--- | :--- |
| UC-601/602 ingest | `LearningEvent` | `DailyProgress` cộng dồn | `Subscription` (không chặn) |
| UC-603 streak | — | `DailyProgress.streak` | 2 ngày liền kề |
| UC-604 dashboard | — | — | `DailyProgress` 7–30 ngày |
| UC-605 báo cáo tuần | `WeeklyReport FINAL` | `v2 REVISED` khi bù trễ | `DailyProgress` cả tuần |
| UC-608 bù trễ | `LearningEvent` (ngày gốc) | Tái tính `DailyProgress` + `WeeklyReport` | `RecalcLog` |

## 7.3 Ràng buộc
- `eventId UNIQUE` toàn cục; `UNIQUE(userId, date)` cho `DailyProgress`.
- `occurredAt` lưu UTC + `tz = Asia/Ho_Chi_Minh`; quy đổi ngày theo tz khi tổng hợp.
- `WeeklyReport` chốt rồi bất biến (chỉ thêm version mới).
- Giữ `LearningEvent` thô 12 tháng, `DailyProgress` 24 tháng; xóa theo yêu cầu GDPR-like của user.

## 7.4 Từ điển trường quan trọng
| Trường | Kiểu | Ràng buộc | Ví dụ |
| :--- | :--- | :--- | :--- |
| `eventId` | uuid | UNIQUE toàn cục, producer sinh | `7c9e...` |
| `type` | enum | `AI_ASKED/QUIZ_COMPLETED/STUDY_SESSION/LOGIN_STUDY` | `QUIZ_COMPLETED` |
| `occurredAt` | timestamptz | UTC, cho phép trễ ≤ 7 ngày | `2026-09-06T14:00:00Z` |
| `durationSec` | int | ≥ 0 | `900` (= 15 phút) |
| `score/maxScore` | int | `0 <= score <= maxScore` | `8/10` |
| `DailyProgress.date` | date | theo tz `Asia/Ho_Chi_Minh` | `2026-09-06` |
| `WeeklyReport.version` | int | tăng dần v1, v2... | `2` |
| `WeeklyReport.status` | enum | `DRAFT/FINAL/PARTIAL/REVISED` | `FINAL` |

## 7.5 Chỉ mục và hiệu năng
- Index `LearningEvent(userId, occurredAt)`, `DailyProgress(userId, date)`, `WeeklyReport(userId, weekStart)`.
- Dashboard đọc từ bảng tổng hợp ngày/tuần, không quét bảng event thô.
- Consumer event chạy partition theo `userId % 16` để scale ngang giờ cao điểm tối.

## 7.6 Ví dụ bản ghi (minh họa)
- `LearningEvent`: `{eventId: 7c9e, userId: u123, type: QUIZ_COMPLETED, occurredAt: 2026-09-06T14:00:00Z, durationSec: 600, score: 8, maxScore: 10}`.
- `DailyProgress`: `{userId: u123, date: 2026-09-06, timeSpentSec: 1500, aiCount: 5, quizCount: 2, avgScore: 77.5, activeDay: true, streak: 4}`.
- `WeeklyReport v1`: `{userId: u123, weekStart: 2026-08-31, version: 1, status: FINAL, totalMin: 320, avgScore: 75, streakBest: 5, vsLastWeekPct: +12}`.

## 7.7 Chính sách lưu trữ và xóa
- Event thô: giữ 12 tháng online, sau đó nén lạnh 12 tháng rồi xóa.
- Tổng hợp ngày/tuần: giữ 24 tháng phục vụ so sánh dài hạn.
- User yêu cầu xóa: anonymize trong 30 ngày, giữ số liệu gộp ẩn danh cho thống kê chung.
