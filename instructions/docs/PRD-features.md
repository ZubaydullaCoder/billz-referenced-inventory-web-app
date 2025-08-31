# Product Requirements Document — Billz Inventory & POS

Version: 0.1 (MVP)

Last updated: 2025-08-24

Purpose

- This single-file PRD captures the MVP modules, their features, functional requirements, and acceptance criteria. Use it as the authoritative implementation and QA checklist for the MVP.

Document structure / Table of contents

- **Module 0: Billing & Subscription Management**
  - Feature 0.1: Free Trial & Subscription Model
  - Feature 0.2: Paywall & Access Control
  - Feature 0.3: Payment Processing Integration
- Module 1: Initial System Setup & Configuration
  - Feature 1.1: Currency Configuration
  - Feature 1.2: Payment Type Management
  - Feature 1.3: Store & Company Profile
  - Feature 1.4: Telegram Bot for Notifications
- Module 2: Product & Inventory Management
  - Feature 2.1: Product Creation & Cataloging
  - Feature 2.2: Pricing & Stock Control
  - Feature 2.3: Bulk Operations (DEFERRED to Post-MVP)
- Module 3: Sales Operations (Point of Sale - POS)
  - Feature 3.1: The Sales Interface
  - Feature 3.2: Customer Management (CRM) in POS
  - Feature 3.3: Payments & Transactions
- Module 4: Financial Control & Analytics
  - Feature 4.1: Cash Flow Management (Shift Operations)
  - Feature 4.2: Reporting & Dashboards
- Module 5: Advanced Modules
  - Feature 5.1: Inventory Counts (Stocktaking)
  - Feature 5.2: Inter-Store Transfers
  - Feature 5.3: Marketing & Promotions

How to use this document

- Each feature below follows the pattern: User Stories, Functional Requirements (FR), and Acceptance Criteria (AC).
- Items explicitly marked "DEFERRED" are Post‑MVP and should not be implemented in the MVP sprint unless re-prioritized.
- For implementation, use each AC as a test case; add concrete API contracts and UI wireframes as separate artifacts linked from the backlog.

---

### **PRD Module 0: Billing & Subscription Management**

#### **Feature 0.1: Free Trial & Subscription Model**

**1. User Stories**

- **User Story 0.1.1:** As a new business owner, I want to try Billz free for 14 days with full access to all features, so I can evaluate if it meets my retail management needs before committing financially.
- **User Story 0.1.2:** As a business owner evaluating Billz, I want a single, comprehensive plan that gives me unlimited access to products, sales, customers, and staff accounts, so I don't have to worry about usage limits during my trial.
- **User Story 0.1.3:** As a business owner ready to subscribe, I want the option to pay monthly or annually (with a discount for annual payment), so I can choose the billing cycle that best fits my cash flow.
- **User Story 0.1.4:** As a subscriber, I want clear visibility into my subscription status, billing cycle, and renewal date, so I can manage my account effectively.

**2. Functional Requirements**

- **FR 0.1.1 - Single Plan Structure:**

  - The system must offer exactly one subscription plan called **"Billz Pro"**.
  - This plan provides **unlimited access** to:
    - Unlimited Products
    - Unlimited Sales Transactions
    - Unlimited Customers
    - Unlimited Staff Accounts / Cashiers
  - The plan must include access to all current and future MVP features without any feature gating or usage limitations.

- **FR 0.1.2 - Free Trial Implementation:**

  - Every new account must automatically receive a **14-day free trial** upon registration.
  - During the trial period, users must have **full, unlimited access** to all "Billz Pro" features.
  - The system must clearly display the trial status and remaining days in the user interface.
  - Trial users must be able to upgrade to a paid subscription at any time during the trial period.

- **FR 0.1.3 - Billing Cycles:**

  - The system must support two billing cycles:
    1. **Monthly:** Standard monthly payment
    2. **Annual:** Yearly payment with a **17% discount** (pay for 10 months, get 12)
  - Users must be able to switch between billing cycles when subscribing or during subscription renewal.
  - The system must automatically calculate and display pricing for both options.

- **FR 0.1.4 - Subscription Lifecycle:**
  - The system must track subscription states: `trialing`, `active`, `expired`, `cancelled`.
  - Upon trial expiration, the system must automatically transition the account to `expired` status.
  - Expired accounts must be restricted from accessing the application until payment is processed.
  - The system must send clear notifications about trial expiration and subscription status changes.

**3. Acceptance Criteria (AC)**

- **AC 0.1.1: New Account Receives Free Trial**

  - **Given** a new user completes the registration process.
  - **When** they log in for the first time.
  - **Then** their account status must be set to `trialing`.
  - **And** they must have full access to all application features.
  - **And** the UI must display "14 days remaining in trial".

- **AC 0.1.2: Unlimited Access During Trial**

  - **Given** a user is in their trial period.
  - **When** they create their 100th product or process their 500th sale.
  - **Then** the system must not impose any usage limits or restrictions.
  - **And** they must continue to have full access to all features.

- **AC 0.1.3: Billing Cycle Options Display**

  - **Given** a user is viewing the subscription/pricing page.
  - **When** the page loads.
  - **Then** they must see two clear options: "Monthly" and "Annual (Save 17%)".
  - **And** the annual price must be calculated as approximately 83% of the monthly price × 12.

- **AC 0.1.4: Trial Expiration Handling**
  - **Given** a user's 14-day trial has expired.
  - **When** they attempt to log in.
  - **Then** the system must redirect them to the subscription/payment page.
  - **And** display a clear message: "Your trial has expired. Subscribe to continue using Billz."

---

#### **Feature 0.2: Paywall & Access Control**

**1. User Stories**

- **User Story 0.2.1:** As a system administrator, I want the application to automatically restrict access for expired subscriptions, so unpaid accounts cannot continue using the service.
- **User Story 0.2.2:** As a user with an expired subscription, I want a clear path to restore my access by completing payment, so I can quickly get back to managing my business.
- **User Story 0.2.3:** As a business owner, I want to be able to cancel my subscription at any time, so I have full control over my billing.
- **User Story 0.2.4:** As a subscriber, I want the system to remind me before my subscription expires, so I can avoid service interruption.

**2. Functional Requirements**

- **FR 0.2.1 - Access Control Middleware:**

  - The system must implement middleware that checks subscription status on every request.
  - For `expired` accounts, the middleware must redirect users to the subscription/payment page.
  - The middleware must allow access to billing-related pages (pricing, payment) even for expired accounts.
  - The system must provide clear error messages when access is restricted.

- **FR 0.2.2 - Subscription Restoration:**

  - Expired accounts must be able to access the subscription page to complete payment.
  - Upon successful payment, the system must immediately restore full access.
  - The system must handle payment failures gracefully and provide clear error messages.
  - Users must be able to resume their work exactly where they left off after payment.

- **FR 0.2.3 - Subscription Management:**

  - Users must be able to view their current subscription details (plan, billing cycle, renewal date).
  - Users must be able to change their billing cycle (monthly ↔ annual).
  - Users must be able to cancel their subscription with immediate effect or at the end of the billing period.
  - Cancelled subscriptions must retain access until the end of the paid period.

- **FR 0.2.4 - Notification System:**
  - The system must send reminders 7 days before trial expiration.
  - The system must send reminders 3 days before subscription expiration.
  - The system must send confirmation emails for successful payments and subscription changes.
  - All notifications must be clear, actionable, and include relevant subscription details.

**3. Acceptance Criteria (AC)**

- **AC 0.2.1: Access Restriction for Expired Accounts**

  - **Given** a user's subscription has expired.
  - **When** they attempt to access any application feature.
  - **Then** the system must redirect them to the subscription page.
  - **And** display the message: "Your subscription has expired. Please renew to continue."

- **AC 0.2.2: Payment Restoration Flow**

  - **Given** an expired subscription.
  - **When** the user completes payment successfully.
  - **Then** their subscription status must change to `active`.
  - **And** they must be redirected back to their previous location in the application.
  - **And** have immediate full access to all features.

- **AC 0.2.3: Subscription Cancellation**

  - **Given** an active subscription.
  - **When** the user cancels their subscription.
  - **Then** they must retain access until the end of their paid period.
  - **And** receive a confirmation email with cancellation details.
  - **And** be able to reactivate their subscription before the period ends.

- **AC 0.2.4: Expiration Reminders**
  - **Given** a user's trial expires in 7 days.
  - **When** the daily reminder job runs.
  - **Then** the user must receive an email notification.
  - **And** the notification must include clear instructions on how to subscribe.

---

#### **Feature 0.3: Payment Processing Integration**

**1. User Stories**

- **User Story 0.3.1:** As a user ready to subscribe, I want to pay using Payme (the popular Uzbek payment system), so I can complete my subscription quickly and securely.
- **User Story 0.3.2:** As a subscriber, I want the payment process to be seamless and provide immediate confirmation, so I don't experience any service interruption.
- **User Story 0.3.3:** As a business owner, I want to receive clear invoices and payment receipts, so I can track my business expenses properly.
- **User Story 0.3.4:** As a user, I want to be able to update my payment method if needed, so I can ensure continuous service.

**2. Functional Requirements**

- **FR 0.3.1 - Payme Integration:**

  - The system must integrate with Payme payment service for subscription payments.
  - The system must support both one-time subscription payments and recurring billing.
  - The system must handle Payme webhooks to confirm successful payments.
  - The system must provide clear error handling for failed payments.

- **FR 0.3.2 - Payment Flow:**

  - Users must be able to initiate subscription payments from the application.
  - The system must generate Payme payment links or QR codes for subscription amounts.
  - The system must handle payment confirmation and immediately activate subscriptions.
  - The system must provide clear feedback during the payment process.

