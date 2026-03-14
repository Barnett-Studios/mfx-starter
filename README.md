# MFX Starter

<!-- [![CI Status](https://github.com/Barnett-Studios/mfx-starter/workflows/CI/badge.svg)](https://github.com/Barnett-Studios/mfx-starter/actions) -->
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)


> Module Federation eXperience: a no-nonsense module federation starter project to unlock your microfrontends architecture journey

## 🚀 Demo

[Live Demo](https://mfx-starter-demo)

## 📋 Project Overview

MFX Starter is a comprehensive, production-ready microfrontends starter project built with Module Federation. It demonstrates how to build scalable, maintainable microfrontend architectures using multiple frameworks (React, Angular, Svelte) within a single cohesive application.

This project provides a solid foundation for teams looking to implement microfrontends with proper error handling, shared state management, authentication, internationalization, and divergent tooling and testing strategies.

## ✨ Key Features

### 🏗️ Architecture
- **Module Federation**: Built with Rsbuild and Module Federation for seamless microfrontend orchestration
- **Multi-Framework Support**: React (Host), Angular (Provider), Svelte (Provider)
- **Monorepo Structure**: npm workspaces for efficient dependency management
- **TypeScript First**: Full TypeScript support across all applications and packages

### 🔧 Developer Experience
- **Hot Module Replacement**: Lightning-fast development with HMR across all MFEs
- **Shared Utilities**: Common utilities and components shared across microfrontends
- **Consistent Tooling**: Unified ESLint, Prettier, and TypeScript configurations
- **Testing Options**: Jest, Karma/Jasmine, Vitest, and Cypress for full testing coverage

### 🛡️ Production Ready
- **Error Boundaries**: Graceful degradation when remote modules fail
- **Authentication**: JWT-based authentication with secure token management
- **Internationalization**: i18next integration for multi-language support
- **State Management**: Zustand for cross-MFE state sharing
- **Security**: CSP-ready, secure token storage, and CORS considerations

### 🎨 Styling & UI
- **Tailwind CSS**: Utility-first CSS framework for React and Svelte
- **SCSS**: Modern CSS preprocessing for Angular
- **Responsive Design**: Mobile-first approach with consistent breakpoints

## 🏁 Getting Started

### Prerequisites

- Node.js >= 24.0.0
- npm >= 8.0.0

### Installation

1. Clone the repository:
```bash
git clone https://github.com/Barnett-Studios/mfx-starter.git
cd mfx-starter
```

2. Install dependencies and build shared packages:
```bash
npm install
```

3. Set up environment variables:
```bash
# Copy environment template files
cp apps/mf_host/.env.example apps/mf_host/.env
cp apps/mf_provider_angular/.env.example apps/mf_provider_angular/.env
cp apps/mf_provider_svelte/.env.example apps/mf_provider_svelte/.env
```

4. Start all applications:
```bash
npm run start:all
```

### Individual Application Commands

**Host Application (React):**
```bash
npm run dev -w mf_host
```

**Angular Provider:**
```bash
npm run dev -w mf_provider_angular
```

**Svelte Provider:**
```bash
npm run dev -w mf_provider_svelte
```

### (Optional) Local Build+Preview Development with Custom Hostnames

When running individual applications or using the `preview:host` scripts, you might encounter URLs like `host-react.mfx.com`, `provider-angular.mfx.com`, and `provider-svelte.mfx.com`. To ensure your browser can resolve these custom hostnames to your local development server, you need to add entries to your system's `hosts` file.

**Example `hosts` file entries:**
```bash
127.0.0.1 host-react.mfx.com
127.0.0.1 provider-angular.mfx.com
127.0.0.1 provider-svelte.mfx.com

- **Linux/macOS:** `/etc/hosts`
- **Windows:** `C:\Windows\System32\drivers\etc\hosts`
```

Additionally, be aware that when microfrontends load resources from different origins (e.g., the host loading a provider), **Cross-Origin Resource Sharing (CORS)** policies apply. Ensure your Rsbuild configurations for development servers are set up to allow the necessary CORS headers to prevent resource loading issues. **Therefore, the correct values for these hostnames must also be configured in the respective `.env` files for each project (e.g., `apps/mf_host/.env`), as commands like `build`use `cross-env env-cmd -f .env rsbuild build` to inject these environment variables, which Rsbuild then uses for CORS configuration.**

### Building for Production

```bash
# Build all applications
npm run build:all

# Build individual applications
npm run build -w mf_host
npm run build -w mf_provider_angular
npm run build -w mf_provider_svelte
```

### Deployment Considerations:

- **Unique URLs:** Each microfrontend's build output (`dist` folder) should be deployed to a unique, publicly accessible URL or path (e.g., `https://your-app.com/`, `https://your-app.com/angular-mfe/`, `https://your-app.com/svelte-mfe/`).

- **Host `remotes` Configuration:** The host application's Module Federation configuration (`module-federation.config.ts`) must specify the full URLs to the Module Federation manifest files of the provider microfrontends it consumes. The base URLs for the providers are configurable via .env files.

- **Shared Packages:** Libraries like `@mfx/shared-utils`, `react`, `i18next`, and `zustand` are configured as `shared` singletons in Module Federation. This means the host application bundles and provides a single instance of these libraries, which is then consumed by the microfrontends. Changes to these shared packages typically require rebuilding and redeploying the host application.

### Running Tests

```bash
# Run all tests
npm run test:all

# Run E2E tests
npm run e2e

# Open Cypress Test Runner
npm run cy:open
```

## 🏛️ Architecture

### High-Level Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        MF Host (React)                          │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  │
│  │   Navigation    │  │   Auth Module   │  │  Notifications  │  │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘  │
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  │
│  │ Angular Widget  │  │ Svelte Widget   │  │   Shared UI     │  │
│  │   (Remote)      │  │   (Remote)      │  │  Components     │  │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                                 │
                                 │
              ┌─────────────────────────────────────┐
              │         Shared Packages             │
              │                                     │
              │  ┌─────────────┐ ┌─────────────┐    │
              │  │   Utils     │ │     UI      │    │
              │  │ (Zustand,   │ │ Components  │    │
              │  │  Axios,     │ │  (React)    │    │
              │  │  i18next)   │ │             │    │
              │  └─────────────┘ └─────────────┘    │
              └─────────────────────────────────────┘
```

### Module Federation Configuration
Each microfrontend exposes specific modules that can be consumed by the host application:

**Host (mf_host):**
- Consumes: `AngularWidget`, `SvelteWidget`, routing modules
- Provides: Global navigation, authentication, notifications, **and shared singleton instances of core libraries (`react`, `i18next`, `zustand`) and `@mfx/shared-utils`.**

**Angular Provider (mf_provider_angular):**
- Exposes: `AngularWidget`, `AngularRoutes`
- Technology: Angular 20+ with SCSS
- **Consumes shared core libraries and `@mfx/shared-utils` from the host.**

**Svelte Provider (mf_provider_svelte):**
- Exposes: `SvelteWidget`, `SvelteRoutes`
- Technology: Svelte 5+ with Tailwind CSS
- **Consumes shared core libraries and `@mfx/shared-utils` from the host.**

### Data Flow & Communication
1. **Shared State**: Zustand stores manage global state (authentication, user preferences) **and are accessible as singletons across all microfrontends.**
2. **Event System**: Custom event dispatcher for **decoupled, fire-and-forget cross-MFE communication.*- Microfrontends dispatch actions (e.g., item added, counter changed) which the host or other MFEs can listen to.
3. **HTTP Client**: Shared Axios instance with authentication and error handling
4. **Internationalization**: Centralized i18next configuration **ensuring consistent language across the entire application.**

### Error Handling Strategy
- **Error Boundaries**: React Error Boundaries wrap each remote module
- **Graceful Degradation**: Fallback UI when remote modules fail to load
- **Global Error Handling**: HTTP interceptors catch and handle API errors
- **User Feedback**: Global notification system for error reporting **(triggered by `dispatchNotification` from any MFE).**

## 🛠️ Technologies Used

### Core Technologies
- **Build Tool**: Rsbuild with Module Federation plugin
- **Languages**: TypeScript, JavaScript
- **Package Manager**: npm with workspaces

### Frameworks & Libraries
- **React**: 19.1.0 (Host application)
- **Angular**: 20.0.6 (Provider application)
- **Svelte**: 5.35.4 (Provider application)
- **Zustand**: 5.0.6 (State management)
- **Axios**: 1.6.5 (HTTP client)
- **i18next**: 23.8.2 (Internationalization)
- **react-i18next**: 13.5.0 (React bindings for i18next)

### Development Tools
- **Testing**: Jest, Karma/Jasmine, Vitest, Cypress
- **Linting**: ESLint with TypeScript support
- **Formatting**: Prettier
- **Styling**: Tailwind CSS, SCSS

### DevOps & CI/CD
- **GitHub Actions**: Automated testing and deployment

## 🤝 Contributing

Contributions from the community are warmly welcomed! Please read our [Contributing Guidelines](CONTRIBUTING.md) before submitting pull requests.

### Development Workflow

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes and add tests
4. Run the test suite: `npm run test:all`
5. Run linting: `npm run lint:all`
6. Commit your changes: `git commit -m 'Add amazing feature'`
7. Push to the branch: `git push origin feature/amazing-feature`
8. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🗺️ Roadmap

### Phase 1: Core Foundation ✅
- [x] Basic Module Federation setup
- [x] Monorepo with npm workspaces
- [ ] Switch to [Nx](https://nx.dev/)  for a better monorepo experience
- [ ] Build & deploy GutHub Actions pipeline
- [x] React Host application
- [x] Angular Provider application
- [x] Svelte Provider application
- [ ] React Provider application
- [ ] Vue Provider application
- [ ] Web Components Provider application (Webpack 5)
- [x] Shared utilities and components
- [x] Basic Authentication system mocks
- [x] Error handling and graceful degradation
- [x] Cross-MFE communication via shared event dispatcher
- [x] Consistent i18n and Auth state across MFEs

### Phase 2: Enhanced Features 🚧
- [ ] Advanced routing with nested microfrontends
- [ ] Performance monitoring and analytics
- [ ] Advanced caching strategies
- [ ] Micro-app deployment strategies
- [ ] Integration with popular state management libraries

### Phase 3: Enterprise Features 📋
- [ ] Single Sign-On (SSO) integration
- [ ] Advanced security features
- [ ] Multi-tenant support
- [ ] Advanced monitoring and logging
- [ ] Performance optimization guides

### Phase 4: Developer Tools 🔧
- [ ] CLI tool for generating new microfrontends
- [ ] Visual development tools
- [ ] Advanced debugging utilities
- [ ] Documentation generator

## 🆘 Support

If you encounter any issues or have questions, check the [Issues](https://github.com/Barnett-Studios/mfx-starter/issues) page

## About

Built and maintained by [Barnett Studios](https://barnett-studios.com) — building products, teams, and systems that last. Part-time technical leadership for startups and scale-ups.
