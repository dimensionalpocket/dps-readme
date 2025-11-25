[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

The foundational service ecosystem powering all Dimensional Pocket studio projects. DPS (Dimensional Pocket Site) provides the core infrastructure—authentication, logging, metrics, and email—that enables rapid development of secure, self-hosted web applications across our portfolio.

## 🏁 Current Progress

> Legend: ⚫ Not started — 🟡 In progress — 🟢 0.x — 🟣 1.0

- 🟢 [DpsConfig](https://github.com/dimensionalpocket/dps-config-rs) (Rust)
- 🟢 [DpsAuthSession](https://github.com/dimensionalpocket/dps-auth-session-rs) (Rust)
- 🟡 [DpsAuthApi](https://github.com/dimensionalpocket/dps-auth-api) (Rust)
- ⚫ DpsConfig (Bun)
- ⚫ DpsClient (Bun)
- 🟡 [DpsAuthWeb](https://github.com/dimensionalpocket/dps-auth-web) (Bun/Vue)
- ⚫ DpsClient (Rust)
- ⚫ DpsLogsApi (Rust)
- ⚫ DpsMetricsApi (Rust)
- ⚫ DpsMonitorWeb (Bun/Vue)
- ⚫ DpsMailerApi (Rust)
- ⚫ DpsMailerWeb (Bun/Vue)

## 🎯 Project Goals

- **Self-hosted**: Run your own services without depending on external providers
- **Open Source**: All components are open-source and freely available
- **Secure**: Opinionated security choices with first-party cookie architecture
- **Modular**: Use only the services you need
- **Technology Focused**: Built with Rust and JavaScript for reliability and modern development

## 🏗️ Architecture Overview

DPS uses an opinionated domain structure designed for security and modularity:

```
mysite.com                     # Your main website
console.mysite.com             # Your main website console

account.mysite.com             # (DpsAuthWeb) User authentication and management interface for all site users
monitor.mysite.com             # (DpsMonitorWeb) Monitoring dashboard for site admins
mailer.mysite.com              # (DpsMailerWeb) Email management interface for site admins

auth.mysite.com/api            # (DpsAuthApi) Authentication API
logs.mysite.com/api            # (DpsLogsApi) Logging API
metrics.mysite.com/api         # (DpsMetricsApi) Metrics API
mailer.mysite.com/api          # (DpsMailerApi) Email API

# APIs can be on any subdomain, just need customizable path (default /api) for cookie access
main.api.mysite.com/api        # Your main website API
other.mysite.com/api           # Your other website API
```

### Security Architecture

The domain structure enables secure cookie sharing using first-party cookies:

- **Cookies**: Set for global `.mysite.com` domain with customizable path (default /api)
- **Automatic sharing**: Available to all web apps under the global domain without extra code
- **Isolation**: Cookies are not sent to requests without the customizable path (default `/api`) (e.g., `assets.mysite.com/images/...`)
- **Flexible subdomains**: Any subdomain under `.mysite.com` can host APIs with the customizable path and receive cookies
- **Browser-native**: Leverages natural browser cookie behavior

### Explanation

This architecture ensures cookies remain fully first-party to avoid browser blocking. Setting cookies to a specific subdomain (like `.api.mysite.com`) would fail when frontend apps run on different subdomains (like `www.mysite.com`), as browsers reject cookies from APIs that don't match the frontend's subdomain. By using the global `.mysite.com` domain with path-based isolation, any frontend subdomain can set and receive cookies while the customizable path restriction ensures cookies are only sent to API endpoints, not to static assets or other non-API requests.

## 📋 Requirements

To use DPS, you need:

1. **A domain** (e.g., `mysite.com`): all apps must run under this domain for first-party cookie access
2. **API /path structure** (e.g., `myapi.mysite.com/api/graphql`, path customizable) if they require session cookie access
3. **Development stack**: any language for your apps; Rust or JavaScript if you want to use existing components

## 🚀 Services

### Authentication Services

#### [DpsAuthApi](https://github.com/dimensionalpocket/dps-auth-api)
- **Technology**: Rust + SQLite + GraphQL
- **Domain**: `auth.mysite.com/api` (customizable subdomain and path)
- **Features**:
  - User sign-up and log-in
  - Password recovery
  - Data redaction (GDPR compliance)
  - Session management and revocation
  - OAuth integration
  - One-Time Password (OTP) support

#### [DpsAuthWeb](https://github.com/dimensionalpocket/dps-auth-web)
- **Technology**: Bun + Vue.js
- **Domain**: `account.mysite.com` (customizable subdomain)
- **Features**:
  - User sign-up, login, and logout interfaces
  - User profile management
  - Admin user management
  - Password recovery flows
  - OAuth authentication flows
  - OTP configuration
  - (If Email services enabled) Manage user email addresses

### Monitoring Services

#### DpsLogsApi
- **Technology**: Rust + SQLite + GraphQL
- **Domain**: `logs.mysite.com/api` (customizable subdomain and path)
- **Features**:
  - Log ingestion and storage
  - Authentication via Auth layer
  - Searchable log data

#### DpsMetricsApi
- **Technology**: Rust + SQLite + GraphQL
- **Domain**: `metrics.mysite.com/api` (customizable subdomain and path)
- **Features**:
  - Metrics ingestion and storage
  - Data parsing and analysis
  - Authentication via Auth layer

#### DpsMonitorWeb
- **Technology**: Bun + Vue.js
- **Domain**: `monitor.mysite.com` (customizable subdomain)
- **Features**:
  - Log search and filtering
  - Metrics dashboards
  - Admin reporting tools
  - Connects to Logs and Metrics APIs

### Email Services

#### DpsMailerApi
- **Technology**: Rust + SQLite + GraphQL
- **Domain**: `mailer.mysite.com/api` (customizable subdomain and path)
- **Features**:
  - Email management
  - Email payload ingestion
  - Email message storage and queuing
  - Email dispatching
  - Authentication via Auth layer

#### DpsMailerWeb
- **Technology**: Bun + Vue.js
- **Domain**: `mailer.mysite.com` (customizable subdomain)
- **Features**:
  - Email delivery monitoring
  - Email management interface
  - Connects to Mailer API

## 🧩 Components

### DpsConfig
- **Languages**: [Rust](https://github.com/dimensionalpocket/dps-config-rs), Bun
- **Usage**: Frontend and Backend
- **Purpose**: Global configuration management for all services

### DpsAuthSession
- **Languages**: [Rust](https://github.com/dimensionalpocket/dps-auth-session-rs), Bun
- **Usage**: Backend only
- **Purpose**: Session token encoding/decoding with secret keys

### DpsClient (Rust, Bun)
- **Languages**: Rust, Bun
- **Usage**: Frontend and Backend
- **Purpose**: Unified client for connecting to all DPS APIs

## 🛠️ Technology Choices

### Languages
- **Rust**: Chosen for reliability, performance, and low resource usage in backend services
- **JavaScript**: Popular choice for frontend development and Node.js/Bun backends
- **Future**: Additional languages like Ruby may be supported

### Database
- **SQLite**: Lightweight, reliable, and easy to deploy
  - **Scalable**: Uses multiple database files, even within the same service (e.g., the Auth API has a separate SQLite database just for current sessions)
  - **Easy to backup**: All databases support automatic backup to any S3-compatible storage
  - **PII data encrypted**: While services don't use encrypted SQLite, specific column values are encrypted via application code (e.g., email addresses) to help with security and compliance

### API Layer
- **GraphQL**: 
  - Solidified pattern for API development
  - Multi-language support
  - Advanced features (batched queries, real-time subscriptions)
  - Superior to REST for complex operations

### Frontend Framework
- **Vue.js**: Modern, reactive framework for admin interfaces

## 📈 Development Status

> **Note**: This project is in early planning stages. Most services and components are not yet implemented.

This README serves as the "ultimate goal" and development guide. Components will be developed incrementally, with each service designed to work independently while integrating seamlessly with the ecosystem.

## 🚦 Getting Started

### For Developers
1. Choose your development stack (Rust or JavaScript)
2. Set up your domain structure
3. Deploy the services you need
4. Integrate DPS components into your application

### For Contributors
1. Check the individual service repositories for development setup
2. Follow the established patterns for new services
3. Ensure compatibility with the overall ecosystem architecture

## 🗺️ Roadmap

1. **Phase 1**: Essential components (DpsConfig)
2. **Phase 2**: Core authentication services and components (DpsAuthSession, DpsAuthApi, DpsClient, DpsAuthWeb)
3. **Phase 3**: Monitoring services (DpsLogsApi, DpsMetricsApi, DpsMonitorWeb)
4. **Phase 4**: Email services (DpsMailerApi, DpsMailerWeb)
5. **Phase 5**: Additional language support and ecosystem expansion

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