- **FR 0.3.3 - Invoicing & Receipts:**

  - The system must generate invoices for all subscription payments.
  - Users must receive email confirmations with payment details.
  - The system must maintain a payment history for each account.
  - Users must be able to download invoices and receipts.

- **FR 0.3.4 - Payment Method Management:**
  - Users must be able to view their payment history.
  - The system must support payment method updates through Payme.
  - Users must receive notifications for upcoming renewals.
  - The system must handle payment failures and provide retry mechanisms.

**3. Acceptance Criteria (AC)**

- **AC 0.3.1: Successful Subscription Payment**

  - **Given** a user initiates a subscription payment.
  - **When** they complete payment via Payme.
  - **Then** the system must receive the webhook confirmation.
  - **And** immediately activate their subscription.
  - **And** send a confirmation email with invoice details.

- **AC 0.3.2: Payment Failure Handling**

  - **Given** a user attempts a subscription payment.
  - **When** the payment fails.
  - **Then** the system must display a clear error message.
  - **And** provide options to retry the payment.
  - **And** not restrict access during the retry process.

- **AC 0.3.3: Invoice Generation**

  - **Given** a successful subscription payment.
  - **When** the payment is confirmed.
  - **Then** the system must generate a downloadable invoice.
  - **And** email it to the user.
  - **And** include all relevant subscription and payment details.

- **AC 0.3.4: Payment History Access**
  - **Given** a user has made multiple subscription payments.
  - **When** they view their account settings.
  - **Then** they must see a complete history of payments.
  - **And** be able to download past invoices.

---

Excellent. Now that we've established the billing foundation, let's proceed with the core application features.

---

### **PRD Module 1: Initial System Setup & Configuration**

#### **Feature 1.1: Currency Configuration**

**1. User Stories**

- **User Story 1.1.1:** As a Store Owner, I want to set a primary "Revenue Currency" for my entire system, so that all my financial reports, inventory valuation, and profit calculations are standardized in a single currency.
- **User Story 1.1.2:** As a Store Owner, I want to set a "Sales Currency" which can be different from my Revenue Currency, so that I can conduct sales and print receipts in a currency that is convenient for my local customers (e.g., Uzbekistani Som - UZS).
- **User Story 1.1.3:** As a Store Owner, if my Sales Currency is different from my Revenue Currency, I want to be required to set a manual exchange rate, so that all sales are accurately converted back to my Revenue Currency for correct financial reporting.

**2. Functional Requirements**

- **FR 1.1.1 - Primary Revenue Currency Selection:** The system must provide a settings page where the user can select one primary Revenue Currency from a predefined list of world currencies (using ISO 4217 codes, e.g., UZS, USD, EUR).

  - This currency will be the default for all financial analytics, profit/loss statements, and inventory cost reporting.
  - For new accounts targeting the Uzbek market, the default Revenue Currency shall be preset to **UZS (Uzbekistani Som)**.

- **FR 1.1.2 - Sales Currency Selection:** The system must allow the user to select a Sales Currency from the same predefined list.

  - This currency will be used for all transactions at the Point of Sale (POS), displayed to the cashier, and printed on customer receipts.
  - The default Sales Currency shall also be preset to **UZS**.

- **FR 1.1.3 - Exchange Rate Mechanism:**

  - The system must continuously check if the selected Revenue Currency and Sales Currency are the same.
  - If the currencies are different, the UI must dynamically display a mandatory numerical input field for the user to define the exchange rate (e.g., `1 USD = [input field] UZS`).
  - The system must store this exchange rate and use it to automatically convert the total of every sales transaction into the primary Revenue Currency for storage in the database.
  - If the currencies are the same, the exchange rate input field must be hidden.

- **FR 1.1.4 - Configuration Persistence:** Once set and saved, the currency settings must be applied globally across the application. The system must provide clear visual feedback (e.g., a success message) upon saving the configuration.

---

Excellent. Let's detail the Acceptance Criteria.

These criteria will act as a precise, testable checklist for the AI implementation agent, ensuring the feature is built exactly as specified.

---

### **PRD Module 1: Initial System Setup & Configuration**

#### **Feature 1.1: Currency Configuration**

**3. Acceptance Criteria (AC)**

- **AC 1.1.1: Setting the Primary Revenue Currency**

  - **Given** I am a logged-in Store Owner on the "Currency Settings" page.
  - **When** I select "USD" from the "Revenue Currency" dropdown list and click the "Save" button.
  - **Then** the system should display a success message confirming the settings have been saved.
  - **And** when I navigate to the main financial dashboard, all monetary values should be displayed with the "USD" symbol or code.

- **AC 1.1.2: Setting the Sales Currency**

  - **Given** I am a logged-in Store Owner on the "Currency Settings" page.
  - **When** I select "UZS" from the "Sales Currency" dropdown list and click "Save".
  - **Then** the system should confirm the settings have been saved.
  - **And** when a cashier starts a new transaction in the Point of Sale (POS) interface, all product prices and the final total must be displayed in UZS.

- **AC 1.1.3: Handling Identical Currencies**

  - **Given** I am on the "Currency Settings" page and the "Revenue Currency" is set to "UZS".
  - **When** I select "UZS" as the "Sales Currency".
  - **Then** the input field for setting an exchange rate must remain hidden.
  - **And** I should be able to save the configuration successfully.

- **AC 1.1.4: Handling Different Currencies and Mandatory Exchange Rate**

  - **Given** I am on the "Currency Settings" page and the "Revenue Currency" is set to "USD".
  - **When** I select "UZS" as the "Sales Currency".
  - **Then** a numerical input field for the exchange rate must appear on the screen.
  - **And** the system must prevent me from saving the configuration if this exchange rate field is left empty, showing a validation error message like "Exchange rate is required."

- **AC 1.1.5: Correct Application of Exchange Rate**

  - **Given** the "Revenue Currency" is set to "USD" and the "Sales Currency" is "UZS".
  - **And** the exchange rate is set to `12500`.
  - **When** a cashier completes a sale for a total of `250,000 UZS`.
  - **Then** the system must record the transaction value as `250,000 UZS` for the customer receipt.
  - **And** the system must calculate and store the revenue for that transaction as `20 USD` in the database for financial reporting.

- **AC 1.1.6: Default Settings for New Accounts**
  - **Given** a new user from Uzbekistan creates an account and logs in for the first time.
  - **When** they navigate to the "Currency Settings" page.
  - **Then** the "Revenue Currency" field should be pre-selected as "UZS - Uzbekistani Som".
  - **And** the "Sales Currency" field should also be pre-selected as "UZS - Uzbekistani Som".

---

Understood. We will define the functionality for managing various payment types, but for the MVP, we will specify that the only _external integration_ to be built is with **Payme**. This is a great way to manage scope while building a solid foundation.

Let's proceed with the PRD for **Feature 1.2: Payment Type Management**.

---

### **PRD Module 1: Initial System Setup & Configuration**

#### **Feature 1.2: Payment Type Management**

**1. User Stories**

- **User Story 1.2.1:** As a Store Owner, I want to manage a list of accepted payment methods, so that I can accurately track how my customers are paying for their purchases (e.g., Cash, Card, Bank Transfer).
- **User Story 1.2.2:** As a Store Owner, I want to add custom, local payment methods that are popular in Uzbekistan, so that my cashiers can select them during checkout.
- **User Story 1.2.3 (MVP Specific):** As a Store Owner, I want to integrate the **Payme** payment service into my POS, so that I can offer a fast and popular QR-code-based payment option to my customers.
- **User Story 1.2.4:** As a Cashier, I want to see a clear list of all available payment methods during checkout, so that I can quickly select the one the customer is using.

**2. Functional Requirements**

- **FR 1.2.1 - Default Payment Types:** The system must come pre-configured with two default, non-deletable payment types:

  - `Cash`
  - `Card`

- **FR 1.2.2 - Custom Payment Type Creation:**

  - The system must provide a settings page for "Payment Types."
  - On this page, the user must have the ability to "Add New Payment Type."
  - Adding a new type requires the user to provide a **Name** (e.g., "Bank Transfer," "Click," "Apelsin").
  - These custom types will function as labels for tracking and reporting purposes within the system.

- **FR 1.2.3 - Payment Type Management:**

  - The user must be able to **Edit** the name of any custom payment type they have created.
  - The user must be able to **Delete** any custom payment type. The default "Cash" and "Card" types cannot be deleted.

- **FR 1.2.4 - Payme Integration (MVP Scope):**
  - The system must include a dedicated section for integrating with the Payme service.
  - This section will require the user to input their Payme merchant credentials (API Key/Merchant ID - _we can specify the exact required credentials later_).
  - Once configured, "Payme" will appear as a distinct payment option in the POS interface.
  - When a cashier selects "Payme" during checkout, the system must:
    1.  Make an API call to the Payme service with the transaction amount.
    2.  Receive and display a unique QR code on the cashier's screen.
    3.  Continuously poll the Payme API for payment confirmation.
    4.  Upon successful payment by the customer, the system must automatically mark the transaction as complete and proceed to the receipt screen.
    5.  The system must handle payment timeouts and failures gracefully, allowing the cashier to cancel the Payme attempt and select a different payment method.

**3. Acceptance Criteria (AC)**

- **AC 1.2.1: Default State and Adding a Custom Type**

  - **Given** I am a new Store Owner navigating to the "Payment Types" settings page for the first time.
  - **When** the page loads.
  - **Then** I should see "Cash" and "Card" listed as default payment types.
  - **And** when I click "Add New," enter the name "Click," and save, the "Click" payment type should appear in the list.

- **AC 1.2.2: Using a Custom Payment Type in POS**

  - **Given** I have created a custom payment type named "Bank Transfer."
  - **When** a cashier is at the payment stage of a sale in the POS.
  - **Then** "Bank Transfer" must be visible as a selectable payment option alongside "Cash" and "Card."
  - **And** if selected, the transaction report must correctly attribute the sale amount to "Bank Transfer."

