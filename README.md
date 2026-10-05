# MediConnect 🩺

> **Modern Telemedicine & Doctor Appointment Platform**  
> Connect with verified healthcare professionals anytime, anywhere through browser-native encrypted video consultations.

---

## 📌 Overview

**MediConnect** is a full-stack telehealth and appointment scheduling web application built with **Next.js 15 (App Router, Server Actions)**, **PostgreSQL (Prisma ORM)**, and **Clerk Auth**. It bridges the gap between healthcare providers and patients by offering instant slot booking, conflict prevention, an internal credit-based economy, and real-time in-browser video calls powered by **Vonage Video API (OpenTok)**.

---

## ✨ Features

### 👤 For Patients
- **Discover & Filter Doctors:** Browse verified doctors by medical specialty and experience.
- **Credit-Based Booking:** Simplified, transparent booking using platform credits (2 credits per consultation).
- **Conflict-Free Scheduling:** Real-time slot availability check prevents double-booking.
- **In-Browser Video Calls:** Attend private, WebRTC-encrypted video consultations directly without installing external software.
- **Appointment History:** Track upcoming, completed, and cancelled appointments with doctor notes and symptoms.

### 👨‍⚕️ For Doctors
- **Professional Onboarding:** Profile setup with medical specialty, years of experience, and credentials.
- **Dynamic Slot Availability:** Configure and manage custom working hours and consultation slots.
- **Earnings & Payouts:** Earn credits per consultation ($8 doctor earnings / $2 platform fee) and request direct PayPal payouts.
- **Appointment Dashboard:** Manage upcoming patient bookings and review patient-reported symptoms.

### 🛡️ For Administrators
- **Doctor Verification:** Review and approve/reject doctor credential applications to ensure healthcare quality.
- **Payout Management:** Review, process, and track doctor payout requests.
- **Credit Oversight:** Monitor platform credit ledger transactions.

---

## 🛠️ Tech Stack

