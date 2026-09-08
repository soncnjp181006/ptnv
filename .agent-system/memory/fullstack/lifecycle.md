# Đặc Tả Vòng Đời 65 Chặng Kỹ Thuật (SDLC Lifecycle Specification)

Tài liệu này là **Đặc tả quy chuẩn vòng đời kỹ thuật toàn diện (Comprehensive Technical Lifecycle Specification)** của bộ Extension Bundle `fullstack`, định nghĩa chi tiết đầu vào (Inputs), sản phẩm bàn giao (Artifacts) và tiêu chí nghiệm thu (Definition of Done - DoD Gates) cho toàn bộ **65 chặng kỹ thuật** phân bổ trên **8 phân hệ nghiệp vụ** được điều phối bởi **9 Kỹ năng Phân hệ Cốt lõi (Master Phase Skills)**.

---

## 🧭 1. Tổng Quan 8 Phân Hệ Nghiệp Vụ Trong Vòng Đời

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                   CHU TRÌNH 8 PHÂN HỆ PHÁT TRIỂN PHẦN MỀM KHÉP KÍN                     │
├────────────────────────────────────────────────────────────────────────────────────────┤
│  [A. Discovery & Planning]  ──►  [B. Product & System Design]  ──►  [C. Foundation]    │
│            ▲                                                                 │         │
│            │ (Vòng lặp khép kín: H8 -> A2)                                   ▼         │
│  [H. Feedback & Improvement] ◄── [G. Operations] ◄── [F. Delivery] ◄── [D. Development]│
│                                                                              │         │
│                                     [E. Quality & Security Testing] ◄────────┘         │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 📋 2. Đặc Tả Chi Tiết Từng Chặng Kỹ Thuật (A1 ➔ H8)

### Phân Hệ A: Discovery & Planning
- **Master Skill Điều Phối**: `agent-fullstack-discovery`
- **Lệnh Slash**: `/agent-fullstack-discovery`
- **Thư mục Module Stages**: `skills/agent-fullstack-discovery/stages/`

> [!IMPORTANT]
> **Quy Chuẩn Bất Biến: Đồng Hành Cùng User & Tiêu Chuẩn Chiều Sâu Thực Chiến (Zero-Superficial Standard)**:
> Nghiệp vụ là nền móng của toàn bộ hệ thống. AI **TUYỆT ĐỐI KHÔNG ĐƯỢC TỰ BIÊN TỰ DIỄN**, không được làm qua loa chiếu lệ hay sinh đặc tả một mình mà không có sự tham gia của User/Domain Expert.
> - **Interactive Interview Gate**: Agent BẮT BUỘC DỪNG LẠI đặt câu hỏi phỏng vấn, đưa ra các phương án đánh đổi và chờ User phản hồi trước khi hoàn thành tài liệu.
> - **Mandatory Granular Decomposition**: Mọi cụm nghiệp vụ lớn BẮT BUỘC PHẢI CHỨA TỐI THIỂU 2 ĐẾN 5 TỆP CHI TIẾT [FORM D]. Cấm folder chỉ có duy nhất 1 file `tong-hop.md` 50 dòng.
> - **7 Trục Chiều Sâu Thực Chiến**: Mọi phân tích phải bao quát đủ Multi-Actor, State Machine, Consistency/Locking, Offline/Degraded, Dual-Approval, Edge Cases/Wastage, và Financial/Reconciliation Balance.
> - **Human Sign-off Gate**: CẤM AI tự ý tick `[x]` vào User Sign-off. Bắt buộc phải có sự xác nhận / ký duyệt thực tế bằng văn bản từ User trong chat.

#### A1. Requirement / Discovery (`stages/A1-discovery.md`)
- **Mục tiêu**: Phỏng vấn làm rõ bài toán, xác định chân dung người dùng (User Personas), nỗi đau (Pain Points), phân rã cây thư mục nghiệp vụ sâu và phác thảo sơ đồ luồng tổng quan.
- **Phương thức tương tác (Collaboration)**:
  - Agent phỏng vấn User qua Interactive Interview Gate với bộ câu hỏi khám phá có cấu trúc (bối cảnh, mục tiêu kinh doanh, đối tượng hưởng lợi).
  - Cùng User phác thảo **Mermaid Flowchart Cấp 0 (High-level Overview Flowchart)** thể hiện bức tranh toàn cảnh.
- **Đầu vào (Inputs)**: Ý tưởng sơ khởi hoặc yêu cầu thô từ người dùng.
- **Sản phẩm bàn giao (Artifacts)**: `specs/A1-discovery.md` và cây thư mục phân tích sâu tại `.agent-system/memory/fullstack/business-analysis/<version>/`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [ ] Có ít nhất 2 User Personas chính kèm Pain Points định lượng.
  - [ ] Sơ đồ Mermaid Flowchart L0 trực quan hóa luồng đi tổng thể.
  - [ ] Bắt buộc hoàn thiện cây thư mục `phan-tich/`, chuẩn bị danh mục các tệp chi tiết [FORM D].
  - [ ] **User Sign-off**: Người dùng xác nhận đúng trọng tâm bài toán và luồng tổng quan qua trao đổi thực tế.

#### A2. Business Analysis & Detailed Flowchart (`stages/A2-business-analysis.md`)
- **Mục tiêu**: Phân tích nghiệp vụ chuyên sâu, xác định quy tắc nghiệp vụ (Business Rules), bóc tách các tệp chi tiết [FORM D] và vẽ sơ đồ luồng chi tiết.
- **Phương thức tương tác (Collaboration)**:
  - Agent cùng User rà soát từng bước nghiệp vụ qua Interactive Interview Gate, thảo luận các nhánh rẽ điều kiện (`if/else`), kịch bản ngoại lệ và cơ chế xử lý lỗi.
  - Vẽ **Mermaid Detailed Process Flowchart** thể hiện đầy đủ Decision Diamonds, Happy Path, Failure Paths và Transaction Boundaries.
