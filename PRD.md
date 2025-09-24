# Product Requirements Document (PRD)
## Personal Expense Tracker v2

### 1. Product Overview

**Vision**: A single-page expense tracking application that provides monthly income, expense, and savings management with a hybrid income/cashflow statement view.

**Core Features**:
- Monthly financial dashboard with income, expenses, and savings sections
- Supabase authentication (email/password, no confirmation)
- Custom categories and items management
- Side-by-side month comparison (desktop)
- Modal-based data entry
- Auto-save with visual indicators
- Dark mode support
- Mobile-responsive design

**Technical Stack**:
- **Frontend**: Next.js 15, TypeScript, Tailwind CSS, shadcn/ui
- **Backend**: Supabase (Auth, Database, RLS)
- **State**: React Context + local state
- **Currency**: KRW (₩) format with thousand separators

---

## 2. Detailed Functional Requirements

### 2.1 Historical Data & Navigation
- Users can navigate through entire history of months
- All historical data remains editable
- Month navigation shows previous/current/next structure

### 2.2 Income Categories & Structure
- Custom income categories (user-defined)
- Income items organized under categories
- Beginning cash as separate item within starting balance section
- Manual entry required (no auto-calculation from previous month)

### 2.3 Expense Management
- Custom expense categories (user-defined)
- One category per expense item (strict hierarchy)
- Single-depth category structure only
- Categories can be reordered using up/down arrows
- All expense types treated equally (credit cards, direct expenses)

### 2.4 Currency & Formatting
- KRW currency format: ₩1,234,567
- No decimal places for amounts
- Negative amounts allowed
- Percentages display with one decimal place (54.9%)
- Conditional formatting: green for positive, red for negative balances
- Empty amounts display as "—" instead of ₩0

### 2.5 Auto-save Behavior
- Auto-save triggers immediately on field blur
- Visual "Saving..." indicator during save operations
- Error messages with retry on save failures
- Debouncing not required (immediate save on blur)

### 2.6 Data Persistence Rules
- Hard delete operations (no soft delete/trash)
- Manual carry-over from previous month (no automatic)
- New months copy structure but not amounts
- Confirmation required for all delete operations

### 2.7 User Interface Specifications
- Desktop: side-by-side month view (3 months default)
- Mobile: single month with prev/next navigation
- Modal-based data entry (not inline forms)
- Manual dark mode toggle
- Empty state: completely blank for new users
- Current month auto-created for new users

---

## 3. User Interface Specifications

### 3.1 Desktop Layout (≥768px)
```
┌──────────────────────────────────────────────────────────────┐
│  Header: [Month Navigation] [Dark Mode] [User Menu]          │
├──────────────────────────────────────────────────────────────┤
│  September 2025    │    October 2025     │   November 2025   │
│  ────────────────  │  ─────────────────  │  ───────────────  │
│  📊 Income         │  📊 Income          │  📊 Income        │
│  💳 Expenses       │  💳 Expenses        │  💳 Expenses      │
│  🏦 Savings        │  🏦 Savings         │  🏦 Savings       │
│  📊 Balances       │  📊 Balances        │  📊 Balances      │
└──────────────────────────────────────────────────────────────┘
```

### 3.2 Mobile Layout (<768px)
```
┌─────────────────────────────┐
│ Header: [🌙] [👤]           │
├─────────────────────────────┤
│ [◀] October 2025 [▶]        │
│ [+ Add Month]               │
├─────────────────────────────┤
│ 📊 Income Section           │
│ 💳 Expenses Section         │
│ 🏦 Savings Section          │
│ 📊 Balance Summary          │
└─────────────────────────────┘
```

### 3.3 Section Structure

**Income Section**:
```
📊 수입 (Income)                                    ₩4,584,096
─────────────────────────────────────────────────────────────
▼ 시작 잔액 (Starting Balance)        ₩250,000 (5.5%) [↑][↓]
  • 이월금 (Previous Month)      ₩250,000 [Edit] [Delete]
                                          [+ Add Item]

▼ 월급 (Salary)                      ₩4,334,096 (94.5%) [↑][↓]
  • 본봉 (Base Salary)            ₩4,334,096 [Edit] [Delete]
                                          [+ Add Item]

                                          [+ Add Category]
─────────────────────────────────────────────────────────────
Income Total:                                       ₩4,584,096
```

