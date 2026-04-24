# Feature Breakdown – SEGS Booking Platform

A detailed walkthrough of every feature in the application, organized by user role.

---

## Customer-Facing Features

### Landing Page

| Feature | Description |
|---------|-------------|
| **Hero Section** | Full-viewport background image with parallax zoom animation on load. Floating phone CTA button. |
| **About Section** | Highlights business credibility: 12+ years experience, safety certifications, professional guides, unique route through ancient Caesarea. |
| **Gallery** | Responsive carousel of tour and location photos. |
| **Info / FAQ Section** | Answers common questions: age requirements, what to wear, booking process, who it's for. |
| **Contact Section** | Address, Google Maps link, WhatsApp and phone CTAs. |
| **Accessibility Widget** | Floating widget with font size toggle (3 levels) and high-contrast mode — both persisted in localStorage. |
| **Language Switcher** | Toggle between Hebrew (RTL) and English (LTR) — persists across sessions, auto-detects browser language on first visit. |

---

### Booking Flow – Guided Tour

A multi-step booking flow, all on a single scrollable page:

**Step 1 – Date Selection**
- Calendar component showing available dates
- Dates with no available slots are visually disabled
- Dates with slots show a subtle indicator

**Step 2 – Time Slot Selection**
- Available slots load for the selected date
- Each slot shows: start time, end time, slots remaining
- Slots at capacity are shown as full and unselectable
- Color-coded by slot category (e.g., private tour, regular tour)

**Step 3 – Guide Information**
- After selecting a slot, the assigned guide(s) appear
- Each guide shows: avatar, name, languages spoken, short bio
- Content adapts to Hebrew or English depending on active language

**Step 4 – Participant Details**
- Stepper inputs for adult, child, senior counts
- Real-time capacity check — cannot exceed remaining slots
- Price calculation updates live

**Step 5 – Gift Card / Discount**
- Optional: enter a gift card code → validated via Supabase RPC → remaining balance applied
- Optional: enter a discount code → validated → percent or fixed discount applied
- Both can be combined (gift card first, then discount on remainder)

**Step 6 – Personal Details & Confirmation**
- Name, email, phone (with Israel +972 country code handling)
- Checkbox: accept terms of service
- Submit → Edge Function validates and creates booking → success toast + confirmation

---

### Booking Flow – Gift Voucher Purchase

A separate booking mode for buying gift cards:

- Select quantities: adult tickets, child tickets, senior tickets
- Enter recipient name and your own contact details
- Submit → unique voucher code generated instantly
- PDF download of the gift card (via Supabase Storage)
- Voucher can later be redeemed in the guided tour booking flow

---

### Tour Request Form

When no slots are available on a requested date:
- A modal prompts the customer to submit a custom tour request
- Fields: preferred date, preferred time, group size, contact info, notes
- Admin sees the request immediately with a real-time badge count notification

---

## Admin Dashboard Features

Requires Google OAuth login with admin role.

### Tour Slots Manager

| Feature | Description |
|---------|-------------|
| **Calendar Views** | Switch between month, week, and day views |
| **Color-Coded Slots** | Each slot category has a configurable color |
| **Create Slot** | Pick date, start time, duration, capacity, language, category, assign guide(s) |
| **Edit / Delete Slot** | Inline edit or delete any slot |
| **Blocked Times** | Block date/time ranges (holidays, maintenance) — no bookings can be made during blocked periods |
| **Category Rules** | Define slot types (name, color, default duration) |

### Bookings Manager

| Feature | Description |
|---------|-------------|
| **Table View** | All bookings with columns: customer name, tour slot, status, participants, created date |
| **Status Filter** | Filter by: pending / approved / confirmed / rejected / cancelled |
| **Status Update** | Change booking status inline — auto-updates participant counts via DB trigger |
| **Manual Booking** | Create a booking manually (for phone/walk-in bookings) |
| **Link External Booking** | Link a legacy or external booking to a tour slot |
| **Real-time Badge** | Tab shows unreviewed booking count, updates live |

### Tour Requests Manager

- View all submitted tour requests
- Approve or reject each request
- Contact customer directly from the panel
- Real-time badge count for new unreviewed requests

### Guides Manager

| Feature | Description |
|---------|-------------|
| **Guide Profiles** | Add/edit guide with Hebrew and English name + bio |
| **Avatar Upload** | Upload guide photo stored in Supabase Storage |
| **Languages** | Assign multiple languages to each guide |
| **Account Linking** | Link guide profile to a Google account by email (auto-linked on first login) |

### Customers Manager

- View all customers extracted from booking data
- See each customer's full booking history
- Search and filter by name or email

### Gift Cards Manager

| Feature | Description |
|---------|-------------|
| **Issue Gift Card** | Create a new gift card with ticket counts and expiry date |
| **Track Balance** | See remaining balance on each card |
| **Redeem Manually** | Mark a gift card as redeemed from the admin panel |
| **Code Lookup** | Search by gift card code |

### Discount Codes Manager

- Create codes with percent or fixed-value discounts
- Set expiry dates and usage limits
- View which codes have been used and how many times

---

## Guide Dashboard Features

Accessed at `/ron-manage/guide` — requires Google OAuth login linked to a guide profile.

| Feature | Description |
|---------|-------------|
| **My Tours** | View all upcoming slots assigned to this guide |
| **My Bookings** | Table of all bookings for this guide's slots |
| **Status View** | See confirmed, pending, and cancelled bookings |
| **Tour Filter** | Toggle between "only my tours" and all active tours |
| **Participant Count** | See how many participants are booked per slot |
| **Auto-Linking** | First login with matching email auto-links to guide profile — no admin action required |

---

## Cross-Cutting Features

| Feature | Implementation |
|---------|---------------|
| **Bilingual (HE/EN)** | 500+ translation keys, Hebrew locale for dates, RTL/LTR layout direction |
| **Responsive Design** | Mobile-first, booking widget repositions on small screens |
| **Accessibility** | shadcn/ui (Radix-based) components, ARIA labels, keyboard navigation, font scaling, contrast mode |
| **Real-time Updates** | Supabase Realtime subscriptions for admin badge counts and live data sync |
| **Toast Notifications** | Sonner-based toasts for success, error, and loading states |
| **Form Validation** | React Hook Form + Zod schemas with inline error messages |
| **Lazy Loading** | Main page sections use React.lazy() + Suspense for code splitting |
| **Auth Guards** | Protected routes redirect unauthenticated users to login |
| **Role-Based Routing** | Admin and guide see different dashboards based on their role |