- **Đầu vào (Inputs)**: `specs/A1-discovery.md` (hoặc `specs/ops/H8-new-req.md` khi ở chu kỳ tiếp theo).
- **Sản phẩm bàn giao (Artifacts)**: `specs/A2-business-analysis.md` và toàn bộ các tệp chi tiết `<ZZ>-*.md` [FORM D] trong từng cụm nghiệp vụ.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [ ] Sơ đồ Mermaid Detailed Flowchart bao phủ 100% các nhánh rẽ điều kiện và kịch bản lỗi.
  - [ ] Bảng Quy tắc nghiệp vụ (Business Rules) rõ ràng, không còn giả định mơ hồ.
  - [ ] **Tiêu chuẩn Chiều Sâu (Anti-Slop)**: Mỗi cụm nghiệp vụ lớn có tối thiểu 2-5 tệp chi tiết [FORM D] bao quát đủ 7 trục thực chiến (CẤM folder chỉ có 1 file `tong-hop.md`).
  - [ ] **User Sign-off**: Người dùng kiểm tra và phê duyệt tính chính xác của luồng nghiệp vụ trong chat.

#### A3. Feasibility Study (`stages/A3-feasibility.md`)
- **Mục tiêu**: Thẩm định tính khả thi công nghệ, thông số tải định lượng, tài nguyên, thời gian và chi phí; thảo luận các phương án đánh đổi (Trade-offs).
- **Phương thức tương tác (Collaboration)**:
  - Agent trình bày bảng so sánh các lựa chọn công nghệ (Options A, B, C) kèm ưu/nhược điểm và rủi ro.
  - Cùng User thảo luận để thống nhất giải pháp phù hợp nhất với năng lực và ngân sách.
- **Đầu vào (Inputs)**: `specs/A2-business-analysis.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/A3-feasibility.md` (Tech Spike Report, Trade-off Matrix, Sơ đồ kiến trúc Mermaid).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [ ] Ma trận so sánh đánh đổi giữa các phương án công nghệ có thông số định lượng (Throughput QPS, Latency SLA).
  - [ ] Nhận diện và đánh giá rủi ro các thư viện/công nghệ phụ thuộc kèm phương án bọc cách ly (Isolation Adapter).
  - [ ] **User Sign-off**: Người dùng lựa chọn và phê duyệt định hướng giải pháp kỹ thuật qua trao đổi thực tế.

#### A4. Scope, Goals & KPIs (`stages/A4-scope-goals.md`)
- **Mục tiêu**: Xác định ranh giới In-Scope, Out-of-Scope, mục tiêu SMART và các chỉ số đo lường thành công (KPIs).
- **Phương thức tương tác (Collaboration)**:
  - Agent đề xuất ranh giới phạm vi; cùng User cắt tỉa những hạng mục chưa cần thiết ra Out-of-Scope để tránh phình phạm vi (Scope Creep).
  - Thống nhất các chỉ số KPIs định lượng cụ thể.
- **Đầu vào (Inputs)**: `specs/A3-feasibility.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/A4-scope-goals.md` (Scope Boundaries Table, SMART Goals, Target KPIs Matrix).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [ ] Ranh giới In-Scope và Out-of-Scope được định nghĩa tường minh, không có vùng xám.
  - [ ] Ít nhất 3 chỉ số KPIs định lượng được (ví dụ: latency < 200ms, tỷ lệ chuyển đổi tăng 15%).
  - [ ] **User Sign-off**: Người dùng phê duyệt ranh giới phạm vi và mục tiêu dự án trong chat.

#### A5. Risk, Effort & Roadmap (`stages/A5-risk-roadmap.md`)
- **Mục tiêu**: Lập ma trận rủi ro (Risk Matrix), ước lượng khối lượng công việc và xây dựng lộ trình phát hành (Roadmap).
- **Phương thức tương tác (Collaboration)**:
  - Agent trình bày ma trận rủi ro và kế hoạch giảm thiểu; cùng User đánh giá mức độ chấp nhận rủi ro của tổ chức.
  - Cùng thống nhất các mốc bàn giao Milestones khả thi.
- **Đầu vào (Inputs)**: `specs/A4-scope-goals.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/A5-risk-roadmap.md` (Risk Matrix 5x5, Mitigation Plans, Milestone Roadmap).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Ma trận rủi ro bao gồm biện pháp giảm thiểu cho từng rủi ro mức High/Critical.
  - [x] Ước lượng Effort có cơ sở (Story Points / T-Shirt Sizing).
  - [x] **User Sign-off**: Người dùng thống nhất lộ trình Milestone Roadmap.

#### A6. Backlog, User Stories & Tasks (`stages/A6-backlog-tasks.md`)
- **Mục tiêu**: Phân rã bài toán thành Backlog gồm các User Stories chuẩn INVEST kèm kịch bản kiểm thử Gherkin.
- **Phương thức tương tác (Collaboration)**:
  - Agent trình bày danh sách User Stories và kịch bản Gherkin `Given-When-Then`.
  - Cùng User rà soát tính đầy đủ, kiểm tra từng tiêu chí chấp nhận.
  - Dừng lại tại **Cổng Phê Duyệt Xuất Xưởng Phân Hệ A (Phase A Gate Review)** để User ký duyệt chính thức mở khóa Phân hệ B (Design).
- **Đầu vào (Inputs)**: `specs/A5-risk-roadmap.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/A6-backlog.md` (User Stories, Acceptance Criteria Gherkin, Task Breakdown, Phase A Sign-off).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 100% User Stories thỏa mãn tiêu chí INVEST.
  - [x] Mỗi Story đều có kịch bản Acceptance Criteria viết bằng cú pháp Gherkin (`Given-When-Then`).
  - [x] **User Phase Sign-off**: Người dùng ký duyệt nghiệm thu toàn bộ Phân hệ A.
  - [x] Kích hoạt rẽ nhánh song song sang B1 (UX) và B3 (Kiến trúc).

---

### Phân Hệ B: Product & System Design
- **Master Skill Điều Phối**: `fullstack-design`
- **Lệnh Slash**: `/agent-fullstack-design`
- **Thư mục Module Stages**: `skills/agent-fullstack-design/stages/`

#### B1. UX / User Flow (`stages/B1-ux-flow.md`)
- **Mục tiêu**: Thiết kế sơ đồ luồng người dùng (User Flow), bản đồ hành trình (Journey Map) và logic xử lý rẽ nhánh giao diện.
- **Đầu vào (Inputs)**: `specs/A6-backlog.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/design/B1-user-flow.md` (User Flow Diagrams, Navigation Tree).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Bao quát toàn bộ các trạng thái: Happy Path, Error State, Empty State, Loading State.
  - [x] Sơ đồ điều hướng không có ngõ cụt (Dead-ends).

