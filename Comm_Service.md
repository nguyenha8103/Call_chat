# COMMUNICATION SERVICE - FUNCTION LIST (SUMMARY)

**Service:** Communication Service (Đa kênh tương tác KH)  
**Port:** 8005  
**Database:** `comm_db` (PostgreSQL)  
**Tech Stack:** ASP.NET Core 8.0 + Entity Framework Core + SignalR (Real-time)  
**Total Functions:** 58  
**Total Modules:** 6

---

## 📋 MỤC LỤC

1. [CHAT MANAGEMENT (12 functions)](#module-1-chat-management-12-functions)
2. [CALL MANAGEMENT (11 functions)](#module-2-call-management-11-functions)
3. [SMS MANAGEMENT (8 functions)](#module-3-sms-management-8-functions)
4. [EMAIL MANAGEMENT (10 functions)](#module-4-email-management-10-functions)
5. [SOCIAL MEDIA INTEGRATION (9 functions)](#module-5-social-media-integration-9-functions)
6. [COMMUNICATION ANALYTICS (8 functions)](#module-6-communication-analytics-8-functions)
7. [KEY FLOWS](#key-flows)
8. [CHECKLIST FIGMA](#checklist-figma)

---

## 💬 MODULE 1: CHAT MANAGEMENT (12 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 1.1 | **Start Chat Session** | Khởi tạo chat session với KH | `workspaceId`, `customerId`, `channel` (web/fb/zalo) | `sessionId`, `success` |
| 1.2 | **Send Message** | Gửi tin nhắn (agent → customer) | `sessionId`, `agentId`, `message`, `attachments[]` | `messageId`, `success` |
| 1.3 | **Receive Message** | Nhận tin nhắn từ KH (webhook) | `sessionId`, `customerId`, `message` | `messageId`, `success` |
| 1.4 | **Get Chat History** | Lấy lịch sử chat của session | `sessionId`, `pagination` | `messages[]`, `total` |
| 1.5 | **List Active Chats** | Danh sách chat đang active | `workspaceId`, `agentId` | `sessions[]` |
| 1.6 | **Assign Chat to Agent** | Gán chat cho agent (manual/auto) | `sessionId`, `agentId` | `success` |
| 1.7 | **Transfer Chat** | Chuyển chat sang agent khác | `sessionId`, `fromAgentId`, `toAgentId`, `notes` | `success` |
| 1.8 | **End Chat Session** | Kết thúc chat session | `sessionId`, `agentId`, `notes` | `success`, `endedAt` |
| 1.9 | **Search Chat Messages** | Tìm kiếm tin nhắn theo keyword | `workspaceId`, `query`, `dateRange` | `messages[]` |
| 1.10 | **Tag Chat Conversation** | Gắn tags cho chat (sales, support, etc.) | `sessionId`, `tags[]` | `success` |
| 1.11 | **Set Chat Status** | Cập nhật status (waiting, active, resolved) | `sessionId`, `status` | `success` |
| 1.12 | **Export Chat Transcript** | Xuất lịch sử chat ra file | `sessionId` | `fileUrl` (PDF/TXT) |

---

## 📞 MODULE 2: CALL MANAGEMENT (11 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 2.1 | **Initiate Outbound Call** | Gọi ra (click-to-call) | `workspaceId`, `agentId`, `phoneNumber`, `customerId` | `callId`, `success` |
| 2.2 | **Receive Inbound Call** | Nhận cuộc gọi đến (webhook) | `workspaceId`, `phoneNumber`, `callerId` | `callId`, `queuePosition` |
| 2.3 | **Answer Call** | Agent nhấc máy trả lời | `callId`, `agentId` | `success`, `answeredAt` |
| 2.4 | **End Call** | Kết thúc cuộc gọi | `callId`, `endReason` | `success`, `duration`, `endedAt` |
| 2.5 | **Get Call Details** | Xem chi tiết cuộc gọi + recording | `callId` | `call` object với duration, recording, transcript |
| 2.6 | **List Calls** | Danh sách cuộc gọi với filters | `workspaceId`, `filters`, `pagination` | `calls[]`, `total` |
| 2.7 | **Transfer Call** | Chuyển máy (transfer) | `callId`, `toAgentId` hoặc `toPhoneNumber` | `success` |
| 2.8 | **Hold/Unhold Call** | Giữ máy / Bỏ giữ | `callId`, `action` (hold/unhold) | `success` |
| 2.9 | **Record Call** | Bắt đầu/Dừng ghi âm | `callId`, `action` (start/stop) | `recordingId`, `success` |
| 2.10 | **Get Call Recording** | Lấy file ghi âm | `callId` | `recordingUrl`, `duration` |
| 2.11 | **Add Call Notes** | Thêm ghi chú sau cuộc gọi | `callId`, `agentId`, `notes`, `tags[]` | `success` |

---

## 📱 MODULE 3: SMS MANAGEMENT (8 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 3.1 | **Send SMS** | Gửi SMS cho 1 KH | `workspaceId`, `phoneNumber`, `message`, `senderId` | `smsId`, `status`, `success` |
| 3.2 | **Send Bulk SMS** | Gửi SMS hàng loạt (campaign) | `workspaceId`, `phoneNumbers[]`, `message` | `campaignId`, `sentCount` |
| 3.3 | **Receive SMS** | Nhận SMS từ KH (webhook) | `phoneNumber`, `message`, `timestamp` | `smsId`, `success` |
| 3.4 | **Get SMS Details** | Xem chi tiết SMS + delivery status | `smsId` | `sms` object với status (sent/delivered/failed) |
| 3.5 | **List SMS** | Danh sách SMS với filters | `workspaceId`, `filters`, `pagination` | `sms[]`, `total` |
| 3.6 | **Search SMS** | Tìm kiếm SMS theo content/phone | `workspaceId`, `query` | `sms[]` |
| 3.7 | **Create SMS Template** | Tạo template SMS (OTP, marketing, etc.) | `workspaceId`, `name`, `content`, `variables[]` | `templateId`, `success` |
| 3.8 | **Get SMS Analytics** | Thống kê SMS (sent, delivered, failed) | `workspaceId`, `dateRange` | `analytics` object |

---

## 📧 MODULE 4: EMAIL MANAGEMENT (10 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 4.1 | **Send Email** | Gửi email cho KH | `workspaceId`, `to`, `subject`, `body`, `attachments[]` | `emailId`, `success` |
| 4.2 | **Send Bulk Email** | Gửi email hàng loạt (campaign) | `workspaceId`, `recipients[]`, `templateId` | `campaignId`, `sentCount` |
| 4.3 | **Receive Email** | Nhận email từ KH (IMAP/webhook) | `from`, `subject`, `body` | `emailId`, `success` |
| 4.4 | **Get Email Details** | Xem chi tiết email + tracking | `emailId` | `email` object với open/click tracking |
| 4.5 | **List Emails** | Danh sách emails với filters | `workspaceId`, `filters`, `pagination` | `emails[]`, `total` |
| 4.6 | **Search Emails** | Tìm kiếm email theo subject/content | `workspaceId`, `query` | `emails[]` |
| 4.7 | **Create Email Template** | Tạo template email (HTML) | `workspaceId`, `name`, `subject`, `htmlBody`, `variables[]` | `templateId`, `success` |
| 4.8 | **Track Email Open** | Tracking khi KH mở email | `emailId`, `openedAt` | `success` |
| 4.9 | **Track Email Click** | Tracking khi KH click link | `emailId`, `linkUrl`, `clickedAt` | `success` |
| 4.10 | **Get Email Analytics** | Thống kê email (sent, opened, clicked) | `workspaceId`, `dateRange` | `analytics` object |

---

## 📲 MODULE 5: SOCIAL MEDIA INTEGRATION (9 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 5.1 | **Connect Facebook Page** | Kết nối Facebook Page | `workspaceId`, `pageId`, `accessToken` | `connectionId`, `success` |
| 5.2 | **Connect Zalo OA** | Kết nối Zalo Official Account | `workspaceId`, `oaId`, `accessToken` | `connectionId`, `success` |
| 5.3 | **Receive FB Messenger** | Nhận tin nhắn từ FB Messenger (webhook) | `pageId`, `senderId`, `message` | `messageId`, `success` |
| 5.4 | **Send FB Messenger** | Gửi tin nhắn qua FB Messenger | `pageId`, `recipientId`, `message` | `messageId`, `success` |
| 5.5 | **Receive Zalo Message** | Nhận tin nhắn từ Zalo (webhook) | `oaId`, `userId`, `message` | `messageId`, `success` |
| 5.6 | **Send Zalo Message** | Gửi tin nhắn qua Zalo | `oaId`, `userId`, `message` | `messageId`, `success` |
| 5.7 | **Get Social Conversations** | Lấy danh sách hội thoại từ social | `workspaceId`, `platform` (fb/zalo), `pagination` | `conversations[]` |
| 5.8 | **Disconnect Social Account** | Ngắt kết nối social account | `connectionId` | `success` |
| 5.9 | **Get Social Analytics** | Thống kê tương tác social media | `workspaceId`, `platform`, `dateRange` | `analytics` object |

---

## 📊 MODULE 6: COMMUNICATION ANALYTICS (8 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 6.1 | **Get Communication Overview** | Tổng quan tất cả kênh | `workspaceId`, `dateRange` | `overview` (total messages, calls, emails) |
| 6.2 | **Get Channel Performance** | Hiệu suất từng kênh (chat/call/email) | `workspaceId`, `channel`, `dateRange` | `performance` object |
| 6.3 | **Get Agent Performance** | Hiệu suất agent (calls handled, chats, etc.) | `workspaceId`, `agentId`, `dateRange` | `performance` object |
| 6.4 | **Get Response Time Report** | Báo cáo thời gian phản hồi | `workspaceId`, `channel`, `dateRange` | `avgResponseTime`, `distribution[]` |
| 6.5 | **Get Customer Satisfaction** | Điểm CSAT từ surveys sau tương tác | `workspaceId`, `channel`, `dateRange` | `csatScore`, `distribution[]` |
| 6.6 | **Get Peak Hours Report** | Giờ cao điểm (traffic by hour) | `workspaceId`, `dateRange` | `peakHours[]` với volume |
| 6.7 | **Get Channel Usage Report** | Báo cáo sử dụng kênh (% chat vs call vs email) | `workspaceId`, `dateRange` | `channelDistribution[]` |
| 6.8 | **Export Analytics** | Xuất báo cáo ra Excel/PDF | `reportType`, `filters` | `fileUrl` |

---

## 🔄 KEY FLOWS

### Flow 1: Live Chat Support (Hỗ trợ qua chat)
```
1. Customer → Opens website chat widget
2. System → Start Chat Session (1.1) → Create session
   → Status = Waiting
   → Check agent availability
3. System → Assign Chat to Agent (1.6) → Auto-assign theo queue
   → Round robin or skill-based routing
4. Agent → Receives notification → Views chat
5. Customer → Send Message → Receive Message (1.3)
6. Agent → Send Message (1.2) → Reply to customer
   → Set Status = Active
7. Agent ↔ Customer → Chat conversation
8. Agent → Resolves issue
9. Agent → End Chat Session (1.8) → Status = Resolved
   → Auto send CSAT survey (optional)
10. System → Tag Chat (1.10) → Tag as "support" / "product inquiry"
```

---

### Flow 2: Outbound Call Campaign (Chiến dịch gọi ra)
```
1. Sales → Upload customer list (phone numbers)
2. Sales → Initiate Outbound Call (2.1) → Click-to-call
   → System dials number
3. Customer → Picks up
4. System → Answer Call (2.3) → Connect agent
5. Agent → Record Call (2.9) → Start recording
6. Agent ↔ Customer → Conversation
7. Agent → Transfer Call (2.7) → Transfer to manager (if needed)
8. Agent → Add Call Notes (2.11) → Log call outcome
   → Tags: "interested", "callback", "not_interested"
9. System → End Call (2.4) → Save duration, recording
10. System → Auto create task in CRM → "Follow up in 3 days"
```

---

### Flow 3: Inbound Call Handling (Xử lý cuộc gọi đến)
```
1. Customer → Calls hotline number
2. System → Receive Inbound Call (2.2) → IVR menu
   "Press 1 for Sales, Press 2 for Support, Press 3 for Billing"
3. Customer → Selects option → Routed to queue
4. System → Find available agent → Queue position
5. Agent → Available → Answer Call (2.3)
6. System → Auto Record Call (2.9) → Start recording
7. System → Lookup customer by phone → Display customer info
8. Agent ↔ Customer → Conversation
9. Agent → Hold Call (2.8) → Check information
10. Agent → Unhold → Resume conversation
11. Agent → End Call (2.4) → Add notes
12. System → Save call log → Link to customer record
```

---

### Flow 4: Email Marketing Campaign (Chiến dịch email)
```
1. Marketing → Create Email Template (4.7)
   → Subject: "Special offer for VIP customers"
   → HTML body với variables: {{customer_name}}, {{offer_code}}
2. Marketing → Select customer segment → VIP customers (500 people)
3. Marketing → Send Bulk Email (4.2)
   → System personalizes each email
   → System sends in batches (to avoid spam)
4. System → Track Email Open (4.8) → Pixel tracking
5. System → Track Email Click (4.9) → Link tracking
6. Customer → Clicks "View Offer" → Redirected to landing page
7. Marketing → Get Email Analytics (4.10)
   → Sent: 500
   → Delivered: 495 (99%)
   → Opened: 248 (50% open rate)
   → Clicked: 124 (25% click rate)
   → Conversions: 37 (7.4% conversion rate)
```

---

### Flow 5: Omnichannel Customer Journey (Hành trình đa kênh)
```
1. Customer → Sends FB Messenger → "Giá sản phẩm X bao nhiêu?"
2. System → Receive FB Messenger (5.3) → Create conversation
   → Auto-assign to agent
3. Agent → Send FB Messenger (5.4) → Reply with price
4. Customer → Asks for discount → Not satisfied
5. Agent → "I'll have manager call you" → Collect phone number
6. Manager → Initiate Outbound Call (2.1) → Call customer
7. Manager ↔ Customer → Negotiates discount over call
8. Manager → End Call (2.4) → Confirms order
9. System → Send Email (4.1) → Order confirmation + invoice
10. System → Send SMS (3.1) → "Your order is confirmed"
11. System → Track all interactions in customer timeline
    → FB chat → Call → Email → SMS
    → Single customer view across channels
```

---

## ✅ CHECKLIST FIGMA

### ✅ MODULE 1: CHAT MANAGEMENT (100%)
- ✅ 1.1 Start Chat Session - Widget chat khởi tạo session
- ✅ 1.2 Send Message - UI chat box với attachments
- ✅ 1.3 Receive Message - Real-time update với SignalR
- ✅ 1.4 Get Chat History - Scrollable chat history
- ✅ 1.5 List Active Chats - Sidebar danh sách chats đang active
- ✅ 1.6 Assign Chat to Agent - Dropdown chọn agent
- ✅ 1.7 Transfer Chat - Dialog transfer với notes
- ✅ 1.8 End Chat Session - Button "End Chat"
- ✅ 1.9 Search Chat Messages - Search box
- ✅ 1.10 Tag Chat - Tags selector (sales, support, complaint)
- ✅ 1.11 Set Chat Status - Status dropdown (waiting, active, resolved)
- ✅ 1.12 Export Transcript - Button "Export PDF"

### ✅ MODULE 2: CALL MANAGEMENT (100%)
- ✅ 2.1 Initiate Outbound Call - Click-to-call button + dialer
- ✅ 2.2 Receive Inbound Call - Incoming call notification + popup
- ✅ 2.3 Answer Call - Button "Answer"
- ✅ 2.4 End Call - Button "End Call"
- ✅ 2.5 Get Call Details - UI call details với player recording
- ✅ 2.6 List Calls - Table call history với filters
- ✅ 2.7 Transfer Call - Button "Transfer" + agent selector
- ✅ 2.8 Hold/Unhold - Toggle button "Hold"
- ✅ 2.9 Record Call - Toggle button "Record" (auto-start)
- ✅ 2.10 Get Recording - Audio player trong call details
- ✅ 2.11 Add Call Notes - Textarea sau khi end call

### ✅ MODULE 3: SMS MANAGEMENT (100%)
- ✅ 3.1 Send SMS - Dialog compose SMS
- ✅ 3.2 Send Bulk SMS - Dialog upload CSV + preview
- ✅ 3.3 Receive SMS - Real-time notification
- ✅ 3.4 Get SMS Details - UI details với delivery status
- ✅ 3.5 List SMS - Table SMS history
- ✅ 3.6 Search SMS - Search box
- ✅ 3.7 Create SMS Template - Dialog template builder
- ✅ 3.8 Get SMS Analytics - Dashboard với charts

### ✅ MODULE 4: EMAIL MANAGEMENT (100%)
- ✅ 4.1 Send Email - Rich text editor (WYSIWYG)
- ✅ 4.2 Send Bulk Email - Campaign builder với preview
- ✅ 4.3 Receive Email - Inbox view
- ✅ 4.4 Get Email Details - Email viewer với tracking status
- ✅ 4.5 List Emails - Table inbox/sent
- ✅ 4.6 Search Emails - Search box với filters
- ✅ 4.7 Create Email Template - HTML editor với variables
- ✅ 4.8 Track Email Open - Badge "Opened" on email
- ✅ 4.9 Track Email Click - Click heatmap
- ✅ 4.10 Get Email Analytics - Dashboard open/click rates

### ✅ MODULE 5: SOCIAL MEDIA INTEGRATION (100%)
- ✅ 5.1 Connect Facebook Page - OAuth dialog
- ✅ 5.2 Connect Zalo OA - OAuth dialog
- ✅ 5.3 Receive FB Messenger - Unified inbox
- ✅ 5.4 Send FB Messenger - Chat interface
- ✅ 5.5 Receive Zalo Message - Unified inbox
- ✅ 5.6 Send Zalo Message - Chat interface
- ✅ 5.7 Get Social Conversations - Unified inbox view
- ✅ 5.8 Disconnect Social Account - Button "Disconnect"
- ✅ 5.9 Get Social Analytics - Dashboard FB/Zalo metrics

### ✅ MODULE 6: COMMUNICATION ANALYTICS (100%)
- ✅ 6.1 Get Communication Overview - Dashboard cards tổng quan
- ✅ 6.2 Get Channel Performance - Charts by channel
- ✅ 6.3 Get Agent Performance - Leaderboard + individual stats
- ✅ 6.4 Get Response Time Report - Distribution chart
- ✅ 6.5 Get Customer Satisfaction - CSAT gauge chart
- ✅ 6.6 Get Peak Hours Report - Heatmap by hour
- ✅ 6.7 Get Channel Usage Report - Pie chart channel distribution
- ✅ 6.8 Export Analytics - Button "Export Excel"

---

## 📊 PROGRESS SUMMARY

| Module | Functions | Status | Progress |
|--------|-----------|--------|----------|
| 1. Chat Management | 12 | ✅ Done | 100% |
| 2. Call Management | 11 | ✅ Done | 100% |
| 3. SMS Management | 8 | ✅ Done | 100% |
| 4. Email Management | 10 | ✅ Done | 100% |
| 5. Social Media Integration | 9 | ✅ Done | 100% |
| 6. Communication Analytics | 8 | ✅ Done | 100% |
| **TOTAL** | **58** | **✅ DONE** | **100%** |

---

## 🎯 NOTES

### Tech Stack Details:
- **Backend:** ASP.NET Core 8.0 + SignalR (Real-time WebSocket)
- **ORM:** Entity Framework Core
- **Database:** PostgreSQL (`comm_db`)
- **Cache:** Redis (active sessions, real-time presence)
- **Message Queue:** RabbitMQ (email/SMS sending queues)
- **VoIP:** Twilio / Vonage (call integration)
- **SMS Gateway:** Twilio / AWS SNS / Esendex
- **Email Service:** SendGrid / AWS SES
- **Social APIs:** Facebook Graph API, Zalo API
- **File Storage:** S3/Azure Blob (chat attachments, call recordings)

### Business Rules:

1. **Chat Routing:**
   - **Round Robin:** Distribute chats evenly
   - **Skill-based:** Route based on tags/keywords
   - **Load-based:** Assign to agent with fewest active chats
   - **VIP Routing:** Priority queue for VIP customers
   - Max concurrent chats per agent: 5

2. **Chat Session Status:**
   ```
   Waiting → Active → Resolved → Closed
      ↓
   Abandoned (nếu customer không reply > 5 phút)
   ```

3. **Call Recording:**
   - All calls auto-recorded (compliance)
   - Recording retention: 90 days
   - Sensitive info masking (credit cards, passwords)
   - Download/playback requires permission

4. **Email Sending Limits:**
   - Bulk email: Max 10,000 emails/hour
   - Daily limit: 50,000 emails
   - Spam score check before sending
   - Unsubscribe link required (GDPR compliance)

5. **SMS Pricing:**
   - Vietnam: ~700 VND/SMS
   - International: varies by country
   - OTP SMS: Higher priority queue
   - Marketing SMS: Must comply with time restrictions (8AM-8PM)

6. **Social Media Rate Limits:**
   - **Facebook Messenger:** 
     - Outbound messages: Only within 24h window after customer message
     - After 24h: Must use Message Tags (UPDATE, etc.)
   - **Zalo OA:**
     - Free tier: 1,000 messages/month
     - Paid tier required for high volume

### Database Schema:

**Chat:**
- `chat_sessions` (id, workspace_id, customer_id, agent_id, channel, status, started_at, ended_at)
- `chat_messages` (id, session_id, sender_type, sender_id, message, attachments_json, sent_at)
- `chat_session_tags` (session_id, tag_id)

**Call:**
- `calls` (id, workspace_id, customer_id, agent_id, direction, phone_number, status, duration, started_at, ended_at)
- `call_recordings` (id, call_id, recording_url, duration, transcript)
- `call_notes` (id, call_id, agent_id, notes, tags_json)

**SMS:**
- `sms_messages` (id, workspace_id, phone_number, message, direction, status, sent_at, delivered_at)
- `sms_campaigns` (id, workspace_id, name, message, sent_count, delivered_count)
- `sms_templates` (id, workspace_id, name, content, variables_json)

**Email:**
- `emails` (id, workspace_id, to_email, from_email, subject, body_html, status, sent_at, opened_at, clicked_at)
- `email_campaigns` (id, workspace_id, template_id, sent_count, opened_count, clicked_count)
- `email_templates` (id, workspace_id, name, subject, html_body, variables_json)
- `email_tracking_events` (id, email_id, event_type, timestamp, metadata_json)

**Social:**
- `social_connections` (id, workspace_id, platform, account_id, access_token, expires_at)
- `social_conversations` (id, connection_id, platform_conversation_id, customer_id, last_message_at)
- `social_messages` (id, conversation_id, sender_type, message, platform_message_id, sent_at)

### Integration Dependencies:
- **CRM Service:** Customer data, auto-create tickets from chats/calls
- **IAM Service:** User authentication, agent permissions
- **Sales Service:** Link calls/chats to orders/quotations
- **Notification Service:** Email/SMS delivery, push notifications

### Permissions:
**Chat:**
- `comm.chat.view`, `comm.chat.send`, `comm.chat.receive`
- `comm.chat.assign`, `comm.chat.transfer`, `comm.chat.end`
- `comm.chat.view_all` (supervisor)

**Call:**
- `comm.call.make`, `comm.call.answer`, `comm.call.transfer`
- `comm.call.record`, `comm.call.view_recordings`, `comm.call.download_recordings`
- `comm.call.view_all` (manager)

**SMS:**
- `comm.sms.send`, `comm.sms.send_bulk`, `comm.sms.view`
- `comm.sms.manage_templates`

**Email:**
- `comm.email.send`, `comm.email.send_bulk`, `comm.email.view`
- `comm.email.manage_templates`, `comm.email.view_analytics`

**Social:**
- `comm.social.connect`, `comm.social.manage`
- `comm.social.send`, `comm.social.receive`

**Analytics:**
- `comm.analytics.view`, `comm.analytics.export`
- `comm.analytics.view_team` (manager)

**Total Permissions:** 28

### Real-time Features (SignalR):
1. **Chat:** Instant message delivery, typing indicators, online/offline status
2. **Call:** Real-time call status updates, queue position
3. **Agent Presence:** Online/away/busy status
4. **Notifications:** Incoming messages, calls, alerts

### Compliance & Security:
- **GDPR:** Right to be forgotten, data export, consent tracking
- **Call Recording Disclosure:** Auto-play "This call may be recorded"
- **Data Retention:** 
  - Chats: 2 years
  - Calls: 90 days recordings, logs indefinitely
  - Emails: 5 years
  - SMS: 2 years
- **Encryption:** End-to-end for sensitive data, TLS for all communications

---

## 🚀 DEPLOYMENT INFO

**Production URL:** `https://api.nextx.vn/comm` (Port 8005)  
**Staging URL:** `https://staging.nextx.vn/comm`  
**Local Dev:** `http://localhost:8005`

**WebSocket Endpoint:** `wss://api.nextx.vn/comm/hub` (SignalR)

---

## 📈 METRICS & KPIs

### Chat Metrics:
- **Concurrent Active Chats:** Current active chat sessions
- **Avg Wait Time:** Time from session start to agent assignment
- **Avg Response Time:** Time agent takes to reply
- **Avg Session Duration:** How long chats last
- **Abandonment Rate:** % chats abandoned before agent response
- **CSAT Score:** Customer satisfaction from post-chat surveys

### Call Metrics:
- **Total Calls:** Inbound + Outbound
- **Avg Call Duration:** Length of calls
- **Avg Wait Time:** Queue time before agent answers
- **Abandonment Rate:** % customers hang up before agent answers
- **First Call Resolution (FCR):** % issues resolved in first call
- **Call Volume by Hour:** Traffic patterns

### Email Metrics:
- **Delivery Rate:** % emails successfully delivered
- **Open Rate:** % emails opened
- **Click Rate:** % emails with link clicks
- **Bounce Rate:** % emails bounced
- **Unsubscribe Rate:** % recipients who unsubscribed
- **Spam Complaint Rate:** % marked as spam (should be < 0.1%)

### SMS Metrics:
- **Delivery Rate:** % SMS successfully delivered
- **Response Rate:** % customers who reply
- **Cost per SMS:** Average cost
- **Opt-out Rate:** % customers who opt out

### Channel Performance:
- **Channel Mix:** % volume by channel (chat, call, email, SMS)
- **Channel Preference:** Customer preferred channel
- **Response Time by Channel:** Compare speed across channels
- **CSAT by Channel:** Satisfaction score per channel

---

**Last Updated:** 04/02/2026  
**Version:** 1.0.0  
**Status:** ✅ Production Ready

