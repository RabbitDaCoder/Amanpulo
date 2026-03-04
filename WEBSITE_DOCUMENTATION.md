# Hotel Booking Website - Complete Documentation

> A comprehensive guide to building a luxury hotel/resort booking website with admin management portal.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Technology Stack](#technology-stack)
3. [Project Structure](#project-structure)
4. [Database Models](#database-models)
5. [Backend API](#backend-api)
6. [Frontend Architecture](#frontend-architecture)
7. [Authentication System](#authentication-system)
8. [Booking Flow](#booking-flow)
9. [Admin Portal](#admin-portal)
10. [Email System](#email-system)
11. [Live Chat Support](#live-chat-support)
12. [State Management](#state-management)
13. [UI Components](#ui-components)
14. [Deployment Configuration](#deployment-configuration)
15. [Environment Variables](#environment-variables)

---

## Project Overview

This is a **full-stack hotel booking reservation system** with the following core features:

### Public Features (Guests)
- Browse available rooms/accommodations
- Filter rooms by category, price, guest capacity
- View room details with image gallery
- Select check-in/check-out dates
- Create bookings with guest information
- Receive booking confirmation via verification code
- Download/email booking receipts as PDF
- Live chat support integration

### Admin Features (Owner Portal)
- Secure login with JWT authentication
- Dashboard with revenue and booking statistics
- Manage rooms (CRUD operations)
- Set seasonal discounts on rooms
- View and manage all bookings
- Update booking statuses (confirm, cancel)
- Search and filter bookings

---

## Technology Stack

### Backend
| Technology | Purpose |
|------------|---------|
| **Node.js** | Runtime environment |
| **Express.js** | Web framework |
| **MongoDB** | Database (NoSQL) |
| **Mongoose** | ODM for MongoDB |
| **JWT** | Authentication tokens |
| **bcryptjs** | Password hashing |
| **Nodemailer** | Email sending |
| **Helmet** | Security headers |
| **CORS** | Cross-origin requests |
| **Morgan** | HTTP request logging |

### Frontend
| Technology | Purpose |
|------------|---------|
| **React 18** | UI library |
| **Vite** | Build tool & dev server |
| **React Router v6** | Client-side routing |
| **Zustand** | State management |
| **Tailwind CSS** | Utility-first styling |
| **shadcn/ui** | UI component library |
| **Axios** | HTTP client |
| **date-fns** | Date manipulation |
| **Lucide React** | Icon library |
| **Sonner** | Toast notifications |
| **html2canvas + jsPDF** | PDF generation |

---

## Project Structure

```
project-root/
├── backend/
│   ├── server.js              # Express app entry point
│   ├── config/
│   │   ├── constants.js       # App configuration constants
│   │   └── database.js        # MongoDB connection
│   ├── controllers/
│   │   ├── authController.js  # Login/logout logic
│   │   ├── bookingController.js # Booking CRUD operations
│   │   └── roomController.js  # Room CRUD operations
│   ├── middleware/
│   │   ├── authMiddleware.js  # JWT verification
│   │   ├── errorMiddleware.js # Error handling
│   │   └── validationMiddleware.js # Input validation
│   ├── models/
│   │   ├── Admin.js           # Admin user schema
│   │   ├── Booking.js         # Booking schema
│   │   └── Room.js            # Room schema
│   ├── routes/
│   │   ├── adminRoutes.js     # Protected admin endpoints
│   │   ├── authRoutes.js      # Authentication endpoints
│   │   ├── bookingRoutes.js   # Public booking endpoints
│   │   └── roomRoutes.js      # Public room endpoints
│   └── utils/
│       ├── emailService.js    # Email templates & sending
│       ├── seedAdmin.js       # Admin account seeding
│       └── seedData.js        # Sample data seeding
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx            # Main app with routing
│   │   ├── main.jsx           # Entry point
│   │   ├── components/
│   │   │   ├── LiveSupport.jsx    # Chat widget
│   │   │   ├── ProtectedRoute.jsx # Auth guard
│   │   │   ├── Receipt.jsx        # PDF receipt component
│   │   │   └── ui/                # shadcn components
│   │   ├── layouts/
│   │   │   ├── MainLayout.jsx     # Public layout
│   │   │   ├── AdminLayout.jsx    # Admin sidebar layout
│   │   │   ├── Header.jsx         # Navigation header
│   │   │   └── Footer.jsx         # Footer
│   │   ├── pages/
│   │   │   ├── HomePage.jsx       # Landing page
│   │   │   ├── RoomsPage.jsx      # Room listing
│   │   │   ├── RoomDetailsPage.jsx # Single room + booking
│   │   │   ├── CheckoutPage.jsx   # Booking form
│   │   │   ├── SuccessPage.jsx    # Confirmation + receipt
│   │   │   ├── AboutPage.jsx      # About the resort
│   │   │   ├── ContactPage.jsx    # Contact information
│   │   │   └── admin/
│   │   │       ├── AdminDashboardPage.jsx
│   │   │       ├── AdminRoomsPage.jsx
│   │   │       └── AdminBookingsPage.jsx
│   │   ├── services/
│   │   │   ├── api.js             # Axios instance
│   │   │   ├── authService.js     # Auth API calls
│   │   │   ├── bookingService.js  # Booking API calls
│   │   │   └── roomService.js     # Room API calls
│   │   ├── store/
│   │   │   ├── authStore.js       # Auth state
│   │   │   ├── bookingStore.js    # Booking state
│   │   │   ├── roomStore.js       # Room state
│   │   │   └── chatStore.js       # Chat widget state
│   │   └── lib/
│   │       ├── utils.js           # Helper functions
│   │       └── pdfGenerator.js    # Receipt PDF generation
│   └── public/
│       ├── manifest.json
│       ├── robots.txt
│       └── sitemap.xml
```

---

## Database Models

### 1. Room Model

```javascript
const roomSchema = {
  name: String,              // Room/Villa name
  description: String,       // Full description
  price: Number,             // Price per night
  seasonalDiscount: {
    isActive: Boolean,       // Discount enabled
    percentage: Number,      // Discount % (0-100)
    startDate: Date,         // Discount start
    endDate: Date            // Discount end
  },
  images: [String],          // Array of image URLs
  totalRooms: Number,        // Available inventory
  maxGuests: Number,         // Maximum capacity
  amenities: [String],       // List of amenities
  isActive: Boolean,         // Soft delete flag
  category: String           // villa, casita, pavilion, suite
}

// Virtual: effectivePrice - calculates discounted price
```

### 2. Booking Model

```javascript
const bookingSchema = {
  bookingReference: String,  // Auto-generated: AMAN-2026-00001
  verificationCode: String,  // 8-char code for confirmation
  roomId: ObjectId,          // Reference to Room
  guestName: String,         // Guest full name
  email: String,             // Guest email
  checkIn: Date,             // Check-in date
  checkOut: Date,            // Check-out date
  guests: {
    adults: Number,          // Number of adults
    children: Number         // Number of children
  },
  totalAmount: Number,       // Total price
  paymentStatus: String,     // awaiting_payment, confirmed, expired, cancelled
  expiresAt: Date,           // Auto-set to 6 hours from creation
  confirmedAt: Date,         // Timestamp of confirmation
  specialRequests: String,   // Guest notes
  adminNotes: String         // Internal notes
}

// Virtuals: numberOfNights, isExpired, totalGuests
// Methods: confirmBooking(), markAsExpired()
// Statics: checkAvailability(), findExpired()
```

### 3. Admin Model

```javascript
const adminSchema = {
  email: String,             // Login email (unique)
  password: String,          // Hashed password
  name: String,              // Display name
  role: String,              // owner, admin, manager
  lastLogin: Date,           // Last login timestamp
  isActive: Boolean          // Account active status
}

// Pre-save hook: Hash password with bcrypt (12 rounds)
// Methods: comparePassword(), updateLastLogin()
```

---

## Backend API

### Public Endpoints

#### Rooms
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/rooms` | Get all active rooms (with filters) |
| GET | `/api/rooms/:id` | Get single room by ID |

**Query Parameters for GET /api/rooms:**
- `category` - Filter by room category
- `minPrice` / `maxPrice` - Price range filter
- `guests` - Minimum guest capacity
- `search` - Text search

#### Bookings
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/bookings` | Create new booking |
| POST | `/api/bookings/confirm` | Confirm booking with verification code |
| GET | `/api/bookings/:reference` | Get booking by reference |
| POST | `/api/bookings/:reference/receipt` | Email receipt to guest |

**Create Booking Request:**
```json
{
  "roomId": "room_id",
  "guestName": "John Doe",
  "email": "john@example.com",
  "checkIn": "2026-03-15",
  "checkOut": "2026-03-20",
  "guests": { "adults": 2, "children": 0 },
  "specialRequests": "Late check-in"
}
```

**Confirm Booking Request:**
```json
{
  "bookingReference": "AMAN-2026-00001",
  "verificationCode": "A1B2C3D4"
}
```

#### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/login` | Admin login |
| GET | `/api/auth/me` | Get current admin (protected) |
| POST | `/api/auth/logout` | Logout |

### Protected Admin Endpoints

> All require `Authorization: Bearer <token>` header

#### Room Management
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/admin/rooms` | Get all rooms (including inactive) |
| POST | `/api/admin/rooms` | Create new room |
| PUT | `/api/admin/rooms/:id` | Update room |
| DELETE | `/api/admin/rooms/:id` | Soft delete room |
| PATCH | `/api/admin/rooms/:id/discount` | Toggle seasonal discount |

#### Booking Management
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/admin/bookings` | Get all bookings (paginated) |
| GET | `/api/admin/bookings/stats` | Get dashboard statistics |
| PATCH | `/api/admin/bookings/:id/status` | Update booking status |

**Bookings Query Parameters:**
- `status` - Filter by payment status
- `page` / `limit` - Pagination
- `search` - Search by reference, email, or name

---

## Frontend Architecture

### Routing Structure

```jsx
<Routes>
  {/* Public Routes - MainLayout wrapper */}
  <Route path="/" element={<HomePage />} />
  <Route path="/rooms" element={<RoomsPage />} />
  <Route path="/rooms/:id" element={<RoomDetailsPage />} />
  <Route path="/checkout" element={<CheckoutPage />} />
  <Route path="/success" element={<SuccessPage />} />
  <Route path="/about" element={<AboutPage />} />
  <Route path="/contact" element={<ContactPage />} />

  {/* Auth Route */}
  <Route path="/owner/login" element={<LoginPage />} />

  {/* Protected Admin Routes - AdminLayout wrapper */}
  <Route path="/owner" element={<ProtectedRoute><AdminLayout /></ProtectedRoute>}>
    <Route index element={<AdminDashboardPage />} />
    <Route path="rooms" element={<AdminRoomsPage />} />
    <Route path="bookings" element={<AdminBookingsPage />} />
  </Route>
</Routes>
```

### Page Descriptions

| Page | Route | Purpose |
|------|-------|---------|
| HomePage | `/` | Landing page with hero, featured rooms, testimonials |
| RoomsPage | `/rooms` | Room listing with category/guest filters |
| RoomDetailsPage | `/rooms/:id` | Room gallery, amenities, date picker, booking form |
| CheckoutPage | `/checkout` | Guest details form, verification code entry |
| SuccessPage | `/success` | Confirmation, receipt preview, download/email |
| LoginPage | `/owner/login` | Admin authentication |
| AdminDashboardPage | `/owner` | Stats: revenue, bookings, rooms |
| AdminRoomsPage | `/owner/rooms` | CRUD rooms, discounts |
| AdminBookingsPage | `/owner/bookings` | View/manage all reservations |

---

## Authentication System

### Flow

1. **Login Request**
   - User submits email/password to `/api/auth/login`
   - Server validates credentials with bcrypt
   - Server generates JWT (7-day expiry)
   - Token returned to client

2. **Token Storage**
   - Token stored in `localStorage` as `amanpulo-token`
   - User data stored in Zustand auth store

3. **Protected Routes**
   - `ProtectedRoute` component checks auth store
   - Redirects to login if not authenticated
   - Passes location for redirect after login

4. **API Authorization**
   - Axios interceptor attaches token to requests
   - `protect` middleware verifies JWT on server
   - 401 response triggers logout on client

### JWT Token Structure

```javascript
// Payload
{
  id: "admin_mongodb_id",
  iat: 1234567890,        // Issued at
  exp: 1235172690         // Expires (7 days)
}
```

### Password Security

- Hashed with bcrypt (12 salt rounds)
- Password field excluded from queries by default (`select: false`)
- Compared using `admin.comparePassword(candidatePassword)`

---

## Booking Flow

### Step-by-Step Process

```
1. BROWSE ROOMS
   └── Guest visits /rooms
   └── Filters by category, capacity
   └── Clicks room card → /rooms/:id

2. SELECT DATES & GUESTS
   └── On RoomDetailsPage
   └── Calendar picker for check-in/check-out
   └── Guest counter (adults + children)
   └── Validates against room.maxGuests
   └── Shows price calculation
   └── Click "Reserve" → stores in bookingStore

3. CHECKOUT
   └── Redirects to /checkout
   └── Guest fills: name, email, special requests
   └── Submits → POST /api/bookings
   └── Server creates booking with:
       - Auto-generated reference (AMAN-2026-XXXXX)
       - Auto-generated verification code
       - 6-hour expiration
       - Status: "awaiting_payment"
   └── Admin receives email notification

4. PAYMENT SIMULATION
   └── Guest receives verification code (sent by admin)
   └── In real implementation: payment gateway integration
   └── Code represents "payment completed" signal

5. CONFIRMATION
   └── Guest enters verification code
   └── POST /api/bookings/confirm
   └── Server verifies code, marks as "confirmed"
   └── Verification code is deleted (security)
   └── Guest receives confirmation email

6. SUCCESS PAGE
   └── Redirects to /success
   └── Shows booking receipt
   └── Auto-sends receipt email
   └── Options: Download PDF, Email Receipt, New Booking
```

### Booking Status States

| Status | Description |
|--------|-------------|
| `awaiting_payment` | Created, pending verification code |
| `confirmed` | Verified and confirmed |
| `expired` | Not confirmed within 6 hours |
| `cancelled` | Manually cancelled by admin |

### Reference Format

```
AMAN-YYYY-NNNNN
│    │    │
│    │    └── 5-digit sequence (resets yearly)
│    └────── Current year
└─────────── Prefix (configurable)
```

---

## Admin Portal

### Dashboard Statistics

```javascript
{
  totalRevenue: Number,      // Sum of confirmed bookings
  rooms: {
    total: Number,           // All rooms
    active: Number           // Active rooms only
  },
  bookings: {
    confirmed: Number,       // Confirmed count
    pending: Number          // Awaiting payment count
  }
}
```

### Room Management Features

1. **Create Room**
   - Name, description, price
   - Category selection
   - Image URLs (multiple)
   - Total rooms (inventory)
   - Max guests capacity
   - Amenities list

2. **Update Room**
   - Edit all fields
   - Toggle active/inactive

3. **Seasonal Discounts**
   - Enable/disable discount
   - Set percentage (0-100%)
   - Set date range
   - Price auto-calculates as `effectivePrice`

4. **Soft Delete**
   - Sets `isActive: false`
   - Room hidden from public
   - Data preserved for existing bookings

### Booking Management Features

1. **List All Bookings**
   - Paginated table view
   - Status badges (color-coded)
   - Search by reference/email/name
   - Filter by status

2. **Booking Details Modal**
   - Full booking information
   - Guest details
   - Room info
   - Dates and pricing
   - Admin notes field

3. **Status Updates**
   - Confirm pending bookings
   - Cancel bookings
   - Add admin notes

---

## Email System

### Email Configuration

Uses Nodemailer with SMTP (Gmail default):

```javascript
{
  host: "smtp.gmail.com",
  port: 587,
  auth: {
    user: process.env.EMAIL_USER,
    pass: process.env.EMAIL_PASS  // App password for Gmail
  }
}
```

### Email Types

| Email | Trigger | Recipient |
|-------|---------|-----------|
| New Booking Notification | Booking created | Admin |
| Booking Confirmation | Booking confirmed | Guest |
| Receipt | Manual send or auto | Guest |

### Email Templates

All emails are HTML formatted with:
- Responsive design
- Hotel branding
- Booking details
- Reference number
- Date formatting

---

## Live Chat Support

### Integration: Smartsupp

```jsx
<LiveSupport
  guestName="John"           // Prefill visitor name
  guestEmail="john@test.com" // Prefill email
  bookingReference="AMAN-2026-00001"
  autoOpen={false}           // Auto-open on mount
  autoOpenDelay={2000}       // Delay in ms
  showButton={true}          // Show floating button
/>
```

### Configuration

```javascript
const SMARTSUPP_KEY = "your_smartsupp_key_here";
```

### Features
- Floating chat button
- Auto-open on checkout page
- Visitor data prefilling
- State managed via chatStore

---

## State Management

Using **Zustand** with persist middleware:

### Auth Store

```javascript
{
  user: null,
  token: null,
  isAuthenticated: false,
  isLoading: false,
  
  // Actions
  login: (user, token) => {},
  logout: () => {},
  setLoading: (loading) => {}
}
```

### Booking Store

```javascript
{
  selectedRoom: null,
  checkIn: null,
  checkOut: null,
  guests: { adults: 1, children: 0 },
  guestDetails: { name: "", email: "", specialRequests: "" },
  bookingResult: null,
  
  // Actions
  setSelectedRoom: (room) => {},
  setDates: (checkIn, checkOut) => {},
  setGuests: (guests) => {},
  setGuestDetails: (details) => {},
  setBookingResult: (result) => {},
  resetBooking: () => {},
  
  // Computed
  getNights: () => number,
  getTotal: () => number
}
```

### Room Store

```javascript
{
  rooms: [],
  filters: { category: "all", guests: "all" },
  isLoading: false,
  error: null,
  
  // Actions
  setRooms: (rooms) => {},
  setFilters: (filters) => {},
  setLoading: (loading) => {},
  setError: (error) => {}
}
```

### Persistence

Booking store persists to localStorage:
- `amanpulo-booking` - Room, dates, guests
- Token stored separately as `amanpulo-token`

---

## UI Components

### shadcn/ui Components Used

| Component | Usage |
|-----------|-------|
| `Button` | All buttons with variants |
| `Card` | Content containers |
| `Input` | Form inputs |
| `Label` | Form labels |
| `Select` | Dropdowns |
| `Dialog` | Modals |
| `Popover` | Date picker container |
| `Calendar` | Date selection |
| `Table` | Data tables |
| `Badge` | Status indicators |
| `Separator` | Dividers |
| `Sheet` | Mobile sidebar |
| `Sonner` | Toast notifications |

### Custom Components

| Component | Description |
|-----------|-------------|
| `LiveSupport` | Smartsupp chat integration |
| `ProtectedRoute` | Auth guard wrapper |
| `Receipt` | PDF-ready receipt layout |

### Styling

- **Tailwind CSS** for utility classes
- Custom color palette: `sand-*` (warm beige tones)
- Custom font: Serif for headings
- Responsive breakpoints: `sm`, `md`, `lg`

---

## Deployment Configuration

### Backend (Render)

```yaml
# render.yaml
services:
  - type: web
    name: amanpulo-api
    runtime: node
    buildCommand: npm install
    startCommand: node server.js
    envVars:
      - key: NODE_ENV
        value: production
      - key: MONGODB_URI
        sync: false
      - key: JWT_SECRET
        sync: false
```

### Frontend (Vercel)

```json
// vercel.json
{
  "rewrites": [
    { "source": "/(.*)", "destination": "/" }
  ]
}
```

### CORS Configuration

```javascript
const allowedOrigins = [
  "http://localhost:5173",
  "https://yourdomain.com",
  "https://www.yourdomain.com"
];
```

---

## Environment Variables

### Backend (.env)

```env
# Server
PORT=5000
NODE_ENV=development

# Database
MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/dbname

# Authentication
JWT_SECRET=your_super_secret_jwt_key_here

# Email (Gmail)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your-email@gmail.com
EMAIL_PASS=your-app-password
EMAIL_FROM=Amanpulo Resort <noreply@amanpulo.com>

# Admin (for seeding)
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=securepassword

# Frontend URL (for CORS)
FRONTEND_URL=https://yourdomain.com
```

### Frontend (.env)

```env
VITE_API_URL=http://localhost:5000/api
```

---

## Key Implementation Details

### Booking Expiration

- Bookings expire after 6 hours if not confirmed
- `expiresAt` field set on creation
- Can implement cron job to auto-expire:

```javascript
// Run periodically
const expiredBookings = await Booking.findExpired();
for (const booking of expiredBookings) {
  await booking.markAsExpired();
}
```

### Room Availability Check

```javascript
// Checks for overlapping bookings
const isAvailable = await Booking.checkAvailability(
  roomId,
  checkInDate,
  checkOutDate,
  excludeBookingId  // For edits
);
```

### PDF Receipt Generation

Uses `html2canvas` + `jsPDF`:

```javascript
import { downloadReceiptPDF } from "@/lib/pdfGenerator";

// In component
await downloadReceiptPDF(receiptRef.current, bookingReference);
```

### Price Calculation

```javascript
// On room model (virtual)
effectivePrice = seasonalDiscount.isActive && inDateRange
  ? price - (price * percentage / 100)
  : price;

// On frontend
totalPrice = nights * room.effectivePrice;
```

---

## Security Considerations

1. **Password Hashing** - bcrypt with 12 rounds
2. **JWT Tokens** - 7-day expiry, stored in localStorage
3. **CORS** - Whitelist specific origins
4. **Helmet** - Security headers
5. **Input Validation** - Mongoose schema validation
6. **Soft Delete** - Preserve data integrity
7. **Verification Codes** - Removed after use
8. **Password Field** - Excluded from queries by default

---

## Extending the System

### Adding Payment Gateway

Replace verification code flow with:
1. Integrate Stripe/PayPal SDK
2. Create payment intent on booking creation
3. Webhook to confirm on successful payment
4. Update booking status automatically

### Adding Multi-Language

1. Use i18next or similar
2. Create translation files
3. Wrap text in `t()` function
4. Add language selector

### Adding Reviews/Ratings

1. Create Review model
2. Add routes for CRUD
3. Display on room pages
4. Calculate average ratings

---

## Summary

This documentation covers a complete hotel booking system with:

- **Public website** for browsing and booking
- **Admin portal** for management
- **Secure authentication** with JWT
- **Email notifications** for bookings
- **Live chat support** integration
- **PDF receipt generation**
- **Responsive design** with Tailwind CSS

Use this as a blueprint to build similar booking/reservation systems for hotels, resorts, vacation rentals, or any hospitality business.
