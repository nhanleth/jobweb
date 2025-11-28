# 📊 BÁO CÁO TỔNG QUAN DỰ ÁN JOBV5

---

## 🎯 TỔNG QUAN DỰ ÁN

### Thông Tin Cơ Bản

| Thông Tin | Chi Tiết |
|-----------|----------|
| **Tên dự án** | JobV5 - Nền Tảng Tuyển Dụng Thông Minh |
| **Framework** | Laravel 12.x |
| **PHP Version** | 8.2+ |
| **Database** | MySQL 8.0+ |
| **Frontend** | Blade Templates + Alpine.js + Tailwind CSS |
| **Real-time** | Laravel Reverb (WebSocket) |
| **AI** | Google Gemini API (Optional) |
| **Cache** | Redis/File Cache |

### Mô Tả

JobV5 là một nền tảng tuyển dụng việc làm hiện đại, tích hợp **AI Chatbot thông minh**, hỗ trợ 3 loại người dùng với các tính năng riêng biệt:

#### 👥 Vai Trò Người Dùng

**1. Admin (Quản trị viên)**
- Quản lý toàn bộ hệ thống
- Xem thống kê và báo cáo
- Quản lý user, company, job listings
- Analytics dashboard (Chatbot, Notifications, Jobs)
- Export reports (CSV, PDF)

**2. Company (Nhà tuyển dụng)**
- Đăng và quản lý tin tuyển dụng
- Xem và quản lý ứng viên
- Thống kê hiệu quả tuyển dụng
- Gói Premium với tính năng mở rộng
- Email notification system

**3. User (Người tìm việc)**
- Tìm kiếm việc làm (filter, search, sort)
- Ứng tuyển và theo dõi trạng thái
- Quản lý CV (upload, download, delete)
- Chatbot hỗ trợ 24/7
- Job alerts (proactive notifications)
- Save jobs (bookmark)

---

## 📁 CẤU TRÚC DỰ ÁN

### Tổng Quan File & Folder

```
jobv5/
├── app/
│   ├── Console/
│   │   └── Commands/
│   │       ├── CheckNewJobsCommand.php
│   │       ├── CheckNotifications.php
│   │       ├── CleanOrphanedFiles.php
│   │       ├── CleanStorageFiles.php
│   │       ├── CleanupOldNotifications.php
│   │       ├── FreshDatabase.php
│   │       └── WeeklySummaryCommand.php
│   ├── Events/
│   │   ├── ChatbotMessageSent.php
│   │   ├── NewJobAlert.php
│   │   └── NotificationSent.php
│   ├── Helpers/
│   │   ├── HtmlSanitizer.php
│   │   └── helpers.php
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Admin/
│   │   │   ├── Company/
│   │   │   ├── User/
│   │   │   ├── ChatbotController.php
│   │   │   ├── ChatbotTemplateController.php
│   │   │   ├── ChatbotTestController.php
│   │   │   └── Auth/
│   │   ├── Middleware/
│   │   └── Requests/
│   ├── Models/
│   ├── Notifications/
│   ├── Providers/
│   ├── Services/
│   │   ├── AI/
│   │   │   ├── Contracts/
│   │   │   ├── ProviderRegistry.php
│   │   │   └── Providers/
│   │   ├── ChatbotService.php
│   │   ├── ConversationContextService.php
│   │   ├── GeminiService.php
│   │   ├── NotificationService.php
│   │   └── ProactiveAlertService.php
│   └── View/
│       └── Composers/
│           ├── CategoryComposer.php
│           └── NotificationComposer.php
├── bootstrap/
│   ├── app.php
│   ├── cache/
│   └── providers.php
├── config/
│   ├── app.php
│   ├── auth.php
│   ├── database.php
│   ├── mail.php
│   ├── queue.php
│   ├── reverb.php
│   ├── services.php
│   └── ...
├── database/
│   ├── factories/
│   ├── migrations/
│   └── seeders/
├── docs/
│   ├── 08-lo-trinh-phat-trien.md
│   ├── NGROK_DEMO_GUIDE.md
│   ├── PROJECT-OVERVIEW-REPORT.md
│   ├── REAL-TIME-WEBSOCKET.md
│   ├── SWEETALERT2-COMPONENTS.md
│   ├── chatweb.md
│   └── components.md
├── public/
│   ├── .htaccess
│   ├── build/
│   │   ├── assets/
│   │   └── manifest.json
│   ├── favicon.ico
│   ├── images/
│   ├── index.php
│   ├── robots.txt
│   ├── storage -> ../storage/app/public
│   └── uploads/
│       └── job-listings/
├── resources/
│   ├── css/
│   │   └── app.css
│   ├── js/
│   │   ├── app.js
│   │   ├── bootstrap.js
│   │   ├── chatbot-realtime.js
│   │   └── echo.js
│   └── views/
│       ├── admin/
│       ├── auth/
│       ├── chatbot/
│       │   └── export-pdf.blade.php
│       ├── company/
│       ├── components/
│       │   ├── application-logo.blade.php
│       │   ├── auth-session-status.blade.php
│       │   ├── avatar.blade.php
│       │   ├── badge.blade.php
│       │   ├── button.blade.php
│       │   ├── card.blade.php
│       │   ├── chatbot-simple-test.blade.php
│       │   ├── chatbot-widget.blade.php
│       │   ├── form-helper.blade.php
│       │   ├── form.blade.php
│       │   ├── modal.blade.php
│       │   ├── navigation.blade.php
│       │   ├── notification-bell.blade.php
│       │   ├── notification-listener.blade.php
│       │   ├── notification.blade.php
│       │   ├── search-input.blade.php
│       │   └── table.blade.php
│       ├── layouts/
│       ├── user/
│       ├── test-chatbot.blade.php
│       └── welcome.blade.php
├── routes/
│   ├── admin.php
│   ├── auth.php
│   ├── channels.php
│   ├── company.php
│   ├── console.php
│   ├── user.php
│   └── web.php
├── storage/
│   ├── app/
│   │   ├── private/
│   │   └── public/
│   │       ├── .gitignore
│   │       ├── avatars/
│   │       ├── company-logos/
│   │       ├── cvs/
│   │       └── uploads/
│   ├── debugbar/
│   ├── framework/
│   └── logs/
├── tests/
│   ├── Feature/
│   ├── Manual/
│   │   ├── check-faq.php
│   │   ├── check_sample_data.php
│   │   ├── create-admin.php
│   │   ├── fix-keywords-issue.php
│   │   ├── test-admin-dashboard.php
│   │   ├── test-chatbot-connection.php
│   │   ├── test-chatbot.php
│   │   ├── test-websocket.php
│   │   └── update-company-faq.php
│   ├── TestCase.php
│   └── Unit/
├── artisan
├── composer.json
├── package.json
├── phpunit.xml
├── tailwind.config.js
├── vite.config.js
└── .env.example
```