- **AC 1.2.3: Payme Integration Setup**

  - **Given** I am on the "Payment Types" settings page.
  - **When** I enter my valid Payme merchant credentials into the integration form and click "Save & Activate."
  - **Then** the system should display a success message, "Payme integration successful."
  - **And** "Payme" should now be an available payment option in the POS.

- **AC 1.2.4: Successful Payme Transaction Flow**

  - **Given** the Payme integration is active and a cashier is processing a sale for `150,000 UZS`.
  - **When** the cashier selects "Payme" as the payment method.
  - **Then** the system must display a unique QR code on the screen.
  - **And** when the customer scans the code and successfully pays via their Payme app.
  - **Then** the POS screen should automatically update to a "Payment Successful" status and allow the cashier to print the receipt.

- **AC 1.2.5: Failed Payme Transaction Flow**
  - **Given** a cashier has generated a Payme QR code for a sale.
  - **When** the customer does not complete the payment within the timeout period (e.g., 2 minutes).
  - **Then** the POS screen should display a "Payment Failed" or "Timed Out" message.
  - **And** the system must provide a button for the cashier to go back and select a different payment method (e.g., "Cash" or "Card").

---

Got it. Let's proceed with defining **Feature 1.3: Store & Company Profile**.

This section is crucial for personalizing the application and ensuring that all transactional data is correctly contextualized with the right location and time.

---

### **PRD Module 1: Initial System Setup & Configuration**

#### **Feature 1.3: Store & Company Profile**

**1. User Stories**

- **User Story 1.3.1:** As a Store Owner, I want to input and save my company's name and contact information, so that this information can be automatically printed on customer receipts and used for system identification.
- **User Story 1.3.2:** As a business owner, I want the system to support multiple store locations, so that as my business grows, I can manage inventory, sales, and staff for each store from a single account.
- **User Story 1.3.3:** As a Store Owner, I must set the correct local time zone for my business, so that all sales transactions, reports, and employee shifts are timestamped accurately, preventing confusion and ensuring correct daily reporting.

**2. Functional Requirements**

- **FR 1.3.1 - Company Information:**

  - The system must provide a "Company Profile" or "Business Details" settings page.
  - This page must contain input fields for:
    - Company Name (Required)
    - Address
    - Phone Number
    - Email Address
  - This information must be saved and dynamically pulled into other modules where it's needed (e.g., the header of a sales receipt).

- **FR 1.3.2 - Multi-Store Architecture & MVP Scoping:**

  - The application's database and backend architecture **must be designed to support multiple stores** from the beginning. Each store should be a distinct entity capable of having its own inventory, sales records, and staff assignments.
  - **MVP Implementation:**
    - Upon account creation, the system will automatically generate a single, default store named "Main Store".
    - The user must be able to edit the name and address of this default store.
    - The UI for adding a _new_ store (e.g., an "Add New Store" button) will be present but can be disabled or marked as "Coming Soon" for the MVP to manage scope. The core focus is on making the first store fully functional.
    - The application interface (e.g., in the main navigation header) should always display the name of the currently active store, establishing the UI pattern for future multi-store switching.

- **FR 1.3.3 - Time Zone Configuration:**
  - The system must provide a dropdown menu in the settings for the user to select their operational time zone.
  - The list should contain standard IANA time zone names (e.g., "Asia/Tashkent").
  - For new accounts, the system shall default the time zone to **"Asia/Tashkent"**.
  - All timestamps recorded in the database (e.g., `created_at` fields for sales, inventory changes) must be stored in UTC but converted to and displayed in the user's selected time zone throughout the application interface.

**3. Acceptance Criteria (AC)**

- **AC 1.3.1: Saving Company Information**

  - **Given** I am on the "Company Profile" page.
  - **When** I enter "My Awesome Shop" as the Company Name and click "Save".
  - **Then** the system should display a success message.
  - **And** when I print a sales receipt, "My Awesome Shop" must be visible in the receipt's header.

- **AC 1.3.2: Managing the First Store (MVP)**

  - **Given** I am a new user who has just created an account.
  - **When** I navigate to the "Stores" management page.
  - **Then** I should see one store listed with the default name "Main Store".
  - **And** when I edit this store's name to "Samarkand Branch" and save, the name in the application's header should update to "Samarkand Branch".

- **AC 1.3.3: Setting the Time Zone Correctly**

  - **Given** my selected time zone in the settings is "Asia/Tashkent".
  - **When** I complete a sale at exactly 4:00 PM local Tashkent time.
  - **Then** the transaction record in the sales history UI must display the time of the sale as "16:00" (or "4:00 PM").

- **AC 1.3.4: Time Zone Default**
  - **Given** a new user signs up for the service.
  - **When** they view the time zone setting for the first time.
  - **Then** the value in the time zone dropdown should be pre-selected as "Asia/Tashkent".

---

Excellent. Let's move on to the Telegram Bot integration. This is a powerful feature for keeping business owners connected to their store's performance without needing to be logged into the main application.

---

### **PRD Module 1: Initial System Setup & Configuration**

#### **Feature 1.4: Telegram Bot for Notifications**

**1. User Stories**

- **User Story 1.4.1:** As a Store Owner, I want to connect my store's account to a Telegram bot, so that I can receive key business summaries and alerts directly on my phone.
- **User Story 1.4.2:** As a Store Owner, I want to set a specific time of day to receive my daily sales report, so that it arrives at my convenience, for example, after the store has closed.
- **User Story 1.4.3:** As a Store Owner, I want to be able to easily enable or disable these notifications, so I have full control over when I receive them.

**2. Functional Requirements**

- **FR 1.4.1 - Bot Configuration Interface:**

  - The system must provide a dedicated settings page for "Telegram Notifications."
  - This page must contain input fields for the user to enter their:
    1.  **Telegram Bot API Token**
    2.  **Telegram Chat ID**
  - The interface should include a brief, clear instruction guide or link on how to obtain these credentials from Telegram's BotFather.

- **FR 1.4.2 - Connection and Status:**

  - A "Test Connection & Save" button must be present. When clicked, the system will attempt to send a test message (e.g., "Connection successful!") to the provided Chat ID using the API Token.
  - If the test is successful, the credentials will be saved, and the UI will display a status indicator like "Connected" or "Active."
  - If the test fails, the system must display a user-friendly error message (e.g., "Connection failed. Please check your Token and Chat ID.").

- **FR 1.4.3 - Notification Control:**

  - The settings page must include a master toggle switch to "Enable/Disable All Notifications." This switch must be "Off" by default for new accounts.

- **FR 1.4.4 - Daily Sales Report:**

  - The user must be able to enable/disable the daily sales report specifically.
  - There must be a time picker input that allows the user to select the hour and minute they wish to receive the report (e.g., 21:30). This time is based on the store's configured time zone.
  - A scheduled background job will run daily. At the user-specified time, it will compile and send a summary of the _previous day's_ sales.
  - The report message must contain at a minimum:
    - Total Revenue
    - Number of Sales (Transactions)
    - Average Sale Value

- **FR 1.4.5 - Weekly & Monthly Reports (Post-MVP Consideration):**
  - The UI should include disabled toggles for "Weekly Summary" and "Monthly Summary" marked as "Coming Soon." The backend logic for these does not need to be built for the MVP, but the UI prepares the user for future enhancements.

**3. Acceptance Criteria (AC)**

- **AC 1.4.1: Successful Bot Setup**

  - **Given** I am on the "Telegram Notifications" settings page.
  - **When** I enter a valid API Token and Chat ID, set the daily report time to 22:00, enable notifications, and click "Test Connection & Save."
  - **Then** I must immediately receive a test message on my Telegram account.
  - **And** the UI on the settings page should show the status as "Active."

- **AC 1.4.2: Receiving the Daily Report on Schedule**

  - **Given** my Telegram bot is successfully connected and notifications are enabled for a 22:00 daily report.
  - **And** my store's total sales for yesterday were 1,500,000 UZS from 15 transactions.
  - **When** the current time in my store's time zone ("Asia/Tashkent") reaches 22:00.
  - **Then** I must receive a Telegram message that includes "Total Revenue: 1,500,000 UZS" and "Number of Sales: 15."

- **AC 1.4.3: Disabling Notifications**

  - **Given** my Telegram bot is active and configured to send daily reports.
  - **When** I navigate to the settings page and turn the master "Enable/Disable" toggle to "Off" and save.
  - **Then** I should not receive the daily sales report at the scheduled time.

- **AC 1.4.4: Handling Invalid Credentials**
  - **Given** I am on the "Telegram Notifications" settings page.
  - **When** I enter an incorrect API Token and click "Test Connection & Save."
  - **Then** the system must display an error message on the screen.
  - **And** the connection status should remain "Inactive" or "Not Connected."

---

Of course. This module is the heart of the inventory system. We will break it down carefully to ensure the foundation is solid, especially regarding product variations, which are critical for retail.

Let's begin the PRD for **Feature 2.1: Product Creation & Cataloging**.

---

### **PRD Module 2: Product & Inventory Management**

#### **Feature 2.1: Product Creation & Cataloging**

**1. User Stories**

- **User Story 2.1.1:** As a Store Owner, I want to add different types of items to my system, including physical products with stock and services that don't have stock, so I can manage my entire business offering.
- **User Story 2.1.2:** As an apparel Store Owner, I need to create a single product like "T-Shirt" and then define its variations (e.g., Color: Red, Blue; Size: S, M, L) without creating a separate product for each combination, so I can manage my inventory efficiently.
- **User Story 2.1.3:** As a Store Owner, I want to add custom details to my products, such as Brand or Supplier, so I can better organize, filter, and analyze my product data.
- **User Story 2.1.4:** As a Store Owner, I want to organize my products into categories and sub-categories (e.g., Menswear > Shoes), so that I and my staff can find items quickly and I can run reports on specific product groups.

