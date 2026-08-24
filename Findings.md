# Findings

## Step 1: Initial Triage & Metadata Analysis (Forensics - step 1.jpg)
-Pemeriksaan awal dilakukan dengan ls -lh untuk melihat ukuran file.</br>
-Integritas file dijaga dengan menghasilkan nilai hash SHA256 menggunakan sha256sum.</br>
-Ekstraksi metadata dasar menggunakan exiftool untuk melihat informasi seperti Creator (Microsoft Word LTSC), waktu pembuatan, dan ID dokumen.

## Step 2: String Analysis & URL Extraction (Forensics - Step 2.jpg)
-Menggunakan perintah strings yang digabungkan dengan grep untuk mengekstrak dan mencari URL atau tautan tersembunyi di dalam file PDF.</br>
-Membaca isi teks mentah dari dokumen yang telah diekstrak ke dalam hasil.txt untuk menganalisis konteks undangan (seperti kop surat, jadwal, dan instruksi kehadiran).

## Step 3: Keyword Hunting & Flagging (Forensics - Step 3.jpg)
-Melakukan pencarian spesifik menggunakan grep pada teks yang diekstrak untuk menemukan kata kunci indikasi penipuan (Red Flags) seperti "SMS", "Transfer",</br> "Pembayaran", atau nomor telepon.</br>
-Tahap ini mengungkap adanya instruksi pembayaran tiket/akomodasi sementara melalui transfer bank atau SMS, yang merupakan teknik penipuan klasik.

## Step 4: PDF Structure & Regex Phone Number Extraction (Forensics - Step 4.jpg)
-Mendapatkan informasi struktural PDF menggunakan pdfinfo (versi PDF, ukuran halaman, jumlah halaman).</br>
-Mengecek adanya file yang disematkan (embedded files) dengan mencari string spesifik.</br>
-Menggunakan Regular Expression (Regex) dengan grep untuk secara akurat memfilter dan mengekstrak nomor telepon/WhatsApp yang disisipkan dalam dokumen.

## Step 5: Image & Object Listing (Forensics - Step 5.jpg)
-Memastikan tidak ada lampiran tersembunyi menggunakan pdfdetach.</br>
-Menganalisis objek gambar di dalam PDF menggunakan pdfimages -list untuk melihat daftar gambar, resolusi, tipe warna, dan ukuran objek sebelum dilakukan ekstraksi penuh.

## Step 6: Image Extraction & Hash Verification (Forensics - Step 6.jpg)
-Mengekstrak metadata dari salah satu gambar yang dihasilkan.</br>
-Melakukan verifikasi hash SHA256 pada seluruh gambar yang diekstrak (.jpg). </br>
-Hasilnya menunjukkan banyak gambar memiliki nilai hash yang sama persis (duplikat). Ini membuktikan bahwa pembuat dokumen melakukan copy-paste aset grafis secara kasar.

## Step 7: File Signature & Hex Analysis (Forensics - Step 7.jpg)
-Memverifikasi tipe file sebenarnya dari objek yang diekstrak menggunakan perintah file.</br>
-Menganalisis magic numbers atau header heksadesimal menggunakan xxd untuk memastikan bahwa struktur file sesuai dengan standar JPEG (ditandai dengan FFD8 FFE0).

## Step 8: Metadata Comparison (Forensics - step 8.jpg)
-Menggunakan exiftool untuk mengekspor metadata dari dua gambar yang berbeda ke dalam file teks (metadata_001.txt dan metadata_002.txt).</br>
-Melakukan perbandingan menggunakan diff untuk melihat perbedaan spesifik antar metadata gambar, yang berguna untuk melacak asal-usul aset.

## Step 9: Batch Header Inspection (Forensics - Step 9.jpg)
-Menerapkan looping di shell dengan xxd untuk membaca beberapa byte pertama dari seluruh file .jpg secara massal.</br>
-Mengecek anomali metadata secara spesifik menggunakan grep -Ei pada exiftool untuk mendeteksi jejak modifikasi perangkat lunak (seperti Photoshop atau profil ICC).

## Step 10: Integrity & Steganography Checks (Forensics - Step 10.jpg)
-Menggunakan jpeginfo untuk memverifikasi integritas struktural dari gambar yang diekstrak (memastikan tidak ada file yang corrupt). </br>
-Menjalankan binwalk pada gambar untuk memindai apakah ada file tersembunyi, payload, atau tanda tangan tambahan yang disisipkan (Steganography).

## Kesimpulan
Dari Gambar-gambar yang diekstrak diidentifikasi sebagai berkas JPEG yang valid berdasarkan pemeriksaan yang telah dilakukan. </br>
Beberapa gambar memiliki hash / sidik jari yang sama, yang tertanam dengan jelas selama pemindaian. </br>
Tidak ada metadata atau tanda tangan tertanam dengan sendirinya, membuktikan tidak ada manipulasi seperti malware atau serangan yang ditanam dan informasi tersembunyi. </br>
</br>
Tetapi terdapat kejanggalan dalam PDF tersebut yang merupakan manipulasi copy-paste dari gambar dan objek kata yang menjadi objek gambar.
