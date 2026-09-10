Mebanjar

Mebanjar adalah platform digital berbasis web untuk membantu pengelolaan administrasi dan operasional Banjar secara terintegrasi.

Mebanjar dirancang agar dapat diakses oleh Krama, Admin Banjar, dan Super Admin melalui browser, baik dari desktop maupun mobile.

✨ Fitur Utama

👥 Manajemen Krama dan Keluarga

📢 Pengumuman Banjar

📅 Kegiatan dan Absensi

💰 Iuran Krama

🧾 Keuangan Banjar

🤝 Koperasi dan Pinjaman Krama

🏛️ Rapat / Paruman

📝 Notulen, Keputusan, dan Tindak Lanjut Rapat

📜 Awig-awig dengan versioning

📁 Arsip Dokumen

📊 Dashboard dan Laporan

🔐 Role-based access control

📝 Audit Log

👤 Role

Mebanjar menggunakan 3 role utama:

Role

Akses

SUPER_ADMIN

Mengelola seluruh Banjar dan administrasi platform

ADMIN

Mengelola data dan operasional Banjar sendiri

KRAMA

Mengakses informasi dan data pribadi miliknya sendiri

Aturan Banjar

Setiap user hanya dapat terhubung dengan satu Banjar.

SUPER_ADMIN dapat memiliki banjar_id = NULL.

ADMIN hanya dapat mengelola Banjar yang menjadi tanggung jawabnya.

KRAMA hanya dapat melihat data pribadi, iuran, pinjaman, dan informasi yang menjadi haknya.

Data antar-Banjar harus selalu terisolasi.

🛠️ Tech Stack

Mebanjar menggunakan pendekatan Laravel Monolith. Tidak ada frontend dan backend terpisah untuk MVP.

Backend

Laravel

PHP

Eloquent ORM

Laravel Policies & Gates

Laravel Queue

Frontend

Blade

Livewire

Alpine.js

Tailwind CSS

Database

PostgreSQL

Infrastructure

Nginx

Ubuntu

Redis (opsional)

S3-compatible Object Storage (opsional)

Git

Penting: Untuk MVP, gunakan Blade + Livewire + Alpine.js. Jangan membuat frontend terpisah menggunakan React/Vue atau REST API layer tanpa kebutuhan yang jelas.

🏗️ Arsitektur

Browser
   │
   ▼
Nginx
   │
   ▼
Laravel
   ├── Blade
   ├── Livewire
   ├── Alpine.js
   ├── Eloquent ORM
   ├── Policies / Gates
   └── Services
          │
          ▼
      PostgreSQL

Laravel Monolith dipilih agar aplikasi lebih sederhana untuk dikembangkan, dideploy, dan dipelihara pada tahap awal.

📦 Modul

Authentication
Banjar Management
User & Role Management
Member / Krama
Family
Announcements
Events
Attendance
Iuran
Finance
Cooperative / Loans
Awig-awig
Meetings / Paruman
Meeting Minutes
Meeting Decisions
Action Items
Document Archive
Dashboard
Audit Log

💻 Development Setup

Requirements

PHP sesuai requirement versi Laravel yang digunakan

Composer

Node.js & npm

PostgreSQL

Git

Installation

git clone <repository-url>
cd mebanjar
composer install
npm install
cp .env.example .env
php artisan key:generate

Konfigurasi PostgreSQL pada .env:

DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=mebanjar
DB_USERNAME=postgres
DB_PASSWORD=your_password

Jalankan migration:

php artisan migrate

Jika tersedia seeder:

php artisan db:seed

Build frontend:

npm run build

Development server:

php artisan serve

Untuk Vite:

npm run dev

🔐 Security & Authorization

Mebanjar menangani data anggota, keuangan, dan pinjaman sehingga authorization dan data isolation wajib diterapkan.

Gunakan Laravel Authentication.

Gunakan Laravel Policies / Gates.

Jangan hanya mengandalkan hidden menu untuk membatasi akses.

Semua query business data harus memperhatikan banjar_id.

Krama tidak boleh mengakses data Krama lain.

Krama hanya boleh melihat pinjaman miliknya sendiri.

Admin hanya dapat mengakses data Banjar sendiri.

Super Admin dapat mengakses seluruh Banjar.

File sensitif harus dilindungi authorization.

Validasi seluruh file upload.

Gunakan CSRF protection.

Password harus menggunakan hashing Laravel.

Perubahan data sensitif dicatat pada Audit Log.

Data Isolation

Admin:

Loan::query()
    ->where('banjar_id', auth()->user()->banjar_id)
    ->get();

Krama:

Loan::query()
    ->where('banjar_id', auth()->user()->banjar_id)
    ->where('member_id', auth()->user()->member->id)
    ->get();

Jangan membuat endpoint yang memungkinkan Krama mengambil seluruh data pinjaman.

🤝 Cooperative / Loan

Modul koperasi menggunakan model pinjaman sederhana.

Krama mengajukan pinjaman
        │
        ▼
     PENDING
        │
   Admin review
     ┌──┴──┐
     ▼     ▼
 APPROVED REJECTED
     │
     ▼
 DISBURSED
     │
     ▼
    ACTIVE
     │
     ▼
 Installments
     │
     ▼
     PAID

