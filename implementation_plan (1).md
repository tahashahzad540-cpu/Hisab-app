# Hisaab - Personal Finance & Khata Expense Tracker

An offline-first, production-grade Android application built with Kotlin, Jetpack Compose, Material 3, and Room DB, featuring multi-currency accounting (default PKR), daily/monthly/yearly ledgers, budget alerts (>80% and >100%), visual canvas analytics, debt management (Lend & Borrow / قرض اور ادھار), PIN & biometric security, and PDF/CSV financial statement exports.

---

## User Review & Critical Decisions

> [!IMPORTANT]
> The application is architected around an **offline-first local Room Database**, ensuring complete user privacy and instant zero-latency usage without requiring a mandatory cloud connection, while enabling full JSON backups and PDF/Excel CSV exports.

- **Storage Engine**: SQLite via Room Database with reactive Kotlin Flows and type-safe DAOs.
- **Default Currency**: PKR (₨) with full runtime switcher for USD ($), EUR (€), GBP (£), AED, SAR, INR, CAD.
- **Security Posture**: 4-digit numeric PIN passcode with animated keypad, dot indicators, shake animation on error, and biometric fingerprint unlock.
- **Khata Module**: Dedicated dual-ledger tracking money to receive ("Lend / واپس لینا ہے") and money to give ("Borrow / واپس دینا ہے"), with WhatsApp/SMS reminder generation.

---

## 1. Overview & Core Concept

- **What It Does**: Provides users with a personal finance assistant and Khata ledger. Users can instantly record transactions, monitor category budgets against visual warnings, analyze spending habits through interactive Canvas Donut & Bar charts, track debts with contacts, and export formal PDF/CSV statements.
- **Target Audience / Persona**: Individuals, freelancers, shopkeepers, and household managers who want a fast, offline-first personal ledger with support for South Asian and international currencies and payment methods (Easypaisa, JazzCash, SadaPay, Nayapay, Bank, Cash).
- **Key Value**: 100% offline autonomy, no mandatory account sign-up, zero data tracking, military-grade PIN and biometric device lock, and automated Khata reminder generation.

---

## 2. User Experience & Visual Design

### Key User Flows
1. **First Launch & Hero Overview**: The user is welcomed with their profile name and live date. The emerald gradient balance card immediately displays net available balance, current period income, and expenses.
2. **Instant Transaction Entry**: Tapping the bottom right FAB opens a sheet with an expense/income toggle, numeric amount input with validation, visual category badges with custom colors/icons, date/time pickers, and payment method chips.
3. **Budget Goal Tracking**: When spending on a category approaches 80% or exceeds 100%, visual warning banners and progress bars highlight overspending.
4. **Day-by-Day Ledger**: Users navigate between Daily, Monthly, and Yearly views, filtering by transaction type or searching by notes/categories.
5. **Khata (Lend & Borrow)**: Users track loans and borrowed money with contacts, record partial or full payments, and generate WhatsApp/SMS reminders.
6. **Reports & Exports**: Users review visual Donut and Bar charts and tap to generate real PDF statements or CSV spreadsheets.

### Visual Identity & Theme
- **Primary Brand**: Deep Emerald Green (`#0F6848` light, `#48DC9B` dark) conveying financial stability and wealth.
- **Secondary Accent**: Warm Metallic Gold (`#C48B00` light, `#FFBA38` dark).
- **Semantic Accents**: Lush Mint Green (`#10B981`) for Income and Coral Crimson (`#EF4444`) for Expenses.
- **Design Language**: Material Design 3 with edge-to-edge system drawing, rounded corners (16–24dp), and high contrast typography.

---

## 3. Key Product Decisions & Trade-Offs

- **Room Database vs Remote-Only Backend**:
  - *Decision*: Built completely on SQLite Room Database with local reactive flows.
  - *Rationale*: Personal financial data is sensitive. Users need instant access without internet, and zero reliance on third-party servers.
- **PDF & CSV Direct Generation**:
  - *Decision*: Native Android `PdfDocument` and CSV string formatting with Android `FileProvider`.
  - *Rationale*: Allows users to export formal financial reports directly to WhatsApp, Gmail, or local storage without requiring external cloud PDF renderers.
- **Biometric with PIN Fallback**:
  - *Decision*: BiometricPrompt with standard fallback to a 4-digit PIN lock screen.
  - *Rationale*: Guarantees the user can always unlock their data even on devices without fingerprint sensors or inside emulators.

---

## 4. Technical Architecture & System Diagram

```
┌──────────────────────────────────────────────────────────┐
│                      UI Layer (Compose)                  │
│                                                          │
│  ┌────────────┐ ┌────────────┐ ┌───────────┐ ┌─────────┐  │
│  │ HomeScreen │ │LedgerScreen│ │ Analytics │ │  Khata  │  │
│  └──────┬─────┘ └──────┬─────┘ └─────┬─────┘ └────┬────┘  │
│         │              │             │            │       │
│         └──────────────┼─────────────┴────────────┘       │
│                        ▼                                  │
│              FinanceViewModel (StateFlow)                 │
└────────────────────────┬─────────────────────────────────┘
                         │
                         ▼
┌──────────────────────────────────────────────────────────┐
│                   Data & Repository                      │
│                                                          │
│                  FinanceRepository                       │
│             ┌──────────┴───────────┐                     │
│             ▼                      ▼                     │
│    AppDatabase (Room)     UserPreferencesRepository       │
│   (Transactions, Cats,       (DataStore Preferences)     │
│     Budgets, Khata)                                      │
└──────────────────────────────────────────────────────────┘
```

### Key Entities
1. `TransactionEntity`: ID, amount, type (INCOME/EXPENSE), category details, timestamp, payment method, note.
2. `CategoryEntity`: ID, name, type, icon name, color hex, isDefault flag.
3. `BudgetEntity`: ID, categoryId, categoryName, monthlyLimit, monthYear.
4. `DebtKhataEntity`: ID, contactName, phoneNumber, type (LEND/BORROW), amount, paidAmount, dueDateMillis, isSettled.