---

## 🌟 TÍNH NĂNG CHÍNH

### 1. 🤖 AI Chatbot

#### Tính Năng Nổi Bật

**Multi-Tier Matching System**
```
Tier 1: Exact Match     → 100% accuracy | FREE | 0.1ms
Tier 2: Keyword Match   → 60-90% accuracy | FREE | 1-5ms  
Tier 3: Fuzzy Match     → 50-80% accuracy | FREE | 5-20ms
Tier 4: Gemini AI       → 95% accuracy | PAID | 500-2000ms
```

**Contextual Conversations**
- Job Search Flow (4 steps: skills → location → experience → salary)
- CV Help Flow (analyze/improve/template/tips)
- Salary Inquiry Flow (position/experience/trend/negotiation)
- Context retention across multiple messages

**Real-time Features**
- WebSocket messaging (Laravel Reverb)
- Typing indicators
- Read receipts
- Auto-reconnect

**Quick Actions**
- 🤖 AI CV Analysis
- 📄 Export Chat PDF

**Analytics Dashboard**
- Match rate tracking
- AI usage & cost monitoring
- Top FAQs analysis
- Unmatched questions for improvement
- Rating distribution
- Conversation history

**Knowledge Base**
- 50+ pre-seeded FAQs
- Categories: job, cv, salary, interview, company, general
- Keyword-based matching
- Priority ranking
- Helpfulness tracking


---

### 2. 💼 Job Management

#### Company Features

**Job Posting**:
- Create/Edit/Delete job listings
- Rich text editor (description)
- Categories, locations, salary ranges
- Job types (Full-time, Part-time, Remote, etc.)
- Expiration dates

**Application Management**:
- View all applications
- Filter by status (pending/reviewed/accepted/rejected)
- Bulk actions
- Email notifications on new applications
- Application analytics

**Dashboard**:
- Total jobs, applications stats
- Active jobs count
- Recent applications
- Charts & graphs

#### User Features

**Job Search**:
- Advanced filters (category, location, salary, type)
- Keyword search
- Sort by (newest, salary, relevance)
- Pagination
- Save jobs (bookmark)

**Job Detail**:
- Full job description
- Company information
- Apply button
- Share job (social)
- Report job

**Application**:
- Apply with uploaded CV
- Cover letter
- Track application status
- Email notifications on status change
- Application history

---

### 3. 📄 CV Management

#### Features

**Upload & Storage**:
- Upload CV (PDF)
- Multiple CVs per user
- File size limit: 5MB
- Secure storage in `storage/app/cvs/`

**Management**:
- View all uploaded CVs
- Download CV
- Delete CV
- Set primary CV (for quick apply)
- Preview CV (PDF.js)

---

### 4. 🔔 Notification System 
#### Types

**In-App Notifications**:
- Real-time via WebSocket
- Notification dropdown
- Mark as read
- Delete notifications
- Notification count badge
---

### 5. 👤 User Management

#### Authentication

