# KinfluxAPI

> A powerful, scalable WhatsApp API platform built for developers and businesses.
> Connect WhatsApp instances, send messages, configure webhooks, and scale your 
> messaging workflows with a clean REST API.

---

## 🌐 Platform Overview

KinfluxAPI is a two-component system:

| Component | Purpose |
|-----------|---------|
| **API Dashboard** | User dashboard, auth, API key management, webhooks, billing |
| **WhatsApp Engine** | WhatsApp connections, QR generation, message sending/receiving |

The two components communicate internally via REST + Socket.IO.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      KinfluxAPI Platform                     │
│                                                             │
│  ┌─────────────────────────┐   ┌─────────────────────────┐ │
│  │   API Dashboard          │   │   WhatsApp Engine        │ │
│  │   (Custom PHP MVC)       │◄──►  (Node.js + Baileys)    │ │
│  │                          │   │                          │ │
│  │  • Landing Page          │   │  • WhatsApp Connections  │ │
│  │  • User Auth + OTP       │   │  • QR Code Generation    │ │
│  │  • Dashboard UI          │   │  • Pairing Code          │ │
│  │  • Instance Management   │   │  • Send Messages         │ │
│  │  • API Key Management    │   │  • Receive Messages      │ │
│  │  • Webhook Configuration │   │  • Socket.IO Realtime    │ │
│  │  • Message Logs          │   │  • Auto-reply Bots       │ │
│  │  • Contacts              │   │  • AI Integration        │ │
│  │  • REST API (v1)         │   │  • Plugin System         │ │
│  │  • SMS Gateway (soon)    │   │  • Blast/Campaigns       │ │
│  └──────────┬───────────────┘   └─────────────────────────┘ │
│             │                               │                │
│             └──────────────┬────────────────┘                │
│                            ▼                                 │
│                  ┌──────────────────┐                       │
│                  │    MySQL Database  │                       │
│                  │                   │                       │
│                  │  • users          │                       │
│                  │  • devices        │                       │
│                  │  • api_keys       │                       │
│                  │  • api_usage_logs │                       │
│                  │  • api_rate_limits│                       │
│                  │  • webhooks       │                       │
│                  │  • webhook_logs   │                       │
│                  │  • message_logs   │                       │
│                  │  • contacts       │                       │
│                  │  • contact_groups │                       │
│                  │  • plans          │                       │
│                  │  • orders         │                       │
│                  │  • plugins        │                       │
│                  └──────────────────┘                       │
└─────────────────────────────────────────────────────────────┘
```

---

## 📁 File Structure

### API Dashboard (Custom PHP MVC)
```
api.onhandi.com/
│
├── public/
│   ├── index.php                     # Entry point
│   ├── .htaccess                     # URL rewriting
│   └── assets/
│       ├── css/
│       │   ├── app.css               # Global styles
│       │   ├── auth.css              # Auth pages
│       │   └── dashboard.css         # Dashboard styles
│       ├── js/
│       │   ├── app.js                # Global JS
│       │   ├── socket.io.min.js      # Socket.IO client
│       │   ├── instances.js          # QR connect logic
│       │   ├── api-keys.js           # API key management
│       │   └── webhooks.js           # Webhook config
│       └── img/
│           ├── logo.svg
│           └── favicon.ico
│
├── app/
│   │
│   ├── Core/
│   │   ├── Controller.php            # Base controller
│   │   ├── Router.php                # Request router
│   │   ├── Database.php              # PDO manager
│   │   ├── Logger.php                # App logger
│   │   ├── CSRF.php                  # CSRF protection
│   │   ├── Env.php                   # Env variable helper
│   │   ├── Notification.php          # Flash notifications
│   │   └── PluginLoader.php          # Plugin system
│   │
│   ├── Middleware/
│   │   ├── Auth.php                  # Session auth
│   │   ├── ApiAuth.php               # API key auth
│   │   └── Tenant.php                # Multi-tenant support
│   │
│   ├── Models/
│   │   ├── User.php                  # Users
│   │   ├── Device.php                # WhatsApp instances
│   │   ├── ApiKey.php                # API keys
│   │   ├── Webhook.php               # Webhooks
│   │   ├── MessageLog.php            # Message history
│   │   ├── Contact.php               # Contacts
│   │   └── ContactGroup.php          # Contact groups
│   │
│   ├── Controllers/
│   │   ├── AuthController.php        # Login/Register/OTP/Logout
│   │   ├── DashboardController.php   # Main dashboard stats
│   │   ├── InstanceController.php    # WA instances + QR
│   │   ├── ApiKeyController.php      # API key CRUD
│   │   ├── WebhookController.php     # Webhook CRUD
│   │   ├── MessageController.php     # Send + message logs
│   │   ├── ContactController.php     # Contacts management
│   │   ├── SettingsController.php    # Account settings
│   │   ├── ProfileController.php     # User profile
│   │   └── Api/
│   │       └── V1/
│   │           ├── SendController.php       # Send messages API
│   │           ├── InstanceController.php   # Instances API
│   │           └── WebhookController.php    # Webhooks API
│   │
│   ├── Services/
│   │   ├── NodeService.php           # Communicates with Node.js engine
│   │   ├── WhatsappService.php       # WhatsApp operations
│   │   └── CreditService.php         # Credits/billing logic
│   │
│   └── Views/
│       ├── layouts/
│       │   ├── main.php              # Dashboard layout (sidebar+header)
│       │   └── auth.php              # Auth layout (centered)
│       │
│       ├── landing/
│       │   └── index.php             # Public landing page
│       │
│       ├── auth/
│       │   ├── login.php             # Login page
│       │   ├── register.php          # Register page
│       │   └── verify-otp.php        # OTP verification
│       │
│       ├── dashboard/
│       │   └── index.php             # Dashboard home
│       │
│       ├── instances/
│       │   └── index.php             # WA instances + QR scan
│       │
│       ├── messages/
│       │   └── index.php             # Message logs + send test
│       │
│       ├── api-keys/
│       │   └── index.php             # API key management
│       │
│       ├── webhooks/
│       │   └── index.php             # Webhook configuration
│       │
│       ├── contacts/
│       │   └── index.php             # Contacts management
│       │
│       └── settings/
│           └── index.php             # Account settings
│
├── storage/
│   ├── logs/
│   │   ├── app.log
│   │   └── php_errors.log
│   ├── cache/
│   │   └── rate_limit/
│   └── exports/
│
├── .env                              # Environment variables (not committed)
├── .env.example                      # Environment template
├── composer.json                     # PHP dependencies
└── vendor/                           # Composer packages (not committed)
```

### WhatsApp Engine (Node.js)
```
verify.onhandi.com/
│
├── server.js                         # Entry point
├── package.json                      # Dependencies
├── .env                              # Environment (not committed)
│
├── server/
│   ├── whatsapp.js                   # Core WA logic (Baileys)
│   ├── pluginLoader.js               # Plugin loader
│   ├── controllers/
│   │   ├── index.js                  # Controller exports
│   │   ├── blast.js                  # Bulk messaging
│   │   ├── incomingMessage.js        # Incoming handler
│   │   └── store.js                  # Chat store
│   ├── database/
│   │   ├── index.js                  # MySQL connection pool
│   │   └── model.js                  # Query models
│   ├── lib/
│   │   ├── cache.js                  # In-memory cache
│   │   ├── helper.js                 # Utility helpers
│   │   ├── middleware.js             # Auth middleware
│   │   ├── pino.js                   # Logger config
│   │   └── specs.js                  # CPU/RAM stats
│   └── router/
│       └── index.js                  # All Express routes
│
├── credentials/                      # WA session files (not committed)
│   └── {phone_number}/
│
└── plugins/                          # Plugin modules
    └── {plugin-slug}/
        └── nodejs/
            ├── server.js
            ├── route.js
            └── incomingMessage.js