**Expenses Section**:
```
💳 지출 (Expenses)                                  ₩3,052,995
─────────────────────────────────────────────────────────────
▼ 현대카드 (Hyundai Card)         ₩2,650,000 (86.8%) [↑][↓]
  • 생활비                        ₩2,500,000 [Edit] [Delete]
  • 기타                            ₩150,000 [Edit] [Delete]
                                          [+ Add Item]
  Subtotal:                                         ₩2,650,000

▼ 정기 지출 (Regular)                ₩298,276 (9.8%) [↑][↓]
  • 청약                             ₩50,000 [Edit] [Delete]
  • 보험                             ₩29,000 [Edit] [Delete]
                                          [+ Add Item]
  Subtotal:                                           ₩298,276

                                          [+ Add Category]
─────────────────────────────────────────────────────────────
Expense Total:                                      ₩3,052,995
```

**Savings Section**:
```
🏦 저축 (Savings)                                     ₩900,000
─────────────────────────────────────────────────────────────
▼ 재정 지원                               ₩900,000 (100%) [↑][↓]
  • 관리비
    Goal: ₩300,000 | 100% | Saved: ₩900,000 | Left: ₩0 | [₩300,000]
  • 지원금
    Goal: ₩600,000 | 100% | Saved: ₩1,800,000 | Left: ₩0 | [₩600,000]
                                          [+ Add Item]
  Subtotal:                                           ₩900,000

                                          [+ Add Category]
─────────────────────────────────────────────────────────────
Savings Total:                                        ₩900,000
```

**Balance Summary**:
```
📊 Balance Before Savings:                 ₩1,531,101 (green)
📊 Final Balance:                            ₩631,101 (green)
```

### 3.4 Modal Interface

**Add/Edit Item Modal**:
```
┌─────────────────────────────────────┐
│  Add Income Item                ✕   │
├─────────────────────────────────────┤
│  Name: [_________________________]  │
│  Amount: [_______________________]  │
│  Note: [________________________]  │
│  (optional)                         │
│                                     │
│           [Cancel] [Save]           │
│           (ESC)    (Enter)          │
└─────────────────────────────────────┘
```

**Add/Edit Savings Item Modal**:
```
┌─────────────────────────────────────┐
│  Add Savings Goal               ✕   │
├─────────────────────────────────────┤
│  Name: [_________________________]  │
│  Goal Amount: [__________________]  │
│  This Month: [___________________]  │
│  Note: [________________________]  │
│  (optional)                         │
│                                     │
│           [Cancel] [Save]           │
└─────────────────────────────────────┘
```

---

## 4. Data Model

### 4.1 Entity Relationships
```
User (1) ──→ (Many) Month
Month (1) ──→ (Many) Category
Category (1) ──→ (Many) Item
Category (1) ──→ (Many) SavingsItem
```

### 4.2 Data Structures

**User**:
- id (uuid, primary key)
- email (string, unique)
- created_at (timestamp)

**Month**:
- id (uuid, primary key)
- user_id (uuid, foreign key)
- year (integer)
- month (integer)
- created_at (timestamp)
- unique constraint: (user_id, year, month)

**Category**:
- id (uuid, primary key)
- month_id (uuid, foreign key)
- name (string)
- type (enum: 'income', 'expense', 'savings')
- sort_order (integer)
- created_at (timestamp)

**Item**:
- id (uuid, primary key)
- category_id (uuid, foreign key)
- name (string)
- amount (bigint) // in KRW, no decimals
- note (text, optional)
- sort_order (integer)
- created_at (timestamp)

**SavingsItem**:
- id (uuid, primary key)
- category_id (uuid, foreign key)
- name (string)
- goal_amount (bigint)
- monthly_amount (bigint)
- total_saved (bigint) // cumulative across months
- note (text, optional)
- sort_order (integer)
- created_at (timestamp)

### 4.3 Calculation Rules

**Income Total**: Sum of all items in income categories
**Expense Total**: Sum of all items in expense categories
**Balance Before Savings**: Income Total - Expense Total
**Savings Total**: Sum of monthly_amount for all savings items
**Final Balance**: Balance Before Savings - Savings Total

**Category Percentages**: (Category Total / Section Total) × 100
**Savings Progress**: (Total Saved / Goal Amount) × 100

---

## 5. Indexed Epics & User Stories

### EPIC-001: Authentication & User Management
**Acceptance Criteria**: Users can sign up, log in, and maintain secure sessions

**User Stories**:
- **US-001.1**: As a user, I can sign up with email/password without email confirmation
- **US-001.2**: As a user, I can log in with my credentials
- **US-001.3**: As a user, I can log out from my session
- **US-001.4**: As a user, I remain logged in across browser sessions
- **US-001.5**: As a user, my data is isolated from other users via RLS

---

### EPIC-002: Dashboard Layout & Navigation
**Acceptance Criteria**: Responsive dashboard with month navigation and proper sections

