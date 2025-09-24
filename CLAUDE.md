# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Personal Expense Tracker v2 - a single-page application built with Next.js 15, TypeScript, and Supabase. The application provides monthly income, expense, and savings management with a hybrid income/cashflow statement view. See `PRD.md` for comprehensive product requirements and specifications.

## Development Commands

```bash
# Development server with Turbopack
npm run dev

# Build for production with Turbopack
npm run build

# Start production server
npm run start

# Lint code
npm run lint
```

The application runs on http://localhost:3000 by default.

## Architecture Overview

### Tech Stack
- **Frontend**: Next.js 15 App Router, TypeScript, Tailwind CSS
- **UI Components**: shadcn/ui with "new-york" style
- **Backend**: Supabase (Auth, Database, RLS)
- **State Management**: React Context + local state
- **Icons**: Lucide React
- **Styling**: Tailwind CSS with CSS variables

### Project Structure
```
src/
├── app/                 # Next.js App Router pages
├── components/          # Reusable UI components
│   └── ui/             # shadcn/ui components
├── lib/                # Utilities and configurations
│   ├── utils.ts        # Tailwind merge utilities
│   └── supabase.ts     # Supabase client configuration
└── hooks/              # Custom React hooks
```

### Key Design Patterns

**Component Architecture**:
- Modal-based data entry (not inline editing)
- Responsive design: side-by-side months (desktop) vs single month (mobile)
- Auto-save on blur with visual feedback
- Hard delete operations with confirmation

**Data Flow**:
1. User actions → Optimistic UI updates
2. Auto-save on field blur
3. Error handling with retry logic
4. Real-time calculation updates

**State Structure**:
```typescript
interface AppState {
  currentMonth: string    // "2025-10"
  months: MonthData[]
  darkMode: boolean
  user: User | null
  loading: boolean
  error: string | null
}
```

### Supabase Configuration

Environment variables required:
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`

The Supabase client is configured in `src/lib/supabase.ts` for authentication and database operations.

### Business Logic Requirements

**Currency & Formatting**:
- KRW currency format: ₩1,234,567 (with thousand separators)
- Integer values only (no decimals)
- Negative amounts allowed
- Empty values display as "—" instead of ₩0
- Percentage format: 54.9% (one decimal place)

**Calculation Rules**:
- Income Total = Sum of all income items
- Expense Total = Sum of all expense items
- Balance Before Savings = Income - Expenses
- Final Balance = Income - Expenses - Savings
- Category percentages = (Category Total / Section Total) × 100

**Data Relationships**:
- User → Months → Categories → Items
- Categories have type: 'income', 'expense', 'savings'
- Items belong to exactly one category
- Savings items track cumulative progress across months

### Component Guidelines

**Core Components to Build**:
- `DashboardLayout` - Main responsive container
- `MonthSelector` - Navigation and month management
- `IncomeSection` - Income categories and items
- `ExpenseSection` - Expense categories with subtotals
- `SavingsSection` - Savings goals with progress
- `BalanceSummary` - Balance calculations display
- `ItemModal` - Universal add/edit modal
- `CategoryCard` - Reusable category container

**Modal System**:
- ESC to cancel, Enter to save
- Validation with inline error messages
- Fields: Name (required), Amount (required), Note (optional)
- Savings modals include Goal Amount field

### Development Guidelines

**Authentication Flow**:
- Email/password auth via Supabase (no email confirmation)
- Protected routes using Supabase session
- Row Level Security (RLS) for data isolation

**Month Management**:
- Sequential month creation only ("Add Month" creates next month)
- New months copy category structure but not amounts
- Manual carry-over entry required (no auto-calculation)
- Historical months remain editable

**Responsive Behavior**:
- Desktop (≥768px): 3-month side-by-side comparison view
- Mobile (<768px): Single month with prev/next navigation
- Dark mode toggle (manual, not system-based)

### Database Schema

Key tables and relationships defined in PRD.md:
- `users` (auth)
- `months` (user_id, year, month)
- `categories` (month_id, name, type, sort_order)
- `items` (category_id, name, amount, note)
- `savings_items` (category_id, goal_amount, monthly_amount, total_saved)

All tables use RLS policies to ensure user data isolation.