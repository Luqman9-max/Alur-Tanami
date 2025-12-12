# Activity Diagram Transaksi Utama with Exception Handling

```mermaid

graph TB
    %% ================== SWIM LANE PEMBELI ==================
    subgraph Pembeli["👤 PEMBELI (CUSTOMER)"]
        A1[Start] --> A2[Browse dan Pilih Produk]
        A2 --> A3[Tambah ke Keranjang]
        A3 --> A4{Lanjut Belanja?}
        A4 -->|Ya| A2
        A4 -->|Tidak| A5[Checkout & Pilih Kota]
        A5 --> A6[Input Kupon Diskon<br/>opsional]
        A6 --> A7[Menunggu Hitung Total]
        A7 --> A8[Review Grand Total<br/>Subtotal + Ongkir - Diskon]
        A8 --> A9[Menunggu Reserve Stok<br/>30 menit]
        A9 --> A10{Upload Bukti<br/>dalam 30 Menit?}
        A10 -->|Ya| A11[Transfer & Upload<br/>Bukti Bayar]
        A10 -->|Tidak| A12[Pesanan Dibatalkan<br/>Timeout]
        A11 --> A13[Menunggu Verifikasi]
        A13 --> A14{Notifikasi Diterima?}
        A14 -->|Approved| A15[Terima Pesanan]
        A14 -->|Rejected| A12
        A15 --> A16[Beri Review & Rating]
        A12 --> A17[End]
        A16 --> A17[End]
    end

    %% ================== SWIM LANE SISTEM ==================
    subgraph Sistem["⚙️ SISTEM / QUALITY CONTROL"]
        B1[Validasi Stok Tersedia]
        B2{Stok<br/>Tersedia?}
        B3[Tampilkan Error<br/>'Stok Habis'<br/>Hapus dari Keranjang]
        
        B4[Terima Input Kupon]
        B5{Kupon Valid?}
        B6[Kupon Tidak Valid<br/>Tampilkan Error]
        B7[Terapkan Diskon Kupon]
        B8[Hitung Grand Total<br/>Subtotal + Ongkir - Diskon]
        
        B9[Reserve Stok<br/>Timeout: 30 menit]
        B10[Set Timer 30 Menit]
        B11{Upload Bukti<br/>dalam 30 Menit?}
        B12[Auto Cancel Order]
        B13[Release Reserved Stok]
        B14[Update Status:<br/>'Cancelled']
        
        B15[Terima & Verifikasi<br/>Bukti Transfer]
        B16{Pembayaran<br/>Valid?}
        B17[Kurangi Stok Aktual<br/>Update Status: Paid]
        B18[Release Reserved Stok<br/>Cancel Order]
        B19[Send Notifikasi Status<br/>]
    end

    %% ================== SWIM LANE PETANI ==================
    subgraph Petani["🌾 PETANI / SELLER"]
        C1[Kelola Produk & Stok]
        C2[Terima Notifikasi<br/>Order Masuk]
        C3[Verifikasi Pembayaran<br/>Manual]
        C4{Approve<br/>Pembayaran?}
        C5[Packing Produk]
        C6[Kirim Barang &<br/>Input Resi]
        C7[Update Status: Shipped]
        C8[Reject dengan Alasan]
    end

    %% ================== ALUR PROSES - CHECKOUT KE VALIDASI STOK ==================
    A5 --> B1
    B1 --> B2
    B2 -->|Tidak| B3
    B3 --> A2  
    B2 -->|Ya| B4  

    %% ================== ALUR PROSES - INPUT KUPON & VALIDASI ==================
    A6 --> B4
    B4 --> B5
    B5 -->|Tidak Valid| B6
    B6 --> B5  
    B5 -->|Valid| B7  
    B7 --> B8

    %% ================== ALUR PROSES - HITUNG TOTAL & RESERVE STOK ==================
    A7 --> B8
    B8 --> A8
    A8 --> B9
    B9 --> B10

    %% ================== ALUR PROSES - TIMER 30 MENIT & UPLOAD BUKTI ==================
    A9 --> B11
    B11 -->|Tidak| B12
    B12 --> B13
    B13 --> B14
    B14 --> B19
    B19 --> A14

    B11 -->|Ya| A11
    A11 --> B15

    %% ================== ALUR PROSES - VERIFIKASI PEMBAYARAN (SISTEM + PETANI) ==================
    B15 --> C2
    C2 --> C3
    C3 --> C4
    C4 -->|Ya| B16
    C4 -->|Tidak| C8

    %% reject oleh petani
    C8 --> B18
    B18 --> B14

    %% validasi pembayaran (sisi sistem)
    B16 -->|Ya| B17
    B16 -->|Tidak| B18

    B17 --> C5
    C5 --> C6
    C6 --> C7
    C7 --> B19
    B18 --> B19
    B14 --> B19
    B19 --> A14

%% STYLE Hitam Putih
style A1 fill:#000,stroke:#000,color:#fff
style A17 fill:#000,stroke:#000,color:#fff

classDef white fill:#fff,stroke:#000,color:#000;
class A2,A3,A4,A5,A6,A7,A8,A9,A10,A11,A12,A13,A14,A15,A16 white;

class B1,B2,B3,B4,B5,B6,B7,B8,B9,B10,B11,B12,B13,B14,B15,B16,B17,B18,B19 white;

class C2,C3,C4,C5,C6,C7,C8 white;

```