**User Stories**:
- **US-002.1**: As a user, I see three main sections (Income, Expenses, Savings) on my dashboard
- **US-002.2**: As a user, I can navigate between months using prev/next buttons
- **US-002.3**: As a user, I can add new months sequentially with explicit action
- **US-002.4**: As a desktop user, I see three months side-by-side by default
- **US-002.5**: As a mobile user, I see single month with navigation controls
- **US-002.6**: As a user, I can toggle dark mode manually
- **US-002.7**: As a new user, current month is automatically created
- **US-002.8**: As a user, I cannot create duplicate months

---

### EPIC-003: Income Management
**Acceptance Criteria**: Full CRUD operations for income categories and items

**User Stories**:
- **US-003.1**: As a user, I can create custom income categories
- **US-003.2**: As a user, I can add income items under categories via modal
- **US-003.3**: As a user, I can edit income items by clicking them (modal opens)
- **US-003.4**: As a user, I can delete income items with confirmation popup
- **US-003.5**: As a user, I can reorder categories using up/down arrows
- **US-003.6**: As a user, I see percentage breakdown of each category within income
- **US-003.7**: As a user, I see total income at section bottom
- **US-003.8**: As a user, I can add optional notes to income items
- **US-003.9**: As a user, beginning cash is manual entry (no auto-calculation)
- **US-003.10**: As a user, empty categories display "—" not ₩0

---

### EPIC-004: Expense Management
**Acceptance Criteria**: Full CRUD operations for expense categories and items with subtotals

**User Stories**:
- **US-004.1**: As a user, I can create custom expense categories
- **US-004.2**: As a user, I can add expense items under categories via modal
- **US-004.3**: As a user, I can edit expense items by clicking them (modal opens)
- **US-004.4**: As a user, I can delete expense items with confirmation popup
- **US-004.5**: As a user, I can reorder categories using up/down arrows
- **US-004.6**: As a user, I see subtotal for each expense category
- **US-004.7**: As a user, I see percentage of total expenses for each category
- **US-004.8**: As a user, I see total expenses at section bottom
- **US-004.9**: As a user, I can add optional notes to expense items
- **US-004.10**: As a user, each item belongs to exactly one category
- **US-004.11**: As a user, categories show "—" when empty instead of ₩0

---

### EPIC-005: Savings Management
**Acceptance Criteria**: Savings goals with progress tracking and allocations

**User Stories**:
- **US-005.1**: As a user, I can create custom savings categories
- **US-005.2**: As a user, I can add savings items with goal amounts via modal
- **US-005.3**: As a user, I can allocate monthly amounts to savings items
- **US-005.4**: As a user, I see: Goal | Progress% | Total Saved | Remaining | Monthly Allocation
- **US-005.5**: As a user, total saved accumulates across all months
- **US-005.6**: As a user, I can edit/delete savings items with confirmation
- **US-005.7**: As a user, I see total savings allocation at section bottom
- **US-005.8**: As a user, savings allocation immediately reduces final balance
- **US-005.9**: As a user, monthly allocation matches what appears in dashboard

---

### EPIC-006: Balance Calculations & Display
**Acceptance Criteria**: Accurate calculations with proper formatting

**User Stories**:
- **US-006.1**: As a user, I see "Income - Expenses" balance before savings
- **US-006.2**: As a user, I see "Income - Expenses - Savings" as final balance
- **US-006.3**: As a user, positive balances show in green color
- **US-006.4**: As a user, negative balances show in red color
- **US-006.5**: As a user, amounts display as ₩1,234,567 format (with commas)
- **US-006.6**: As a user, percentages show with one decimal place (54.9%)
- **US-006.7**: As a user, empty amounts show "—" instead of 0
- **US-006.8**: As a user, calculations update in real-time

---

### EPIC-007: Modal Data Entry System
**Acceptance Criteria**: Consistent modal interface for all data entry

**User Stories**:
- **US-007.1**: As a user, clicking "Add Item" opens a modal
- **US-007.2**: As a user, clicking existing items opens edit modal
- **US-007.3**: As a user, modal contains Name, Amount, and optional Note fields
- **US-007.4**: As a user, pressing Enter saves the item
- **US-007.5**: As a user, pressing ESC cancels without saving
- **US-007.6**: As a user, I see validation errors inline in the modal
- **US-007.7**: As a user, modal has clear Save/Cancel buttons
- **US-007.8**: As a user, modal prevents invalid data submission
- **US-007.9**: As a user, savings modal includes Goal Amount field

---

### EPIC-008: Data Persistence & Auto-save
**Acceptance Criteria**: Reliable data saving with visual feedback