**2. Functional Requirements**

- **FR 2.1.1 - Product Types:**

  - The system must support the creation of two primary product types for the MVP:
    1.  **Standard Product:** An item with a trackable stock quantity. This is the default type.
    2.  **Service:** An item with no stock quantity (e.g., delivery, alterations). The UI for stock management must be hidden or disabled for this type.
  - _(Post-MVP Note: A `Bundle/Set` product type will be introduced in a future version)._

- **FR 2.1.2 - Core Product Fields:**

  - The "Add Product" form must include the following fields:
    - **Product Name:** (Text, Required)
    - **Product Type:** (Selector: Standard Product / Service)
    - **SKU / Article:** (Text, Optional) - A unique identifier for the product. The system should provide a "Generate" button to create a unique random SKU.
    - **Unit of Measurement:** (Selector, e.g., piece, kg, meter) - Defaulted to "piece".
    - **Category:** (Selector) - Allows assignment to a pre-defined category.

  Note: Product image upload and management is intentionally excluded from the MVP. The "Add Product" form must not include a photo uploader or any image fields; product records will be created and managed without images.

- **FR 2.1.3 - Product Variations (for Standard Products):**

  - The system must have a separate settings area to manage "Variation Attributes" (e.g., "Color", "Size", "Material").
  - When creating a "Standard Product," the user can choose to make it a "Simple Product" (no variations) or a "Variable Product."
  - If "Variable Product" is chosen, the user can select from the created attributes (e.g., select "Color" and "Size").
  - The system will then dynamically generate a table/matrix of all possible combinations (variations).

    - For **each individual variation** (e.g., Red / Small), the user must be able to set a unique:

      - SKU (can be auto-generated based on the parent SKU)
      - Cost Price
      - Selling Price
      - Stock Quantity

      Images or photos for variations are not supported in the MVP and must not be included in the variation configuration.

- **FR 2.1.4 - Custom Attributes (Characteristics):**

  - The system must have a settings area to define custom text-based fields (e.g., "Brand," "Supplier," "Country of Origin").
  - These defined fields will appear on the "Add Product" form for the user to fill in, providing richer product data.

- **FR 2.1.5 - Category Management:**
  - The system must provide an interface to create, rename, and delete product categories.
  - Categories must support a parent-child hierarchy to allow for sub-categories.
  - The main product list ("Catalog") must be filterable by category.

**3. Acceptance Criteria (AC)**

- **AC 2.1.1: Creating a Simple Standard Product**

  - **Given** I am on the "Add Product" page.
  - **When** I enter the name "Leather Wallet," set the price to `300,000 UZS`, the stock to `15`, and click "Save."
  - **Then** the product "Leather Wallet" must appear in my main product catalog with a quantity of 15.

- **AC 2.1.2: Creating a Service Product**

  - **Given** I am on the "Add Product" page.
  - **When** I select the product type "Service," enter the name "Gift Wrapping," and set the price to `15,000 UZS`.
  - **Then** the input fields for stock quantity must be hidden or disabled.
  - **And** upon saving, the "Gift Wrapping" service must appear in the catalog without a stock count.

- **AC 2.1.3: Creating a Product with Variations**

  - **Given** I have already defined "Color" (with values Red, Blue) and "Size" (with values S, M) as variation attributes.
  - **When** I create a new "Standard Product" named "Polo Shirt" and select "Color" and "Size" as its attributes.
  - **Then** the system must generate a list of 4 variations (Red-S, Red-M, Blue-S, Blue-M).
  - **And** I must be able to set the stock quantity of "Red-S" to `10` and the stock of "Blue-M" to `12`, with each having potentially different prices.

- **AC 2.1.4: Using Categories**
  - **Given** I have created a parent category "Apparel" and a sub-category "Shirts."
  - **When** I create the "Polo Shirt" product.
  - **Then** I can assign it to the "Apparel > Shirts" category.
  - **And** when I later view the catalog and filter by the "Shirts" category, only the "Polo Shirt" and other items in that category should be displayed.

---

Of course. Let's define the requirements for **Feature 2.2: Pricing & Stock Control**.

This feature builds directly upon the product foundation we just established, giving the store owner the tools to manage profitability and inventory levels.

---

### **PRD Module 2: Product & Inventory Management**

#### **Feature 2.2: Pricing & Stock Control**

**1. User Stories**

- **User Story 2.2.1:** As a Store Owner, I need to set both a "Cost Price" and a "Selling Price" for every product, so that the system can automatically calculate my gross profit on each sale.
- **User Story 2.2.2:** As a Store Owner who also sells to other businesses, I want the option to add a "Wholesale Price" to my products, so I can easily manage B2B transactions.
- **User Story 2.2.3:** As a Store Manager, I want to set a "Low Stock Threshold" for my key products, so that the system automatically alerts me when it's time to reorder from my suppliers.
- **User Story 2.2.4:** As a Store Owner, I want to enable a system-wide setting that prevents any item from being sold for less than its cost, so I can protect my business from accidental losses caused by excessive discounts.

**2. Functional Requirements**

- **FR 2.2.1 - Core Price Fields:**

  - On the "Add/Edit Product" form, for each product or variation, there must be two distinct, required numerical input fields:
    1.  **Cost Price:** The price paid to the supplier for the item.
    2.  **Selling Price:** The standard retail price for the customer.
  - The system should display a read-only calculated field for "Margin" or "Markup" directly on this page, which updates in real-time as the price fields are changed.

- **FR 2.2.2 - Wholesale Pricing:**

  - An optional numerical input field for "Wholesale Price" must be available on the "Add/Edit Product" form. This field can be left empty if the user does not engage in wholesale.

- **FR 2.2.3 - Stock Management Fields:**

  - For each product or variation, there must be a numerical input field for **Initial Stock Quantity**. This is used to set the starting inventory level when adding a new product.
  - There must be a numerical input field for **Low Stock Threshold**. This value defaults to a sensible number (e.g., 2 or 5) but can be changed by the user.

- **FR 2.2.4 - Low Stock Alert Mechanism:**

  - The system must maintain a dedicated "Low Stock" report or a filterable view in the main product catalog.
  - Whenever a sale, return, or manual adjustment causes a product's stock level to become less than or equal to its "Low Stock Threshold," that product must automatically be added to this "Low Stock" list.

- **FR 2.2.5 - Negative Margin Protection:**
  - There must be a global toggle switch in the main system settings labeled "Enable Negative Margin Protection." This setting is "Off" by default.
  - When this setting is **enabled**, the Point of Sale (POS) system must validate the final price of an item before completing a sale. If a manual discount or price override results in the item's final selling price being lower than its stored "Cost Price," the system must block the action and display a clear warning message to the cashier.

**3. Acceptance Criteria (AC)**

- **AC 2.2.1: Price and Margin Calculation**

  - **Given** I am creating a new product.
  - **When** I enter `100,000` in the "Cost Price" field and `150,000` in the "Selling Price" field.
  - **Then** the system must automatically display a calculated margin of `50,000` (or a markup of `50%`).

- **AC 2.2.2: Triggering a Low Stock Alert**

  - **Given** a product "Running Shoes" has a "Low Stock Threshold" set to `3`.
  - **And** its current stock quantity is `4`.
  - **When** a cashier sells one unit of "Running Shoes."
  - **Then** the stock quantity for that product must update to `3`.
  - **And** the "Running Shoes" product must now appear on the "Low Stock" report.

- **AC 2.2.3: Negative Margin Protection Blocks a Sale**

  - **Given** the "Enable Negative Margin Protection" setting is turned **On**.
  - **And** a product has a "Cost Price" of `200,000 UZS` and a "Selling Price" of `250,000 UZS`.
  - **When** a cashier tries to apply a manual discount of `60,000 UZS` to this item.
  - **Then** the system must prevent the discount from being applied and display a warning message like, "Price cannot be lower than the cost price."

- **AC 2.2.4: Negative Margin Protection Allows a Valid Sale**

  - **Given** the same setup as above.
  - **When** a cashier applies a manual discount of `40,000 UZS` (making the final price `210,000 UZS`).
  - **Then** the system must accept the discount and allow the sale to proceed normally.

- **AC 2.2.5: Barcode Scanning in POS**
  - **Given** a product variation "Polo Shirt, Blue, M" has a unique SKU of `P-BL-M-001`.
  - **When** a cashier uses a barcode scanner that inputs `P-BL-M-001` into the POS search field.
  - **Then** the "Polo Shirt, Blue, M" variation must be instantly added to the sales cart.

---

Of course. Let's define the requirements for the final feature in this module, **Feature 2.3: Bulk Operations**.

This is a critical feature for user efficiency, especially when first setting up the store or managing large inventory updates. To reduce MVP scope and accelerate delivery, the bulk Excel/CSV import feature is deferred to Post-MVP.

---

### **PRD Module 2: Product & Inventory Management**

#### **Feature 2.3: Bulk Operations**

**1. User Stories**

- **User Story 2.3.1:** As a new Store Owner, I want to add all my existing products at once by uploading a single Excel file, so I can avoid the slow process of entering hundreds of items manually. (This story is deferred to Post-MVP.)
- **User Story 2.3.2:** As a Store Manager receiving a new shipment, I want to update the stock quantities and cost prices of multiple products simultaneously using a file, so I can get the new inventory into the system quickly. (Deferred to Post-MVP.)
- **User Story 2.3.3:** As a Store Owner cleaning up my catalog, I want to select multiple old products and archive them all with a single click, so I can manage my product list efficiently. (In-scope for MVP.)

**2. Functional Requirements**

