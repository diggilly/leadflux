# 🚀 LeadFlux - Multi-Tenant SaaS Lead Recovery Platform

> **Automate lead qualification, website verification, and WhatsApp outreach at scale**

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Core Features](#-core-features)
- [Architecture](#-architecture)
- [Technology Stack](#-technology-stack)
- [Phase 1: Foundation ✅](#-phase-1-foundation-)
- [Phase 2: Multi-Tenant SaaS ✅](#-phase-2-multi-tenant-saas-)
- [Phase 3: WhatsApp Integration 🔄](#-phase-3-whatsapp-integration-)
- [Getting Started](#-getting-started)
- [Security & Compliance](#-security--compliance)
- [Roadmap](#-roadmap)

---

## 🎯 Overview

**LeadFlux** is a production-ready, multi-tenant SaaS platform designed to help businesses:

1. **Import leads** from Excel/CSV files with intelligent column mapping
2. **Verify websites** automatically to qualify leads based on online presence
3. **Send targeted WhatsApp messages** via the onhandi.com gateway
4. **Track campaigns** with real-time analytics and isolated user workspaces

Built for marketing agencies, sales teams, and entrepreneurs who need to:
- ✅ Qualify leads at scale without manual research
- ✅ Personalize outreach based on website status
- ✅ Maintain data isolation between clients/teams
- ✅ Scale messaging while avoiding WhatsApp bans

---

## ✨ Core Features

### 🔍 Lead Import & Verification
- **Smart Excel/CSV Import**: Auto-detect columns (Name, Phone, Website)
- **Website Verification Engine**: 
  - Tests HTTP/HTTPS endpoints
  - Handles Facebook URLs, redirects, and edge cases
  - Returns detailed status: `WORKING`, `NOT_WORKING`, `NO_WEBSITE`, `ERROR`
- **Live Progress Tracking**: Modal with progress bar during bulk verification
- **Verification Results Dashboard**: Filterable table with targeting recommendations

### 🏢 Multi-Tenant SaaS Architecture
- **Complete Data Isolation**: Every query scoped to `user_id`
- **Per-User Workspaces**: Custom names, slugs, timezones, branding
- **Subscription System**: Free/Starter/Pro/Enterprise plans with feature limits
- **Tenant Settings**: Per-user WhatsApp credentials, daily limits, working hours
- **Admin Tools**: Database management, migration runner, SQL executor (with safety confirmations)

### 👤 User Experience
- **Modern Responsive UI**: Bootstrap 5, custom CSS, mobile-optimized
- **Real-Time Notifications**: Toast system for actions, alerts, and system updates
- **Profile Management**: Update info, change password, manage preferences
- **Onboarding Flow**: 3-step workspace setup with timezone and use-case selection

### 🔐 Security & Reliability
- **Authentication**: Email/password login with OTP phone verification
- **CSRF Protection**: Token validation on all state-changing requests
- **Role-Based Access**: Admin vs. regular user permissions
- **Error Handling**: Graceful failures with user-friendly messages
- **Logging**: Comprehensive activity logs for audit trails

---

## 🏗️ Architecture

```
/home3/nyumban9/app.onhandi.com/
│
├── app/
│   ├── Controllers/
│   │   ├── AuthController.php (handles login, register, OTP via middleware)
│   │   ├── DashboardController.php
│   │   ├── WhatsAppController.php
│   │   ├── LeadController.php
│   │   ├── CampaignController.php
│   │   ├── ContactController.php
│   │   ├── HistoryController.php
│   │   ├── SettingsController.php
│   │   ├── ProfileController.php
│   │   ├── DatabaseController.php
│   │   ├── CronController.php
│   │   ├── NotificationController.php
│   │   ├── DatabaseToolsController.php
│   │   ├── OnboardingController.php
│   │   ├── PromoteController.php
│   │   └── TroubleshootController.php
│   │
│   ├── Middleware/
│   │   ├── Auth.php (authentication middleware)
│   │   └── Tenant.php (multi-tenant middleware)
│   │
│   ├── Models/
│   │   ├── Lead.php
│   │   ├── Campaign.php
│   │   ├── Contact.php
│   │   └── Log.php
│   │
│   ├── Services/
│   │   ├── WhatsAppGateway.php
│   │   ├── LeadProcessor.php
│   │   ├── MessageTemplates.php
│   │   ├── Campaign.php
│   │   ├── ExportService.php
│   │   └── WebsiteVerifier.php
│   │
│   ├── Views/
│   │   ├── layouts/
│   │   │   ├── main.php (main dashboard layout)
│   │   │   └── auth.php (login/register/OTP layout)
│   │   │
│   │   ├── dashboard/
│   │   │   └── content.php (main dashboard content with WhatsApp)
│   │   │
│   │   ├── auth/
│   │   │   ├── login.php
│   │   │   ├── register.php (if exists, or handled by middleware)
│   │   │   └── verify-otp.php
│   │   │
│   │   ├── onboarding/
│   │   │   └── index.php
│   │   │
│   │   ├── marketing/
│   │   │   ├── verify.php (import & verify leads)
│   │   │   ├── campaigns.php
│   │   │   └── contacts.php
│   │   │
│   │   ├── settings/
│   │   │   └── index.php
│   │   │
│   │   ├── profile/
│   │   │   └── index.php
│   │   │
│   │   ├── history/
│   │   │   └── index.php
│   │   │
│   │   ├── admin/
│   │   │   └── database-tools.php
│   │   │
│   │   └── whatsapp/
│   │       └── sessions.php (if separate page exists)
│   │
│   └── Core/
│       ├── Controller.php (base controller with tenant methods)
│       ├── Router.php
│       ├── Database.php
│       └── Logger.php
│
├── public/
│   ├── index.php (main entry point with .env loader)
│   ├── .htaccess
│   └── assets/ (CSS, JS, images)
│
├── storage/
│   ├── logs/
│   │   └── app.log
│   ├── cache/
│   │   └── whatsapp/
│   └── exports/
│
├── .env (environment variables)
├── vendor/
└── composer.json
```

### Data Flow
```
User Uploads Excel
        ↓
LeadController::processMapped()
        ↓
WebsiteVerifier::checkDetailed() → HTTP/HTTPS requests
        ↓
Results saved to `leads` table (scoped to user_id)
        ↓
Dashboard displays user's isolated data
        ↓
[Phase 3] WhatsAppGateway sends messages via onhandi.com API
```
### qualified leads
```
📥 Qualified Leads (88 imported)
│
├─📱 STEP 1: WhatsApp Number Verification
│  ├─ POST to https://tools.onhandi.com/api/number-checker/check
│  ├─ Batch up to 100 numbers per API call
│  ├─ Cache results for 24h to avoid redundant checks
│  └─ ONLY proceed if response: {"isValid": true}
│
├─📨 STEP 2: Send Personalized Messages (Anti-Ban Protected)
│  ├─ IF website WORKING → 
│  │   "Hi {name}, noticed your site is live! We offer maintenance, SEO, social media management. Interested?"
│  ├─ IF website MISSING/NOT_WORKING → 
│  │   "Hi {name}, saw your business could use a professional website. We design affordable, mobile-friendly sites. Free consultation?"
│  ├─ Anti-ban logic:
│  │  • Random delay: sleep(rand(5,15)) between each message
│  │  • Batch cooldown: sleep(rand(120,300)) after every 15 messages
│  │  • Daily limits: 30/day (new number) / 70/day (warmed) tracked in DB
│  │  • Working hours: Only send 09:00-18:00 Africa/Nairobi
│  │  • Message rotation: Cycle 3-5 templates randomly
│  └─ POST to https://tools.onhandi.com/api/sessions/{sessionId}/send
│
├─📊 STEP 3: Log & Track
│  ├─ Log every action: WHATSAPP_CHECKED → MESSAGE_SENT/FAILED
│  ├─ Store messageId for delivery tracking
│  └─ Dashboard shows real-time progress via AJAX
│
└─🎛️ STEP 4: Dashboard Controls
   ├─ WhatsApp session status indicator (🟢 Connected / 🔴 Disconnected)
   ├─ "Send Test Message" button for manual triggers
   ├─ Live send progress with success/fail counters
   └─ Anti-ban status panel: "Today: X/30 sent • Next allowed: HH:MM"
```

### whatsapp integration 
```
✅ app/Messaging/WhatsAppGateway.php
   - Full onhandi.com API client (number checker + message sender)
   - Batch processing (100 numbers max per call)
   - 24h result caching to avoid redundant API calls
   - Error handling with retries

✅ app/Services/LeadProcessor.php  
   - Complete workflow: Verify → Qualify → Check WhatsApp → Send
   - Anti-ban logic: 
     • Random delay: sleep(rand(5,15)) between messages
     • Batch cooldown: sleep(rand(120,300)) every 15 messages
     • Daily limits: 30/day (new) / 70/day (warmed) tracked in DB
     • Working hours: Only send 09:00-18:00 Africa/Nairobi
   - Message rotation: Cycle 3-5 templates randomly via MessageRotator

✅ app/Controllers/WhatsAppController.php
   - AJAX endpoints: /api/whatsapp/status, /api/whatsapp/send-test
   - Real-time progress updates for dashboard
   - Session management for onhandi.com gateway

✅ Updated Dashboard UI
   - WhatsApp session status indicator (🟢 Connected / 🔴 Disconnected)
   - "Send Test Message" button for qualified leads
   - Live send progress with success/fail counters
   - Anti-ban status panel (today's count, next allowed send time)

✅ Message Templates (Your Business Logic)
   • IF website WORKING → 
     "Hi {name}, noticed your site is live! We offer maintenance, SEO, social media management. Interested?"
   • IF website MISSING/NOT_WORKING → 
     "Hi {name}, saw your business could use a professional website. We design affordable, mobile-friendly sites. Free consultation?"
```


---

## 💻 Technology Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Backend** | PHP 8.3+ | Server-side logic |
| **Framework** | Custom MVC | Lightweight, no bloat |
| **Database** | MySQL 8+/MariaDB | Data persistence |
| **Frontend** | Bootstrap 5 + Vanilla JS | Responsive UI |
| **HTTP Client** | cURL | External API calls |
| **Excel Parsing** | PhpSpreadsheet | Import .xlsx/.csv files |
| **Session Management** | Native PHP Sessions | Authentication state |
| **Hosting** | Shared Hosting (cPanel) | Deployment target |
| **WhatsApp API** | tools.onhandi.com | Message sending |

---

### 🚀 LeadFlux Public API - Complete File Structure & Code
Great! Now let me give you the exact file structure and complete code for your API system.
📁 Complete File Structure
```
app.onhandi.com/
├── app/
│   ├── Controllers/
│   │   ├── Api/
│   │   │   ├── ApiController.php          ← Base API controller
│   │   │   ├── LeadsController.php        ← Leads endpoints
│   │   │   ├── NumbersController.php      ← Number verification
│   │   │   ├── CampaignsController.php    ← Campaign management
│   │   │   ├── WhatsAppController.php     ← WhatsApp messaging
│   │   │   └── ApiKeysController.php      ← API key management
│   │   └── ... (existing controllers)
│   │
│   ├── Middleware/
│   │   ├── ApiAuth.php                    ← API authentication
│   │   └── ... (existing middleware)
│   │
│   ├── Services/
│   │   ├── ApiRateLimiter.php             ← Rate limiting service
│   │   └── ... (existing services)
│   │
│   └── Views/
│       └── api/
│           ├── documentation.php          ← API docs page
│           └── keys.php                   ← API key management UI
│
├── public/
│   └── api/
│       └── docs.php                       ← Public API documentation
│
└── storage/
    └── logs/
        └── api.log                        ← API usage logs
```

## ✅ Phase 1: Foundation (Completed)

### Delivered Features
- ✅ **Core MVC Framework**: Router, Controller, Database, Logger
- ✅ **Authentication System**: Login, registration, CSRF protection
- ✅ **Lead Import Pipeline**: Excel upload → column mapping → database insert
- ✅ **Website Verification Service**: 
  - Robust URL parsing with `parse_url()`
  - HTTP/HTTPS fallback testing
  - Detailed result objects (status, HTTP code, response time)
- ✅ **Dashboard UI**: Stats cards, verification results table, live logs
- ✅ **Progress Modal**: Real-time feedback during bulk operations
- ✅ **Error Handling**: User-friendly messages, logging, graceful failures

### Key Files Created
```
app/Core/Controller.php
app/Core/Router.php
app/Controllers/LeadController.php
app/Services/WebsiteVerifier.php
app/Views/dashboard.php
```

---

## ✅ Phase 2: Multi-Tenant SaaS (Completed)

### Delivered Features
- ✅ **Tenant Isolation Middleware**: `Tenant::requireTenant()` enforces data scoping
- ✅ **Database Schema Updates**: 
  - `user_id` columns on all data tables (`leads`, `campaigns`, `contacts`, etc.)
  - `tenant_settings` table for per-user config
  - `subscriptions` table for plan management
- ✅ **Workspace Onboarding**: 3-step setup flow with slug generation
- ✅ **Per-User Settings**: WhatsApp credentials, timezone, daily limits, working hours
- ✅ **Admin Database Tools**: 
  - Migration runner with one-click execution
  - Safe SQL executor with dangerous operation warnings
  - Real query results display (SELECT, DESCRIBE, SHOW)
- ✅ **Notification System**: Toast alerts for all user actions
- ✅ **Responsive Header**: Workspace selector, user menu, notification badge
- ✅ **Profile Management**: Update info, change password, view stats

### Key Files Created
```
app/Middleware/Tenant.php
app/Controllers/OnboardingController.php
app/Controllers/DatabaseToolsController.php
app/Services/NotificationService.php
app/Views/onboarding/index.php
app/Views/admin/database-tools.php
database/migrations.sql (tenant isolation)
```

### Security Enhancements
- 🔐 **Query Scoping**: All data queries include `WHERE user_id = :user_id`
- 🔐 **Session Validation**: `$_SESSION['user_id']` cast to int to prevent injection
- 🔐 **Admin-Only Routes**: Database tools protected by `Auth::requireAdmin()`
- 🔐 **SQL Safety**: Dangerous operations require explicit confirmation modal

---

## 🔄 Phase 3: WhatsApp Integration (Next)

### 🎯 Objective
Enable automated, compliant WhatsApp messaging to qualified leads using the **onhandi.com API**, with anti-ban protections and per-user configuration.

### 📦 Components to Implement

#### 1. `app/Messaging/WhatsAppGateway.php`
```php
class WhatsAppGateway
{
    // API Client for tools.onhandi.com
    - checkNumber(string $phone): array    // Verify WhatsApp availability
    - sendMessage(string $phone, string $message, array $options): array  // Send message
    - getDeliveryStatus(string $messageId): array  // Track delivery
    
    // Anti-Ban Logic
    - applyRandomDelay(int $min, int $max): void  // Sleep between messages
    - checkDailyLimit(int $userId): bool  // Enforce per-user limits
    - isWithinWorkingHours(string $timezone): bool  // Respect business hours
    
    // Caching
    - cacheNumberCheck(string $phone, array $result, int $ttl): void  // 24h cache
    - getCachedResult(string $phone): ?array  // Avoid redundant API calls
}
```

#### 2. `app/Services/LeadProcessor.php`
```php
class LeadProcessor
{
    // Complete workflow
    public function processQualifiedLeads(int $userId, int $batchSize = 10): array
    {
        // 1. Fetch qualified leads (scoped to user)
        // 2. Check WhatsApp availability via gateway
        // 3. Apply anti-ban logic (delays, limits, hours)
        // 4. Send personalized message based on website status:
        //    - WORKING → "SEO/Maintenance" offer
        //    - NOT_WORKING/NO_WEBSITE → "Web Design" offer
        // 5. Log results to activity_logs (user-scoped)
        // 6. Update lead status (SENT/FAILED)
    }
}
```

#### 3. `app/Controllers/WhatsAppController.php`
```php
class WhatsAppController extends Controller
{
    // AJAX endpoints for dashboard
    public function getStatus(): void      // Check gateway connection
    public function sendTest(): void       // Send test message to phone
    public function startCampaign(): void  // Begin bulk send with progress
    public function getProgress(): void    // Poll for real-time updates
}
```

#### 4. Updated Dashboard UI
- 🟢 **Connection Status Indicator**: WhatsApp gateway status (Connected/Disconnected)
- 📤 **Send Test Message**: Input phone + send hardcoded "LeadFlux is up and running"
- 📊 **Live Send Progress**: Progress bar + success/fail counters during campaigns
- ⚙️ **Anti-Ban Panel**: "Today: X/30 sent • Next: HH:MM" with limit controls
- 📋 **Message Templates**: Rotate 3-5 templates randomly to avoid spam detection

#### 5. Business Logic Implementation
```php
// Message selection based on website verification
if ($lead['website_status'] === 'WORKING') {
    $message = "Hi {name}, noticed your site is live! 🎉 We offer maintenance, SEO, and social media management. Interested in a free audit?";
} elseif (in_array($lead['website_status'], ['NOT_WORKING', 'NO_WEBSITE', 'ERROR'])) {
    $message = "Hi {name}, saw your business could use a professional website. 🚀 We design affordable, mobile-friendly sites. Free consultation?";
}
```

#### 6. Export Functionality
- 📥 `with_website.csv`: Leads with working websites (SEO/maintenance offers)
- 📥 `without_website.csv`: Leads without/broken websites (web design offers)
- 📊 `campaign_report.csv`: Send results with timestamps and status codes

### 🔐 Anti-Ban Protection Strategy
| Technique | Implementation | Purpose |
|-----------|---------------|---------|
| **Random Delays** | `sleep(rand(5, 15))` between messages | Mimic human behavior |
| **Batch Cooldowns** | `sleep(rand(120, 300))` after 15 messages | Avoid rate limiting |
| **Daily Limits** | Track sends in DB; enforce per-plan limits | Prevent account flags |
| **Working Hours** | Only send 09:00-18:00 Africa/Nairobi (configurable) | Respect recipient time |
| **Message Rotation** | Cycle 3-5 templates randomly | Avoid spam pattern detection |
| **Number Validation** | Pre-check via Number Checker API | Avoid sending to invalid numbers |

### 📊 Dashboard Enhancements
```
┌─────────────────────────────────────────┐
│ 🟢 WhatsApp: Connected  •  Today: 12/30 │
├─────────────────────────────────────────┤
│ [Send Test Message]  [Start Campaign]   │
│                                         │
│ Progress: [████████░░░░] 80%           │
│ ✅ Sent: 24  ❌ Failed: 2  ⏳ Pending: 4 │
│                                         │
│ Next Send: 14:32 (in 8 min)            │
│ Anti-Ban: Random delay • Batch cooldown│
└─────────────────────────────────────────┘
```

### 🔗 API Integration Details
**Endpoint**: `https://tools.onhandi.com/api/sessions/{sessionId}/send`  
**Headers**: 
```
X-API-Key: {user_configured_key}
Content-Type: application/json
```
**Request Body**:
```json
{
  "to": "254712345678",
  "message": "Your personalized message here",
  "skipValidation": false
}
```

**Response Handling**:
- ✅ `200-299`: Message queued/sent → log as `SENT`
- ❌ `401/403`: Auth error → alert user to check credentials
- ❌ `404`: Invalid endpoint → alert user to check base URL
- ❌ `429`: Rate limited → apply exponential backoff
- ❌ Other: Log error, mark as `FAILED`, continue batch

---

## 🚀 Getting Started

### Prerequisites
- PHP 8.3+ with extensions: `curl`, `pdo_mysql`, `mbstring`, `zip`, `xml`
- MySQL 8+ or MariaDB 10.5+
- Composer for dependency management
- cPanel or similar hosting with cron support

### Installation
```bash
# 1. Clone repository
git clone https://github.com/yourorg/leadflux.git
cd leadflux

# 2. Install dependencies
composer install

# 3. Configure environment
cp .env.example .env
# Edit .env with your database credentials and WhatsApp API details

# 4. Run migrations
php artisan migrate

# 5. Set permissions
chmod -R 755 storage bootstrap/cache

# 6. Setup cron (for scheduled tasks)
* * * * * /usr/local/bin/php /path/to/leadflux/artisan schedule:run >> /dev/null 2>&1

# 7. Access application
# Visit: https://yourdomain.com/public
```

### First-Time Setup
1. Register admin account (first user auto-promoted to admin)
2. Complete workspace onboarding (name, slug, timezone)
3. Configure WhatsApp credentials in Settings → WhatsApp Gateway
4. Import leads via Marketing → Verify
5. Start campaigns from Dashboard → WhatsApp Panel

---

## 🔐 Security & Compliance

### Data Protection
- 🔒 **Encryption**: Passwords hashed with `password_hash()` (bcrypt)
- 🔒 **Input Sanitization**: All user inputs escaped with `htmlspecialchars()`
- 🔒 **SQL Injection Prevention**: Prepared statements with bound parameters
- 🔒 **XSS Protection**: Output encoding on all dynamic content

### WhatsApp Compliance
- ✅ **Opt-In Requirement**: Only message leads who provided phone numbers
- ✅ **Unsubscribe Handling**: Include opt-out instructions in messages
- ✅ **Rate Limiting**: Respect WhatsApp's messaging policies via anti-ban logic
- ✅ **Data Retention**: Configurable log retention periods

### Multi-Tenant Security
- 🔐 **Query Scoping**: Middleware enforces `user_id` isolation on all data access
- 🔐 **Session Validation**: User ID cast to int to prevent session fixation
- 🔐 **Admin Separation**: Admin features protected by role checks
- 🔐 **Audit Logging**: All sensitive actions logged with user context

---

## 🗓️ Roadmap

### Phase 3: WhatsApp Integration (Current)
- [ ] WhatsAppGateway API client with error handling
- [ ] LeadProcessor workflow with anti-ban logic
- [ ] Real-time campaign progress dashboard
- [ ] Message template rotation system
- [ ] Export qualified leads by website status

### Phase 4: Billing & Monetization
- [ ] Stripe/PayPal integration for subscription payments
- [ ] Usage-based billing (messages sent, leads imported)
- [ ] Plan upgrade/downgrade flow
- [ ] Invoice generation and payment history

### Phase 5: Advanced Analytics
- [ ] Campaign performance dashboards
- [ ] Lead conversion tracking
- [ ] A/B testing for message templates
- [ ] Exportable reports (PDF/CSV)

### Phase 6: Team Collaboration
- [ ] Multi-user workspaces with roles (Admin, Member, Viewer)
- [ ] Shared lead pools and assignment
- [ ] Team activity feeds and notifications
- [ ] Audit trails for compliance

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m 'Add amazing feature'`
4. Push to branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

### Coding Standards
- Follow PSR-12 coding style
- Use strict types: `declare(strict_types=1);`
- Document public methods with PHPDoc
- Write tests for new features (future)

---

## 📄 License

LeadFlux is proprietary software. All rights reserved.

---

## 🆘 Support

- 📧 Email: support@leadflux.app
- 🐛 Issues: [GitHub Issues](https://github.com/yourorg/leadflux/issues)
- 📚 Docs: [Wiki](https://github.com/yourorg/leadflux/wiki)

---

> **LeadFlux** — Turn leads into conversations, automatically. 🚀

*Built with ❤️ by kinflux digital*
