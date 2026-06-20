# Docmobi Admin Dashboard

A pixel-perfect, fully functional healthcare admin dashboard built with Next.js, TypeScript, TanStack Query, and shadcn/ui.

## Features

### ✨ Core Features
- **User Authentication**: NextAuth implementation with login, forgot password, and OTP verification
- **Dashboard Overview**: Real-time stats with weekly trends chart using Recharts
- **Doctor Management**: Approve/reject registrations, manage doctor profiles with pagination
- **Patient Management**: View all registered patients with status management (Active/Block)
- **Appointment Management**: Track all appointments with cancellation capability
- **Earnings Tracking**: Monitor doctor-wise earnings and appointment history
- **Category Management**: Add, edit, and manage medical specialties with status toggle
- **Referral System**: Manage referral codes and toggle the referral system globally

For more detailed technical information, please refer to [TECHNICAL_DOCUMENTATION.md](./TECHNICAL_DOCUMENTATION.md).

### 🎨 Design & UX
- **Responsive Design**: Fully mobile-responsive with collapsible sidebar
- **Skeleton Loaders**: Professional loading states for all data tables
- **Toast Notifications**: Real-time feedback using Sonner
- **Material Design**: Built with shadcn/ui components
- **Blue Gradient Theme**: Professional healthcare color scheme

### 🔧 Technical Stack
- **Framework**: Next.js 16 with App Router
- **Data Fetching**: TanStack Query (React Query) v5
- **HTTP Client**: Axios with token interceptors
- **UI Components**: shadcn/ui with Tailwind CSS v4
- **Charts**: Recharts for data visualization
- **Notifications**: Sonner for toasts
- **Icons**: Lucide React

## Environment Setup

Create a `.env.local` file in the root directory:

```env
NEXT_PUBLIC_BASE_URL=http://localhost:5000/api/v1
NEXT_PUBLIC_SOCKET_URL=http://localhost:5000
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=replace-with-a-long-random-secret
```

Replace with your actual API base URL.

MongoDB is configured in the backend app, not in this dashboard. For local development, set this in `../aroggyapath-backend/.env`:

```env
MONGO_DB_URL=mongodb://127.0.0.1:27017/aroggyapath
PORT=5000
```

## API Integration

### Authentication Flow

All API requests automatically include the JWT token via axios interceptor:

```typescript
// Login
POST /auth/login
Body: { email: "admin@example.com", password: "123456" }
Response: { accessToken, refreshToken }

// Forgot Password
POST /auth/forget
Body: { email: "admin@example.com" }

// Verify OTP
POST /auth/verify-otp
Body: { email, otp }

// Reset Password
POST /auth/reset-password
Body: { email, otp, password }

// Change Password
POST /auth/change-password
Body: { oldPassword, newPassword }
```

### API Endpoints

**Dashboard**
- `GET /user/dashboard/overview` - Dashboard stats and weekly trends

**Doctors**
- `GET /user/role/doctor` - List all doctors with pagination
- `GET /user/{id}` - Get doctor details
- `PATCH /user/doctor/{id}/approval` - Approve/reject doctor registration
- `PATCH /user/doctor/{id}` - Update doctor profile
- `DELETE /user/doctor/{id}` - Delete doctor

**Patients**
- `GET /user/role/patient` - List all patients with pagination
- `GET /user/{id}` - Get patient details
- `PATCH /user/patient/{id}` - Update patient status
- `DELETE /user/patient/{id}` - Delete patient

**Appointments**
- `GET /appointment` - List all appointments with pagination
- `GET /appointment/{id}` - Get appointment details
- `PATCH /appointment/{id}` - Update appointment
- `PATCH /appointment/{id}/cancel` - Cancel appointment

**Categories**
- `GET /category` - List all categories with pagination
- `POST /category` - Create new category
- `GET /category/{id}` - Get category details
- `PATCH /category/{id}` - Update category
- `DELETE /category/{id}` - Delete category

**Earnings**
- `GET /earnings/overview` - Get earnings summary
- `GET /earnings/doctors` - List doctor earnings with pagination

## Project Structure

```
app/
├── login/
│   └── page.tsx              # Login page
├── forgot-password/
│   └── page.tsx              # Forgot password flow (email → OTP → reset)
├── dashboard/
│   ├── layout.tsx            # Dashboard layout with sidebar
│   ├── page.tsx              # Dashboard home with overview stats
│   ├── doctors/
│   │   └── page.tsx          # Doctor management
│   ├── patients/
│   │   └── page.tsx          # Patient management
│   ├── appointments/
│   │   └── page.tsx          # Appointment management
│   ├── earnings/
│   │   └── page.tsx          # Earnings tracking
│   ├── categories/
│   │   └── page.tsx          # Category management
│   └── settings/
│       └── page.tsx          # Settings and password change
├── page.tsx                  # Root redirect
└── layout.tsx                # Root layout with providers

components/
├── providers.tsx             # QueryClientProvider setup
├── dashboard-sidebar.tsx     # Sidebar navigation
└── skeletons.tsx            # Loading skeletons

lib/
└── api.ts                    # Axios instance with interceptors
```

## Key Features Breakdown

### 1. Token Management
- Access tokens sent via Authorization header
- Automatic token refresh on 401 response
- Tokens stored in localStorage
- Logout clears stored tokens

### 2. Data Fetching with TanStack Query
- Automatic caching (5-minute stale time)
- Pagination support on all list endpoints
- Real-time invalidation on mutations
- Optimistic updates for better UX

### 3. Form Validation
- Client-side validation on all forms
- Server-side error handling with toast notifications
- Password strength validation
- Email format validation

### 4. Responsive Design
- Mobile-first approach
- Collapsible sidebar on mobile
- Touch-friendly buttons and controls
- Optimized table layouts for small screens

## Usage Examples

### Fetching Data with Query

```typescript
const { data, isLoading, error } = useQuery({
  queryKey: ["doctors", page, search, status],
  queryFn: () => doctorsAPI.getDoctors(page, 10, search, status),
});
```

### Mutating Data

```typescript
const mutation = useMutation({
  mutationFn: (id: string) => doctorsAPI.approveDoctor(id, "approved"),
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ["doctors"] });
    toast.success("Doctor approved");
  },
});
```

### Pagination

```typescript
const [page, setPage] = useState(1);
const ITEMS_PER_PAGE = 10;

const { data } = useQuery({
  queryKey: ["items", page],
  queryFn: () => api.getItems(page, ITEMS_PER_PAGE),
});

const totalPages = Math.ceil(data?.pagination?.total / ITEMS_PER_PAGE);
```

## Customization

### Theme Colors
Edit `app/globals.css` to customize the color scheme. Currently using blue healthcare theme.

### API Base URL
Update `NEXT_PUBLIC_BASE_URL` in `.env.local` to point to your backend.

### Pagination Size
Change `ITEMS_PER_PAGE` constant in each page component.

### Toast Position
Modify toast position in `components/providers.tsx`.

## Error Handling

All API errors are automatically caught and displayed as toast notifications:

```typescript
onError: (error: any) => {
  toast.error(error.response?.data?.message || "Failed to perform action");
}
```

## Performance Optimizations

- Skeleton loaders for smooth perceived performance
- TanStack Query caching to reduce API calls
- Optimized re-renders with React Query
- Code splitting with Next.js dynamic imports
- Image optimization with next/image

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)
- Mobile browsers (iOS Safari, Chrome Mobile)

## License

MIT

## Support

For issues or questions, please check the API documentation or contact support.
