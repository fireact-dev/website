+++
title = "SaaS Package Guide"
description = "Build a complete SaaS platform with subscription management and billing"
keywords = ["saas", "subscription", "billing", "stripe", "firebase", "react"]
+++

# SaaS Package Guide

The `@fireact.dev/saas` package extends the core functionality to build a complete SaaS platform with subscription management, billing, and team features.

## Prerequisites

Before setting up the SaaS package:
* Complete the [Core Package Setup](/docs-core/)
* Have a Firebase project on the Blaze (pay-as-you-go) plan
* Have a Stripe account for payment processing

## Setup Process

### 1. Package Installation

```bash
npm install @fireact.dev/saas @stripe/stripe-js
```

### 2. SaaS Configuration

Create `src/saasConfig.json`:
```json
{
    "stripe": {
        "public_api_key": "your-stripe-public-key"
    },
    "plans": [
        {
            "id": "free",
            "titleKey": "plans.free.title",
            "popular": false,
            "priceIds": ["your-stripe-price-id"],
            "currency": "$",
            "price": 0,
            "frequency": "month",
            "descriptionKeys": [
                "plans.free.feature1",
                "plans.free.feature2",
                "plans.free.feature3"
            ],
            "free": true,
            "legacy": false
        }
    ],
    "permissions": {
        "access": {
            "label": "Access",
            "default": true,
            "admin": false
        },
        "editor": {
            "label": "Editor",
            "default": false,
            "admin": false
        },
        "admin": {
            "label": "Administrator",
            "default": false,
            "admin": true
        }
    },
    "settings": {
        "name": {
            "type": "string",
            "required": true,
            "label": "subscription.name",
            "placeholder": "subscription.namePlaceholder"
        }
    },
    "pages": {
        "billing": "/subscription/:id/billing",
        "createPlan": "/create-plan",
        "subscription": "/subscription/:id",
        "users": "/subscription/:id/users",
        "invite": "/subscription/:id/users/invite",
        "settings": "/subscription/:id/settings",
        "changePlan": "/subscription/:id/billing/change-plan",
        "cancelSubscription": "/subscription/:id/billing/cancel",
        "managePaymentMethods": "/subscription/:id/billing/payment-methods",
        "updateBillingDetails": "/subscription/:id/billing/update-details",
        "transferOwnership": "/subscription/:id/billing/transfer-ownership"
    }
}
```

### 3. Update Firestore Rules

Update your Firestore security rules:

```plaintext
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    // Allow authenticated users to read and write their own user document
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }

    // Subscription document rules
    match /subscriptions/{docId} {
      // Allow listing subscriptions for authenticated users
      allow list: if request.auth != null;
      
      // Allow get only to users in permissions.access array
      allow get: if request.auth != null 
        && get(/databases/$(database)/documents/subscriptions/$(docId)).data.permissions.access.hasAny([request.auth.uid]);
      
      // Only allow updates to settings fields if user's UID is in permissions.admin array
      allow update: if request.auth != null 
        && get(/databases/$(database)/documents/subscriptions/$(docId)).data.permissions.admin.hasAny([request.auth.uid])
        && request.resource.data.diff(resource.data).affectedKeys().hasOnly(['settings']);
      
      // Block all other write operations
      allow create, delete: if false;

      // Invoices subcollection rules
      match /invoices/{invoiceId} {
        // Allow reading invoices to subscription admins
        allow read: if request.auth != null 
          && get(/databases/$(database)/documents/subscriptions/$(docId)).data.permissions.admin.hasAny([request.auth.uid]);
        
        // Block direct writes - only allow through cloud functions
        allow write: if false;
      }
    }

    // Invites collection rules
    match /invites/{inviteId} {
      // Allow reading invites to subscription admins and invited users
      allow read: if request.auth != null && (
        get(/databases/$(database)/documents/subscriptions/$(resource.data.subscription_id)).data.permissions.admin.hasAny([request.auth.uid])
        || (request.auth.token.email != null && request.auth.token.email.lower() == resource.data.email)
      );
      
      // Block direct writes - only allow through cloud functions
      allow write: if false;
    }
  }
}
```

### 4. Cloud Functions Setup

1. Install the cloud functions package in your `functions` directory:
```bash
cd functions
npm install @fireact.dev/saas-cloud-functions firebase-admin firebase-functions stripe
```

2. Create `src/saasConfig.json` in your functions directory:
```json
{
    "stripe": {
        "secret_api_key": "your_stripe_secret_key",
        "end_point_secret": "your_stripe_webhook_endpoint_secret"
    },
    "emulators": {
        "enabled": false,
        "useTestKeys": false
    },
    "plans": [
        {
            "id": "free",
            "titleKey": "plans.free.title",
            "popular": false,
            "priceIds": [
                "your_stripe_price_id"
            ],
            "currency": "$",
            "price": 0,
            "frequency": "month",
            "descriptionKeys": [
                "plans.free.feature1",
                "plans.free.feature2",
                "plans.free.feature3"
            ],
            "free": true,
            "legacy": false
        }
    ],
    "permissions": {
        "access": {
            "label": "Access",
            "default": true,
            "admin": false
        },
        "admin": {
            "label": "Administrator",
            "default": false,
            "admin": true
        }
    }
}
```

