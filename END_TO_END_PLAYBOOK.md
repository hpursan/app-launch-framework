# Zero-to-App Store Playbook: Meal Planner Mobile App
*A comprehensive guide covering architecture, infrastructure, development workflow, and App Store submission. Use this as a template for future mobile apps.*

## 1. Architecture Overview

### **Technology Stack**
*   **Frontend**: React Native (Expo SDK 50+), Expo Router.
*   **Backend**: Node.js, Express.
*   **Database**: Supabase (PostgreSQL).
*   **Auth**: Supabase Auth (Google & Apple Sign-In).
*   **Payments**: RevenueCat (In-App Purchases).
*   **Hosting**: Render (Web Service).
*   **Monitoring**: Sentry (Error Tracking), UptimeRobot (Keep-alive).

### **Repository Structure**
```
/
├── backend/           # Node.js API
│   ├── services/      # Business logic (RecipeService, etc.)
│   ├── routes/        # API endpoints
│   └── scripts/       # Ad-hoc admin scripts (recipe uploads)
├── frontend/          # Expo Application
│   ├── app/           # Expo Router screens
│   ├── components/    # Reusable UI components
│   └── eas.json       # Build configuration
└── docs/              # Roadmaps and guides
```

---

## 2. Infrastructure Setup (Step-by-Step)

### **A. Supabase (Database & Auth)**
1.  **Create Project**: Set up a new project in Supabase.
2.  **Auth Config**:
    *   Enable **Apple** provider (requires Service ID from Apple Developer Portal).
    *   Enable **Google** provider (requires OAuth Client ID from Google Cloud Console).
    *   *Tip*: Use `native` bundle ID for iOS client IDs.
3.  **Database**:
    *   Create tables (`recipes`, `users`, `plans`) editor or SQL editor.
    *   **RLS (Row Level Security)**: CRITICAL. Enable RLS or your app will be insecure. Create policies to allow public read access for recipes but restricted write access.

### **B. RevenueCat (Monetization)**
1.  **Project**: Create app in RevenueCat.
2.  **API Keys**: Generate Public API Keys for iOS.
3.  **Apple Connection**:
    *   Go to App Store Connect -> Users and Access -> Integrations -> Request API Key.
    *   Upload the **p8** key file to RevenueCat to allow it to validate receipts.
4.  **Offerings**: Create an "Offering" (e.g., `default_paywall`) and attach your App Store Product ID (e.g., `mealplanner_pro_monthly`).

### **C. Render (Backend Hosting)**
1.  **New Web Service**: Connect to GitHub repo, point to `backend` directory.
2.  **Build Command**: `npm install`.
3.  **Start Command**: `node server.js`.
4.  **Environment Variables**:
    *   `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY`.
    *   `RC_WEBHOOK_KEY` (if using webhooks).
