# Activity Diagram: Aktivitas Utama & Petani
```mermaid
graph TB
    %% ================== ADMIN ==================
    subgraph Admin["👨‍💼 ADMIN (OPERATOR)"]
        A1[Mulai] --> A2[Login Admin]
        A2 --> A3[Dashboard Admin]
        A3 --> A4{Pilih Menu}

        %% Menu 1: Kelola Ongkir
        A4 -->|1. Kelola Ongkir| A5[Lihat Daftar Kota & Tarif]
        A5 --> A6{Aksi?}
        A6 -->|Tambah| A7[Input: Nama Kota<br/>Province, Shipping Cost]
        A6 -->|Edit| A8[Update Tarif Ongkir]
        A6 -->|Delete| A9[Hapus Kota]

        %% Menu 2: Kelola Kupon
        A4 -->|2. Kelola Kupon| A10[Lihat Daftar Kupon]
        A10 --> A11{Aksi?}
        A11 -->|Buat Baru| A12[Input: Kode, Discount Type<br/>Amount/Percentage, Min Purchase<br/>Usage Limit, Valid Until]
        A11 -->|Edit| A13[Update Kupon]
        A11 -->|Monitor| A14[Lihat Coupon Usage Report]

        %% Menu 3: Escrow Monitoring
        A4 -->|3. Monitoring Escrow| A15[Lihat Semua Order]
        A15 --> A16[Filter by Status]
        A16 --> A17[View Escrow Status<br/>& Fund Flow]
        A17 --> A18{Check?}
        A18 -->|Dana Ditahan| A19[View: Pesanan pending<br/>Escrow Status: DITAHAN<br/>Total Dana: Rp X]
        A18 -->|Dana Dikirim| A20[View: Pesanan selesai<br/>Escrow Status: DIKIRIM_KE_PETANI<br/>Total Dana: Rp Y]
        A18 -->|Refund| A21[View: Pesanan refund<br/>Escrow Status: DIREFUND_KE_PEMBELI<br/>Total Dana: Rp Z]
        A19 --> A22[Export Escrow Report]
        A20 --> A22
        A21 --> A22

        %% Menu 4: Verifikasi Petani
        A4 -->|4. Verifikasi Petani| A23[Lihat Pengajuan Petani Baru]
        A23 --> A24[Cek Detail Registrasi]
        A24 --> A25{Approve Akun?}
        A25 -->|Ya| A26[Aktifkan Akun Petani<br/>Status: VERIFIED]
        A25 -->|Tidak| A27[Reject dengan Alasan<br/>Status: REJECTED]

        %% Menu 5: Broadcast Notifikasi
        A4 -->|5. Broadcast Promo| A28[Compose Message]
        A28 --> A29[Select Audience<br/>All Users / Role Specific]
        A29 --> A30[Schedule Broadcast]
        A30 --> A31[Monitor Delivery Status]

        %% Menu 6: Dashboard Analytics
        A4 -->|6. Analytics| A32[View Dashboard Metrics]
        A32 --> A33[Lihat: GMV, Transaction Count<br/>Active Users, Top Sellers<br/>Top Products, Conversion Rate]

        %% Kembali ke dashboard
        A7 --> A34[Kembali ke Dashboard]
        A8 --> A34
        A9 --> A34
        A12 --> A34
        A13 --> A34
        A14 --> A34
        A22 --> A34
        A26 --> A34
        A27 --> A34
        A31 --> A34
        A33 --> A34
        A34 --> A35{Lanjut?}
        A35 -->|Ya| A3
        A35 -->|Tidak| A36[Logout]
        A36 --> A37[Selesai]
    end

    %% ================== SISTEM ==================
    subgraph Sistem["⚙️ SISTEM"]
        %% Data Validation
        B1[Validasi Data Input]
        B2{Data Valid?}
        B3[Error Message]
        B4[Simpan ke Database]
        B5{Sukses<br/>Disimpan?}
        B6[Display Success Message]

        %% Notification & Update
        B7{Perlu notifikasi?}
        B8[Send Notification<br/>ke User Terkait]
        B9[Update Cache<br/>Master Data]
        B10[Log Action<br/>Audit Trail]

        %% Escrow Specific
        B11[Hitung Total Escrow<br/>Ditahan + Dikirim + Refund]
        B12[Generate Escrow Report]
        B13[Reconcile dengan<br/>Payment Gateway]
    end

    %% ================== KONEKSI ADMIN – SISTEM ==================
    A7 --> B1
    A8 --> B1
    A9 --> B1
    A12 --> B1
    A13 --> B1
    A26 --> B1
    A27 --> B1

    B1 --> B2
    B2 -->|Tidak| B3
    B3 --> A34
    B2 -->|Ya| B4
    B4 --> B5
    B5 -->|Tidak| B3
    B5 -->|Ya| B6

    B6 --> B7
    B7 -->|Ya| B8
    B7 -->|Tidak| B9
    B8 --> B9
    B9 --> B10
    B10 --> A34

    %% Escrow Monitoring Flow
    A17 --> B11
    B11 --> B12
    B12 --> B13
    B13 --> A22

    %% ============ STYLE ============
    style A1 fill:#000,stroke:#000,color:#fff
    style A37 fill:#000,stroke:#000,color:#fff
    style A2 fill:#1e3a8a,stroke:#000,color:#fff
    style A3 fill:#1e3a8a,stroke:#000,color:#fff

    classDef admin fill:#dbeafe,stroke:#1e3a8a,color:#000,stroke-width:2px;
    classDef sistem fill:#fef5e7,stroke:#f39c12,color:#000,stroke-width:2px;
    classDef decision fill:#d5f4e6,stroke:#27ae60,color:#000,stroke-width:2px;
    classDef escrow fill:#fff9e6,stroke:#fbc02d,color:#000,stroke-width:3px;
    classDef success fill:#d4edda,stroke:#28a745,color:#000,stroke-width:2px;
    classDef error fill:#f8d7da,stroke:#dc3545,color:#000,stroke-width:2px;

    class A5,A10,A15,A23,A24,A28,A29,A30,A32 admin;
    class B1,B4,B6,B8,B9,B10 sistem;
    class A4,A6,A11,A18,A25,A35,B2,B5,B7 decision;
    class A17,A19,A20,A21,B11,B12,B13 escrow;
    class A26,B6 success;
    class A27,B3,A9 error;
```