Dana koperasi harus dipisahkan dari dana operasional Banjar.

📜 Awig-awig

Awig-awig menggunakan versioning.

Versi yang sudah dipublikasikan tidak boleh langsung ditimpa.

Published Version
       │
       ▼
Create New Draft
       │
       ▼
Edit
       │
       ▼
Publish New Version
       │
       ▼
Archive Previous Version

🏛️ Rapat / Paruman

Rapat tidak hanya menjadi arsip. Keputusan harus dapat ditindaklanjuti.

Rapat
 ├── Agenda
 ├── Absensi
 ├── Notulen
 ├── Keputusan
 │     └── Tindak Lanjut
 │            ├── PIC
 │            ├── Deadline
 │            └── Status

📁 Struktur Project

app/
├── Enums/
├── Livewire/
│   ├── Dashboard/
│   ├── Members/
│   ├── Announcements/
│   ├── Events/
│   ├── Fees/
│   ├── Payments/
│   ├── Finance/
│   ├── Cooperative/
│   ├── AwigAwig/
│   └── Meetings/
├── Models/
├── Policies/
└── Services/
    ├── LoanService.php
    ├── PaymentService.php
    ├── FinancialService.php
    ├── MeetingService.php
    └── AwigAwigService.php

🧑‍💻 Development Guidelines

Saat membuat atau mengubah fitur:

Ikuti konvensi Laravel.

Gunakan Eloquent Relationship jika memungkinkan.

Gunakan Form Request untuk validasi kompleks.

Gunakan Policy / Gate untuk authorization.

Gunakan Service Class untuk business logic kompleks.

Hindari business logic berlebihan di Blade atau Livewire.

Gunakan database transaction untuk proses multi-tabel.

Tambahkan foreign key dan database constraint.

Tambahkan index pada kolom yang sering difilter atau digunakan sebagai relationship.

Pastikan data selalu terisolasi berdasarkan banjar_id.

Jangan menambahkan role baru tanpa kebutuhan bisnis.

Pertahankan UI sederhana dan mobile-first.

📱 UI / UX

Mebanjar ditujukan untuk masyarakat dengan tingkat literasi digital yang beragam.

Mobile-first.

Bahasa UI sederhana.

Hindari istilah teknis.

Tombol harus jelas.

Form jangan terlalu panjang.

Informasi penting mudah ditemukan.

Gunakan Belum Dibayar daripada UNPAID.

Gunakan + Buat Kegiatan daripada Create Event.

🧪 Testing

Minimal setiap modul harus memiliki test untuk:

Happy path

Validation

Authorization

Data isolation antar-Banjar

Data isolation antar-Krama

Edge case transaksi keuangan dan pinjaman

Jalankan test:

php artisan test

🚀 Deployment

Production direkomendasikan:

Ubuntu
  │
  ├── Nginx
  ├── PHP-FPM
  ├── Laravel
  ├── PostgreSQL
  └── Redis (optional)

Production wajib menggunakan HTTPS.

Credential dan secret production tidak boleh disimpan di Git.

🗺️ Development Roadmap

Phase 1 — Foundation

Authentication

Banjar

Roles

User

Member

Family

Dashboard

Phase 2 — Daily Banjar

Announcements

Events

Attendance

QR Attendance

Phase 3 — Finance

Iuran

Payments

Funds

Financial Transactions

Reports

Phase 4 — Meetings

Meetings

Agendas

Attendance

Minutes

Decisions

Action Items

Phase 5 — Cooperative

Cooperative Account

Loans

Loan Approval

Installments

Loan Payments

Reports

Phase 6 — Knowledge

Awig-awig

Versioning

Search

Document Archive

Phase 7 — Production Hardening

Audit Log

Export

Backup

Security hardening

Performance optimization

Mobile UX improvements

🤖 AI-Assisted Development

Project ini dapat dikembangkan dengan bantuan AI coding assistant. AI wajib mengikuti arsitektur dan business rules yang sudah ditentukan.

Sebelum melakukan perubahan besar:

Baca README.md.

Baca PRD jika tersedia.

Periksa struktur project yang sudah ada.

Jangan mengganti tech stack tanpa alasan yang jelas.

Jangan membuat React/Vue frontend terpisah untuk MVP.

Jangan membuat API layer hanya karena dianggap lebih modern.

Jangan menambahkan role baru tanpa kebutuhan bisnis.

Jangan mengubah business rule Banjar tanpa alasan yang jelas.

Jangan mengabaikan authorization dan data isolation.

Hindari refactor besar jika perubahan kecil sudah cukup.

Prinsip Utama

Simple, secure, maintainable, and useful for Banjar.

Teknologi harus membantu kebutuhan Banjar, bukan membuat aplikasi menjadi lebih kompleks dari yang diperlukan.

📄 Documentation

README.md — Overview, arsitektur, dan development guideline

PRD — Product Requirement Document

Laravel migrations — Struktur database

Laravel Policies — Authorization dan data access rules