#### B2. Wireframe / UI Design (`stages/B2-wireframe-ui.md`)
- **Mục tiêu**: Thiết kế cấu trúc Wireframe, lưới Layout Responsive, Design Tokens và đặc tả Components.
- **Đầu vào (Inputs)**: `specs/design/B1-user-flow.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/design/B2-wireframe.md` (Wireframe Specs, Design Tokens 5 tầng màu, Component Hierarchy).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Tuân thủ 6 Tiêu chuẩn Bất biến Zero-Generic (Bảng màu 5 tầng, Concentric Radius, Zero Overflow).
  - [x] Đủ 6 microstates cho controls (default, hover, focus, active, disabled, loading).
  - [x] Responsive layout chuẩn mobile-first (<640px = 1 cột, touch target >=44px).

#### B3. System Architecture (`stages/B3-system-arch.md`)
- **Mục tiêu**: Thiết kế kiến trúc hệ thống tổng thể theo mô hình C4 Model (Context & Container) và các quyết định kiến trúc ADR.
- **Đầu vào (Inputs)**: `specs/A6-backlog.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/arch/B3-system-arch.md` (C4 Context/Container Diagrams, Tech Stack ADRs).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Sơ đồ kiến trúc C4 thể hiện rõ ranh giới các hệ thống và giao thức kết nối.
  - [x] Quyết định công nghệ có lý do và cân nhắc đánh đổi (Trade-off Analysis).
  - [x] Kích hoạt nhánh B4 (Domain Design) và B7 (Security Requirements).

#### B4. Domain / Module Design (`stages/B4-domain-design.md`)
- **Mục tiêu**: Phân tích Bounded Contexts, Aggregate Roots, Entities và Value Objects theo phương pháp Domain-Driven Design.
- **Đầu vào (Inputs)**: `specs/arch/B3-system-arch.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/arch/B4-domain-modules.md` (Domain Model, Ubiquitous Language, Aggregate Boundaries).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Xác định rõ ràng các Bounded Contexts và ranh giới nghiệp vụ.
  - [x] Aggregate Roots bảo toàn các bất biến nghiệp vụ (Invariants).
  - [x] Kích hoạt song song B5 (Data/ERD) và B6 (API Contract).

#### B5. Data / ERD / DB Design (`stages/B5-data-erd-db.md`)
- **Mục tiêu**: Thiết kế mô hình dữ liệu quan hệ (ERD chuẩn 3NF), cấu trúc bảng, khóa chính/khóa ngoại, chỉ mục (Index) và phân vùng.
- **Đầu vào (Inputs)**: `specs/arch/B4-domain-modules.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/data/B5-erd-db.md` (ERD Diagram Mermaid, Schema DDL, Indexing Strategy).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Đạt chuẩn hóa 3NF (hoặc giải thích lý do Denormalization có chủ đích).
  - [x] Đầy đủ khóa ngoại kèm quy tắc `ON DELETE / ON UPDATE`.
  - [x] Chiến lược Index bao phủ các câu truy vấn trọng yếu, chống Full Table Scan.

#### B6. API / Contract Design (`stages/B6-api-contract.md`)
- **Mục tiêu**: Thiết kế giao ước API chuẩn OpenAPI Spec 3.1 / GraphQL Schema, đặc tả request/response schemas và mã lỗi.
- **Đầu vào (Inputs)**: `specs/arch/B4-domain-modules.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/api/B6-api-contract.yaml` (OpenAPI Spec 3.1, Error Code Registry).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] OpenAPI Spec 3.1 hợp lệ (Valid Syntax).
  - [x] Đầy đủ schema validation cho Request Body và Query Params.
  - [x] Cấu trúc phản hồi lỗi chuẩn hóa (RFC 7807 Problem Details).

#### B7. Security Requirements (`stages/B7-security-reqs.md`)
- **Mục tiêu**: Thiết lập các yêu cầu an ninh: Xác thực (AuthN), Phân quyền (RBAC/ABAC), Mã hóa tại chỗ và trên đường truyền.
- **Đầu vào (Inputs)**: `specs/arch/B3-system-arch.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/sec/B7-security-reqs.md` (Auth Architecture, RBAC Matrix, Cryptography Policies).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Ma trận phân quyền RBAC bao quát mọi endpoints và roles.
  - [x] Quy định mã hóa TLS 1.3 và AES-256 cho dữ liệu nhạy cảm (PII).