**Features**:
- Register (User/Company)
- Login with email/password
- Remember me
- Email verification
- Password reset (email link)
- Logout

**Middleware**:
- Role-based access control (Admin/Company/User)
- Guest/Auth guards
- Verified email check

#### Profile Management

**User Profile**:
- Personal information (name, email, phone)
- Avatar upload
- Bio/Summary
- Skills
- Experience
- Education
- Update password

**Company Profile**:
- Company information (name, description)
- Logo upload
- Website, industry, size
- Social media links
- Contact information

---

### 6. 📊 Admin Panel

#### Dashboard

- Total users, companies, jobs, applications
- Charts (users over time, jobs by category)
- Recent activities
- System health

#### User Management

- List all users (paginated, searchable)
- View user details
- Edit user info
- Delete/Ban user
- Roles & permissions

#### Company Management

- List all companies
- Approve/Reject company registration
- View company profile & jobs
- Delete company

#### Job Management

- List all jobs
- Filter by status, category
- View job details
- Approve/Reject jobs (if moderation enabled)
- Delete inappropriate jobs

#### Chatbot Analytics

- Conversation statistics
- Match rate tracking
- AI usage & cost
- Top FAQs
- Unmatched questions
- Rating distribution
- Export reports (CSV)

---

### 7. 🎨 UI/UX

**Trạng thái**: ✅ **Complete**

#### Design System

**Framework**: Tailwind CSS v3
**Components**: 
- SweetAlert2 (modals, alerts)
- Alpine.js (interactive components)
- Font Awesome icons
- Smooth animations

**Responsive**:
- Mobile-first design
- Tablet optimization
- Desktop layout
- Touch-friendly interactions

**Accessibility**:
- ARIA labels
- Keyboard navigation
- Screen reader support
- Color contrast

---

## 🗄️ DATABASE SCHEMA

### Core Tables

**users**
```sql
id, name, email, password, email_verified_at, role, avatar, 
phone, bio, skills, remember_token, created_at, updated_at
```

**companies**
```sql
id, user_id, name, description, logo, website, industry, 
size, location, social_links, created_at, updated_at
```

**job_listings**
```sql
id, company_id, title, description, category_id, location, 
job_type, salary_min, salary_max, experience_required, 
status, expires_at, created_at, updated_at
```

**applications**
```sql
id, job_listing_id, user_id, cv_path, cover_letter, 
status, notes, created_at, updated_at
```

**categories**
```sql
id, name, slug, description, icon, created_at, updated_at
```

### Chatbot Tables

**chatbot_faqs**
```sql
id, question, answer, keywords (JSON), match_pattern, 
category, priority, view_count, helpful_count, 
not_helpful_count, status, created_at, updated_at
```

**chatbot_conversations**
```sql
id, user_id, session_id, current_topic, context_data (JSON), 
context (JSON), current_intent, current_step, 
context_updated_at, message_count, started_at, 
last_message_at, rating, feedback, created_at, updated_at
```

**chatbot_messages**
```sql
id, conversation_id, sender_type, message, message_type, 
matched_faq_id, match_score, match_method, ai_used, 
is_helpful, created_at
```

### Notification Tables

**notifications** (Laravel default)
```sql
id, type, notifiable_type, notifiable_id, data (JSON), 
read_at, created_at, updated_at
```

**proactive_alerts**
```sql
id, user_id, keywords (JSON), location, salary_min, 
salary_max, alert_frequency, is_active, last_sent_at, 
created_at, updated_at
```

---

## 🔐 SECURITY

### Implemented

✅ **Authentication**
- Laravel Sanctum/Breeze
- Password hashing (bcrypt)
- Email verification
- Rate limiting

✅ **Authorization**
- Role-based access (Admin/Company/User)
- Middleware guards
- Policy classes

✅ **Input Validation**
- Form Request validation
- CSRF protection
- XSS prevention (HtmlSanitizer)
- SQL injection prevention (Eloquent ORM)

✅ **File Upload Security**
- File type validation
- File size limits
- Secure storage paths
- Unique filename generation

✅ **Environment Security**
- `.env` file protection
- API keys hidden
- Debug mode off in production

---

## 🚀 DEPLOYMENT

### Requirements

- PHP 8.2+
- MySQL 8.0+
- Composer
- Node.js & NPM
- Redis (recommended)

### Setup Steps

**1. Clone & Install**
```bash
git clone <repo>
cd jobv5
composer install
npm install
```

**2. Environment**
```bash
cp .env.example .env
php artisan key:generate
```

**3. Database**
```bash
php artisan migrate
php artisan db:seed
```

**4. Storage**
```bash
php artisan storage:link
```

**5. Build Assets**
```bash
npm run build
```

**6. WebSocket** (Optional)
```bash
php artisan reverb:start
```

**7. Scheduler** (Cron)
```bash
* * * * * cd /path-to-project && php artisan schedule:run >> /dev/null 2>&1
```

**8. Serve**
```bash
php artisan serve
```