# Activity Diagram: Admin Operations
```mermaid
graph TB
    %% ================== ADMIN ==================
    subgraph Admin["👨‍💼 ADMIN (OPERATOR)"]
        A1[Mulai] --> A2[Login Admin]
        A2 --> A3[Dashboard Admin]
        A3 --> A4{Pilih Menu}

        %% Menu 1: Kelola Ongkir
        A4 -->|1. Kelola Ongkir| A5[Lihat Daftar Kota & Tarif]
        A5 --> A6{Aksi?}
        A6 -->|Tambah| A7[Input: Nama Kota<br/>Province, Shipping Cost]
        A6 -->|Edit| A8[Update Tarif Ongkir]
        A6 -->|Delete| A9[Hapus Kota]

        %% Menu 2: Kelola Kupon
        A4 -->|2. Kelola Kupon| A10[Lihat Daftar Kupon]
        A10 --> A11{Aksi?}
        A11 -->|Buat Baru| A12[Input: Kode, Discount Type<br/>Amount/Percentage, Min Purchase<br/>Usage Limit, Valid Until]
        A11 -->|Edit| A13[Update Kupon]
        A11 -->|Monitor| A14[Lihat Coupon Usage Report]

        %% Menu 3: Escrow Monitoring
        A4 -->|3. Monitoring Escrow| A15[Lihat Semua Order]
        A15 --> A16[Filter by Status]
        A16 --> A17[View Escrow Status<br/>& Fund Flow]
        A17 --> A18{Check?}
        A18 -->|Dana Ditahan| A19[View: Pesanan pending<br/>Escrow Status: DITAHAN<br/>Total Dana: Rp X]
        A18 -->|Dana Dikirim| A20[View: Pesanan selesai<br/>Escrow Status: DIKIRIM_KE_PETANI<br/>Total Dana: Rp Y]
        A18 -->|Refund| A21[View: Pesanan refund<br/>Escrow Status: DIREFUND_KE_PEMBELI<br/>Total Dana: Rp Z]
        A19 --> A22[Export Escrow Report]
        A20 --> A22
        A21 --> A22

        %% Menu 4: Verifikasi Petani
        A4 -->|4. Verifikasi Petani| A23[Lihat Pengajuan Petani Baru]
        A23 --> A24[Cek Detail Registrasi]
        A24 --> A25{Approve Akun?}
        A25 -->|Ya| A26[Aktifkan Akun Petani<br/>Status: VERIFIED]
        A25 -->|Tidak| A27[Reject dengan Alasan<br/>Status: REJECTED]

        %% Menu 5: Broadcast Notifikasi
        A4 -->|5. Broadcast Promo| A28[Compose Message]
        A28 --> A29[Select Audience<br/>All Users / Role Specific]
        A29 --> A30[Schedule Broadcast]
        A30 --> A31[Monitor Delivery Status]

        %% Menu 6: Dashboard Analytics
        A4 -->|6. Analytics| A32[View Dashboard Metrics]
        A32 --> A33[Lihat: GMV, Transaction Count<br/>Active Users, Top Sellers<br/>Top Products, Conversion Rate]

        %% Kembali ke dashboard
        A7 --> A34[Kembali ke Dashboard]
        A8 --> A34
        A9 --> A34
        A12 --> A34
        A13 --> A34
        A14 --> A34
        A22 --> A34
        A26 --> A34
        A27 --> A34
        A31 --> A34
        A33 --> A34
        A34 --> A35{Lanjut?}
        A35 -->|Ya| A3
        A35 -->|Tidak| A36[Logout]
        A36 --> A37[Selesai]
    end

    %% ================== SISTEM ==================
    subgraph Sistem["⚙️ SISTEM"]
        %% Data Validation
        B1[Validasi Data Input]
        B2{Data Valid?}
        B3[Error Message]
        B4[Simpan ke Database]
        B5{Sukses<br/>Disimpan?}
        B6[Display Success Message]

        %% Notification & Update
        B7{Perlu notifikasi?}
        B8[Send Notification<br/>ke User Terkait]
        B9[Update Cache<br/>Master Data]
        B10[Log Action<br/>Audit Trail]

        %% Escrow Specific
        B11[Hitung Total Escrow<br/>Ditahan + Dikirim + Refund]
        B12[Generate Escrow Report]
        B13[Reconcile dengan<br/>Payment Gateway]
    end

    %% ================== KONEKSI ADMIN – SISTEM ==================
    A7 --> B1
    A8 --> B1
    A9 --> B1
    A12 --> B1
    A13 --> B1
    A26 --> B1
    A27 --> B1

    B1 --> B2
    B2 -->|Tidak| B3
    B3 --> A34
    B2 -->|Ya| B4
    B4 --> B5
    B5 -->|Tidak| B3
    B5 -->|Ya| B6

    B6 --> B7
    B7 -->|Ya| B8
    B7 -->|Tidak| B9
    B8 --> B9
    B9 --> B10
    B10 --> A34

    %% Escrow Monitoring Flow
    A17 --> B11
    B11 --> B12
    B12 --> B13
    B13 --> A22

    %% ============ STYLE ============
    style A1 fill:#000,stroke:#000,color:#fff
    style A37 fill:#000,stroke:#000,color:#fff
    style A2 fill:#1e3a8a,stroke:#000,color:#fff
    style A3 fill:#1e3a8a,stroke:#000,color:#fff

    classDef admin fill:#dbeafe,stroke:#1e3a8a,color:#000,stroke-width:2px;
    classDef sistem fill:#fef5e7,stroke:#f39c12,color:#000,stroke-width:2px;
    classDef decision fill:#d5f4e6,stroke:#27ae60,color:#000,stroke-width:2px;
    classDef escrow fill:#fff9e6,stroke:#fbc02d,color:#000,stroke-width:3px;
    classDef success fill:#d4edda,stroke:#28a745,color:#000,stroke-width:2px;
    classDef error fill:#f8d7da,stroke:#dc3545,color:#000,stroke-width:2px;

    class A5,A10,A15,A23,A24,A28,A29,A30,A32 admin;
    class B1,B4,B6,B8,B9,B10 sistem;
    class A4,A6,A11,A18,A25,A35,B2,B5,B7 decision;
    class A17,A19,A20,A21,B11,B12,B13 escrow;
    class A26,B6 success;
    class A27,B3,A9 error;

```