#### B8. Threat Modeling (`stages/B8-threat-modeling.md`)
- **Mục tiêu**: Phân tích mô hình mối đe dọa STRIDE, nhận diện bề mặt tấn công và thiết lập các biện pháp kiểm soát giảm thiểu.
- **Đầu vào (Inputs)**: `specs/sec/B7-security-reqs.md`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/sec/B8-threat-model.md` (STRIDE Threat Matrix, Mitigation Controls).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Phân tích đầy đủ 6 nhóm nguy cơ STRIDE.
  - [x] Mọi nguy cơ High/Critical đều có biện pháp giảm thiểu tương ứng.

---

### Phân Hệ C: Engineering Foundation
- **Master Skill Điều Phối**: `fullstack-foundation`
- **Lệnh Slash**: `/agent-fullstack-foundation`
- **Thư mục Module Stages**: `skills/agent-fullstack-foundation/stages/`

#### C1. Repository Scaffold (`stages/C1-repository.md`)
- **Mục tiêu**: Khởi tạo repository, cấu hình `.gitignore`, editorconfig, linter config và tooling ban đầu.
- **Đầu vào (Inputs)**: Hợp lưu từ `B2`, `B5`, `B6`.
- **Sản phẩm bàn giao (Artifacts)**: Git Repository cấu hình chuẩn, `.gitignore`, `.editorconfig`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Git repo khởi tạo sạch sẽ, không commit file rác hoặc nhị phân.
  - [x] `.gitignore` bao phủ đầy đủ các file môi trường, cache và build artifacts.

#### C2. Git / Branch Strategy (`stages/C2-git-strategy.md`)
- **Mục tiêu**: Thiết lập chiến lược phân nhánh Trunk-Based Development, quy ước commit Conventional Commits, bảo vệ nhánh `main`.
- **Đầu vào (Inputs)**: Repository đã khởi tạo từ `C1`.
- **Sản phẩm bàn giao (Artifacts)**: `CONTRIBUTING.md`, `.github/pull_request_template.md`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Quy tắc Branch Protection được định nghĩa (cấm push trực tiếp lên main, bắt buộc PR).
  - [x] Quy ước Commit tuân thủ Conventional Commits (`feat:`, `fix:`, `chore:`).

#### C3. Project Structure (`stages/C3-project-structure.md`)
- **Mục tiêu**: Cấu trúc thư mục mã nguồn theo kiến trúc Clean Architecture / Hexagonal Architecture.
- **Đầu vào (Inputs)**: Repository từ `C2`, Domain Model từ `B4`.
- **Sản phẩm bàn giao (Artifacts)**: Khung thư mục mã nguồn chuẩn (`core/`, `ports/`, `adapters/`, `api/`).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Phân tách rõ ràng giữa Domain Logic và Infrastructure.
  - [x] Dependency Rule: Tầng bên trong không phụ thuộc vào tầng bên ngoài.

#### C4. Environment / Config (`stages/C4-env-config.md`)
- **Mục tiêu**: Cấu hình môi trường chuẩn 12-Factor App, schema validation cho biến cấu hình và tệp mẫu `.env.example`.
- **Đầu vào (Inputs)**: Cấu trúc dự án từ `C3`.
- **Sản phẩm bàn giao (Artifacts)**: `.env.example`, `config/loader` có schema validation.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Tuyệt đối không hardcode credentials trong mã nguồn.
  - [x] Ứng dụng kiểm tra và báo lỗi ngay khi khởi động nếu thiếu biến môi trường bắt buộc.

#### C5. Secrets Management (`stages/C5-secrets.md`)
- **Mục tiêu**: Thiết lập giải pháp quản lý bí mật an toàn (Zero Secret Leakage), Vault, SOPS hoặc GitHub Secrets.
- **Đầu vào (Inputs)**: Cấu hình biến môi trường từ `C4`.
- **Sản phẩm bàn giao (Artifacts)**: Cấu hình mã hóa secrets, pre-commit hook quét secret (Gitleaks).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Pre-commit hook chặn đứng việc commit API keys hoặc certificates.
  - [x] Quy trình nạp secrets cho môi trường CI/CD được tài liệu hóa.

#### C6. Docker / Local Dev (`stages/C6-docker-local.md`)
- **Mục tiêu**: Viết Multi-stage Dockerfile và Docker Compose phục vụ môi trường phát triển cục bộ một lệnh chạy (`docker compose up`).
- **Đầu vào (Inputs)**: Cấu hình dự án và cơ sở dữ liệu từ `C4`, `B5`.
- **Sản phẩm bàn giao (Artifacts)**: `Dockerfile`, `compose.yml` (App, Postgres/MySQL, Redis).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Lệnh `docker compose up -d` khởi động toàn bộ stack thành công.
  - [x] Container image được tối ưu dung lượng (Multi-stage build).

#### C7. CI Pipeline (`stages/C7-ci-pipeline.md`)
- **Mục tiêu**: Thiết lập pipeline tự động hóa tích hợp liên tục (CI) chạy linter, format check, type check và build test.
- **Đầu vào (Inputs)**: Dockerfile và cấu hình từ `C6`.
- **Sản phẩm bàn giao (Artifacts)**: `.github/workflows/ci.yml`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Pipeline tự động kích hoạt khi có Pull Request mới.
  - [x] Thời gian chạy CI tối ưu (< 5 phút).
  - [x] Kích hoạt rẽ nhánh song song sang Phân hệ D (Backend, Frontend, Migration).

---

### Phân Hệ D: Development & Review
- **Master Skill Điều Phối**: `fullstack-dev`
- **Lệnh Slash**: `/agent-fullstack-dev`
- **Thư mục Module Stages**: `skills/agent-fullstack-dev/stages/`

#### D1. Backend Development (`stages/D1-backend-dev.md`)
- **Mục tiêu**: Lập trình mã nguồn Backend: Controllers, Services, Repository Pattern, xử lý nghiệp vụ theo B4, B6.
- **Đầu vào (Inputs)**: `specs/api/B6-api-contract.yaml`, `specs/arch/B4-domain-modules.md`.
- **Sản phẩm bàn giao (Artifacts)**: Mã nguồn backend hoàn chỉnh tại `src/backend/`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Đáp ứng 100% các endpoints định nghĩa trong API Contract.
  - [x] Áp dụng Dependency Injection và Repository Pattern.
  - [x] Xử lý lỗi tập trung, không để lộ Stack Trace ra ngoài.

#### D2. Frontend Development (`stages/D2-frontend-dev.md`)
- **Mục tiêu**: Lập trình mã nguồn Frontend: Component UI, State Management, Responsive Layout theo B1, B2.
- **Đầu vào (Inputs)**: `specs/design/B1-user-flow.md`, `specs/design/B2-wireframe.md`.
- **Sản phẩm bàn giao (Artifacts)**: Mã nguồn frontend hoàn chỉnh tại `src/frontend/`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Zero Horizontal Overflow trên mọi kích thước màn hình.
  - [x] Đủ 4 tầng phản hồi theo ngữ cảnh (Inline Alert, In-pane Banner, Snackbar, Dialog).
  - [x] Tối ưu hóa render và lazy-loading assets.

#### D3. Database Migration (`stages/D3-db-migration.md`)
- **Mục tiêu**: Viết các tệp migration tạo schema, bảng, index và seed dữ liệu mẫu theo thiết kế B5.
- **Đầu vào (Inputs)**: `specs/data/B5-erd-db.md`.
- **Sản phẩm bàn giao (Artifacts)**: Scripts migration Up/Down tại `migrations/`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Các tệp migration có tính chất lũy đẳng (Idempotent).
  - [x] Hỗ trợ rollback sạch sẽ (`down` migration).

#### D4. External Services (`stages/D4-external-services.md`)
- **Mục tiêu**: Tích hợp các dịch vụ bên thứ ba (Cổng thanh toán, Email, SMS, Storage) qua Adapter pattern kèm Circuit Breaker.
- **Đầu vào (Inputs)**: Yêu cầu tích hợp từ B3, B6.
- **Sản phẩm bàn giao (Artifacts)**: `src/integrations/` adapters, mock clients phục vụ kiểm thử.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Có cơ chế Circuit Breaker và Retry Policy với Exponential Backoff.
  - [x] Có Mock Client để chạy test độc lập không phụ thuộc Internet.

#### D5. System Integration (`stages/D5-integration.md`)
- **Mục tiêu**: Ghép nối liên tầng: Kết nối Frontend với Backend API và Database, kiểm tra luồng dữ liệu thông suốt.
- **Đầu vào (Inputs)**: Hợp lưu từ `D1`, `D2`, `D3`, `D4`.
- **Sản phẩm bàn giao (Artifacts)**: Hệ thống tích hợp thông suốt trên môi trường local.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Luồng dữ liệu End-to-End từ giao diện người dùng tới cơ sở dữ liệu hoạt động chính xác.
  - [x] CORS, Cookies và Authentication Headers được cấu hình đúng.

#### D6. Commit & Push (`stages/D6-commit-push.md`)
- **Mục tiêu**: Đóng gói Atomic Commits theo chuẩn Conventional Commits và đẩy mã nguồn lên remote branch an toàn.
- **Đầu vào (Inputs)**: Mã nguồn đã qua tích hợp từ `D5`.
- **Sản phẩm bàn giao (Artifacts)**: Git Commit Log sạch sẽ trên nhánh tính năng.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Mỗi commit đại diện cho một thay đổi logic duy nhất (Atomic Commit).
  - [x] Vượt qua 100% pre-commit checks (Lint, Secret scan).

#### D7. Pull Request (`stages/D7-pull-request.md`)
- **Mục tiêu**: Mở Pull Request kèm mô tả chi tiết, hình ảnh minh chứng kết quả, checklist tự kiểm và liên kết Issue.
- **Đầu vào (Inputs)**: Nhánh tính năng đã push từ `D6`.
- **Sản phẩm bàn giao (Artifacts)**: Pull Request trên GitHub / GitLab.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Mô tả PR nêu rõ: Thay đổi cái gì (What), Tại sao (Why), Bằng chứng kiểm thử (Evidence).
  - [x] 100% CI Checks tự động báo Pass xanh.

#### D8. Code Review (`stages/D8-code-review.md`)
- **Mục tiêu**: Thẩm duyệt mã nguồn ngang hàng (Peer Review), kiểm tra Clean Code, rủi ro bảo mật và code smells.
- **Đầu vào (Inputs)**: Pull Request từ `D7`.
- **Sản phẩm bàn giao (Artifacts)**: Review Comments, Approval từ Reviewer.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Đạt tối thiểu 1 Approval từ Senior / Lead.
  - [x] Toàn bộ ý kiến thảo luận (Discussions) đã được phản hồi và Resolved.

#### D9. Merge (`stages/D9-merge.md`)
- **Mục tiêu**: Hợp nhất PR vào nhánh chính (`main`) theo chiến lược Squash hoặc Rebase Merge.
- **Đầu vào (Inputs)**: Pull Request đã được Approved từ `D8`.
- **Sản phẩm bàn giao (Artifacts)**: Commit hợp nhất trên nhánh `main`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Nhánh chính luôn ở trạng thái Build xanh (Green Main).
  - [x] Xóa nhánh tính năng tạm thời sau khi merge thành công.

---

### Phân Hệ E: Quality & Security Testing
- **Master Skill Điều Phối**: `fullstack-qa`
- **Lệnh Slash**: `/agent-fullstack-qa`
- **Thư mục Module Stages**: `skills/agent-fullstack-qa/stages/`

#### E1. Lint & Static Analysis (`stages/E1-lint-static.md`)
- **Mục tiêu**: Chạy linter, type checker và công cụ phân tích tĩnh SAST (Ruff, ESLint, SonarQube).
- **Đầu vào (Inputs)**: Mã nguồn trên nhánh `main` sau merge từ `D9`.
- **Sản phẩm bàn giao (Artifacts)**: Linter Report, SAST Quality Report.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 0 Lỗi Lint (Zero Errors), 0 Cảnh báo loại trừ (Zero Warnings).
  - [x] Type Check vượt qua 100% không dùng `any` bừa bãi.

#### E2. Unit Test (`stages/E2-unit-test.md`)
- **Mục tiêu**: Viết và thực thi bộ Unit Tests bao phủ các hàm logic nghiệp vụ, edge cases, tính toán.
- **Đầu vào (Inputs)**: Mã nguồn từ `E1`.
- **Sản phẩm bàn giao (Artifacts)**: Unit Test Report, Code Coverage Report.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 100% Unit Tests Pass.
  - [x] Độ phủ mã nguồn (Code Coverage) đạt tối thiểu 80% (tầng domain >= 90%).

#### E3. Integration Test (`stages/E3-integration-test.md`)
- **Mục tiêu**: Kiểm thử tích hợp giữa Service và Database, tương tác API, kiểm tra giao dịch (Transactions).
- **Đầu vào (Inputs)**: Mã nguồn từ `E2`.
- **Sản phẩm bàn giao (Artifacts)**: Integration Test Report.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Kiểm thử với cơ sở dữ liệu thật trong container (Testcontainers).
  - [x] Xác minh tính toàn vẹn dữ liệu khi rollback transaction.

#### E4. Contract Test (`stages/E4-contract-test.md`)
- **Mục tiêu**: Kiểm thử tương thích giao ước API giữa Consumer và Provider (Pact / Schemathesis).
- **Đầu vào (Inputs)**: `specs/api/B6-api-contract.yaml`.
- **Sản phẩm bàn giao (Artifacts)**: Contract Test Report.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 0 Breaking Changes so với phiên bản trước.
  - [x] Schema tương thích 100% với giao ước đã thống nhất.

#### E5. E2E Test (`stages/E5-e2e-test.md`)
- **Mục tiêu**: Kiểm thử tự động luồng người dùng đầu-cuối (Playwright / Cypress) trên trình duyệt mô phỏng.
- **Đầu vào (Inputs)**: Hệ thống tích hợp đang chạy.
- **Sản phẩm bàn giao (Artifacts)**: E2E Test Automation Report, Video/Screenshots minh chứng.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Toàn bộ kịch bản Happy Path chính đều Pass.
  - [x] Kiểm tra hiển thị responsive trên 3 kích thước màn hình (Desktop, Tablet, Mobile).

#### E6. Security Test (`stages/E6-security-test.md`)
- **Mục tiêu**: Quét bảo mật động DAST, kiểm tra OWASP Top 10, kiểm tra lỗ hổng thư viện phụ thuộc (Trivy, Snyk).
- **Đầu vào (Inputs)**: Hệ thống đang chạy, dependencies lockfile.
- **Sản phẩm bàn giao (Artifacts)**: Security Audit Report.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 0 Lỗ hổng mức độ Critical.
  - [x] 0 Lỗ hổng mức độ High chưa có kế hoạch khắc phục.

#### E7. Performance Test (`stages/E7-perf-test.md`)
- **Mục tiêu**: Kiểm thử hiệu năng và tải chịu đựng (k6 / Locust), đo Latency P95, P99 và tỷ lệ lỗi khi quá tải.
- **Đầu vào (Inputs)**: Hệ thống trên môi trường kiểm thử.
- **Sản phẩm bàn giao (Artifacts)**: Performance Benchmark Report.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Thỏa mãn chỉ tiêu Latency P95 đặt ra tại chặng A4.
  - [x] Error rate < 0.1% dưới tải danh định.

#### E8. User Acceptance Testing (UAT) (`stages/E8-uat.md`)
- **Mục tiêu**: Kiểm thử chấp nhận người dùng thực tế và ký duyệt nghiệm thu từ Product Owner / Khách hàng.
- **Đầu vào (Inputs)**: Hệ thống hoạt động hoàn chỉnh, tài liệu UAT test cases.
- **Sản phẩm bàn giao (Artifacts)**: UAT Sign-off Document.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 100% kịch bản UAT được nghiệm thu đạt chuẩn.
  - [x] Product Owner ký duyệt bàn giao (Sign-off).

#### E9. Release Readiness (`stages/E9-readiness.md`)
- **Mục tiêu**: Thẩm tra toàn diện bảng kiểm tra xuất xưởng (Release Readiness Checklist) trước khi đóng gói.
- **Đầu vào (Inputs)**: Báo cáo tổng hợp từ E1 đến E8.
- **Sản phẩm bàn giao (Artifacts)**: `specs/qa/E9-release-readiness.md`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 8 phân hệ kiểm thử trước đó đều đạt kết quả Pass.
  - [x] Có kế hoạch Rollback khẩn cấp nếu triển khai thất bại.

---

### Phân Hệ F: Build, Release & Delivery
- **Master Skill Điều Phối**: `fullstack-delivery`
- **Lệnh Slash**: `/agent-fullstack-delivery`
- **Thư mục Module Stages**: `skills/agent-fullstack-delivery/stages/`

#### F1. Build Production (`stages/F1-build.md`)
- **Mục tiêu**: Đóng gói mã nguồn bản phát hành: Biên dịch, minify, tree-shaking và tối ưu dung lượng binary.
- **Đầu vào (Inputs)**: Mã nguồn đã được nghiệm thu qua `E9`.
- **Sản phẩm bàn giao (Artifacts)**: Production Build Artifacts.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Build thành công với cờ tối ưu hóa sản xuất (`production mode`).
  - [x] Không kèm source maps nhạy cảm hoặc debug symbols.

#### F2. Artifact Packaging & Signing (`stages/F2-artifact.md`)
- **Mục tiêu**: Đóng gói Docker Image OCI, tạo checksum SHA-256, ký số Cosign và đẩy lên Container Registry.
- **Đầu vào (Inputs)**: Build output từ `F1`.
- **Sản phẩm bàn giao (Artifacts)**: Signed OCI Container Image trên Registry, Software Bill of Materials (SBOM).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Checksum SHA-256 được tính toán và lưu trữ.
  - [x] Image được ký số và quét 0 lỗ hổng bảo mật.

#### F3. Version Bump SemVer (`stages/F3-version.md`)
- **Mục tiêu**: Tự động phân tích commit logs và nâng phiên bản theo chuẩn SemVer 2.0.0.
- **Đầu vào (Inputs)**: Git log lịch sử commits từ bản release trước.
- **Sản phẩm bàn giao (Artifacts)**: SemVer Version Tag (ví dụ `v1.2.0`).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Phân loại chính xác mức độ nâng phiên bản: Patch, Minor hay Major.
  - [x] Đồng bộ số phiên bản vào `package.json`, `version.txt` hoặc `manifest.yml`.

#### F4. Changelog Generation (`stages/F4-changelog.md`)
- **Mục tiêu**: Tự động sinh `CHANGELOG.md` chuẩn Keep-a-Changelog phân chia rõ Features, Fixes, Breaking Changes.
- **Đầu vào (Inputs)**: Phiên bản mới từ `F3` và danh sách commits.
- **Sản phẩm bàn giao (Artifacts)**: Tệp `CHANGELOG.md` cập nhật.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Tuân thủ định dạng Keep-a-Changelog.
  - [x] Mỗi mục thay đổi đều có liên kết tới PR hoặc Issue tương ứng.

#### F5. Release Publishing (`stages/F5-release.md`)
- **Mục tiêu**: Tạo GitHub/GitLab Release, gắn Git Tag và đính kèm Release Notes cùng Artifacts.
- **Đầu vào (Inputs)**: Version từ `F3`, Changelog từ `F4`, Artifact từ `F2`.
- **Sản phẩm bàn giao (Artifacts)**: GitHub/GitLab Release công bố chính thức.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Release được công bố trên remote repository kèm Git Tag bất biến.

#### F6. Deploy Development (`stages/F6-deploy-dev.md`)
- **Mục tiêu**: Triển khai tự động bản build mới lên môi trường Development.
- **Đầu vào (Inputs)**: Artifact đã publish từ `F5`.
- **Sản phẩm bàn giao (Artifacts)**: Môi trường Dev online với phiên bản mới.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Triển khai thành công, Pods/Containers hoạt động bình thường.
  - [x] Health check endpoint trả về mã `200 OK`.

#### F7. Deploy Staging (`stages/F7-deploy-staging.md`)
- **Mục tiêu**: Triển khai lên môi trường Staging với cấu hình đồng nhất môi trường sản xuất (Environment Parity).
- **Đầu vào (Inputs)**: Phiên bản đã kiểm tra ổn định trên Dev từ `F6`.
- **Sản phẩm bàn giao (Artifacts)**: Môi trường Staging online.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Môi trường Staging sử dụng tài nguyên và cấu hình tương đương Production.
  - [x] Migration cơ sở dữ liệu trên Staging thực thi trơn tru không lỗi.

#### F8. Staging Smoke Test (`stages/F8-smoke-uat.md`)
- **Mục tiêu**: Chạy bộ kiểm thử nhanh tự động (Smoke Test) trên Staging để thẩm tra các luồng sống còn.
- **Đầu vào (Inputs)**: Staging environment từ `F7`.
- **Sản phẩm bàn giao (Artifacts)**: Staging Smoke Test Report.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 100% Smoke Tests Pass trong vòng 3 phút.
  - [x] Sẵn sàng kích hoạt triển khai Production.

#### F9. Deploy Production (`stages/F9-deploy-prod.md`)
- **Mục tiêu**: Triển khai an toàn lên Production với chiến lược Canary hoặc Blue-Green Zero Downtime.
- **Đầu vào (Inputs)**: Phê duyệt từ `F8`.
- **Sản phẩm bàn giao (Artifacts)**: Production Live Deployment.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Zero downtime trong suốt quá trình chuyển giao lưu lượng.
  - [x] 100% phiên bản mới tiếp nhận lưu lượng người dùng thật.

#### F10. Post-deploy Verification (`stages/F10-post-deploy.md`)
- **Mục tiêu**: Giám sát sát sao trạng thái hệ thống sau triển khai: Synthetic testing, tỷ lệ lỗi và xác nhận ổn định.
- **Đầu vào (Inputs)**: Production live telemetry từ `F9`.
- **Sản phẩm bàn giao (Artifacts)**: Post-deploy Sign-off Note.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Error rate 0% trong 15 phút đầu tiên tiếp nhận tải thật.
  - [x] Chuyển giao quyền kiểm soát sang Phân hệ Vận hành `G1 (Operate)`.

---

### Phân Hệ G: Operations & Observability
- **Master Skill Điều Phối**: `fullstack-ops`
- **Lệnh Slash**: `/agent-fullstack-ops`
- **Thư mục Module Stages**: `skills/agent-fullstack-ops/stages/`

#### G1. Operate & Runbooks (`stages/G1-operate.md`)
- **Mục tiêu**: Thiết lập quy trình vận hành tiêu chuẩn (SOP), sổ tay hướng dẫn Runbooks và quản trị cam kết SLA/SLO/SLI.
- **Đầu vào (Inputs)**: Bàn giao từ `F10`.
- **Sản phẩm bàn giao (Artifacts)**: Production SOP Runbooks.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Định nghĩa rõ ràng các chỉ số SLO và SLI cho từng dịch vụ.
  - [x] Có quy trình hướng dẫn xử lý các tình huống vận hành thông thường.

#### G2. Infrastructure as Code (`stages/G2-infra.md`)
- **Mục tiêu**: Quản lý toàn bộ hạ tầng bằng mã nguồn (Terraform, Kubernetes Manifests, Helm Charts).
- **Đầu vào (Inputs)**: Cấu hình hạ tầng từ `G1`.
- **Sản phẩm bàn giao (Artifacts)**: Mã nguồn IaC, State file hạ tầng.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 100% tài nguyên đám mây được quản lý bằng IaC (Zero ClickOps).
  - [x] Kích hoạt song song G5 (Logs), G6 (Metrics), G7 (Tracing).

#### G3. Backup & Disaster Recovery (`stages/G3-backup-restore.md`)
- **Mục tiêu**: Thiết lập chính sách sao lưu tự động định kỳ, mã hóa dữ liệu backup và tổ chức diễn tập khôi phục thảm họa (RPO/RTO).
- **Đầu vào (Inputs)**: Cơ sở dữ liệu và cấu hình lưu trữ từ `G1`.
- **Sản phẩm bàn giao (Artifacts)**: Backup Policy, Kịch bản và Báo cáo diễn tập Disaster Recovery.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Thời gian khôi phục (RTO) và lượng dữ liệu mất mát tối đa (RPO) đạt cam kết.
  - [x] Diễn tập khôi phục từ bản backup thành công thực tế.

#### G4. Auto-scaling & Resource Optimization (`stages/G4-scaling.md`)
- **Mục tiêu**: Cấu hình tự động co giãn tài nguyên (HPA / Cluster Autoscaler) theo tải CPU, RAM hoặc Custom Metrics.
- **Đầu vào (Inputs)**: Số liệu tải thực tế từ `G1`.
- **Sản phẩm bàn giao (Artifacts)**: HPA / Scaling Rules Config.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Hệ thống tự động mở rộng pods khi tải vượt ngưỡng 75% CPU.
  - [x] Tự động thu hẹp tài nguyên khi hết giờ cao điểm để tiết kiệm chi phí.

#### G5. Centralized Logging (`stages/G5-logs.md`)
- **Mục tiêu**: Thiết lập đường truyền thu thập log tập trung (Loki / OpenSearch), structured JSON logging và Correlation ID.
- **Đầu vào (Inputs)**: Hạ tầng từ `G2`.
- **Sản phẩm bàn giao (Artifacts)**: Centralized Logging Pipeline, Log Parsing Rules.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 100% log ứng dụng xuất dạng Structured JSON.
  - [x] Mọi request đều mang Correlation ID xuyên suốt các dịch vụ.

#### G6. Prometheus Metrics (`stages/G6-metrics.md`)
- **Mục tiêu**: Thu thập 4 Golden Signals (Latency, Traffic, Errors, Saturation) và hiển thị trên Grafana Dashboards.
- **Đầu vào (Inputs)**: Hạ tầng từ `G2`.
- **Sản phẩm bàn giao (Artifacts)**: Prometheus Exporters Config, Grafana Dashboards JSON.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Thu thập đầy đủ 4 Golden Signals của dịch vụ.
  - [x] Dashboard Grafana hiển thị dữ liệu thời gian thực.

#### G7. Distributed Tracing (`stages/G7-tracing.md`)
- **Mục tiêu**: Thiết lập phân tán dấu vết yêu cầu bằng chuẩn OpenTelemetry và Jaeger / Tempo.
- **Đầu vào (Inputs)**: Hạ tầng từ `G2`.
- **Sản phẩm bàn giao (Artifacts)**: OpenTelemetry Instrumentation Config.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Truy vết được 100% span cuộc gọi từ Frontend ➔ API ➔ Database ➔ Dịch vụ ngoài.
  - [x] Nhận diện chính xác điểm nghẽn độ trễ (Latency Bottlenecks).

#### G8. Proactive Alerting (`stages/G8-alerting.md`)
- **Mục tiêu**: Thiết lập hệ thống cảnh báo chủ động qua Slack / PagerDuty dựa trên ngưỡng SLO và chính sách trực On-call.
- **Đầu vào (Inputs)**: Hợp lưu từ `G5`, `G6`, `G7`.
- **Sản phẩm bàn giao (Artifacts)**: Alertmanager Rules, On-call Escalation Policy.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Cảnh báo kích hoạt tức thời (< 1 phút) khi xảy ra sự cố nghiêm trọng P1/P2.
  - [x] Kết nối chuyển giao sang chặng ứng cứu sự cố `H1 (Incident)`.

---

### Phân Hệ H: Feedback & Continuous Improvement
- **Master Skill Điều Phối**: `fullstack-improve`
- **Lệnh Slash**: `/agent-fullstack-improve`
- **Thư mục Module Stages**: `skills/agent-fullstack-improve/stages/`

#### H1. Incident Declaration (`stages/H1-incident.md`)
- **Mục tiêu**: Khai báo sự cố, phân loại mức độ nghiêm trọng (P1 Blocker / P2 Critical / P3 Minor) và kích hoạt War Room.
- **Đầu vào (Inputs)**: Cảnh báo từ `G8` hoặc báo cáo lỗi từ người dùng.
- **Sản phẩm bàn giao (Artifacts)**: Incident Ticket, War Room Channel.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Phân loại đúng Severity Level trong vòng 5 phút từ khi phát hiện.
  - [x] Triệu tập đúng các kỹ sư On-call phụ trách.

#### H2. Triage & Diagnose (`stages/H2-triage.md`)
- **Mục tiêu**: Cô lập phạm vi ảnh hưởng, phân tích nguyên nhân sơ bộ thông qua Logs và Traces để quyết định hành động.
- **Đầu vào (Inputs)**: Incident ticket từ `H1`, dữ liệu Telemetry từ Phân hệ G.
- **Sản phẩm bàn giao (Artifacts)**: Triage Diagnosis Note.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Xác định phạm vi người dùng bị ảnh hưởng (Blast Radius).
  - [x] Ra quyết định dứt khoát: Kích hoạt Rollback hay Triển khai Hotfix.

#### H3. Rollback & Emergency Hotfix (`stages/H3-rollback-hotfix.md`)
- **Mục tiêu**: Khôi phục trạng thái dịch vụ ổn định trong thời gian ngắn nhất bằng Rollback hoặc Hotfix khẩn cấp.
- **Đầu vào (Inputs)**: Quyết định từ `H2`.
- **Sản phẩm bàn giao (Artifacts)**: Rollback Execution Log hoặc Emergency Hotfix PR.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Dịch vụ khôi phục trạng thái bình thường (MTTR < 15 phút đối với P1).
  - [x] Đóng sự cố khẩn cấp và chuyển giao sang phân tích căn nguyên.

#### H4. Root Cause Analysis (RCA) (`stages/H4-rca.md`)
- **Mục tiêu**: Phân tích căn nguyên sâu xa (5 Whys), tái hiện dòng thời gian sự cố và đúc kết bài học Blameless Post-mortem.
- **Đầu vào (Inputs)**: Dữ liệu sự cố từ `H1`, `H2`, `H3`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/ops/H4-rca.md` (Timeline, 5 Whys, Action Items).
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Xác định nguyên nhân gốc rễ (Root Cause) không đổ lỗi cá nhân (Blameless).
  - [x] Đề xuất các Action Items cụ thể để ngăn ngừa tái diễn.