- **FR 2.3.1 - Bulk Import (DEFERRED to Post-MVP):**

  - The bulk Excel/CSV import feature is explicitly excluded from the MVP scope and will be planned for a Post-MVP phase.
  - Rationale: deferring reduces initial development risk and allows focus on in-app bulk management UX and core POS reliability.
  - Post-MVP notes (for future implementation): when implemented the import should provide a downloadable template, support ParentSKU-based variations, use SKU as the unique identifier, and perform row-level validation with clear error reporting.

- **FR 2.3.2 - Bulk Actions in the Catalog UI (MVP):**

  - The main product list/catalog view must include a checkbox next to each product row to enable selection of multiple products.
  - When at least one product is selected, a "Bulk Actions" menu must become visible.
  - For the MVP, this menu must provide the following actions:
    1.  **Assign Category:** select a category from a dropdown and apply it to all selected products.
    2.  **Archive Products:** soft-delete selected products (remove from active POS/catalog views but retain for reporting).
    3.  **Simple Bulk Edit:** allow setting a small set of common fields across selected products (e.g., Low Stock Threshold, toggle Active/Inactive). Complex bulk edits requiring file import are deferred to Post-MVP.

**3. Acceptance Criteria (AC)**

- **AC 2.3.1: Bulk Import Deferred**

  - **Given** the project is in MVP scope,
  - **When** a stakeholder reviews the PRD,
  - **Then** the document must clearly state that the bulk Excel/CSV import feature is deferred to Post-MVP and will not be delivered in the MVP release.

- **AC 2.3.2: Performing a Bulk Archive in the UI**

  - **Given** I am viewing my product catalog and have several products visible,
  - **When** I select multiple products via checkboxes and choose "Archive,"
  - **Then** those products must be moved to an "Archived" state, removed from the default catalog and POS views, and become visible in an "Archived" filter.

- **AC 2.3.3: Performing Other Supported Bulk Actions (MVP)**

  - **Given** I select multiple products,
  - **When** I apply a supported bulk action (e.g., change category, set Low Stock Threshold, toggle Active/Inactive),
  - **Then** the chosen field(s) must be updated for all selected products and a success message displayed.

- **AC 2.3.4: Post-MVP Import Acceptance Criteria (Documented)**

  - **Given** the bulk import is deferred,
  - **When** planning for Post-MVP,
  - **Then** the team should implement row-level validation and clear error messages (e.g., "Error on Row 10: ProductName is a required field") when the import is implemented.

---

Excellent. We are now moving into the most interactive part of the application: the Point of Sale (POS). This interface must be fast, intuitive, and robust, as it will be used by cashiers for every single customer transaction.

Let's define **Feature 3.1: The Sales Interface**.

---

### **PRD Module 3: Sales Operations (Point of Sale - POS)**

#### **Feature 3.1: The Sales Interface**

**1. User Stories**

- **User Story 3.1.1:** As a Cashier, I need to find products instantly by typing their name or scanning their barcode, so I can serve customers quickly and keep queues short.
- **User Story 3.1.2:** As a Cashier, I need to easily change item quantities and remove products from the cart, so I can accommodate customer changes without starting the sale over.
- **User Story 3.1.3:** As a Cashier, I need to apply discounts to either a specific item or the entire bill, so I can handle store promotions or customer negotiations.
- **User Story 3.1.4:** As a Store Owner, I want the sales screen to be clean and easy to read, clearly showing the itemized list and the final total, to minimize cashier errors.

**2. Functional Requirements**

- **FR 3.1.1 - Interface Layout:**

  - The POS screen will have a two-panel layout. The left panel is the **Sales Cart**, which lists all items for the current transaction. The right panel is the **Product & Customer Area**.
  - A large, always-visible **Search Bar** must be at the top of the right panel and should be the default focus when the screen is ready for a new sale.
  - A **Totals Summary** section at the bottom of the Sales Cart must clearly display the Subtotal, total Discount amount, and the final Amount Due.

- **FR 3.1.2 - Product Search & Addition:**

  - The search bar must perform a real-time search on Product Name, SKU, and Barcode fields.
  - As the cashier types, a dropdown list of matching products must appear instantly. Clicking a product adds it to the cart.
  - The system must be fully compatible with standard USB barcode scanners (which function as keyboard input). When a barcode is scanned, the corresponding product must be added to the cart immediately.
  - If a product with variations is found, the system may prompt the cashier to select the specific variation (e.g., Size and Color) if a unique barcode for the variation was not scanned.

- **FR 3.1.3 - Sales Cart Management:**

  - Each line item in the cart must display: Product Name, Quantity, Price per Unit, and the Line Total.
  - The **Quantity** of any item in the cart must be directly editable.
  - A "Remove" button (e.g., a trash can icon) must be present for each line item.
  - If a product that is already in the cart is added again (e.g., scanned a second time), the system must increment the quantity of the existing line item by 1, not add a new line.

- **FR 3.1.4 - Discount Functionality:**

  - The system must allow a cashier to apply a discount to an individual line item. The discount can be a **percentage (%)** or a **fixed amount**.
  - The system must allow a cashier to apply a discount to the entire cart's subtotal, also as a **percentage (%)** or a **fixed amount**.
  - All applied discounts must be clearly reflected in the Totals Summary.

- **FR 3.1.5 - Main Action Buttons:**
  - A prominent **"Checkout"** button must be visible, which will navigate the cashier to the payment screen to finalize the sale.
  - A **"Clear Sale"** button must be available to completely empty the cart and reset the interface for a new customer.
  - A **"Hold Sale"** button must be present on the interface (its functionality will be detailed in a subsequent feature).

**3. Acceptance Criteria (AC)**

- **AC 3.1.1: Adding a Product via Barcode Scan**

  - **Given** the POS interface is ready for a new sale.
  - **When** the cashier scans a product's barcode.
  - **Then** the corresponding product must instantly appear as a line item in the Sales Cart with a quantity of 1.

- **AC 3.1.2: Incrementing Quantity of an Existing Item**

  - **Given** the product "Leather Wallet" is already in the cart with a quantity of 1.
  - **When** the cashier scans the barcode for "Leather Wallet" again.
  - **Then** the Sales Cart must still show only one line item for "Leather Wallet," but its quantity must now be 2.

- **AC 3.1.3: Applying a Line Item Discount**

  - **Given** a product is in the cart with a price of `200,000 UZS`.
  - **When** the cashier selects that item and applies a `10%` discount.
  - **Then** the line total for that item must update to `180,000 UZS`, and the final Amount Due must be recalculated accordingly.

- **AC 3.1.4: Applying a Cart-Wide Discount**

  - **Given** the cart has a subtotal of `500,000 UZS`.
  - **When** the cashier applies a fixed discount of `50,000 UZS` to the entire sale.
  - **Then** the Totals Summary must show a "Discount" line of `-50,000 UZS` and the final "Amount Due" must be `450,000 UZS`.

- **AC 3.1.5: Clearing the Sale**
  - **Given** there are multiple items in the Sales Cart.
  - **When** the cashier clicks the "Clear Sale" button and confirms the action.
  - **Then** the Sales Cart must become empty, and the Totals Summary must reset to zero.

---

Excellent. Let's integrate customer management directly into the sales flow. This is essential for building a customer database, tracking purchase history, and enabling loyalty programs.

Here is the PRD for **Feature 3.2: Customer Management (CRM) in POS**.

---

### **PRD Module 3: Sales Operations (Point of Sale - POS)**

#### **Feature 3.2: Customer Management (CRM) in POS**

**1. User Stories**

- **User Story 3.2.1:** As a Cashier, I want to search for an existing customer by their name or phone number and attach them to a sale, so their purchase is recorded and they can receive any applicable loyalty benefits.
- **User Story 3.2.2:** As a Cashier, when a customer is new, I need a fast way to add their basic contact information (Name and Phone Number) directly from the sales screen, so I don't disrupt the checkout flow.
- **User Story 3.2.3:** As a Store Owner, I want to be able to segment my customers into groups (e.g., "VIPs," "Family"), so I can analyze their buying habits and potentially offer group-specific discounts in the future.
- **User Story 3.2.4:** As a Store Owner, I want my cashiers to be able to record how a new customer heard about our store, so I can understand which of my marketing channels are most effective.

**2. Functional Requirements**

- **FR 3.2.1 - Customer Search & Selection:**

  - The POS interface (in the right-hand panel) must include a dedicated search bar for customers.
  - The search must be real-time and query the customer database by **Name** and **Phone Number**.
  - Once a customer is selected from the search results, their name must be clearly displayed as being "attached" to the current sale.
  - The system must allow for sales to be completed without a customer attached (i.e., a "guest" checkout).

- **FR 3.2.2 - Quick Add Customer Form:**

  - A button labeled "Add New Customer" must be present near the customer search bar.
  - Clicking this button must open a simple modal/pop-up form directly within the POS interface.
  - The form must contain the following fields:
    - **Full Name:** (Text, Required)
    - **Phone Number:** (Text, Required, with validation for a numerical format)
    - **Customer Group:** (Optional, Dropdown)
    - **Marketing Source:** (Optional, Dropdown, e.g., "How did you hear about us?")
  - Upon saving, the new customer is created in the database and automatically attached to the current sale.

- **FR 3.2.3 - Customer Group Management:**

  - The system must have a separate settings page (outside of the POS) for managing "Customer Groups."
  - On this page, a Store Owner can create, rename, and delete groups.
  - The list of created groups will populate the "Customer Group" dropdown in the Quick Add Customer form.

- **FR 3.2.4 - Marketing Source Management:**
  - The system must have a separate settings page for managing "Marketing Sources."
  - A Store Owner can create, rename, and delete sources (e.g., "Instagram," "Telegram," "Friend Referral").
  - This list will populate the "Marketing Source" dropdown in the Quick Add Customer form.

