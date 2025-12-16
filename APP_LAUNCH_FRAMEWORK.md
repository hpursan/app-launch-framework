# Ultimate App Launch Framework v2.0

This document serves as a master protocol for launching new applications. It is designed to be used with an AI assistant to go from a vague business idea to a fully scaffolded, production-ready infrastructure using a standardized, scalable technology stack.

**Standard Stack:**
- **Frontend:** React Native (Expo), Expo Router.
- **State Management:** TanStack Query (Server State) + Zustand/Context (Client State).
- **Design System:** Custom Design Tokens (Colors, Typography, Spacing).
- **Backend:** Node.js (Express) or Serverless Functions.
- **Database & Auth:** Supabase (PostgreSQL, Auth).
- **Observability:** Sentry (Error Tracking), PostHog (Analytics).
- **Testing:** Jest, React Native Testing Library.
- **Deployment & CI:** Expo Application Services (EAS), Vercel.

---

## Phase 1: Idea Refinement & Market Psychology
**Goal:** Transform a raw idea into a psychologically grounded product requirement.

**Input:** [YOUR BUSINESS IDEA HERE]

**Prompt for AI:**
> "Act as a Product Manager and Consumer Psychologist. I have a business idea: '[INSERT IDEA]'. 
> 1. Analyze this idea using behavioral psychology principles (e.g., Hook Model, Fogg Behavior Model). How do we make this habit-forming?
> 2. Identify the core 'Job to be Done' for the user.
> 3. List 3 critical MVP features that solve this core problem immediately.
> 4. Suggest a premium, high-contrast, and modern design aesthetic (color palettes, typography) that fits this psychological profile.
> 5. Create a user persona and walk through their 'Happy Path' in the app."

---

## Phase 2: Technical Architecture & Scaffolding
**Goal:** Specific prompts to generate the boilerplate code for the standard stack.

### Step 2.0: Design System Integration
**Prompt for AI:**
> "Before we build UI, let's define the Design System based on the aesthetic from Phase 1.
> 1. Create a `constants/Colors.js` with semantic names (e.g., `background.primary`, `text.muted`, `action.success`) instead of raw hex codes.
> 2. Create `constants/Typography.js` and `constants/Spacing.js` to enforce consistency.
> 3. All future components MUST import from these files, avoiding 'magic numbers' in stylesheets."

### Step 2.1: Frontend Core (Smart State Management)
**Prompt for AI:**
> "Initialize a new React Native project using Expo and Expo Router.
> 1. Install dependencies:  `@tanstack/react-query`, `@supabase/supabase-js`, `expo-linear-gradient`, `expo-router`, `expo-status-bar`.
> 2. Set up the `QueryClientProvider` in the root layout to manage server state caching.
> 3. Create a `app/_layout.js` that handles Auth state (redirecting to Login if not authenticated)."

### Step 2.2: Backend & Database (Supabase)
**Prompt for AI:**
> "We are using Supabase for Database and Auth.
> 1. Design a PostgreSQL schema for this app. Based on the features from Phase 1, what tables run policies (RLS) do we need?
> 2. Create a `services/supabase.js` file in the frontend to initialize the client.
> 3. Outline the specific SQL commands I need to run in the Supabase SQL editor to set up the tables and Row Level Security policies to ensure users can only see their own data."

### Step 2.3: Authentication Flow
**Prompt for AI:**
> "Implement a robust Authentication flow.
> 1. Create a session store (Context or Zustand) that persists the user session.
> 2. Build a modern, animated `LoginScreen` and `SignUpScreen` using the Design System tokens.
> 3. Handle errors gracefully (e.g., 'Invalid login', 'Network error') using a Toast or Alert component."

---

## Phase 3: Cross-Cutting Concerns (The "Standards")
**Goal:** Ensure quality, security, and observability are baked in, not added on.