3. Update your functions' `src/index.ts`:
```typescript
import { initializeApp } from 'firebase-admin/app';

// Initialize Firebase Admin
initializeApp();

import type { Plan, Permission } from '@fireact.dev/saas-cloud-functions';
import configFile from './saasConfig.json';

declare global {
    var saasConfig: {
        stripe: {
            secret_api_key: string;
            end_point_secret: string;
        };
        emulators: {
            enabled: boolean;
            useTestKeys: boolean;
        };
        plans: Plan[];
        permissions: Record<string, Permission>;
    };
}

// Initialize global config
global.saasConfig = configFile;

// Import cloud functions
import {
  createSubscription,
  createInvite,
  getSubscriptionUsers,
  acceptInvite,
  rejectInvite,
  revokeInvite,
  removeUser,
  updateUserPermissions,
  stripeWebhook,
  changeSubscriptionPlan,
  cancelSubscription,
  getPaymentMethods,
  createSetupIntent,
  setDefaultPaymentMethod,
  deletePaymentMethod,
  updateBillingDetails,
  getBillingDetails,
  transferSubscriptionOwnership
} from '@fireact.dev/saas-cloud-functions';

// Export cloud functions
export {
  createSubscription,
  createInvite,
  getSubscriptionUsers,
  acceptInvite,
  rejectInvite,
  revokeInvite,
  removeUser,
  updateUserPermissions,
  stripeWebhook,
  changeSubscriptionPlan,
  cancelSubscription,
  getPaymentMethods,
  createSetupIntent,
  setDefaultPaymentMethod,
  deletePaymentMethod,
  updateBillingDetails,
  getBillingDetails,
  transferSubscriptionOwnership
}
```

### 5. Stripe Setup

1. Get your Stripe API keys from the [Stripe Dashboard](https://dashboard.stripe.com/apikeys)

2. Set up webhook endpoint:
   * Go to Stripe Dashboard > Developers > Webhooks
   * Add endpoint: `https://your-region-your-project.cloudfunctions.net/stripeWebhook`
   * Select events:
     * `customer.subscription.created`
     * `customer.subscription.updated`
     * `customer.subscription.deleted`
     * `invoice.created`
     * `invoice.paid`
     * `invoice.overdue`
     * `invoice.payment_failed`
     * `invoice.updated`
   * Copy the signing secret for your `saasConfig.json`

## Available Components

### Subscription Components
* `Billing` - Subscription billing management
* `BillingForm` - Billing information form
* `CancelSubscription` - Subscription cancellation interface
* `ChangePlan` - Plan change interface
* `CreatePlan` - Plan creation interface
* `ManagePaymentMethods` - Payment methods management
* `Plans` - Plan listing and selection
* `SubscriptionDashboard` - Subscription overview
* `SubscriptionSettings` - Subscription settings management
* `TransferSubscriptionOwnership` - Ownership transfer interface
* `UpdateBillingDetails` - Billing details update form

### User Management Components
* `EditPermissionsModal` - User permissions editor
* `InviteUser` - User invitation interface
* `UserList` - User listing and management
* `UserTable` - User data display

### Navigation Components
* `MainDesktopMenu` - Main desktop navigation
* `MainMobileMenu` - Main mobile navigation
* `SubscriptionDesktopMenu` - Subscription desktop navigation
* `SubscriptionMobileMenu` - Subscription mobile navigation

### Layout Components
* `SubscriptionLayout` - Layout for subscription pages

### Context Providers
* `SubscriptionProvider` - Subscription data context
* `useSubscription` - Subscription context hook

## Cloud Functions

### Subscription Management
* `createSubscription` - Create a new subscription
* `changeSubscriptionPlan` - Change subscription plan
* `cancelSubscription` - Cancel subscription
* `transferSubscriptionOwnership` - Transfer subscription ownership

### User Management
* `createInvite` - Create user invitation
* `acceptInvite` - Accept invitation
* `rejectInvite` - Reject invitation
* `revokeInvite` - Revoke invitation
* `removeUser` - Remove user from subscription
* `updateUserPermissions` - Update user permissions
* `getSubscriptionUsers` - Get subscription users

### Payment Management
* `getPaymentMethods` - Get payment methods
* `createSetupIntent` - Create Stripe setup intent
* `setDefaultPaymentMethod` - Set default payment method
* `deletePaymentMethod` - Delete payment method
* `updateBillingDetails` - Update billing details
* `getBillingDetails` - Get billing details

### Webhooks
* `stripeWebhook` - Handle Stripe webhook events

## Development and Testing

### Using Firebase Emulators

1. Update functions' `saasConfig.json`:
```json
{
    "emulators": {
        "enabled": true,
        "useTestKeys": true
    }
}
```

2. Start the emulators:
```bash
firebase emulators:start
```

## Deployment

1. Build your functions:
```bash
cd functions
npm run build
```

2. Deploy to Firebase:
```bash
firebase deploy --only functions
```

Your SaaS platform will be available at your Firebase Hosting URL with full subscription management capabilities.
