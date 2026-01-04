# Hotel PMS - Quick Reference

## 🎯 What's Been Built

This is a **production-grade Hotel Property Management System (PMS)** UI with enterprise-level architecture and design.

### ✅ Completed Features

1. **Full Project Setup**
   - React 18 + Vite + TypeScript
   - Tailwind CSS with Catalyst-inspired components
   - All dependencies installed and configured

2. **Authentication & RBAC**
   - Login system with role-based access control
   - Protected routes with permission checking
   - Zustand store with persistence

3. **Dashboard Module**
   - Real-time occupancy statistics
   - Today's arrivals/departures
   - Revenue tracking
   - Quick action buttons
   - Fully responsive design

4. **UI Component Library**
   - Button (5 variants)
   - Input with validation states
   - Card components
   - Badge with color variants

5. **Core Architecture**
   - Type-safe DTOs for all entities
   - Mocked API service layer
   - Protected routing system
   - Role-based dashboard layout

## 🚀 Getting Started

### Run the Application

```bash
npm run dev
```

Open [http://localhost:5173](http://localhost:5173)

### Login Credentials

**Admin Account:**
- Email: `admin@hotel.com`
- Password: any value
- Access: Full permissions

**Front Desk Account:**
- Email: `staff@hotel.com`
- Password: any value
- Access: Limited to front desk operations

## 📂 Project Structure

```
src/
├── components/ui/        # Reusable UI components
│   ├── Button.tsx
│   ├── Input.tsx
│   ├── Card.tsx
│   ├── Badge.tsx
│   └── index.ts
│
├── layouts/
│   └── DashboardLayout.tsx   # Main dashboard wrapper
│
├── modules/
│   ├── auth/
│   │   └── LoginPage.tsx
│   ├── dashboard/
│   │   └── DashboardPage.tsx
│   ├── front-desk/           # Coming soon
│   └── reservations/         # Coming soon
│
├── routes/
│   └── ProtectedRoute.tsx    # Route guards
│
├── services/
│   └── index.ts              # Mocked API layer
│
├── stores/
│   └── authStore.ts          # Zustand auth store
│
├── types/
│   └── index.ts              # TypeScript DTOs
│
├── utils/
│   └── index.ts              # Helper functions
│
├── App.tsx                   # Main router
└── main.tsx                  # Entry point
```

## 🎨 Available Components

### Button
```tsx
import { Button } from '@/components/ui/Button';

<Button variant="primary">Click Me</Button>
<Button variant="secondary">Cancel</Button>
<Button variant="outline">Outline</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="danger">Delete</Button>
<Button size="sm" isLoading>Loading...</Button>
```

### Input
```tsx
import { Input } from '@/components/ui/Input';

<Input 
  label="Email" 
  type="email" 
  placeholder="you@hotel.com"
  error="Invalid email"
  helperText="Enter your work email"
  required
/>
```

### Card
```tsx
import { Card, CardHeader, CardTitle, CardDescription, CardContent } from '@/components/ui/Card';

<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
    <CardDescription>Description</CardDescription>
  </CardHeader>
  <CardContent>
    Content goes here
  </CardContent>
</Card>
```

### Badge
```tsx
import { Badge } from '@/components/ui/Badge';

<Badge variant="success">Active</Badge>
<Badge variant="warning">Pending</Badge>
<Badge variant="danger">Cancelled</Badge>
<Badge variant="info">Info</Badge>
```

## 🔐 RBAC System

### Permission Types
```typescript
type Permission = 
  | 'view_dashboard'
  | 'manage_reservations'
  | 'check_in'
  | 'check_out'
  | 'manage_rooms'
  | 'manage_rates'
  | 'view_reports'
  | 'manage_guests'
  | 'process_payments'
  | 'manage_housekeeping'
  | 'manage_users';
```

### Check Permissions
```tsx
import { useAuthStore } from '@/stores/authStore';

const { hasPermission, hasAnyPermission } = useAuthStore();

// Check single permission
if (hasPermission('manage_rooms')) {
  // Show room management UI
}

// Check any of multiple permissions
if (hasAnyPermission(['check_in', 'check_out'])) {
  // Show front desk operations
}
```

### Protected Routes
```tsx
import { ProtectedRoute } from '@/routes/ProtectedRoute';

<Route element={<ProtectedRoute requiredPermissions={['manage_rooms']} />}>
  <Route path="/rooms" element={<RoomsPage />} />
</Route>
```

## 📊 Mock Data Services

All API calls are mocked and ready for backend integration:

```typescript
import { reservationService, roomService, guestService } from '@/services';

// Get all reservations
const reservations = await reservationService.getAll();

// Get available rooms
const rooms = await roomService.getAvailable('2026-01-01', '2026-01-05');

// Search guests
const guests = await guestService.search('John');

// Create new reservation
const newReservation = await reservationService.create({
  guest: guestData,
  rooms: [room],
  checkIn: '2026-01-01',
  checkOut: '2026-01-05',
  // ...
});
```

## 🎯 Next Steps

### Immediate Tasks
1. **Build Reservations Module**
   - Reservation list with filters
   - Create/edit reservation form
   - Confirmation workflow

2. **Build Front Desk Module**
   - Check-in process
   - Check-out with billing
   - Room assignment

3. **Build Guest Management**
   - Guest directory
   - Guest profile details
   - Stay history

### Future Enhancements
- Room management & housekeeping
- Reports & analytics
- Rate management
- Multi-property support
- Real backend integration

## 🛠️ Development Tips

### Adding a New Module

1. **Create the module folder**
   ```
   src/modules/your-feature/
   ```

2. **Define types** in `src/types/index.ts`

3. **Create service** in `src/services/`

4. **Build UI components** in the module folder

5. **Add routes** in `src/App.tsx`

6. **Update navigation** in `src/layouts/DashboardLayout.tsx`

### Tailwind CSS Classes
All components use Tailwind. Common patterns:
- Spacing: `p-4`, `m-6`, `space-y-4`
- Colors: `text-gray-900`, `bg-primary-600`
- Layout: `flex`, `grid`, `grid-cols-4`
- Responsive: `sm:`, `md:`, `lg:`, `xl:`

## 📱 Responsive Design

The app is fully responsive with breakpoints:
- **Mobile**: < 640px
- **Tablet**: 640px - 1024px
- **Desktop**: > 1024px

Sidebar collapses to hamburger menu on mobile.

## 🎨 Design System

### Colors
- **Primary**: Blue (600, 700, etc.)
- **Success**: Green
- **Warning**: Yellow
- **Danger**: Red
- **Neutral**: Gray scale

### Typography
- Headings: `font-bold`
- Body: `font-normal`
- Small: `text-sm`, `text-xs`

### Spacing
Consistent 4px grid: `p-4`, `p-6`, `gap-4`, etc.

## 🧪 Testing the App

1. Start the dev server: `npm run dev`
2. Open `http://localhost:5173`
3. Login with `admin@hotel.com` or `staff@hotel.com`
4. Explore the dashboard
5. Check responsive behavior (resize window)
6. Try different user roles

## 📦 Build & Deploy

```bash
# Build for production
npm run build

# Preview production build
npm run preview
```

Output in `dist/` folder.

---

**Questions?** Check the main [README.md](README.md) or review the codebase!