```

---

## 🗺️ Routes

### Dashboard Web Routes
```
GET  /                        → Landing page
GET  /login                   → Login
POST /auth/authenticate        → Process login
GET  /register                → Register
POST /auth/create-account      → Process register
GET  /verify-otp              → OTP verification
POST /auth/verify-otp          → Process OTP
GET  /auth/logout              → Logout

GET  /dashboard               → Dashboard home
GET  /instances               → WhatsApp instances
GET  /api-keys                → API key management
GET  /webhooks                → Webhook configuration
GET  /messages                → Message logs
GET  /contacts                → Contacts
GET  /settings                → Settings
GET  /profile                 → Profile
```

### REST API (v1) - Requires API Key
```
# Instances
GET    /api/v1/instances              # List instances
POST   /api/v1/instances              # Create instance
GET    /api/v1/instances/{token}      # Get instance info
DELETE /api/v1/instances/{token}      # Delete instance

# Messaging
POST   /api/v1/send/text              # Send text message
POST   /api/v1/send/media             # Send image/video/doc/audio
POST   /api/v1/send/location          # Send location
POST   /api/v1/send/vcard             # Send contact card
POST   /api/v1/send/button            # Send button message
POST   /api/v1/send/list              # Send list message
POST   /api/v1/send/poll              # Send poll
POST   /api/v1/send/product           # Send product card
POST   /api/v1/send/sticker           # Send sticker

