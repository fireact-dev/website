+++
title = "Core Package Guide"
description = "Set up a user portal with authentication and user management"
keywords = ["core", "authentication", "user management", "firebase", "react"]
+++

# Core Package Guide

The `@fireact.dev/core` package provides essential components and utilities for building a user portal with authentication and user management features.

## Prerequisites

Before you begin, ensure you have:
* Node.js and npm installed
* Basic knowledge of React and TypeScript
* A Firebase project (or ready to create one)

## Setup Process

### 1. Firebase Setup

1. Create a Firebase project:
   * Go to the [Firebase Console](https://console.firebase.google.com/)
   * Click "Add project" and follow the setup wizard
   * Enable email/password authentication in the Authentication section

2. Set up Firestore:
   * Navigate to "Firestore Database"
   * Click "Create database"
   * Copy and implement the following security rules:
   ```plaintext
   rules_version = '2';

   service cloud.firestore {
     match /databases/{database}/documents {
       // Allow authenticated users to read and write their own user document
       match /users/{userId} {
         allow read, write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }
   ```

3. Configure Firebase Hosting:
   * Go to the Hosting section
   * Follow the setup instructions

### 2. Project Creation

1. Create a new Vite project with TypeScript:
```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
```

2. Install required dependencies:
```bash
npm install @fireact.dev/core firebase react-router-dom i18next react-i18next @headlessui/react @heroicons/react tailwindcss i18next-browser-languagedetector
```

### 3. Configuration Setup

1. Set up Tailwind CSS:
```bash
npx tailwindcss init
```

2. Update `tailwind.config.js`:
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
    "./node_modules/@fireact.dev/core/dist/**/*.{js,mjs}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

3. Create `postcss.config.js`:
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

4. Add Tailwind directives to `src/index.css`:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 4. Firebase Configuration

Create `src/config.json`:
```json
{
  "firebase": {
    "apiKey": "your-api-key",
    "authDomain": "your-auth-domain",
    "projectId": "your-project-id",
    "storageBucket": "your-storage-bucket",
    "messagingSenderId": "your-messaging-sender-id",
    "appId": "your-app-id"
  },
  "pages": {
    "home": "/",
    "dashboard": "/dashboard",
    "profile": "/profile",
    "editName": "/edit-name",
    "editEmail": "/edit-email",
    "changePassword": "/change-password",
    "deleteAccount": "/delete-account",
    "signIn": "/signin",
    "signUp": "/signup",
    "resetPassword": "/reset-password"
  },
  "socialLogin": {
    "google": false,
    "microsoft": false,
    "facebook": false,
    "apple": false,
    "github": false,
    "twitter": false,
    "yahoo": false
  },
  "emulators": {
    "enabled": false,
    "host": "localhost",
    "ports": {
      "auth": 9099,
      "firestore": 8080,
      "functions": 5555,
      "hosting": 5002
    }
  }
}
```

### 5. Internationalization Setup

1. Create the i18n directory structure:
```
src/
  i18n/
    locales/
      en.ts
      zh.ts
```

2. Download base language files from the [GitHub repository](https://github.com/fireact-dev/core/tree/main/src/i18n/locales)

### 6. Application Setup

Create `src/App.tsx`:
```tsx
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom';
import {
  AuthProvider,
  ConfigProvider,
  LoadingProvider,
  PublicLayout,
  AuthenticatedLayout,
  SignIn,
  SignUp,
  ResetPassword,
  Dashboard,
  Profile,
  EditName,
  EditEmail,
  ChangePassword,
  DeleteAccount,
  DesktopMenuItems,
  MobileMenuItems,
  Logo
} from '@fireact.dev/core';
import config from './config.json';
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import LanguageDetector from 'i18next-browser-languagedetector';
import en from './i18n/locales/en';
import zh from './i18n/locales/zh';

// Initialize i18next
i18n
  .use(LanguageDetector)
  .use(initReactI18next)
  .init({
    resources: {
      en: {
        translation: en
      },
      zh: {
        translation: zh
      }
    },
    fallbackLng: 'en',
    interpolation: {
      escapeValue: false
    }
  });

function App() {
  return (
    <Router>
      <ConfigProvider config={config}>
        <AuthProvider>
          <LoadingProvider>
            <Routes>
              <Route element={
                <AuthenticatedLayout 
                  desktopMenuItems={<DesktopMenuItems />}
                  mobileMenuItems={<MobileMenuItems />}
                  logo={<Logo className="w-10 h-10" />}
                />
              }>
                <Route path={config.pages.home} element={<Navigate to={config.pages.dashboard} />} />
                <Route path={config.pages.dashboard} element={<Dashboard />} />
                <Route path={config.pages.profile} element={<Profile />} />
                <Route path={config.pages.editName} element={<EditName />} />
                <Route path={config.pages.editEmail} element={<EditEmail />} />
                <Route path={config.pages.changePassword} element={<ChangePassword />} />
                <Route path={config.pages.deleteAccount} element={<DeleteAccount />} />
              </Route>
              <Route element={<PublicLayout logo={<Logo className="w-20 h-20" />} />}>
                <Route path={config.pages.signIn} element={<SignIn />} />
                <Route path={config.pages.signUp} element={<SignUp />} />
                <Route path={config.pages.resetPassword} element={<ResetPassword />} />
              </Route>
            </Routes>
          </LoadingProvider>
        </AuthProvider>
      </ConfigProvider>
    </Router>
  );
}

export default App;
```

## Available Components

### Authentication Components
* `SignIn` - Sign in form
* `SignUp` - Sign up form
* `ResetPassword` - Password reset form
* `PrivateRoute` - Protected route component

### User Management Components
* `Profile` - User profile component
* `EditName` - Name editing form
* `EditEmail` - Email editing form
* `ChangePassword` - Password change form
* `DeleteAccount` - Account deletion interface
* `Avatar` - User avatar display

### Navigation Components
* `DesktopMenuItems` - Desktop navigation menu
* `MobileMenuItems` - Mobile navigation menu
* `Logo` - Application logo
* `LanguageSwitcher` - Language selection component

### Layout Components
* `AuthenticatedLayout` - Layout for authenticated pages
* `PublicLayout` - Layout for public pages
* `Message` - Message display component
* `Dashboard` - User dashboard

### Context Providers
* `AuthContext` - Firebase authentication context
* `ConfigProvider` - Application configuration context
* `LoadingContext` - Loading state management

## Development and Testing

### Using Firebase Emulators

1. Configure emulators in `firebase.json`:
```json
{
  "emulators": {
    "auth": {
      "port": 9099,
      "host": "127.0.0.1"
    },
    "firestore": {
      "port": 8080,
      "host": "127.0.0.1"
    },
    "functions": {
      "port": 5555,
      "host": "127.0.0.1"
    },
    "hosting": {
      "port": 5002,
      "host": "127.0.0.1"
    },
    "ui": {
      "enabled": true,
      "port": 4000,
      "host": "127.0.0.1"
    }
  }
}
```

2. Enable emulators in your config:
```json
{
  "emulators": {
    "enabled": true,
    "host": "localhost",
    "ports": {
      "auth": 9099,
      "firestore": 8080,
      "functions": 5555,
      "hosting": 5002
    }
  }
}
```

3. Start the emulators:
```bash
firebase emulators:start
```

## Deployment

1. Install Firebase CLI:
```bash
npm install -g firebase-tools
```

2. Login to Firebase:
```bash
firebase login
```

3. Initialize Firebase:
```bash
firebase init
```

4. Build and deploy:
```bash
npm run build
firebase deploy
```

Your application will be available at:
* `https://your-project-id.web.app`
* `https://your-project-id.firebaseapp.com`