5.  **Cold Start Prevention**:
    *   Render Free Tier spins down after inactivity.
    *   **Solution**: Use [UptimeRobot](https://uptimerobot.com) to ping `https://your-app.onrender.com/health` or `/api/status` every 5 minutes.

---

## 3. Local Development Workflow

### **Environment Variables**
*   **Frontend**: `.env` file in `frontend/`.
    *   `EXPO_PUBLIC_API_URL`: Use `http://localhost:3000` (simulator) or your LAN IP `http://192.168.x.x:3000` (real device).
*   **Backend**: `.env` file in `backend/`.

### **Running Locally**
1.  **Start Backend**:
    ```bash
    cd backend
    node server.js
    ```
2.  **Start Frontend (Simulator)**:
    ```bash
    cd frontend
    npx expo run:ios
    ```
3.  **Start Frontend (Expo Go)**:
    ```bash
    cd frontend
    npx expo start
    ```

### **Testing on Specific Simulators**
Great for screenshots.
```bash
# List available devices
xcrun simctl list devices available

# Run on specific iPad
npx expo run:ios --device "iPad Pro (13-inch) (M4)"
```

---

## 4. Production Build & Deployment

### **A. Backend Deployment**
*   **Automatic**: Push changes to `master` branch. Render auto-deploys.
*   **Manual**: Trigger deploy from Render dashboard.

### **B. Frontend Build (EAS)**
1.  **Configure**: Edit `frontend/eas.json`.
    *   Essential: Set `build.production.env.EXPO_PUBLIC_API_URL` to your **Live Render URL**.
2.  **Build**:
    ```bash
    cd frontend
    eas build -p ios --profile production
    ```
3.  **Submit**:
    *   After build completes, it asks to submit, or use:
    ```bash
    eas submit -p ios --latest
    ```

---

## 5. App Store Submission Guide (The "Final Boss")

### **Prerequisites**
*   **Build 20+**: Ensure `app.json` version and `buildNumber` are incremented.
*   **Icons**: 1024x1024 `.png` in assets.

### **Metadata Checklist**
1.  **Screenshots**:
    *   **iPhone**: 6.5" (iPhone 14 Pro Max) and 5.5" (iPhone 8 Plus).
    *   **iPad**: 12.9" (iPad Pro 2nd Gen or 6th Gen).
    *   **Tip**: If simulator screenshots are the wrong size (e.g. status bars), use `sips` to force resize:
        ```bash
        # Force resize to iPad 12.9" requirements
        sips -z 2732 2048 my_screenshot.png
        ```
2.  **Privacy Policy**: Host a simple HTML file on your backend (`public/privacy.html`).
3.  **Support URL**: Can be your website or a GitHub issue page.

### **App Privacy Questionnaire (Answers)**
*   **Data Collection**: Yes.
*   **Types**:
    *   **Contact Info** (Name, Email) -> Linked, App Functionality.
    *   **Identifiers** (User ID, Device ID) -> Linked, App Functionality.
    *   **Purchases** (RevenueCat data) -> Linked, App Functionality.
    *   **Usage Data** (Product Interaction) -> Linked, Analytics (Sentry).
    *   **Diagnostics** (Crash Data) -> Linked, Analytics (Sentry).
*   **Tracking**: No (unless selling data).

### **Content Rights & Age Rating**
*   **Content Rights**: No 3rd party content (unless licensed).
*   **Age Rating**:
    *   Medical/Treatment: **None** (It's food, not medicine).
    *   Health/Wellness Topics: **Yes** (Dietary advice).
    *   Result: **4+** (Suitable for all ages).

### **Export Compliance**
*   **Uses Encryption?**: Yes (HTTPS).
*   **Exempt?**: Yes.
*   **Non-Standard?**: No.

---

## 6. Useful Commands Cheat Sheet

| Action | Command |
| :--- | :--- |
| **Check Backend** | `curl -I https://your-backend.onrender.com` |
| **Resize Image** | `sips -z <Height> <Width> <ImageFile>` |
| **Find Files** | `find . -name "*pattern*"` |
| **List Simulators** | `xcrun simctl list devices` |
| **Expo Build** | `eas build -p ios --profile production` |
| **Expo Local Build** | `npx expo run:ios` |
| **Git Status** | `git status` |
| **Upload Script** | `node backend/scripts/upload_v2_recipes.js` |

## 7. Lessons Learned & "Gotchas"
1.  **iPad Screenshots**: The strict pixel requirements (2048x2732) often fail with standard simulator screenshots due to status bars. Always verify dimensions or resize.
2.  **Cold Starts**: Render free tier sleeps after 15 mins. This breaks the app experience ("Network Request Failed"). UptimeRobot is mandatory for free tier.
3.  **Local vs Prod API**: Don't hardcode `localhost` in your code! Use `EXPO_PUBLIC_API_URL` and set it differently in `.env` (local) vs `eas.json` (prod).
4.  **RevenueCat Credentials**: If purchase fails silently, verify the "Shared Secret" in RC dashboard matches App Store Connect.
5.  **Supabase RLS**: If recipe uploads fail, check RLS policies. The "service_role" key bypasses RLS, but the client "anon" key does not.
