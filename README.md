# Hotel PMS - Property Management System

A production-grade Hotel Property Management System UI built with modern web technologies, inspired by enterprise PMS solutions like IDS Next.

## 🚀 Tech Stack

- **Frontend Framework**: React 18 with TypeScript
- **Build Tool**: Vite
- **Styling**: Tailwind CSS with custom Catalyst UI components
- **State Management**: Zustand
- **Routing**: React Router v6 with protected routes
- **Form Validation**: React Hook Form + Zod
- **Date Handling**: date-fns
- **Icons**: lucide-react
- **API Layer**: Mocked services (backend-ready architecture)

## ✨ Features

### Implemented
- ✅ **Authentication System** - Role-based login with RBAC
- ✅ **Dashboard** - Real-time stats, occupancy tracking, today's arrivals/departures
- ✅ **Responsive Layout** - Mobile, tablet, and desktop optimized
- ✅ **Protected Routes** - Permission-based access control
- ✅ **Mock Data Layer** - Fully functional with realistic hotel data
- ✅ **Enterprise UI** - Catalyst-inspired components matching IDS Next quality

### Coming Soon
- 🔄 Reservations Management
- 🔄 Front Desk Operations (Check-in/Check-out)
- 🔄 Guest Directory
- 🔄 Room Management & Housekeeping
- 🔄 Reports & Analytics
- 🔄 Settings & Configuration

## 📁 Project Structure

```
src/
├── components/        # Reusable UI components
│   └── ui/           # Base UI elements (Button, Input, Card, Badge)
├── layouts/          # Layout wrappers (DashboardLayout)
├── modules/          # Feature modules
│   ├── auth/        # Authentication
│   ├── dashboard/   # Dashboard
│   ├── reservations/
│   ├── front-desk/
│   └── ...
├── routes/          # Route protection & guards
├── services/        # API contracts & mocked implementations
├── stores/          # Zustand state management
├── types/           # TypeScript DTOs and interfaces
└── utils/           # Helper functions
```

## 🚦 Getting Started

### Prerequisites
- Node.js 18+ and npm

### Installation

1. **Install dependencies**:
   ```bash
   npm install
   ```

2. **Start development server**:
   ```bash
   npm run dev
   ```

3. **Open your browser** at `http://localhost:5173`

### Demo Accounts

Login with these credentials:

| Email | Role | Permissions |
|-------|------|-------------|
| `admin@hotel.com` | Admin | Full access to all features |
| `staff@hotel.com` | Front Desk | Reservations, check-in/out, guest management |

*Password: any value (mock authentication)*

## 🎨 UI Components

The project includes custom Catalyst-inspired components:

- **Button** - Primary, secondary, outline, ghost, and danger variants
- **Input** - Form inputs with validation states
- **Card** - Content containers with header, body, and footer
- **Badge** - Status indicators with color variants

All components are fully typed, accessible, and responsive.

## 🔐 RBAC System

The application implements Role-Based Access Control at the UI level:

- **Roles**: Admin, Manager, Front Desk, Housekeeping, Guest
- **Permissions**: View dashboard, manage reservations, check-in/out, etc.
- **Protected Routes**: Automatically redirect unauthorized users

## 📊 Dashboard Features

- **Occupancy Stats** - Real-time room availability and occupancy rates
- **Revenue Tracking** - Daily revenue with target comparisons
- **Today's Activity** - Arrivals and departures with guest details
- **Quick Actions** - One-click access to common tasks
- **Responsive Design** - Works seamlessly on all devices

## 🛠️ Development

### Build for Production
```bash
npm run build
```

### Preview Production Build
```bash
npm run preview
```

## 🎯 Architecture Principles

1. **Headless Architecture** - All API calls are abstracted in the services layer
2. **Type Safety** - Strict TypeScript with comprehensive DTOs
3. **Component Isolation** - Reusable, composable UI components
4. **State Management** - Zustand for global state with persistence
5. **Mock-First Development** - Realistic mocked data for immediate functionality
6. **Mobile-First Design** - Responsive from the ground up
7. **Enterprise Quality** - Production-grade code matching IDS Next/Hotelogix standards

## 📝 Adding New Features

### 1. Create Types
Define DTOs in `src/types/index.ts`

### 2. Create Service
Add mocked API functions in `src/services/`

### 3. Create Store (if needed)
Add Zustand store in `src/stores/`

### 4. Create Components
Build UI in `src/modules/[feature]/`

### 5. Add Routes
Register routes in `src/App.tsx`

---

Built with ❤️ for the hospitality industry