**3. Acceptance Criteria (AC)**

- **AC 3.2.1: Attaching an Existing Customer to a Sale**

  - **Given** a customer named "Alisher Usmanov" with phone number "998901234567" exists in the system.
  - **When** the cashier types "12345" into the customer search bar.
  - **Then** "Alisher Usmanov" should appear in the search results.
  - **And** when the cashier selects him, his name should be displayed as the customer for the current transaction.

- **AC 3.2.2: Adding a New Customer During Checkout**

  - **Given** a new customer is at the checkout.
  - **When** the cashier clicks "Add New Customer," enters the required name and phone number, and clicks "Save."
  - **Then** the modal must close, and the newly created customer's name must immediately be displayed as attached to the sale.

- **AC 3.2.3: Assigning a Customer to a Group**

  - **Given** the Store Owner has created a "VIP Clients" group in the settings.
  - **When** a cashier adds a new customer via the Quick Add form.
  - **Then** "VIP Clients" must be a selectable option in the "Customer Group" dropdown.
  - **And** if selected, the customer's profile must be associated with this group.

- **AC 3.2.4: Completing a "Guest" Sale**
  - **Given** a customer does not want to provide their details.
  - **When** the cashier adds products to the cart but does not search for or add a customer.
  - **Then** the cashier must still be able to proceed to the "Checkout" and complete the sale successfully. The transaction report should log this sale under a "Guest" or null customer.

---

Of course. Let's define the final and most critical part of the sales process: **Feature 3.3: Payments & Transactions**. This feature also includes the essential post-sale activity of handling returns.

---

### **PRD Module 3: Sales Operations (Point of Sale - POS)**

#### **Feature 3.3: Payments & Transactions**

**1. User Stories**

- **User Story 3.3.1:** As a Cashier, when a customer pays with cash, I need the system to quickly calculate the correct change to give back, so I can avoid manual math and reduce errors.
- **User Story 3.3.2:** As a Cashier, I need to be able to accept multiple payment methods for a single purchase (e.g., part cash, part card), so I can accommodate my customers' needs.
- **User Story 3.3.3:** As a Store Manager, I need a straightforward process to handle customer returns by looking up their original receipt, so I can ensure accurate refunds and proper inventory restocks.
- **User Story 3.3.4:** As a Store Owner, I need absolute confidence that every completed sale or return automatically updates my inventory levels in real-time, so my stock data is always accurate.

**2. Functional Requirements**

- **FR 3.3.1 - Payment Screen:**

  - After the cashier clicks the "Checkout" button from the main sales interface, a dedicated Payment Screen/Modal must appear.
  - This screen must clearly display the final "Amount Due."
  - It must show buttons for all configured payment methods (e.g., `Cash`, `Card`, `Payme`, and any other custom types).

- **FR 3.3.2 - Single Payment Method Flow:**

  - **Cash:** When `Cash` is selected, an input field for "Amount Tendered" must appear. The system will automatically calculate and display the "Change Due." The "Complete Sale" button is enabled once the tendered amount is greater than or equal to the amount due.
  - **Other Methods (Card, Payme, etc.):** When any other single method is selected, the system assumes the full amount is being paid by that method.

- **FR 3.3.3 - Split Payment Flow:**

  - The cashier must be able to enter a partial amount for the first payment method.
  - The system must then display the "Remaining Balance."
  - The cashier can then select another payment method to cover the remaining balance.
  - The "Complete Sale" button must only be enabled when the "Remaining Balance" is zero.

- **FR 3.3.4 - Transaction Finalization:**

  - Upon clicking "Complete Sale," the system must:
    1.  Decrement the stock quantities of all sold items from the inventory.
    2.  Generate a unique, sequential receipt/transaction ID.
    3.  Save the complete transaction record, including all items, discounts, customer info (if any), cashier ID, and payment breakdown.
    4.  Transition to a "Sale Complete" screen with options to "Print Receipt" or start a "New Sale."

- **FR 3.3.5 - Returns & Exchanges Workflow:**
  - The application must have a "Sales History" or "Returns" section, accessible from the main menu.
  - The user must be able to find the original transaction by entering the **Transaction ID** from the customer's receipt.
  - The system will display the details of the original sale. The user can select which item(s) and what quantity are being returned.
  - Upon confirmation, the system must:
    1.  Increment the stock quantities of the returned items back into inventory.
    2.  Record a new "Return" transaction, linked to the original sale ID.
    3.  The refund amount is calculated, and the system records it as a cash-out event.
  - _(MVP Scope for Exchanges: An exchange will be handled as two separate transactions: first, process the return as described above, then start a new sale for the new item)._

**3. Acceptance Criteria (AC)**

- **AC 3.3.1: Correct Change Calculation for Cash Payment**

  - **Given** the final amount due is `185,000 UZS`.
  - **When** the cashier selects "Cash" and enters `200,000` in the "Amount Tendered" field.
  - **Then** the system must display "Change Due: 15,000 UZS."
  - **And** the "Complete Sale" button must be active.

- **AC 3.3.2: Successful Split Payment**

  - **Given** the final amount due is `500,000 UZS`.
  - **When** the cashier selects "Card" and enters `300,000` as the amount.
  - **Then** the system must show a "Remaining Balance" of `200,000 UZS`.
  - **And** when the cashier then selects "Cash" and completes the payment, the sale must be finalized successfully.

- **AC 3.3.3: Inventory Decrements on Sale**

  - **Given** a product "Polo Shirt" has a stock quantity of `20`.
  - **When** a sale including 3 units of "Polo Shirt" is successfully completed.
  - **Then** the inventory level for "Polo Shirt" must be updated to `17`.

- **AC 3.3.4: Inventory Increments on Return**

  - **Given** a product "Leather Wallet" has a stock quantity of `10`.
  - **And** a customer returns one "Leather Wallet" from a previous purchase.
  - **When** the cashier successfully processes this return.
  - **Then** the inventory level for "Leather Wallet" must be updated to `11`.

- **AC 3.3.5: Finding a Transaction for Return**
  - **Given** a completed sale has a transaction ID of `SALE-2025-0015`.
  - **When** the cashier enters `SALE-2025-0015` into the search field in the "Returns" section.
  - **Then** the system must display the itemized list and details from that specific sale.

---

Excellent. Let's move into the operational finance features. This module is critical for maintaining accountability and ensuring the physical cash in the register matches the sales recorded in the system.

Here is the PRD for **Feature 4.1: Cash Flow Management**.

---

### **PRD Module 4: Financial Control & Analytics**

#### **Feature 4.1: Cash Flow Management (Cashier Shift Operations)**

**1. User Stories**

- **User Story 4.1.1:** As a Store Manager, I need each cashier to declare their starting cash amount (float) when they begin their shift, so we have an accurate baseline for the day's financial reconciliation.
- **User Story 4.1.2:** As a Cashier, at the end of my shift, I need a clear summary of all my cash transactions and a way to enter my final counted amount, so I can easily balance my cash drawer and account for any discrepancies.
- **User Story 4.1.3:** As a Cashier, I need a simple way to log any money I take out of the register for small business expenses (petty cash), so it doesn't create a shortage in my end-of-day count.
- **User Story 4.1.4:** As a Store Manager, I need to formally record the removal of large amounts of cash from the register (e.g., for a bank deposit), so there is a clear and auditable trail of all cash movements.

**2. Functional Requirements**

- **FR 4.1.1 - Shift Management Prerequisite:**

  - The Point of Sale (POS) interface must be locked or restricted from making sales until a cashier has officially started a "Cash Shift."

- **FR 4.1.2 - Opening a Shift:**

  - The system must provide an "Open Shift" function.
  - This function must prompt the user to enter a single numerical value: the **Starting Cash Amount** (float).
  - Upon confirmation, the system will record the shift's start time, the logged-in user's ID, and the starting cash amount, and then unlock the POS for sales.

- **FR 4.1.3 - In-Shift Cash Operations:**

  - During an active shift, the cashier must have access to two functions:
    1.  **Record Expense (Cash Out):** This opens a form requiring a numerical **Amount** and a text **Reason**.
    2.  **Record Cash Transfer (Incassation):** This opens a form requiring a numerical **Amount** and an optional **Note**.
  - All such transactions must be logged and associated with the current active shift.

- **FR 4.1.4 - Closing a Shift:**
  - The system must provide a "Close Shift" function.
  - This function will navigate the user to a reconciliation summary screen that automatically calculates and displays:
    - Starting Cash
    - Total Cash Sales (from completed transactions)
    - Total Cash Refunds (from return transactions)
    - Total Cash Expenses (from "Cash Out" logs)
    - Total Cash Transfers
    - **Expected Cash in Drawer** (Calculated as: `Starting Cash + Sales - Refunds - Expenses - Transfers`)
  - The screen must have a required input field for the **Actual Counted Cash**.
  - The system will automatically calculate and display the **Difference** (`Actual Counted Cash - Expected Cash`). This value can be positive (overage) or negative (shortage).
  - Upon confirming the closure, the shift is marked as "Closed," and a permanent, detailed shift report is generated and saved.

**3. Acceptance Criteria (AC)**

- **AC 4.1.1: POS is Locked Before Shift Opening**

  - **Given** a cashier has logged in but has not yet opened a shift.
  - **When** they attempt to add a product to the cart in the POS.
  - **Then** the system must prevent the action and display a prompt or modal that says "Please open a new shift to begin sales."

- **AC 4.1.2: Successful Shift Opening**

  - **Given** the POS is locked.
  - **When** the cashier clicks "Open Shift" and enters `250,000 UZS` as the starting cash.
  - **Then** the shift is recorded as active, and the cashier can now successfully add products to the cart.