# Activity Diagram Petani (Seller)

```mermaid
graph TB
    subgraph Petani["🌾 PETANI (SELLER)"]
        A1[Start] --> A2[Login Petani]
        A2 --> A3[Dashboard Petani]
        A3 --> A4{Pilih Menu}
        
        A4 -->|Kelola Produk| A5[Lihat Katalog<br/>Produk Saya]
        A5 --> A6{Aksi Produk?}
        A6 -->|Tambah| A7[Input Nama, Harga,<br/>Stok, Kategori, Foto]
        A6 -->|Edit| A8[Update Data Produk]
        A6 -->|Hapus| A9[Hapus Produk]
        
        A4 -->|Proses Pesanan| A10[Lihat Order Masuk]
        A10 --> A11[Pilih Order Status<br/>Awaiting Payment]
        A11 --> A12[Download & Cek<br/>Bukti Transfer]
        A12 --> A13{Bukti Transfer<br/>Valid?}
        A13 -->|Ya| A14[Approve Pembayaran]
        A14 --> A15[Packing Produk]
        A15 --> A16[Kirim Barang]
        A16 --> A17[Input Nomor Resi]
        A17 --> A18[Update Status: Shipped]
        A13 -->|Tidak| A19[Tolak Pembayaran]
        A19 --> A20[Input Alasan Penolakan]
        
        A4 -->|Kelola Ulasan| A21[Lihat Review Produk]
        A21 --> A22[Balas Komentar Pembeli<br/>opsional]
        
        A7 --> A23[Kembali ke Dashboard]
        A8 --> A23
        A9 --> A23
        A18 --> A23
        A20 --> A23
        A22 --> A23
        A23 --> A24[End]
    end
    
    subgraph Sistem["⚙️ SISTEM"]
        B1[Validasi Data Produk]
        B2{Stok & Harga<br/>Valid?}
        B3[Simpan ke Database]
        B4[Tampilkan Error]
        B5[Update Reserved Stock]
        B6[Kurangi Stok Aktual]
        B7[Release Reserved Stock]
        B8[Send Notifikasi<br/>ke Pembeli]
        B9[Update Status Order]
    end
    
    subgraph Pembeli["👤 PEMBELI"]
        C1[Terima Notifikasi Status]
        C2{Status =<br/>Shipped?}
        C3[Terima Paket]
        C4[Konfirmasi Penerimaan]
        C5[Status: Completed]
        C6[Beri Review & Rating]
    end
    
    A7 --> B1
    A8 --> B1
    B1 --> B2
    B2 -->|Ya| B3
    B2 -->|Tidak| B4
    B4 --> A23
    B3 --> A23
    
    A11 --> B5
    A14 --> B6
    B6 --> A15
    A19 --> B7
    A20 --> B7
    B7 --> B8
    
    A18 --> B9
    B9 --> B8
    B8 --> C1
    C1 --> C2
    C2 -->|Ya| C3
    C2 -->|Tidak| C1
    C3 --> C4
    C4 --> C5
    C5 --> C6
    
%% STYLE
style A1 fill:#000,stroke:#000,color:#fff
style A24 fill:#000,stroke:#000,color:#fff
classDef white fill:#fff,stroke:#000,color:#000;
class A2,A3,A4,A5,A7,A8,A9,A10,A11,A12,A13,A14,A15,A16,A17,A18,A19,A20,A21,A22,A23 white;
class B1,B2,B3,B4,B5,B6,B7,B8,B9 white;
class C1,C2,C3,C4,C5,C6 white;


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