### Step 3.1: Testing Infrastructure
**Prompt for AI:**
> "Set up the testing environment. 
> 1. Configure Jest and `@testing-library/react-native`.
> 2. Create a `jest.setup.js` file that mocks `AsyncStorage`, `Supabase`, `Expo Router`, and critical native modules.
> 3. Write a 'Golden Path' integration test that simulates a user logging in and performing the main action of the app."

### Step 3.2: Accessibility & Security
**Prompt for AI:**
> "Review the current codebase for standards.
> 1. **Accessibility:** Ensure all Touchable elements have `minHeight: 44`, `accessibilityLabel`, and high contrast.
> 2. **Security:** Verify that no API keys are hardcoded (use `.env`), and that all database queries use RLS.
> 3. **Error Handling:** Create a global Error Boundary component to catch crashes."

### Step 3.3: Observability & Analytics

#### Option A: Managed (Sentry + PostHog) - *Recommended for Speed*
**Prompt for AI:**
> "Let's make this production-ready using managed services.
> 1. Provide instructions to integrate Sentry (`@sentry/react-native`) for crash reporting and auto-instrumentation.
> 2. Provide instructions to integrate PostHog for event tracking.
> 3. Create a `services/analytics.js` wrapper so we can easily swap providers later."

#### Option B: Open Source Standards (OpenTelemetry + Jaeger/Prometheus) - *Recommended for Control*
**Prompt for AI:**
> "We want to use vendor-neutral standards.
> 1. Install `@opentelemetry/api` and the React Native compatible SDKs.
> 2. Create a `services/telemetry.js` that initializes a `WebTracerProvider`.
> 3. Configure it to export traces via OTLP (HTTP) to a generic OpenTelemetry Collector endpoint.
> 4. **Infrastructure:** Generate a `docker-compose.yml` that spins up:
>    - **OTel Collector** (Gateway to receive app data).
>    - **Jaeger** (For visualizing traces).
>    - **Prometheus** (For storing metrics).
> 5. **Instrumentation:** Show me how to manually wrap a critical function (e.g., 'Login') in a custom Span."

---

## Phase 4: DevOps & CI/CD
**Goal:** Automate the build and release process.

**Prompt for AI:**
> "Set up the release pipeline.
> 1. Generate an `eas.json` file configured for 'preview' (internal testing) and 'production' (app store) builds.
> 2. Ensure the build profile separates development environment variables from production ones.
> 3. Create a GitHub Actions workflow file that runs tests on every Pull Request."

---

## Phase 5: App Store Readiness
**Goal:** Prepare the non-code assets required for submission.

**Prompt for AI:**
> "Act as an App Store Optimization (ASO) expert and Legal assistant.
> 1. **Privacy:** Generate a text-based Privacy Policy and Terms of Service tailored to the features we built (e.g., 'we collect email for auth').
> 2. **Marketing:** Write compelling App Store Title (30 char), Subtitle (30 char), and Description based on the psychology from Phase 1.
> 3. **Config:** Generate the `app.json` 'infoPlist' and 'android.permissions' sections, ensuring every permission (Camera, Location) has a valid, user-friendly explanation string."

---

## Phase 6: Manual Configuration Checklist
**Goal:** Steps the AI cannot do for you.

- [ ] **Supabase Project:** Create project at [database.new](https://database.new).
- [ ] **Environment Variables:**
    - Create `.env`.
    - Add `EXPO_PUBLIC_SUPABASE_URL`, `EXPO_PUBLIC_SUPABASE_ANON_KEY`, `SENTRY_DSN`.
- [ ] **Auth Providers:**
    - Enable Email/Password provider in Supabase.
    - (Optional) Configure Google/Apple OAuth.
- [ ] **Database Setup:** Run the SQL scripts generated in Phase 2.2.
- [ ] **Assets:** Generate app icons/splash: `npx expo-image-generator`.
- [ ] **EAS Login:** Run `eas login` and `eas build:configure` to link the project.

---

## Usage Guide
1. **Copy** the Phase 1 prompt and fill in your idea.
2. **Refine** the MVP based on the AI's analysis.
3. **Execute** Phase 2-5 prompts sequentially.
4. **Complete** the Phase 6 checklist to launch.