- **AC 4.1.3: Accurate Calculation of Expected Cash**

  - **Given** a shift was started with `250,000 UZS`.
  - **And** during the shift, there were `1,000,000 UZS` in cash sales and a `50,000 UZS` cash expense was recorded for "staff lunch."
  - **When** the cashier initiates the "Close Shift" process.
  - **Then** the system must display "Expected Cash in Drawer: 1,200,000 UZS" on the summary screen.

- **AC 4.1.4: Closing a Balanced Shift**

  - **Given** the "Expected Cash in Drawer" is `1,200,000 UZS`.
  - **When** the cashier enters `1,200,000` in the "Actual Counted Cash" field and confirms.
  - **Then** the system must display a "Difference" of `0`.
  - **And** the shift must be successfully closed and a report generated.

- **AC 4.1.5: Closing a Shift with a Shortage**
  - **Given** the "Expected Cash in Drawer" is `1,200,000 UZS`.
  - **When** the cashier enters `1,195,000` in the "Actual Counted Cash" field.
  - **Then** the system must display a "Difference" of `-5,000 UZS` (highlighted in red).
  - **And** the cashier must be able to confirm and close the shift, with the discrepancy logged in the final report.

---

Excellent. Let's define the final feature for this module, which is arguably the most important for the business owner: **Feature 4.2: Reporting & Dashboards**. This is where all the collected data is transformed into actionable business insights.

---

### **PRD Module 4: Financial Control & Analytics**

#### **Feature 4.2: Reporting & Dashboards**

**1. User Stories**

- **User Story 4.2.1:** As a Store Owner, I want a visual dashboard that gives me a quick, at-a-glance summary of my key business metrics (like revenue and profit) as soon as I log in.
- **User Story 4.2.2:** As a Store Owner, I need to know my **Gross Profit** for any period, so I can understand the true profitability of my sales after accounting for the cost of goods.
- **User Story 4.2.3:** As a Store Manager, I want to easily identify my best-selling products and top-performing employees, so I can make smart decisions about inventory and staff incentives.
- **User Story 4.2.4:** As a Store Owner, I need the ability to view and export detailed sales reports for specific date ranges, so I can perform my own analysis or for accounting purposes.

**2. Functional Requirements**

- **FR 4.2.1 - Main Dashboard:**

  - The main landing page after login must be a dashboard displaying key performance indicators (KPIs).
  - The dashboard must include a date range filter (with presets like "Today," "This Week," "This Month," and a custom range selector).
  - The dashboard must display the following KPIs, which update based on the selected date range:
    1.  **Total Revenue:** The total sum of money from all sales.
    2.  **Gross Profit:** Calculated as `Total Revenue - Total Cost of Goods Sold`.
    3.  **Number of Sales:** The total count of completed transactions.
    4.  **Average Sale Value:** Calculated as `Total Revenue / Number of Sales`.

- **FR 4.2.2 - Profitability Calculation Engine:**

  - The system must calculate the "Cost of Goods Sold" (COGS) for every single item in every transaction, based on the "Cost Price" stored for that product.
  - All sales reports that show profit must use this COGS calculation to determine the Gross Profit.
  - _(MVP Scope: Net Profit, which includes operational expenses from Feature 4.1, will be a separate, more advanced report for a future version. The core focus is on Gross Profit from sales)._

- **FR 4.2.3 - Dedicated Reports:**

  - The application must have a dedicated "Reports" section with the following reports for the MVP:
    1.  **Sales Summary:** A report showing the KPIs from the dashboard, but with the ability to view trends over time (e.g., a bar chart of daily revenue for the selected period).
    2.  **Top Products:** A filterable list of the top-selling products, sortable by either **Total Revenue Generated** or **Total Quantity Sold**.
    3.  **Top Employees:** A filterable list of the top-performing sales staff, sortable by **Total Revenue Generated** or **Total Number of Sales**. (This is dependent on cashiers assigning sales consultants to transactions).
    4.  **Transactions Report:** A detailed, line-by-line list of every transaction within the selected date range, showing ID, date/time, customer, total amount, and cashier.

- **FR 4.2.4 - Report Functionality:**
  - All reports must be filterable by a date range.
  - All report data presented in a table format must be exportable to a `.CSV` file.

**3. Acceptance Criteria (AC)**

- **AC 4.2.1: Dashboard Displays Correct Data**

  - **Given** the total sales for "Today" are `2,000,000 UZS`.
  - **When** the Store Owner logs in and views the dashboard with the "Today" filter selected.
  - **Then** the "Total Revenue" widget must display `2,000,000 UZS`.

- **AC 4.2.2: Accurate Gross Profit Calculation**

  - **Given** a sale is completed for a single item with a "Selling Price" of `300,000 UZS` and a "Cost Price" of `200,000 UZS`.
  - **When** the sales reports for that period are generated.
  - **Then** the "Gross Profit" for that transaction must be calculated and displayed as `100,000 UZS`.

- **AC 4.2.3: Top Products Report is Correctly Ordered**

  - **Given** three products were sold today: Product A (Revenue: 500k), Product B (Revenue: 1M), and Product C (Revenue: 250k).
  - **When** the Store Owner views the "Top Products" report for today, sorted by "Total Revenue Generated."
  - **Then** the products must be listed in the order: Product B, Product A, Product C.

- **AC 4.2.4: Successful Data Export**
  - **Given** I am viewing the "Transactions Report" which shows 50 transactions on the screen.
  - **When** I click the "Export to CSV" button.
  - **Then** a `.csv` file must be downloaded to my computer.
  - **And** when I open the file, it must contain 50 rows of data corresponding to the transactions I was viewing.

---

Of course. Let's move on to the advanced modules.

An accurate inventory count is one of the most critical operational tasks in retail. This feature provides the tool to ensure the system's data perfectly reflects the physical reality of the store's inventory.

Here is the PRD for **Feature 5.1: Inventory Counts (Stocktaking)**.

---

### **PRD Module 5: Advanced Modules**

#### **Feature 5.1: Inventory Counts (Stocktaking)**

**1. User Stories**

- **User Story 5.1.1:** As a Store Owner, I need to perform a full inventory count of my entire store periodically, so I can identify and correct discrepancies caused by theft, damage, or administrative errors.
- **User Story 5.1.2:** As a Store Manager, I want the ability to perform a partial count on a specific category or brand, so I can conduct smaller, more frequent checks on high-value or fast-moving items without shutting down the whole store.
- **User Story 5.1.3:** As a staff member conducting a count, I need a simple interface where I can scan a product's barcode and enter the physical quantity I've counted, so I can work quickly and accurately.
- **User Story 5.1.4:** As a Store Owner, after a count is completed, I need a clear summary report that shows me exactly what the discrepancies are and what their financial value is (at cost), so I can understand the financial impact of any inventory loss.

**2. Functional Requirements**

- **FR 5.1.1 - Creating an Inventory Count Session:**

  - The system must have a dedicated "Inventory Counts" module.
  - The user must be able to start a "New Inventory Count." This action creates a "session."
  - The user must define the scope of the count:
    1.  **Full Count:** Includes all active products in the selected store's inventory.
    2.  **Partial Count:** The user is presented with filters to select a subset of products to be counted (e.g., by Category, Brand, or Supplier).
  - Each count session is saved with a status (e.g., "In Progress," "Completed").

- **FR 5.1.2 - The Counting Interface:**

  - Once a session is started, the system generates a list of all products within the defined scope.
  - For each product, the interface must display:
    - Product Name and key identifiers (SKU, variation details).
    - **Expected Quantity:** The current stock level according to the system.
    - A numerical input field for the **Counted Quantity**.
  - The interface must have a search bar that allows staff to quickly find a product on the list by scanning its barcode or typing its name/SKU.
  - The system must save the progress of the count continuously, allowing a user to pause and resume the session later.

- **FR 5.1.3 - Review and Reconciliation:**

  - Once the user has entered counts for all items, they can proceed to a "Review" stage.
  - The review screen must provide a clear summary, categorizing products into three lists:
    1.  **Matching Items:** `Counted Quantity` equals `Expected Quantity`.
    2.  **Shortages:** `Counted Quantity` is less than `Expected Quantity`.
    3.  **Surpluses:** `Counted Quantity` is greater than `Expected Quantity`.
  - For all items with discrepancies (shortages/surpluses), the report must display the difference in quantity and the total **financial value of the discrepancy**, calculated using the item's **Cost Price**.

- **FR 5.1.4 - Finalizing the Count:**
  - The review screen must have a final, clearly labeled "Complete and Apply Changes" button, which requires a confirmation step (e.g., a pop-up asking "Are you sure? This will update your inventory levels.").
  - Upon confirmation, the system must perform a **stock adjustment** for every item in the count, setting its official stock quantity to the new `Counted Quantity`.
  - The inventory count session's status is changed to "Completed," and the detailed report is saved permanently for historical reference.

**3. Acceptance Criteria (AC)**

- **AC 5.1.1: Starting a Partial Count**

  - **Given** I want to count only the "Shoes" category.
  - **When** I start a new inventory count, select "Partial Count," and filter for the "Shoes" category.
  - **Then** the generated counting list must only contain products from the "Shoes" category.

- **AC 5.1.2: Entering Counted Quantities**

  - **Given** a count is "In Progress" and a product "Leather Wallet" has an "Expected Quantity" of `15`.
  - **When** I scan the wallet's barcode, and enter `14` into its "Counted Quantity" field.
  - **Then** the system must save this value for the session, and I can move to the next product.

- **AC 5.1.3: Reviewing the Discrepancy Report**

  - **Given** I have completed entering all counts for a session.
  - **And** for "Leather Wallet," Expected was `15`, Counted is `14`, and its Cost Price is `100,000 UZS`.
  - **When** I navigate to the "Review" screen.
  - **Then** the report must clearly list "Leather Wallet" under the "Shortages" section with a quantity difference of `-1` and a financial discrepancy value of `-100,000 UZS`.

