# CRM SERVICE - FUNCTION LIST (SUMMARY)

**Service:** CRM Service (Customer Relationship Management)  
**Port:** 8004  
**Database:** `crm_db` (PostgreSQL)  
**Tech Stack:** ASP.NET Core 8.0 + Entity Framework Core  
**Total Functions:** 54  
**Total Modules:** 5

---

## 📋 MỤC LỤC

1. [CUSTOMER MANAGEMENT (13 functions)](#module-1-customer-management-13-functions)
2. [KPI MANAGEMENT (9 functions)](#module-2-kpi-management-9-functions)
3. [SURVEY MANAGEMENT (10 functions)](#module-3-survey-management-10-functions)
4. [TICKET MANAGEMENT (13 functions)](#module-4-ticket-management-13-functions)
5. [TASK MANAGEMENT (9 functions)](#module-5-task-management-9-functions)
6. [KEY FLOWS](#key-flows)
7. [CHECKLIST FIGMA](#checklist-figma)

---

## 👥 MODULE 1: CUSTOMER MANAGEMENT (13 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 1.1 | **Create Customer** | Tạo khách hàng mới (individual/company) | `workspaceId`, `type`, `name`, `email`, `phone`, `address` | `customerId`, `code`, `success` |
| 1.2 | **Update Customer** | Cập nhật thông tin khách hàng | `customerId`, `data` | `success`, `customer` |
| 1.3 | **Delete Customer** | Xóa khách hàng (soft delete) | `customerId` | `success` |
| 1.4 | **Get Customer Details** | Xem chi tiết KH + history + stats | `customerId` | `customer` object với orders, tickets, interactions |
| 1.5 | **List Customers** | Danh sách KH với filters & segments | `workspaceId`, `filters`, `pagination` | `customers[]`, `total` |
| 1.6 | **Search Customers** | Tìm kiếm KH theo name/email/phone | `query` | `customers[]` |
| 1.7 | **Import Customers** | Import KH từ Excel/CSV | `workspaceId`, `file` | `successCount`, `errorCount`, `errors[]` |
| 1.8 | **Export Customers** | Xuất danh sách KH ra Excel | `workspaceId`, `filters` | `fileUrl` |
| 1.9 | **Manage Customer Segments** | Tạo/Sửa segments (VIP, Regular, etc.) | `workspaceId`, `name`, `criteria` | `segmentId`, `success` |
| 1.10 | **Assign Customer to Segment** | Gán KH vào segment | `customerId`, `segmentId` | `success` |
| 1.11 | **Log Customer Interaction** | Ghi nhận tương tác (call, email, meeting) | `customerId`, `type`, `notes`, `userId` | `interactionId`, `success` |
| 1.12 | **Get Customer Lifetime Value** | Tính CLV (tổng doanh thu từ KH) | `customerId` | `clv`, `totalOrders`, `avgOrderValue` |
| 1.13 | **Merge Duplicate Customers** | Gộp KH trùng lặp | `primaryCustomerId`, `duplicateCustomerIds[]` | `success` |

---

## 📊 MODULE 2: KPI MANAGEMENT (9 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 2.1 | **Create KPI** | Tạo KPI mới cho user/team | `workspaceId`, `name`, `target`, `unit`, `assignee` | `kpiId`, `success` |
| 2.2 | **Update KPI** | Cập nhật KPI (target, deadline) | `kpiId`, `data` | `success`, `kpi` |
| 2.3 | **Delete KPI** | Xóa KPI | `kpiId` | `success` |
| 2.4 | **Get KPI Details** | Xem chi tiết KPI + progress | `kpiId` | `kpi` object với actual, progress%, history |
| 2.5 | **List KPIs** | Danh sách KPI với filters | `workspaceId`, `filters`, `pagination` | `kpis[]`, `total` |
| 2.6 | **Update KPI Progress** | Cập nhật tiến độ KPI | `kpiId`, `actualValue`, `notes` | `success`, `progress%` |
| 2.7 | **Get User KPIs** | Lấy danh sách KPI của user | `userId`, `period` | `kpis[]` với progress |
| 2.8 | **Get Team KPIs** | Lấy KPI của team/department | `teamId`, `period` | `kpis[]` với aggregated data |
| 2.9 | **KPI Performance Report** | Báo cáo đạt/không đạt KPI | `workspaceId`, `period` | `report` object với achievement rate |

---

## 📋 MODULE 3: SURVEY MANAGEMENT (10 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 3.1 | **Create Survey** | Tạo khảo sát mới (CSAT, NPS, custom) | `workspaceId`, `title`, `type`, `questions[]` | `surveyId`, `code`, `success` |
| 3.2 | **Update Survey** | Cập nhật khảo sát (draft only) | `surveyId`, `data` | `success`, `survey` |
| 3.3 | **Delete Survey** | Xóa khảo sát | `surveyId` | `success` |
| 3.4 | **Get Survey Details** | Xem chi tiết survey + questions | `surveyId` | `survey` object |
| 3.5 | **List Surveys** | Danh sách khảo sát với filters | `workspaceId`, `filters`, `pagination` | `surveys[]`, `total` |
| 3.6 | **Publish Survey** | Publish khảo sát → Gửi cho KH | `surveyId`, `customerIds[]` hoặc `segmentId` | `success`, `sentCount` |
| 3.7 | **Submit Survey Response** | KH trả lời khảo sát | `surveyId`, `customerId`, `answers[]` | `responseId`, `success` |
| 3.8 | **Get Survey Responses** | Lấy danh sách responses | `surveyId`, `pagination` | `responses[]`, `total` |
| 3.9 | **Get Survey Analytics** | Phân tích kết quả khảo sát | `surveyId` | `analytics` (avg score, distribution, trends) |
| 3.10 | **Export Survey Results** | Xuất kết quả khảo sát ra Excel | `surveyId` | `fileUrl` |

---

## 🎫 MODULE 4: TICKET MANAGEMENT (13 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 4.1 | **Create Ticket** | Tạo ticket support mới | `workspaceId`, `customerId`, `subject`, `description`, `priority` | `ticketId`, `code`, `success` |
| 4.2 | **Update Ticket** | Cập nhật ticket (subject, priority, etc.) | `ticketId`, `data` | `success`, `ticket` |
| 4.3 | **Delete Ticket** | Xóa ticket (soft delete) | `ticketId` | `success` |
| 4.4 | **Get Ticket Details** | Xem chi tiết ticket + comments + history | `ticketId` | `ticket` object |
| 4.5 | **List Tickets** | Danh sách tickets với filters | `workspaceId`, `filters`, `pagination` | `tickets[]`, `total` |
| 4.6 | **Search Tickets** | Tìm kiếm ticket theo code/subject | `query` | `tickets[]` |
| 4.7 | **Assign Ticket** | Gán ticket cho agent | `ticketId`, `assigneeId` | `success` |
| 4.8 | **Update Ticket Status** | Cập nhật trạng thái (open → in_progress → resolved) | `ticketId`, `status`, `notes` | `success`, `ticket` |
| 4.9 | **Add Comment to Ticket** | Thêm comment/note vào ticket | `ticketId`, `userId`, `comment`, `isPublic` | `commentId`, `success` |
| 4.10 | **Add Attachment to Ticket** | Đính kèm file vào ticket | `ticketId`, `file` | `attachmentId`, `fileUrl` |
| 4.11 | **Resolve Ticket** | Đánh dấu ticket đã giải quyết | `ticketId`, `resolution`, `userId` | `success`, `resolvedAt` |
| 4.12 | **Reopen Ticket** | Mở lại ticket đã resolved | `ticketId`, `reason` | `success` |
| 4.13 | **Get Ticket Metrics** | Thống kê tickets (avg resolution time, etc.) | `workspaceId`, `period` | `metrics` object |

---

## ✅ MODULE 5: TASK MANAGEMENT (9 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 5.1 | **Create Task** | Tạo nhiệm vụ mới (liên kết KH/ticket/deal) | `workspaceId`, `title`, `assigneeId`, `dueDate`, `relatedTo` | `taskId`, `success` |
| 5.2 | **Update Task** | Cập nhật nhiệm vụ | `taskId`, `data` | `success`, `task` |
| 5.3 | **Delete Task** | Xóa nhiệm vụ | `taskId` | `success` |
| 5.4 | **Get Task Details** | Xem chi tiết task + comments | `taskId` | `task` object |
| 5.5 | **List Tasks** | Danh sách tasks với filters | `workspaceId`, `filters`, `pagination` | `tasks[]`, `total` |
| 5.6 | **Assign Task** | Gán task cho user | `taskId`, `assigneeId` | `success` |
| 5.7 | **Update Task Status** | Cập nhật trạng thái (todo → in_progress → done) | `taskId`, `status` | `success`, `task` |
| 5.8 | **Get My Tasks** | Lấy danh sách tasks của user | `userId`, `status` | `tasks[]` |
| 5.9 | **Get Overdue Tasks** | Danh sách tasks quá hạn | `workspaceId` hoặc `userId` | `tasks[]` |

---

## 🔄 KEY FLOWS

### Flow 1: Customer Onboarding (Khách hàng mới)
```
1. Sales → Create Customer (1.1) → Tạo KH mới
   → Auto generate customer code: CUS-YYYYMMDD-XXX
2. System → Assign to Segment (1.10) → Tự động phân loại (based on criteria)
3. Sales → Log Interaction (1.11) → Ghi nhận cuộc gọi/email đầu tiên
4. Sales → Create Task (5.1) → Tạo task follow-up KH
5. System → Trigger welcome survey (3.6) → Gửi khảo sát chào mừng
```

### Flow 2: Support Ticket Lifecycle (Chu trình ticket)
```
1. Customer → Submit ticket qua email/chat/phone
2. System → Create Ticket (4.1) → Auto create ticket
   → Status = Open
   → Auto assign theo round-robin or skill-based routing
3. Agent → View ticket → Add Comment (4.9) → Trả lời KH
4. Agent → Update Status (4.8) → Open → In Progress → Waiting
5. Agent → Add Attachment (4.10) → Đính kèm file (nếu cần)
6. Agent → Resolve Ticket (4.11) → Mark as Resolved
   → Send resolution email to customer
7. Customer → Not satisfied → Reopen Ticket (4.12)
8. System → Track Metrics (4.13) → Avg resolution time, CSAT score
```

### Flow 3: KPI Tracking (Theo dõi KPI)
```
1. Manager → Create KPI (2.1) → Tạo KPI cho sales team
   Example: "Doanh thu tháng 2: 500M VND"
2. System → Assign to users/team
3. Sales → Daily work → System auto Update Progress (2.6)
   → Track from Orders/Deals closed
4. Sales → View My KPIs (2.7) → Xem tiến độ cá nhân
5. Manager → View Team KPIs (2.8) → Dashboard overview
6. End of period → KPI Performance Report (2.9)
   → Achievement rate, Top performers
```

### Flow 4: Survey Campaign (Chiến dịch khảo sát)
```
1. Marketing → Create Survey (3.1) → Tạo CSAT survey
   Questions: "How satisfied are you? (1-5 stars)"
2. Marketing → Publish Survey (3.6) → Gửi cho segment "Customers đã mua hàng"
   → Send via Email/SMS
3. Customer → Click link → Submit Response (3.7)
4. System → Auto calculate score
5. Marketing → Get Analytics (3.9) → View results
   → Avg CSAT: 4.2/5
   → Distribution: 5★ (40%), 4★ (35%), 3★ (15%), 2★ (7%), 1★ (3%)
6. Marketing → Export Results (3.10) → Excel report
```

### Flow 5: Task Management for Sales (Quản lý công việc sales)
```
1. Sales → Create Customer (1.1) → KH mới
2. System → Auto Create Task (5.1) → "Follow up KH trong 3 ngày"
   → Assignee = Sales user
   → Due date = +3 days
   → Related to = Customer
3. Sales → Get My Tasks (5.8) → View danh sách tasks hôm nay
4. Sales → Log Interaction (1.11) → Call KH
5. Sales → Update Task Status (5.7) → Mark as Done
6. Sales → Create new Task (5.1) → "Gửi quotation"
7. System → Get Overdue Tasks (5.9) → Notify tasks quá hạn
```

---

## ✅ CHECKLIST FIGMA

### ✅ MODULE 1: CUSTOMER MANAGEMENT (100%)
- ✅ 1.1 Create Customer - Dialog tạo KH với tabs Individual/Company
- ✅ 1.2 Update Customer - Dialog cập nhật thông tin
- ✅ 1.3 Delete Customer - Dialog xác nhận xóa
- ✅ 1.4 Get Customer Details - UI profile KH với tabs (Info, Orders, Tickets, Interactions)
- ✅ 1.5 List Customers - Table với filters + segment badges
- ✅ 1.6 Search Customers - Search box với autocomplete
- ✅ 1.7 Import Customers - Dialog upload Excel/CSV
- ✅ 1.8 Export Customers - Button "Xuất Excel"
- ✅ 1.9 Manage Segments - Dialog tạo segment với criteria builder
- ✅ 1.10 Assign to Segment - Dropdown chọn segment
- ✅ 1.11 Log Interaction - Dialog ghi nhận tương tác (call/email/meeting)
- ✅ 1.12 Get CLV - Badge hiển thị CLV trên customer card
- ✅ 1.13 Merge Customers - Dialog chọn primary + duplicates

### ✅ MODULE 2: KPI MANAGEMENT (100%)
- ✅ 2.1 Create KPI - Dialog tạo KPI với target/deadline
- ✅ 2.2 Update KPI - Dialog cập nhật
- ✅ 2.3 Delete KPI - Dialog xác nhận xóa
- ✅ 2.4 Get KPI Details - UI hiển thị progress bar + chart
- ✅ 2.5 List KPIs - Table với progress indicators
- ✅ 2.6 Update Progress - Dialog nhập actual value
- ✅ 2.7 Get My KPIs - Dashboard cá nhân với KPI cards
- ✅ 2.8 Get Team KPIs - Dashboard team với aggregated view
- ✅ 2.9 KPI Report - UI báo cáo achievement rate với charts

### ✅ MODULE 3: SURVEY MANAGEMENT (100%)
- ✅ 3.1 Create Survey - Dialog tạo survey với question builder
- ✅ 3.2 Update Survey - Dialog cập nhật (draft only)
- ✅ 3.3 Delete Survey - Dialog xác nhận xóa
- ✅ 3.4 Get Survey Details - UI preview survey
- ✅ 3.5 List Surveys - Table với status (draft/published/closed)
- ✅ 3.6 Publish Survey - Dialog chọn recipients (customers/segments)
- ✅ 3.7 Submit Response - Public form cho KH trả lời
- ✅ 3.8 Get Responses - Table danh sách responses
- ✅ 3.9 Get Analytics - Dashboard với charts (CSAT, NPS scores)
- ✅ 3.10 Export Results - Button "Xuất Excel"

### ✅ MODULE 4: TICKET MANAGEMENT (100%)
- ✅ 4.1 Create Ticket - Dialog tạo ticket với priority/category
- ✅ 4.2 Update Ticket - Dialog cập nhật
- ✅ 4.3 Delete Ticket - Dialog xác nhận xóa
- ✅ 4.4 Get Ticket Details - UI chi tiết với timeline comments
- ✅ 4.5 List Tickets - Table với status flow + priority badges
- ✅ 4.6 Search Tickets - Search box
- ✅ 4.7 Assign Ticket - Dropdown chọn agent
- ✅ 4.8 Update Status - Dropdown status với confirmation
- ✅ 4.9 Add Comment - Comment box (public/internal toggle)
- ✅ 4.10 Add Attachment - File upload button
- ✅ 4.11 Resolve Ticket - Dialog nhập resolution notes
- ✅ 4.12 Reopen Ticket - Dialog nhập reason
- ✅ 4.13 Get Metrics - Dashboard với avg resolution time, SLA compliance

### ✅ MODULE 5: TASK MANAGEMENT (100%)
- ✅ 5.1 Create Task - Dialog tạo task với assignee/due date
- ✅ 5.2 Update Task - Dialog cập nhật
- ✅ 5.3 Delete Task - Dialog xác nhận xóa
- ✅ 5.4 Get Task Details - UI chi tiết task với comments
- ✅ 5.5 List Tasks - Table/Kanban board view
- ✅ 5.6 Assign Task - Dropdown chọn assignee
- ✅ 5.7 Update Status - Drag & drop on Kanban or dropdown
- ✅ 5.8 Get My Tasks - Dashboard "My Tasks" với filters
- ✅ 5.9 Get Overdue Tasks - Badge notification + list

---

## 📊 PROGRESS SUMMARY

| Module | Functions | Status | Progress |
|--------|-----------|--------|----------|
| 1. Customer Management | 13 | ✅ Done | 100% |
| 2. KPI Management | 9 | ✅ Done | 100% |
| 3. Survey Management | 10 | ✅ Done | 100% |
| 4. Ticket Management | 13 | ✅ Done | 100% |
| 5. Task Management | 9 | ✅ Done | 100% |
| **TOTAL** | **54** | **✅ DONE** | **100%** |

---

## 🎯 NOTES

### Tech Stack Details:
- **Backend:** ASP.NET Core 8.0
- **ORM:** Entity Framework Core
- **Database:** PostgreSQL (`crm_db`)
- **Cache:** Redis (customer data, segments)
- **Email:** SMTP service (surveys, ticket notifications)
- **SMS:** Twilio/Vonage (survey links, ticket updates)
- **File Storage:** S3/Azure Blob (ticket attachments)

### Business Rules:

1. **Customer Segmentation:**
   - Auto-segmentation based on criteria:
     - **VIP:** CLV > 100M VND
     - **Premium:** 50M < CLV < 100M
     - **Regular:** CLV < 50M
   - Manual segment assignment override allowed
   - Customer có thể thuộc nhiều segments

2. **Ticket SLA:**
   - **Priority High:** Response time < 1 hour, Resolution < 4 hours
   - **Priority Medium:** Response time < 4 hours, Resolution < 24 hours
   - **Priority Low:** Response time < 24 hours, Resolution < 72 hours
   - Auto-escalate nếu vượt SLA

3. **Ticket Status Flow:**
   ```
   Open → In Progress → Waiting (for customer) → Resolved → Closed
     ↓                                               ↓
   Cancelled ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← Reopened
   ```

4. **KPI Rules:**
   - KPI có thể là: Revenue, Number of deals, Calls made, Emails sent, etc.
   - Progress auto-update từ system (orders, calls, emails tracked)
   - Manual update allowed
   - Achievement: < 80% (red), 80-99% (yellow), >= 100% (green)

5. **Survey Types:**
   - **CSAT (Customer Satisfaction):** 1-5 stars
   - **NPS (Net Promoter Score):** 0-10 scale
   - **Custom:** Multiple choice, text, rating, etc.
   - Auto-send surveys:
     - After order delivery (CSAT)
     - After ticket resolution (CSAT)
     - Quarterly loyalty survey (NPS)

6. **Task Auto-creation Rules:**
   - New customer → Task "Follow up in 3 days"
   - Ticket resolved → Task "Check customer satisfaction after 24h"
   - Deal won → Task "Onboard customer"
   - Task overdue → Auto-notify assignee + manager

### Database Schema:

**Customers:**
- `customers` (id, code, type, name, email, phone, address, segment_id)
- `customer_segments` (id, name, criteria_json, color)
- `customer_interactions` (id, customer_id, type, notes, user_id, created_at)

**KPIs:**
- `kpis` (id, workspace_id, name, target, unit, assignee_id, deadline)
- `kpi_progress` (id, kpi_id, actual_value, progress_pct, updated_at)

**Surveys:**
- `surveys` (id, workspace_id, title, type, status, questions_json)
- `survey_recipients` (id, survey_id, customer_id, sent_at, completed_at)
- `survey_responses` (id, survey_id, customer_id, answers_json, score)

**Tickets:**
- `tickets` (id, code, workspace_id, customer_id, subject, description, priority, status, assignee_id)
- `ticket_comments` (id, ticket_id, user_id, comment, is_public)
- `ticket_attachments` (id, ticket_id, file_name, file_url)
- `ticket_sla_logs` (id, ticket_id, sla_type, due_at, met, violated_at)

**Tasks:**
- `tasks` (id, workspace_id, title, description, assignee_id, due_date, status, related_type, related_id)
- `task_comments` (id, task_id, user_id, comment)

### Integration Dependencies:
- **Sales Service:** Get customer orders, revenue (for CLV calculation)
- **IAM Service:** User authentication, workspace context, permissions
- **Notification Service:** Email surveys, SMS ticket updates, task reminders
- **Call Service:** Log calls as customer interactions, auto-create tickets from calls
- **Chat Service:** Log chat conversations, create tickets from chat

### Permissions:
**Customers:**
- `crm.customers.view`, `crm.customers.create`, `crm.customers.update`, `crm.customers.delete`
- `crm.customers.import`, `crm.customers.export`, `crm.customers.merge`
- `crm.segments.manage`

**KPIs:**
- `crm.kpis.view`, `crm.kpis.create`, `crm.kpis.update`, `crm.kpis.delete`
- `crm.kpis.update_progress`, `crm.kpis.view_team`

**Surveys:**
- `crm.surveys.view`, `crm.surveys.create`, `crm.surveys.update`, `crm.surveys.delete`
- `crm.surveys.publish`, `crm.surveys.view_results`, `crm.surveys.export`

**Tickets:**
- `crm.tickets.view`, `crm.tickets.create`, `crm.tickets.update`, `crm.tickets.delete`
- `crm.tickets.assign`, `crm.tickets.resolve`, `crm.tickets.view_all` (admin)

**Tasks:**
- `crm.tasks.view`, `crm.tasks.create`, `crm.tasks.update`, `crm.tasks.delete`
- `crm.tasks.assign`, `crm.tasks.view_all` (manager)

**Total Permissions:** 36

### Customer Lifetime Value (CLV) Calculation:
```csharp
CLV = Σ(all orders revenue) + Σ(contract values)
Average Order Value = CLV / Number of Orders
Customer Health Score = (Recent purchase + Interaction frequency + Survey score) / 3
```

### Ticket Auto-assignment Rules:
1. **Round Robin:** Distribute evenly among available agents
2. **Skill-based:** Assign based on agent skills/expertise
3. **Load-based:** Assign to agent with fewest open tickets
4. **VIP Customer:** Auto-assign to senior agents

### Survey Scoring:
- **CSAT:** Avg of 1-5 stars → % satisfied (4-5 stars)
- **NPS:** 
  - Promoters (9-10): Happy customers
  - Passives (7-8): Satisfied but not enthusiastic
  - Detractors (0-6): Unhappy
  - NPS Score = % Promoters - % Detractors

---

## 🚀 DEPLOYMENT INFO

**Production URL:** `https://api.nextx.vn/crm` (Port 8004)  
**Staging URL:** `https://staging.nextx.vn/crm`  
**Local Dev:** `http://localhost:8004`

---

## 📈 METRICS & ANALYTICS

### Customer Metrics:
- Total Customers
- New Customers (this month)
- Customer Growth Rate
- Customer Churn Rate
- Average CLV
- Customer Health Score

### Ticket Metrics:
- Total Tickets
- Open Tickets
- Avg First Response Time
- Avg Resolution Time
- SLA Compliance Rate
- Customer Satisfaction (CSAT)

### KPI Metrics:
- Total KPIs
- KPIs On Track (>= 80%)
- KPIs At Risk (< 80%)
- Team Achievement Rate
- Individual Achievement Rate

### Survey Metrics:
- Survey Response Rate
- Avg CSAT Score
- NPS Score
- Survey Completion Rate

---

**Last Updated:** 04/02/2026  
**Version:** 1.0.0  
**Status:** ✅ Production Ready