#### H5. User Feedback Registry (`stages/H5-user-feedback.md`)
- **Mục tiêu**: Thu thập, phân loại và chuẩn hóa ý kiến phản hồi từ người dùng thật (Khảo sát NPS, Support Tickets, CSAT).
- **Đầu vào (Inputs)**: Phản hồi người dùng từ các kênh chăm sóc khách hàng và vận hành.
- **Sản phẩm bàn giao (Artifacts)**: Feedback Registry Document.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Phân loại rõ phản hồi thành: Lỗi vặt (Bug), Trải nghiệm kém (UX Frustration), Tính năng mong muốn (Feature Request).

#### H6. Analytics & Business KPIs (`stages/H6-analytics-kpi.md`)
- **Mục tiêu**: Đo lường hiệu quả kinh doanh thực tế, phân tích dữ liệu hành vi người dùng và đối chiếu với mục tiêu đặt ra tại A4.
- **Đầu vào (Inputs)**: Dữ liệu Analytics, KPIs thực tế.
- **Sản phẩm bàn giao (Artifacts)**: Business KPI Evaluation Report.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Đo lường được mức độ hoàn thành chỉ tiêu KPIs đã cam kết tại A4.
  - [x] Nhận diện các điểm rơi rớt (Drop-off points) trong phễu người dùng.