- **AC 5.1.4: Verifying Stock Level Update After Finalization**
  - **Given** the "Leather Wallet" product has a current stock level of `15` in the main catalog.
  - **And** an inventory count session where it was counted as `14` is ready to be finalized.
  - **When** I click "Complete and Apply Changes" and confirm the action.
  - **Then** the inventory count's status must change to "Completed."
  - **And** if I immediately check the "Leather Wallet" product in the main catalog, its stock quantity must be `14`.

---

Of course. Let's define the requirements for **Feature 5.2: Inter-Store Transfers**.

This feature is foundational for any retail business planning to operate more than one physical location. It ensures inventory can be moved between stores with full traceability.

---

### **PRD Module 5: Advanced Modules**

#### **Feature 5.2: Inter-Store Transfers**

**1. User Stories**

- **User Story 5.2.1:** As a Manager of a well-stocked store, I need to send inventory to another branch that is running low on specific items, so we can balance our stock and avoid missed sales.
- **User Story 5.2.2:** As a Manager of a receiving store, I need to see a list of all incoming shipments and formally accept them into my inventory, so I can confirm the transfer is complete and the items are ready for sale.
- **User Story 5.2.3:** As a Business Owner, I need a clear overview of all stock that is currently "in-transit" between stores, so I have an accurate, real-time picture of my entire company's inventory, regardless of its physical location.

**2. Functional Requirements**

- **FR 5.2.1 - Transfer Creation:**

  - The system must have a dedicated "Stock Transfers" module.
  - A user must be able to initiate a "New Transfer." The creation form must include:
    - **Source Store:** A dropdown to select the store the stock is coming FROM.
    - **Destination Store:** A dropdown to select the store the stock is going TO.
    - A product search/scan field to add items to the transfer list.
  - For each item added, the user must specify the quantity to transfer. The system must validate that the source store has sufficient available stock for the requested quantity.
  - Saving this form creates a transfer record with a status of **"Draft."**

- **FR 5.2.2 - Dispatching a Transfer:**

  - A user at the source store must be able to "Dispatch" a transfer from the "Draft" status.
  - Upon dispatch, the system must:
    1.  Change the transfer's status to **"In-Transit."**
    2.  **Immediately deduct** the quantities of the dispatched items from the **Source Store's** sellable inventory.
    3.  The stock is now considered "in-transit" and is not yet available in the destination store's inventory.

- **FR 5.2.3 - Receiving a Transfer:**

  - A user at the destination store must be able to view all transfers with an "In-Transit" status that are destined for their store.
  - The user can open the transfer to see the list of expected items and quantities.
  - A button labeled "Accept & Receive" must be present.
  - Upon clicking "Accept & Receive," the system must:
    1.  Change the transfer's status to **"Completed."**
    2.  **Immediately add** the quantities of the received items to the **Destination Store's** sellable inventory.
  - _(MVP Scope: The initial version will assume the full quantity is always received. A more advanced version might allow for receiving partial quantities or noting discrepancies)._

- **FR 5.2.4 - Tracking and History:**
  - The "Stock Transfers" module must display a complete list of all transfers (Draft, In-Transit, and Completed).
  - This list must be filterable by status, source store, and destination store.

**3. Acceptance Criteria (AC)**

- **AC 5.2.1: Stock Validation During Creation**

  - **Given** Store A has `10` units of "Product X."
  - **When** a user tries to create a transfer of `12` units of "Product X" from Store A to Store B.
  - **Then** the system must display an error message like "Insufficient stock. Only 10 units available." and prevent the transfer from being created.

- **AC 5.2.2: Correct Inventory Deduction on Dispatch**

  - **Given** Store A has `50` units of "Product Y" and Store B has `5` units.
  - **And** a "Draft" transfer of `20` units of "Product Y" from Store A to Store B exists.
  - **When** the manager at Store A clicks "Dispatch."
  - **Then** the transfer status must change to "In-Transit."
  - **And** the sellable stock for "Product Y" at Store A must immediately become `30`.
  - **And** the sellable stock for "Product Y" at Store B must remain `5`.

- **AC 5.2.3: Correct Inventory Addition on Receipt**

  - **Given** the "In-Transit" transfer from the previous scenario.
  - **And** Store A's stock is `30` and Store B's stock is `5`.
  - **When** the manager at Store B clicks "Accept & Receive."
  - **Then** the transfer status must change to "Completed."
  - **And** the sellable stock for "Product Y" at Store B must immediately become `25`.
  - **And** the sellable stock for "Product Y" at Store A must remain `30`.

- **AC 5.2.4: Viewing In-Transit Inventory**
  - **Given** a transfer of `20` units of "Product Y" is "In-Transit" from Store A to Store B.
  - **When** the Business Owner views the "Stock Transfers" list and filters for "In-Transit" status.
  - **Then** the transfer must be visible in the list, clearly showing the source, destination, and the items being transferred.

---

Excellent. Let's define the final feature of this module, which will provide powerful tools for driving sales: **Feature 5.3: Marketing & Promotions**.

For the MVP, we will focus on the most common and effective types of promotions seen in retail.

---

### **PRD Module 5: Advanced Modules**

#### **Feature 5.3: Marketing & Promotions**

**1. User Stories**

- **User Story 5.3.1:** As a Store Owner, I want to create a simple percentage-based discount campaign (e.g., "20% off all T-shirts") for a specific period, so I can run seasonal sales.
- **User Story 5.3.2:** As a Store Owner, I want to create a "Buy X, Get Y" type of promotion (e.g., "Buy one pair of jeans, get a second pair 50% off"), so I can encourage customers to purchase multiple items.
- **User Story 5.3.3:** As a Store Owner, I want to be able to generate unique, single-use promo codes that give a specific discount, so I can collaborate with influencers or run targeted online campaigns and track their effectiveness.
- **User Story 5.3.4:** As a Cashier, when I add eligible items to the cart, I expect the system to automatically apply any active promotions without me having to do it manually, so the process is fast and error-free.

**2. Functional Requirements**

- **FR 5.3.1 - Promotion Management Interface:**

  - The system must have a dedicated "Marketing & Promotions" module.
  - The user must be able to "Create New Promotion" and choose from a list of promotion types.
  - Every promotion must have the following common configuration fields:
    - **Name:** (e.g., "Summer Sale")
    - **Start Date & End Date:** Defines the active period of the promotion.
    - **Applicable Stores:** Allows the user to select if the promotion runs in all stores or only specific ones.
    - **Product Selection:** A mechanism to select which products are part of the promotion (e.g., by category, brand, or individual selection).

- **FR 5.3.2 - Promotion Type 1: Percentage Discount:**

  - This type requires one additional field: **Discount Percentage**.
  - When active, any product selected for this promotion will have its price automatically reduced by this percentage in the POS cart.

- **FR 5.3.3 - Promotion Type 2: "Carousel" (Buy X, Get Y):**

  - This type requires more complex configuration:
    - **Condition (Buy):** Define the quantity and the set of products the customer must buy (e.g., Buy `1` item from the "Jeans" category).
    - **Reward (Get):** Define the quantity, the set of products the customer can get, and the discount to apply (e.g., Get `1` item from the "Jeans" category at `50%` off).
  - The system must automatically apply the discount to the lowest-priced eligible item in the cart.

- **FR 5.3.4 - Promo Code Functionality:**

  - Any promotion can optionally have promo codes enabled.
  - The system must allow the user to generate a batch of unique, random codes (e.g., "Generate 100 codes").
  - The POS interface must have an input field to "Apply Promo Code."
  - The system must validate the code. If valid and unused, the associated promotion's discount is applied to the cart. Each code can only be used once.

- **FR 5.3.5 - Automatic Application in POS:**
  - The POS system must automatically check the contents of the cart against all **active** promotions (that do not require a promo code).
  - If the cart's contents meet the criteria for a promotion, the discount must be applied automatically and displayed clearly as a line item in the cart summary.
  - The system must handle cases where multiple promotions could apply, defaulting to applying the one that gives the customer the largest discount.

**3. Acceptance Criteria (AC)**

- **AC 5.3.1: Simple Percentage Discount Works Correctly**

  - **Given** an active "20% off" promotion is running for the "T-Shirts" category.
  - **When** a cashier adds a T-shirt priced at `100,000 UZS` to the cart.
  - **Then** the system must automatically add a discount line of `-20,000 UZS`, and the final price for that item should be `80,000 UZS`.

- **AC 5.3.2: "Carousel" Promotion (BOGO) Works Correctly**

  - **Given** an active promotion "Buy one pair of jeans, get a second 50% off."
  - **When** a cashier adds two pairs of jeans to the cart, one priced at `400,000 UZS` and the other at `350,000 UZS`.
  - **Then** the system must automatically apply a `50%` discount to the cheaper pair, resulting in a discount of `175,000 UZS`.

- **AC 5.3.3: Successful Promo Code Application**

  - **Given** a valid, unused promo code `INSTA25` exists for a "25% off the total sale" promotion.
  - **When** a cashier enters `INSTA25` into the promo code field in the POS.
  - **Then** a `25%` discount must be applied to the cart's subtotal.
  - **And** the system must mark the `INSTA25` code as "used" so it cannot be applied again.

- **AC 5.3.4: Handling Invalid Promo Code**

  - **Given** the same setup as above.
  - **When** a cashier enters an incorrect code like `INSTA30` or a used code.
  - **Then** the system must display an error message like "Invalid or expired promo code" and not apply any discount.

- **AC 5.3.5: Promotion Deactivates After End Date**
  - **Given** the "20% off T-shirts" promotion was set to end yesterday.
  - **When** a cashier adds a T-shirt to the cart today.
  - **Then** no discount should be automatically applied.

---