# Utilities
POST   /api/v1/check-number           # Check if number is on WA
POST   /api/v1/fetch-groups           # Get all groups

# Webhooks
GET    /api/v1/webhooks               # List webhooks
POST   /api/v1/webhooks               # Create webhook
POST   /api/v1/webhooks/{id}          # Update webhook
DELETE /api/v1/webhooks/{id}          # Delete webhook

# API Keys
GET    /api/v1/api-keys               # List your keys
POST   /api/v1/api-keys               # Create new key
DELETE /api/v1/api-keys/{id}          # Revoke key
```

---

## 🗄️ Database Tables

### Core Tables
```
users                 User accounts
devices               WhatsApp instances per user
```

### API Management
```
api_keys              API keys per user
api_usage_logs        Every API call logged
api_rate_limits       Per-minute rate limiting
```

### Messaging
```
message_histories     All sent/received messages
autoreplies           Auto-reply rules per device
chat_sessions         AI bot conversation sessions
chat_messages         AI conversation messages
```

### Campaigns
```
campaigns             Blast campaign definitions
blasts                Individual blast recipients
```

### Webhooks
```
webhooks              Webhook endpoint configs
webhook_logs          Delivery attempts + responses
```

### Contacts
```
contacts              User contacts
contact_groups        Contact grouping
tags                  Contact tags
```

### Billing (Phase 2)
```
plans                 Subscription plans
orders                Purchase orders
```

### System
```
plugins               Installed plugins
password_resets       Password reset tokens
migrations            Migration history
```

---

## 🚀 Roadmap

### Phase 1 - Core (Now)
- [x] WhatsApp Engine (Node.js + Baileys)
- [x] Database schema
- [x] QR + Pairing code connect
- [x] All message types (text, media, location, etc)
- [ ] PHP MVC app setup
- [ ] Landing page
- [ ] Auth (login/register/OTP)
- [ ] Dashboard
- [ ] Instance management + QR
- [ ] API Keys
- [ ] Webhooks
- [ ] REST API v1

### Phase 2 - Growth
- [ ] Message logs UI
- [ ] Contacts management
- [ ] Analytics dashboard
- [ ] Credits system
- [ ] Plans + Pricing
- [ ] Billing integration

### Phase 3 - Expansion
- [ ] SMS Gateway
- [ ] Bulk SMS campaigns
- [ ] Email gateway
- [ ] Admin panel
- [ ] Mobile app

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| WhatsApp Protocol | Baileys v7 |
| Realtime | Socket.IO v4 |
| Node Backend | Express v5 |
| PHP Framework | Custom MVC (PHP 8.3) |
| Database | MySQL 8 |
| Web Server | LiteSpeed (cPanel) |
| Auth | PHP Sessions + API Keys |
| AI Bots | ChatGPT / Gemini / Claude / Groq / Deepseek |
| Logger | Pino (Node) + Custom (PHP) |

---

## 📋 Requirements

### Server
```
PHP >= 8.2
Node.js >= 20
MySQL >= 8.0
Composer >= 2.0
cPanel with Node.js App selector
```

### PHP Extensions
```
ext-curl
ext-gd
ext-json
ext-mbstring
ext-pdo
ext-zip
```

### Node.js Packages
```
baileys
express
socket.io
mysql2
dotenv
axios
sharp
qrcode
```

---

## ⚙️ Environment Setup

Copy `.env.example` to `.env` and fill in your values:

```env
# Application
APP_NAME=KinfluxAPI
APP_ENV=production
APP_DEBUG=false
APP_URL=https://api.onhandi.com

# Database
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=your_username
DB_PASSWORD=your_password

# WhatsApp Engine
NODE_SERVER_URL=https://verify.onhandi.com
NODE_SERVER_PORT=3100
NODE_AUTH=your_node_auth_token

# Security
CSRF_SECRET=your_csrf_secret
SESSION_LIFETIME=120
LOG_LEVEL=error
```

---

## 🔒 Security

- All API endpoints require valid API key
- CSRF protection on all forms
- Rate limiting per API key
- Session-based auth for dashboard
- OTP verification on register
- No credentials stored in repository

---

## 📄 License

Private - KinfluxAPI © 2025

---

*Built with ❤️ by KinfluxAPI Team*
```