#### H7. Prioritization Matrix (`stages/H7-prioritization.md`)
- **Mục tiêu**: Tổng hợp Action Items từ RCA (H4), Phản hồi người dùng (H5) và Phân tích KPI (H6) để xếp thứ tự ưu tiên bằng phương pháp RICE / MoSCoW.
- **Đầu vào (Inputs)**: Hợp lưu từ `H4`, `H5`, `H6`.
- **Sản phẩm bàn giao (Artifacts)**: Prioritized Backlog Matrix.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] 100% đề xuất cải tiến được chấm điểm RICE (Reach, Impact, Confidence, Effort).
  - [x] Chọn lọc ra danh sách các tính năng ưu tiên cao nhất cho chu kỳ tiếp theo.

#### H8. New Requirement Specification (`stages/H8-new-req.md`)
- **Mục tiêu**: Chuyển hóa các đề xuất cải tiến đã được ưu tiên thành Yêu cầu Mới và **kích hoạt vòng lặp khép kín chuyển giao trực tiếp về Chặng A2 (Business Analysis)**!
- **Đầu vào (Inputs)**: Ma trận ưu tiên từ `H7`.
- **Sản phẩm bàn giao (Artifacts)**: `specs/ops/H8-new-req.md`.
- **Tiêu chí nghiệm thu (DoD Gate)**:
  - [x] Đặc tả yêu cầu cải tiến rõ ràng phạm vi thay đổi.
  - [x] **Kích hoạt tự động chặng A2 (Business Analysis) để khởi động chu kỳ phát triển mới**, bảo đảm hệ thống vận hành liên tục khép kín.
