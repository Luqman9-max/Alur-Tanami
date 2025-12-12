# Activity Diagram Aktivitas Utama & Petani

```mermaid
graph TB
    %% ================== PEMBELI ==================
    subgraph Pembeli["PEMBELI"]
        P1[Mulai] --> P2[Browse & pilih produk]
        P2 --> P2a{Lanjut belanja?}
        P2a -->|Ya| P2
        P2a -->|Tidak| P3[Tambah ke keranjang]
        P3 --> P4[Checkout: pilih kota & input kupon]
        P4 --> P5[Lihat ringkasan total]
        P5 --> P6[Lakukan transfer & upload bukti]
        P6 --> P7[Menunggu hasil verifikasi]
        P7 --> P8{Status pesanan?}
        P8 -->|Disetujui| P9[Terima pesanan]
        P9 --> P10[Beri review]
        P10 --> P11[Selesai]
        P8 -->|Dibatalkan| P11
    end

    %% ================== SISTEM ==================
    subgraph Sistem["SISTEM"]
        S1[Validasi stok semua produk]
        S2{Stok cukup?}
        S3[Tolak: stok tidak cukup<br/>batalkan pesanan]

        S4[Validasi kupon & hitung diskon]
        S5[Hitung grand total<br/>]
        S6[Reserve stok 30 menit]

        S7[Terima & simpan bukti bayar]
        S8{Bukti diterima<br/>dalam 30 menit?}
        S9[Auto-cancel & release stok]

        S10[Update status:<br/>'Awaiting Payment']
        S11[Update status: 'Paid'<br/>& kurangi stok aktual]
        S12[Update status: 'Cancelled'<br/>& release stok]
        S13[Update status: 'Shipped']
        S14[Kirim notifikasi status<br/>ke pembeli]
    end

    %% ================== PETANI ==================
    subgraph Petani["PETANI"]
        F0[Login Petani]
        F0 --> F1[Dashboard Petani]
        F1 --> F2a[Menu Proses Pesanan]
        F1 --> F2b[Menu Kelola Produk]
        F1 --> F2c[Menu Kelola Ulasan]

        %% ---- Kelola Produk (ringkas) ----
        F2b --> F3[Lihat & kelola produk<br/>]
        F3 --> F1

        %% ---- Kelola Ulasan (ringkas) ----
        F2c --> F4[Lihat ulasan & balas komentar]
        F4 --> F1

        %% ---- Proses Pesanan (detail) ----
        F2a --> F5[Lihat order masuk]
        F5 --> F6[Pilih order status<br/>'Awaiting Payment']
        F6 --> F7[Lihat detail & bukti transfer]
        F7 --> F8{Pembayaran valid?}
        F8 -->|Ya| F9[Approve pembayaran]
        F8 -->|Tidak| F10[Tolak pembayaran<br/>+ isi alasan]

        F9 --> F11[Packing & kirim barang]
        F11 --> F12[Input nomor resi]
    end

    %% ============ KONEKSI PEMBELI – SISTEM (CHECKOUT) ============
    P4 --> S1
    S1 --> S2
    S2 -->|Tidak| S3
    S3 --> P11

    S2 -->|Ya| S4
    S4 --> S5
    S5 --> S6
    S6 --> P5

    %% ============ KONEKSI PEMBELI – SISTEM (UPLOAD & TIMEOUT) ============
    P6 --> S7
    S6 --> S8
    S8 -->|Tidak| S9
    S9 --> S12
    S12 --> S14
    S14 --> P7

    S8 -->|Ya| S10
    S7 --> S10

    %% ============ KONEKSI SISTEM – PETANI (VERIFIKASI) ============
    S10 --> F5          
    F7 --> F8

    %% kalau disetujui
    F9 --> S11
    S11 --> S14
    S11 --> F11

    %% kalau ditolak
    F10 --> S12
    S12 --> S14

    %% ============ PENGIRIMAN BARANG & STATUS SHIPPED ============
    F12 --> S13
    S13 --> S14
    S14 --> P7         

    %% ============ STYLE SEDERHANA (HITAM PUTIH) ============
    style P1 fill:#000,stroke:#000,color:#fff
    style P11 fill:#000,stroke:#000,color:#fff

    classDef box fill:#ffffff,stroke:#000,color:#000;
    class P2,P2a,P3,P4,P5,P6,P7,P8,P9,P10 box;
    class S1,S2,S3,S4,S5,S6,S7,S8,S9,S10,S11,S12,S13,S14 box;
    class F0,F1,F2a,F2b,F2c,F3,F4,F5,F6,F7,F8,F9,F10,F11,F12 box;
```

# Activity Diagram Admin

```mermaid
graph TB
    subgraph Admin["👨‍💼 ADMIN (OPERATOR)"]
        A1[Start] --> A2[Login Admin]
        A2 --> A3[Dashboard Admin]
        A3 --> A4{Pilih Menu}
        
        A4 -->|Kelola Ongkir| A5[Lihat Daftar Kota]
        A5 --> A6{Aksi?}
        A6 -->|Tambah| A7[Input Nama Kota<br/>& Tarif]
        A6 -->|Edit| A8[Update Harga Ongkir]
        
        A4 -->|Kelola Kupon| A9[Lihat Daftar Kupon]
        A9 --> A10{Aksi?}
        A10 -->|Buat Baru| A11[Input Kode, Diskon<br/>& Expired Date]
        A10 -->|Nonaktifkan| A12[Set Status: Inactive]
        
        A4 -->|Monitoring| A13[Lihat Semua Order]
        A13 --> A14[Filter & Export Data]
        
        A4 -->|Verifikasi Petani| A15[Lihat Pengajuan Petani]
        A15 --> A16{Approve<br/>Akun?}
        A16 -->|Ya| A17[Aktifkan Akun Petani]
        A16 -->|Tidak| A18[Tolak dengan Alasan]
        
        A7 --> A19[Kembali ke Dashboard]
        A8 --> A19
        A11 --> A19
        A12 --> A19
        A14 --> A19
        A17 --> A19
        A18 --> A19
        A19 --> A20[End]
    end
    
    subgraph Sistem["⚙️ SISTEM"]
        B1[Validasi Data Input]
        B2{Data Valid?}
        B3[Simpan ke Database]
        B4[Tampilkan Error]
        B5{Berhasil<br/>Disimpan?}
        B6[Tampilkan Pesan Sukses]
        B7[Send Notifikasi<br/>jika diperlukan]
        B8[Update Cache<br/>Master Data]
    end
    
    A7 --> B1
    A8 --> B1
    A11 --> B1
    A12 --> B1
    A17 --> B1
    A18 --> B1
    
    B1 --> B2
    B2 -->|Ya| B3
    B2 -->|Tidak| B4
    B4 --> A19
    B3 --> B5
    B5 -->|Ya| B6
    B5 -->|Tidak| B4
    B6 --> B7
    B7 --> B8
    B8 --> A19
    
%% STYLE
style A1 fill:#000,stroke:#000,color:#fff
style A20 fill:#000,stroke:#000,color:#fff

classDef white fill:#fff,stroke:#000,color:#000;
class A2,A3,A4,A5,A7,A8,A9,A11,A12,A13,A14,A15,A17,A18,A19 white;
class B1,B3,B4,B6,B7,B8 white;