| Domain | Technology |
| :--- | :--- |
| **Framework** | [Next.js 15](https://nextjs.org/) (App Router, React 19, Server Actions) |
| **Styling** | [Tailwind CSS v4](https://tailwindcss.com/) & [Shadcn UI](https://ui.shadcn.com/) (Radix UI) |
| **Database & ORM** | [PostgreSQL](https://www.postgresql.org/) (Neon DB) with [Prisma ORM](https://www.prisma.io/) |
| **Authentication** | [Clerk](https://clerk.com/) (Multi-role RBAC: Patient, Doctor, Admin) |
| **Telehealth / Video** | [Vonage Video API](https://developer.vonage.com/en/video/overview) (OpenTok WebRTC) |
| **Form Handling** | [React Hook Form](https://react-hook-form.com/) & [Zod](https://zod.dev/) |
| **Icons & Notifications** | [Lucide React](https://lucide.dev/) & [Sonner](https://sonner.emilkowal.dev/) |

---

## 📐 System Workflow

```mermaid
flowchart TD
    Start([User Visits MediConnect]) --> Auth[Authenticate via Clerk]
    Auth --> RoleCheck{User Role?}

    %% Patient Flow
    RoleCheck -->|Patient| PatientBrowse[Browse Verified Doctors]
    PatientBrowse --> SelectSlot[Select Consultation Slot]
    SelectSlot --> CheckCredits{Credits >= 2?}
    CheckCredits -->|No| BuyCredits[Purchase Credit Package]
    BuyCredits --> SelectSlot
    CheckCredits -->|Yes| BookAppointment[Book Appointment]
    BookAppointment --> PreventOverlap{Overlapping Slot?}
    PreventOverlap -->|Yes| ErrorMsg[Booking Rejected]
    PreventOverlap -->|No| DeductCredits[Deduct 2 Credits & Generate Vonage Session]
    DeductCredits --> VideoRoom[Join In-Browser Video Consultation]

    %% Doctor Flow
    RoleCheck -->|Doctor| DocSubmit[Submit Profile & Credentials]
    DocSubmit --> AdminReview{Admin Approval}
    AdminReview -->|Approved| DocActive[Set Availability Slots]
    AdminReview -->|Rejected| DocRejected[Profile Rejected]
    DocActive --> VideoRoom
    VideoRoom --> EarnCredits[Earn Consultation Credits]
    EarnCredits --> RequestPayout[Request Payout via PayPal]
    RequestPayout --> AdminPayout[Admin Processes Payout]

    %% Admin Flow
    RoleCheck -->|Admin| AdminPortal[Admin Dashboard]
    AdminPortal --> AdminReview
    AdminPortal --> AdminPayout
```

---

## 🚀 Getting Started

### Prerequisites
- [Node.js](https://nodejs.org/) (v18.18+ or v20+ recommended)
- [npm](https://www.npmjs.com/) or [yarn](https://yarnpkg.com/)
- PostgreSQL database instance (local or hosted on Neon, Supabase, etc.)
- Accounts with [Clerk](https://clerk.com/) and [Vonage Developer](https://developer.vonage.com/)

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/gitsofpranav/MediConnect.git
   cd MediConnect/doctors-appointment-platform
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Configure Environment Variables:**
   Copy the example environment file and fill in your API credentials:
   ```bash
   cp .env.example .env
   ```
   Provide the required variables:
   ```env
   # Clerk Auth
   NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
   CLERK_SECRET_KEY=your_clerk_secret_key
   NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
   NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
   NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/onboarding
   NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/onboarding

   # Vonage Video API
   NEXT_PUBLIC_VONAGE_APPLICATION_ID=your_vonage_app_id
   VONAGE_PRIVATE_KEY=path_to_private_key_or_key_content

   # PostgreSQL Database
   DATABASE_URL="postgresql://user:password@host:port/database?sslmode=require"
   ```

4. **Initialize Database with Prisma:**
   ```bash
   npx prisma generate
   npx prisma db push
   ```

5. **Start Development Server:**
   ```bash
   npm run dev
   ```
   Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## 📂 Project Structure

```plaintext
doctors-appointment-platform/
├── actions/                  # Next.js Server Actions (Backend business logic)
│   ├── admin.js              # Doctor verifications & payout approval
│   ├── appointments.js       # Slot scheduling, collision checks & Vonage sessions
│   ├── credits.js            # Internal credit deduction & ledger tracking
│   ├── doctor.js             # Doctor profile and availability slot handling
│   ├── doctors-listing.js    # Doctor search and directory queries
│   ├── onboarding.js         # Role selection and onboarding routing
│   └── payout.js             # Doctor payout requests and calculations
├── app/                      # Next.js App Router (Pages and Layouts)
│   ├── (auth)/               # Sign-in and Sign-up authentication routes
│   ├── (main)/               # Authenticated application portal
│   │   ├── admin/            # Admin verification and payout panel
│   │   ├── appointments/     # Patient & Doctor appointment dashboard
│   │   ├── doctor/           # Doctor slot availability management
│   │   ├── doctors/          # Doctor catalog & booking flow
│   │   ├── onboarding/       # Post-signup role onboarding
│   │   ├── pricing/          # Platform credit purchase packages
│   │   └── video-call/       # WebRTC video consultation room
│   ├── layout.js             # Global root layout & theme providers
│   └── page.js               # Landing page
├── components/               # Reusable UI components & dialogs (shadcn/ui)
├── hooks/                    # Custom React hooks
├── lib/                      # Database clients (Prisma), utility helpers & data
├── prisma/
│   └── schema.prisma         # PostgreSQL database schema & models
└── public/                   # Static media, icons, and illustrations
```

---

## 🔒 Security & Best Practices

- **Role-Based Access Control (RBAC):** Server-side session verification prevents unauthorized access across Patient, Doctor, and Admin endpoints.
- **Server Actions for Sensitive Logic:** Payment processing, credit balances, and Vonage private keys are strictly executed on the server, avoiding client credential exposure.
- **Conflict Prevention Logic:** Prevents double-booking via boundary range validation on the PostgreSQL level.
- **Encrypted WebRTC Streams:** Real-time peer-to-peer video sessions are provisioned with unique, ephemeral tokens per appointment.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