**User Stories**:
- **US-008.1**: As a user, changes auto-save immediately on field blur
- **US-008.2**: As a user, I see "Saving..." indicator during save operations
- **US-008.3**: As a user, I see error messages if save fails
- **US-008.4**: As a user, failed saves retry automatically
- **US-008.5**: As a user, I can edit data from any historical month
- **US-008.6**: As a user, previous month carry-over requires manual entry
- **US-008.7**: As a user, hard deletes remove data permanently
- **US-008.8**: As a user, all operations require confirmation before delete

---

### EPIC-009: Month Management
**Acceptance Criteria**: Seamless month creation and navigation

**User Stories**:
- **US-009.1**: As a user, "Add Month" creates next sequential month only
- **US-009.2**: As a user, new months copy category structure but not amounts
- **US-009.3**: As a user, I manually enter previous month carry-over amounts
- **US-009.4**: As a user, I cannot create duplicate months
- **US-009.5**: As a user, I can view entire history of months
- **US-009.6**: As a user, month navigation is smooth and responsive
- **US-009.7**: As a user, new month creation requires explicit action
- **US-009.8**: As a user, desktop view shows side-by-side comparison

---

### EPIC-010: Database Schema & Security
**Acceptance Criteria**: Efficient data structure with proper relationships and security

**User Stories**:
- **US-010.1**: Database stores users, months, categories, items, and savings
- **US-010.2**: RLS policies ensure complete data isolation between users
- **US-010.3**: Categories maintain user-defined sort order
- **US-010.4**: Items properly link to categories and months
- **US-010.5**: Savings track cumulative progress across months
- **US-010.6**: Efficient queries support month comparisons
- **US-010.7**: All foreign key relationships maintain data integrity
- **US-010.8**: Database handles KRW amounts as integers (no decimals)

---

## 6. Technical Specifications

### 6.1 Component Architecture

**Core Components**:
- `DashboardLayout` - Main responsive container
- `MonthSelector` - Navigation and month management
- `IncomeSection` - Income categories and items display
- `ExpenseSection` - Expense categories with subtotals
- `SavingsSection` - Savings goals with progress tracking
- `BalanceSummary` - Balance calculations display
- `ItemModal` - Universal add/edit modal
- `CategoryCard` - Reusable category container
- `ItemRow` - Individual item display with actions

**Shared Components**:
- `Button` - Consistent button styling
- `Input` - Form input with validation
- `Modal` - Base modal component
- `LoadingSpinner` - Loading state indicator
- `ThemeToggle` - Dark mode switch

### 6.2 State Management

**Context Structure**:
```typescript
interface AppState {
  currentMonth: string // "2025-10"
  months: MonthData[]
  darkMode: boolean
  user: User | null
  loading: boolean
  error: string | null
}
```

**Data Flow**:
1. User actions trigger state updates
2. Optimistic UI updates for immediate feedback
3. Auto-save on blur events
4. Error handling with retry logic
5. Real-time calculation updates

### 6.3 API Design

**Supabase RLS Policies**:
```sql
-- Users can only access their own data
CREATE POLICY user_isolation ON months
  FOR ALL TO authenticated
  USING (user_id = auth.uid());

CREATE POLICY user_isolation ON categories
  FOR ALL TO authenticated
  USING (month_id IN (
    SELECT id FROM months WHERE user_id = auth.uid()
  ));
```

**Key Functions**:
- `getMonthData(userId, year, month)` - Fetch complete month data
- `saveItem(item)` - Upsert item with auto-save
- `deleteItem(itemId)` - Hard delete with confirmation
- `updateCategoryOrder(categoryId, newOrder)` - Reorder categories
- `createMonth(userId, year, month)` - Initialize new month

### 6.4 Validation Rules

**Amount Validation**:
- Integer values only (no decimals)
- Negative values allowed
- Maximum value: 999,999,999 KRW
- Display format: ₩1,234,567

**Required Fields**:
- Item name (max 100 characters)
- Amount (required for all items)
- Goal amount (required for savings items)

**Business Rules**:
- One category per item (strict hierarchy)
- Unique month per user (year, month combination)
- Sequential month creation only
- Manual carry-over entry required

---

## 7. Notes & Clarifications

### 7.1 Key Design Decisions
- Modal input instead of inline editing for better UX consistency
- Hard delete to maintain data cleanliness
- Manual carry-over to ensure user awareness of balances
- Side-by-side comparison as default desktop view
- Auto-save on blur for seamless experience

### 7.2 Future Considerations
- This PRD focuses on core functionality
- Advanced features (recurring items, budgets, reports) deliberately excluded
- Single-user design (no sharing/collaboration)
- KRW-only support initially

### 7.3 Success Metrics
- User can complete full month setup in under 5 minutes
- Data entry feels natural and responsive
- Month-to-month navigation is smooth
- All calculations display correctly in real-time
- Mobile experience matches desktop functionality