# PROPOSAL TESIS

## Rancang Bangun dan Evaluasi Sistem Intelligent Document Processing Berbasis Field-Aware Confidence Routing untuk Ekstraksi Informasi Terstruktur pada Dokumen Administratif Berbahasa Indonesia

**Nama:** [NAMA MAHASISWA]  
**NIM:** [NIM]  
**Program Studi:** Magister Teknologi Informasi  
**Institusi:** BINUS Graduate Program, Universitas Bina Nusantara  
**Tahun:** 2026

---

# BAB I  
# PENDAHULUAN

## 1.1 Latar Belakang

Dokumen administratif masih menjadi bagian penting dalam proses bisnis sehari-hari. Invoice, kuitansi, purchase order, formulir, dan bukti pembayaran sering dipakai sebagai dasar pencatatan, validasi transaksi, dan pengambilan keputusan. Walaupun banyak dokumen sudah tersedia dalam bentuk digital, data di dalamnya belum tentu langsung siap digunakan oleh sistem. Dokumen bisa berupa PDF, hasil scan, foto dari ponsel, atau file yang kualitasnya tidak selalu rapi.

Masalah yang muncul bukan hanya membaca teks dari dokumen, tetapi memahami informasi mana yang penting. Sebagai contoh, pada invoice terdapat beberapa field seperti nomor invoice, tanggal, nama vendor, subtotal, pajak, dan total pembayaran. Jika sistem salah mengambil nilai total, dampaknya bisa lebih besar dibanding salah membaca kata biasa. Karena itu, penelitian ini tidak hanya berfokus pada OCR, tetapi pada bagaimana sistem memahami dan mengambil keputusan pada level field.

Dalam penelitian ini, istilah **field** berarti satu unit informasi yang ingin diambil dari dokumen. Contohnya adalah `invoice_number`, `invoice_date`, `vendor_name`, `tax_amount`, dan `grand_total`. Sementara itu, **field awareness** berarti kemampuan sistem untuk memperlakukan setiap field secara berbeda sesuai jenis data, posisi, aturan validasi, tingkat risiko, dan tingkat keyakinan sistem terhadap hasil ekstraksi. Dengan kata lain, sistem tidak hanya bertanya "apakah dokumen ini berhasil dibaca?", tetapi juga "field mana yang sudah aman diterima, field mana yang perlu diperiksa ulang, dan field mana yang harus dikoreksi manusia?".

Pendekatan field-aware menjadi penting karena dokumen administratif biasanya bersifat semi-terstruktur. Beberapa informasi memiliki label yang jelas, misalnya "Invoice No" atau "Tanggal", tetapi informasi lain dapat muncul pada tabel, footer, atau bagian ringkasan pembayaran. Format rupiah, penggunaan stempel, tanda tangan, kualitas scan, serta variasi layout lokal Indonesia juga membuat proses ekstraksi menjadi lebih menantang.

Perkembangan teknologi terbaru menunjukkan bahwa kemampuan sistem pemrosesan dokumen sudah meningkat pesat. LayoutLMv3 memperkenalkan pre-training multimodal untuk memahami teks dan layout dokumen (Huang et al., 2022). Donut menunjukkan bahwa document understanding dapat dilakukan tanpa OCR tradisional melalui pendekatan OCR-free (Kim et al., 2022). GOT-OCR 2.0 memperkenalkan konsep OCR-2.0 dengan model unified end-to-end sekitar 580 juta parameter yang dapat menangani teks, formula, tabel, chart, dan region-level recognition (Wei et al., 2024). Qwen2.5-VL juga menunjukkan kemampuan kuat untuk document parsing, object localization, dan structured data extraction dari invoice, form, tabel, chart, dan layout dokumen (Bai et al., 2025). Selain itu, Docling menyediakan toolkit open-source untuk konversi PDF dengan dukungan layout analysis dan table structure recognition (Auer et al., 2024), sedangkan OmniDocBench memberikan benchmark dokumen yang lebih beragam dan detail untuk mengevaluasi sistem document parsing (Ouyang et al., 2024).

Di sisi lain, penelitian terbaru tentang hybrid OCR-LLM untuk ekstraksi informasi dokumen skala enterprise menunjukkan bahwa pendekatan tunggal tidak selalu optimal. Wang dan Shen (2025) mengusulkan framework yang memilih strategi ekstraksi berdasarkan karakteristik dokumen dan mengevaluasi 25 konfigurasi pada beberapa format dokumen, termasuk PNG, DOCX, XLSX, dan PDF. Paper tersebut relevan sebagai baseline metodologis karena menekankan trade-off akurasi dan efisiensi, penggunaan OCR+LLM secara adaptif, serta format-aware routing untuk dokumen yang berulang dan semi-terstruktur. Namun, karena paper tersebut masih berstatus arXiv preprint dan belum memiliki rujukan jurnal atau konferensi terpublikasi, penelitian ini tidak menjadikannya satu-satunya fondasi teoretis. Paper tersebut digunakan sebagai patokan baseline dan inspirasi metodologi, sementara landasan utama tetap diperkuat oleh penelitian yang sudah terpublikasi pada area document understanding, OCR, table understanding, confidence calibration, dan selective prediction.

Berdasarkan posisi tersebut, kemajuan model tidak langsung menyelesaikan semua masalah operasional. Model besar seperti Vision-Language Model (VLM) memang kuat, tetapi tidak selalu efisien jika digunakan untuk semua field. Field sederhana dapat diproses dengan OCR dan rule yang lebih ringan, sedangkan field sulit atau berisiko dapat dinaikkan ke model yang lebih kuat. Berbeda dari format-aware routing yang memilih jalur berdasarkan jenis atau format dokumen secara umum (Wang & Shen, 2025), penelitian ini mengusulkan **field-aware confidence routing**, yaitu mekanisme yang menentukan jalur pemrosesan berdasarkan confidence, validasi, konteks, dan risiko masing-masing field. Dengan demikian, keputusan tidak berhenti pada level dokumen, tetapi turun ke level informasi yang benar-benar akan dipakai oleh sistem bisnis.

## 1.2 Perumusan Masalah

Berdasarkan latar belakang tersebut, rumusan masalah dalam penelitian ini adalah sebagai berikut.

1. Bagaimana merancang sistem Intelligent Document Processing yang berfokus pada field awareness untuk dokumen administratif berbahasa Indonesia?
2. Bagaimana menghitung confidence score pada level field dengan mempertimbangkan kualitas OCR, hasil ekstraksi, validasi skema, konsistensi konteks, dan riwayat koreksi?
3. Bagaimana sistem menentukan apakah suatu field dapat diterima otomatis, perlu diproses ulang dengan model yang lebih kuat, atau perlu dikoreksi manusia?
4. Bagaimana correction memory dapat digunakan agar koreksi manusia pada dokumen sebelumnya membantu proses ekstraksi pada dokumen berikutnya?
5. Bagaimana performa metode yang diusulkan dibandingkan dengan baseline seperti OCR+rule, OCR+LLM, direct VLM-OCR, dan routing berbasis format?

## 1.3 Tujuan Penelitian

Tujuan dari penelitian ini adalah:

1. Merancang prototipe sistem Intelligent Document Processing untuk dokumen administratif berbahasa Indonesia.
2. Mengembangkan pendekatan field-aware confidence scoring yang mudah dijelaskan dan dapat diaudit.
3. Mengembangkan mekanisme routing pada level field untuk keputusan accept, escalate, dan human review.
4. Mengembangkan correction memory berbasis kemiripan template atau layout dokumen.
5. Mengevaluasi sistem menggunakan metrik akurasi field, calibration, false accept rate, latency, STP rate, dan waktu koreksi manusia.

## 1.4 Manfaat Penelitian

Secara akademik, penelitian ini diharapkan dapat memberikan kontribusi pada pengembangan workflow IDP yang tidak hanya mengejar akurasi rata-rata, tetapi juga memperhatikan risiko pada masing-masing field. Penelitian ini juga mencoba menghubungkan konsep confidence calibration dan selective prediction dengan kebutuhan praktis pemrosesan dokumen.

Secara praktis, penelitian ini diharapkan dapat membantu organisasi mengurangi input manual, mempercepat proses pengolahan dokumen, dan mengurangi kesalahan pada field penting seperti nominal, tanggal, dan nomor dokumen. Sistem yang dirancang juga diharapkan lebih realistis untuk digunakan karena tidak memaksa semua dokumen diproses dengan model yang paling mahal.

## 1.5 Ruang Lingkup Penelitian

Ruang lingkup penelitian ini adalah:

1. Dokumen yang digunakan adalah dokumen administratif berbahasa Indonesia, terutama invoice, kuitansi, formulir, dan bukti pembayaran.
2. Field utama yang diteliti meliputi nomor dokumen, tanggal, nama entitas, NPWP atau identitas pajak, subtotal, pajak, total, dan item tabel.
3. Sistem dibangun sebagai prototipe penelitian, bukan sistem produksi penuh.
4. VLM digunakan sebagai baseline modern dan jalur eskalasi, bukan satu-satunya metode ekstraksi.
5. Evaluasi dilakukan pada data yang dianotasi, baik dari dokumen nyata yang dianonimkan maupun dokumen sintetis yang dibuat menyerupai kondisi dokumen Indonesia.

---

# BAB II  
# TINJAUAN PUSTAKA

## 2.1 Intelligent Document Processing

Intelligent Document Processing (IDP) adalah proses mengubah dokumen tidak terstruktur atau semi-terstruktur menjadi data terstruktur yang dapat digunakan oleh sistem. Dalam praktiknya, IDP biasanya terdiri dari beberapa tahap, seperti input dokumen, preprocessing, OCR atau document parsing, ekstraksi informasi, validasi, dan ekspor hasil.

Pada dokumen administratif, IDP tidak cukup hanya menghasilkan teks. Sistem harus mengetahui bagian mana yang merupakan nomor dokumen, tanggal, nama vendor, atau total pembayaran. Karena itu, penelitian ini memandang IDP sebagai workflow pengambilan keputusan, bukan hanya pipeline OCR.

## 2.2 OCR and Document Layout Analysis

Optical Character Recognition (OCR) digunakan untuk membaca teks dari gambar atau dokumen scan. Tesseract merupakan salah satu OCR engine klasik yang banyak digunakan (Smith, 2007). PaddleOCR dan PP-OCR menunjukkan perkembangan OCR modern yang lebih ringan dan praktis untuk berbagai bahasa (Du et al., 2020). Namun, OCR saja belum cukup karena dokumen memiliki layout yang berbeda-beda.

Document Layout Analysis membantu sistem memahami struktur halaman, seperti judul, paragraf, tabel, header, footer, dan gambar. LayoutParser menyediakan toolkit untuk layout analysis berbasis deep learning (Shen et al., 2021). PubLayNet dan DocLayNet menyediakan dataset besar untuk melatih dan mengevaluasi deteksi layout (Pfitzmann et al., 2022; Zhong et al., 2019). DocLayout-YOLO juga menunjukkan arah baru model layout yang lebih cepat dan adaptif (Zhao et al., 2024).

Bagi penelitian ini, OCR dan layout analysis menjadi dasar untuk menemukan kandidat field. Namun, hasil dari tahap ini masih harus divalidasi karena teks yang terbaca belum tentu merupakan field yang benar.

## 2.3 Table Understanding

Banyak dokumen administratif memiliki informasi penting dalam bentuk tabel, misalnya daftar item pada invoice atau rincian pembayaran. TableBank, PubTables-1M, dan TableFormer menunjukkan bahwa table detection dan table structure recognition membutuhkan pendekatan khusus karena struktur baris dan kolom tidak selalu terlihat jelas (Li et al., 2020; Nassar et al., 2022; Smock et al., 2022, 2023).

Pada penelitian ini, table parser digunakan sebagai salah satu jalur ekstraksi. Namun, keputusan akhir tetap dibuat pada level field. Misalnya, sistem dapat memakai table parser untuk `line_items`, tetapi tetap memakai rule atau OCR biasa untuk `invoice_date` jika field tersebut sudah jelas.

## 2.4 OCR-free Document Understanding

Pendekatan OCR-free mencoba memahami dokumen langsung dari gambar tanpa memisahkan OCR sebagai tahap utama. Donut merupakan salah satu contoh penting karena menggunakan Transformer untuk document understanding tanpa OCR engine tradisional (Kim et al., 2022). Nougat juga menunjukkan kemampuan model visual Transformer untuk mengubah dokumen akademik menjadi format markup (Blecher et al., 2023).

Pendekatan ini menarik karena dapat mengurangi kesalahan yang biasanya terjadi ketika OCR salah membaca teks. Namun, untuk dokumen administratif, pendekatan OCR-free belum tentu selalu paling efisien. Beberapa field mudah tetap dapat diselesaikan dengan metode yang lebih sederhana. Karena itu, penelitian ini tidak memilih satu pendekatan tunggal, tetapi menggunakan routing yang menyesuaikan kondisi field.

## 2.5 Vision-Language Models for Structured Information Extraction

Vision-Language Model (VLM) adalah model yang dapat memproses gambar dan teks sekaligus. Dalam konteks dokumen, VLM dapat membantu membaca layout, memahami posisi informasi, dan menghasilkan data terstruktur. Qwen2.5-VL adalah salah satu contoh SOTA terbaru. Technical report Qwen2.5-VL menjelaskan bahwa model ini mendukung visual recognition, object localization, document parsing, structured data extraction dari invoice, form, dan tabel, serta tersedia dalam ukuran 3B, 7B, dan 72B (Bai et al., 2025).

GOT-OCR 2.0 juga penting karena memperluas konsep OCR dari sekadar membaca teks menjadi OCR-2.0. Model ini memiliki sekitar 580 juta parameter dan dirancang untuk menangani berbagai jenis "karakter" visual, termasuk teks biasa, formula, tabel, chart, dan bahkan region-level recognition (Wei et al., 2024). Dengan kemampuan tersebut, GOT-OCR 2.0 dapat menjadi baseline kuat untuk direct VLM/OCR-style extraction.

Docling tidak sama dengan VLM generatif, tetapi relevan sebagai SOTA toolkit untuk document conversion. Docling menggabungkan layout analysis berbasis DocLayNet dan table structure recognition berbasis TableFormer untuk menghasilkan representasi dokumen yang lebih terstruktur (Auer et al., 2024). OmniDocBench juga relevan karena menunjukkan bahwa evaluasi document parsing perlu mencakup variasi dokumen yang lebih luas dan anotasi yang lebih detail (Ouyang et al., 2024).

Walaupun model-model tersebut kuat, penelitian ini tetap memandang VLM sebagai bagian dari sistem, bukan pengganti semua komponen. Alasannya sederhana: tidak semua field membutuhkan model besar. Field yang mudah sebaiknya diproses dengan jalur yang murah dan cepat, sedangkan field yang sulit dapat dinaikkan ke VLM.

## 2.6 Confidence Calibration and Selective Prediction

Confidence score adalah nilai yang menunjukkan seberapa yakin sistem terhadap hasilnya. Namun, confidence tidak selalu sama dengan kebenaran. Guo et al. (2017) menunjukkan bahwa neural network modern dapat memiliki confidence yang tidak terkalibrasi, sehingga prediksi dengan skor tinggi belum tentu benar. Geifman dan El-Yaniv (2017) memperkenalkan selective classification, yaitu kondisi ketika sistem boleh menolak membuat prediksi untuk mengurangi risiko.

Konsep ini cocok dengan IDP. Sistem sebaiknya tidak memaksa semua field langsung diterima. Jika confidence rendah atau validasi gagal, sistem dapat memilih untuk memproses ulang field tersebut atau mengirimnya ke human review.

## 2.7 Human-in-the-Loop dan Correction Memory

Human-in-the-loop (HITL) berarti manusia dilibatkan untuk memeriksa atau memperbaiki hasil sistem. Dalam penelitian ini, HITL tidak dimaksudkan untuk menggantikan sistem otomatis, tetapi sebagai pengaman untuk field yang berisiko.

Sebelum istilah correction memory digunakan lebih jauh, perlu dijelaskan bahwa **correction memory** adalah penyimpanan pola koreksi manusia agar dapat dipakai lagi pada dokumen berikutnya. Misalnya, pada template invoice tertentu sistem sering salah mengambil nilai `subtotal` sebagai `grand_total`. Setelah manusia memperbaikinya, pola tersebut disimpan. Jika dokumen baru memiliki layout yang mirip, sistem dapat menggunakan pola koreksi tersebut sebagai sinyal tambahan.

Dengan correction memory, koreksi manusia tidak berhenti sebagai pekerjaan manual, tetapi menjadi data pembelajaran workflow.

## 2.8 Penelitian Terdahulu dan Posisi Penelitian

Penelitian terdahulu dapat dikelompokkan menjadi beberapa area. Area pertama adalah OCR dan layout analysis, seperti Tesseract, PP-OCR, LayoutParser, PubLayNet, dan DocLayNet (Du et al., 2020; Pfitzmann et al., 2022; Shen et al., 2021; Smith, 2007; Zhong et al., 2019). Area kedua adalah document understanding berbasis Transformer, seperti LayoutLM, LayoutLMv2, LayoutLMv3, DocFormer, Donut, dan Nougat (Appalaraju et al., 2021; Blecher et al., 2023; Huang et al., 2022; Kim et al., 2022; Xu et al., 2020, 2021). Area ketiga adalah table understanding, seperti TableBank, PubTables-1M, dan TableFormer (Li et al., 2020; Nassar et al., 2022; Smock et al., 2022). Area keempat adalah VLM dan document parsing modern, seperti Qwen2.5-VL, GOT-OCR 2.0, Docling, MinerU, dan OmniDocBench (Auer et al., 2024; Bai et al., 2025; Ouyang et al., 2024; Wang et al., 2024; Wei et al., 2024).

Posisi penelitian ini berada di antara area-area tersebut. Penelitian ini tidak bertujuan membuat OCR baru atau VLM baru. Fokus penelitian adalah merancang workflow IDP yang field-aware, sehingga setiap field memiliki confidence, validasi, dan jalur keputusan sendiri. Dengan demikian, kontribusi penelitian lebih dekat pada orkestrasi dan manajemen risiko ekstraksi data.

## 2.9 Analisis Baseline Hybrid OCR-LLM

Paper *Hybrid OCR-LLM Framework for Enterprise-Scale Document Information Extraction Under Copy-heavy Task* dari Wang dan Shen (2025) relevan dengan penelitian ini karena sama-sama melihat ekstraksi dokumen sebagai masalah pemilihan strategi, bukan sekadar pemilihan satu model terbaik. Paper tersebut menguji kombinasi OCR dan LLM pada tugas ekstraksi dokumen yang repetitif, mengevaluasi beberapa format dokumen, serta menunjukkan bahwa strategi table-based dan routing berdasarkan karakteristik dokumen dapat meningkatkan akurasi dan latensi.

Secara substansi, paper tersebut layak dijadikan baseline metodologis karena memberi contoh cara membandingkan beberapa jalur ekstraksi, seperti direct extraction, replacement-based extraction, dan table-based extraction. Gagasan format-aware routing juga dekat dengan kebutuhan IDP praktis, terutama ketika dokumen memiliki template berulang. Namun, statusnya perlu diperlakukan hati-hati. Berdasarkan halaman arXiv, paper ini baru disubmit pada 11 Oktober 2025 dan sampai pengecekan proposal ini belum memiliki journal reference atau conference reference. Artinya, paper ini belum sekuat paper peer-reviewed sebagai dasar utama tesis.

Oleh karena itu, penelitian ini menggunakan Wang dan Shen (2025) sebagai baseline pembanding dan inspirasi rancangan eksperimen, bukan sebagai satu-satunya referensi dasar. Perbedaan utama penelitian ini adalah unit keputusan routing. Wang dan Shen (2025) menekankan format-aware atau document-characteristic-aware strategy selection, sedangkan penelitian ini menurunkannya ke level field. Dengan field-aware confidence routing, satu dokumen yang sama dapat memiliki beberapa keputusan berbeda: `invoice_date` dapat diterima otomatis, `grand_total` dapat dieskalasi ke VLM karena konflik subtotal-pajak, dan `line_items` dapat diarahkan ke table parser atau human review. Perbedaan ini membuat kontribusi penelitian lebih spesifik pada pengendalian risiko ekstraksi informasi terstruktur.

## 2.10 Gap Penelitian

Gap penelitian yang diambil adalah:

1. Banyak penelitian fokus pada peningkatan akurasi model, tetapi belum menjadikan keputusan level field sebagai pusat workflow.
2. VLM sudah kuat untuk document parsing, tetapi penggunaannya untuk semua field belum tentu efisien dari sisi biaya dan waktu.
3. Confidence calibration banyak dibahas pada klasifikasi, tetapi belum selalu diterapkan secara operasional pada ekstraksi dokumen.
4. HITL sering dipakai sebagai proses review, tetapi koreksi manusia belum selalu disimpan sebagai correction memory yang dipicu oleh kemiripan template.
5. Dokumen administratif Indonesia memiliki format lokal, variasi rupiah, tanggal, NPWP, stempel, dan kualitas scan yang perlu diuji secara khusus.

## 2.11 Novelty Penelitian

Novelty penelitian ini adalah rancangan **field-aware confidence routing** untuk IDP dokumen administratif Indonesia. Pada pendekatan ini, setiap field memiliki kandidat nilai, confidence score, hasil validasi, keputusan routing, dan riwayat koreksi. Field yang sudah aman dapat diterima otomatis, field yang belum yakin dapat dinaikkan ke model yang lebih kuat, dan field yang berisiko dapat dikirim ke human review.

---

# BAB III  
# METODE PENELITIAN

## 3.1 Kerangka Pikir

Kerangka pikir penelitian ini dimulai dari masalah dokumen administratif yang belum siap menjadi data terstruktur. Sistem kemudian membaca dokumen, menemukan kandidat field, menghitung confidence, menentukan jalur routing, dan menyimpan koreksi manusia sebagai correction memory.

![Gambar 3.1 Kerangka pikir field-aware IDP](generated_figures_v4/gambar_3_1_kerangka_pikir_field_aware.png)

**Gambar 3.1 Kerangka pikir field-aware IDP.**

## 3.2 Contoh Field Awareness pada Invoice

Bagian ini disiapkan untuk menjelaskan field awareness secara visual. Gambar invoice perlu diberi highlight pada beberapa field penting agar pembaca langsung memahami bahwa sistem mengambil keputusan pada level field, bukan hanya membaca seluruh dokumen.

> **[MASUKKAN GAMBAR INVOICE YANG DIBERI HIGHLIGHT FIELD-AWARE]**

**Gambar 3.2 Slot gambar invoice dengan highlight field-aware.**

Field yang disarankan untuk diberi highlight:

1. `invoice_number`
2. `invoice_date`
3. `vendor_name`
4. `subtotal`
5. `tax_amount`
6. `grand_total`
7. `line_items`

## 3.3 Langkah Penelitian

Langkah penelitian dirancang agar pengembangan sistem dan evaluasi dapat dilakukan secara bertahap.

![Gambar 3.3 Langkah penelitian](generated_figures_v4/gambar_3_2_langkah_penelitian.png)

**Gambar 3.3 Langkah penelitian.**

## 3.4 Jenis dan Pendekatan Penelitian

Penelitian ini menggunakan pendekatan rancang bangun dan evaluasi eksperimental. Rancang bangun dilakukan untuk membuat prototipe IDP, sedangkan evaluasi dilakukan untuk membandingkan performa metode usulan dengan beberapa baseline.

Pendekatan ini dipilih karena tujuan penelitian bukan hanya menjelaskan konsep, tetapi juga menguji apakah field-aware confidence routing benar-benar membantu meningkatkan akurasi dan menurunkan risiko kesalahan.

## 3.5 Data dan Objek Penelitian

Objek penelitian adalah dokumen administratif berbahasa Indonesia. Data yang digunakan ditargetkan minimal 500 dokumen agar variasi layout dan kualitas dokumen cukup untuk menguji routing.

| Aspek | Rancangan |
|---|---|
| Jumlah dokumen | Minimal 500 dokumen |
| Jenis dokumen | Invoice, kuitansi, formulir, bukti pembayaran |
| Variasi template | 12-20 layout |
| Field target | 10-20 field |
| Kondisi dokumen | PDF bersih, scan, foto miring, blur, noise, stempel |
| Variasi lokal | Rupiah, tanggal Indonesia, NPWP, PPN |

Data akan dibagi menjadi data pengembangan, validasi, dan pengujian. Data pengembangan digunakan untuk membuat rule dan prototipe awal. Data validasi digunakan untuk mengatur threshold dan bobot confidence. Data pengujian digunakan untuk evaluasi akhir.

## 3.6 Perancangan Sistem

Sistem yang diusulkan terdiri dari beberapa komponen utama: input dokumen, preprocessing, field candidate detection, extraction route, confidence and validation, routing decision, human review, correction memory, dan output terstruktur.

![Gambar 3.4 Arsitektur sistem](generated_figures_v4/gambar_3_3_arsitektur_sistem_bersih.png)

**Gambar 3.4 Arsitektur sistem field-aware confidence routing.**

Penjelasan komponen:

1. **Input dokumen:** menerima PDF, scan, atau foto.
2. **Preprocessing:** memperbaiki orientasi, kualitas gambar, dan noise.
3. **Field candidate detection:** mencari kandidat field berdasarkan anchor text, layout, dan pola template.
4. **Extraction route:** menjalankan OCR+rule, table parser, OCR+LLM, atau direct VLM-OCR sesuai kebutuhan.
5. **Confidence and validation:** menghitung confidence dan memeriksa aturan field.
6. **Routing decision:** menentukan accept, escalate, atau human review.
7. **Correction memory:** menyimpan pola koreksi manusia untuk dokumen yang mirip.
8. **Output:** menghasilkan data terstruktur seperti JSON, CSV, atau database.

## 3.7 Model Data Field

Setiap field disimpan sebagai data terstruktur. Contoh struktur field adalah:

```json
{
  "field_name": "grand_total",
  "candidate_value": "Rp 1.250.000",
  "normalized_value": 1250000,
  "field_type": "currency",
  "source_route": "OCR+LLM",
  "confidence": 0.91,
  "validation_status": "valid",
  "decision": "accept"
}
```

Model ini membantu proses audit. Jika sistem salah mengambil nilai, peneliti dapat melihat field mana yang salah, jalur mana yang dipakai, dan alasan sistem menerima atau menolak field tersebut.

## 3.8 Perhitungan Confidence Score

Confidence score dihitung pada level field. Misalkan dokumen dinyatakan sebagai \(d\), dan daftar field target pada dokumen adalah:

$$
F(d) = \{f_1, f_2, \ldots, f_n\}
$$

Untuk setiap field \(f_i\), sistem menghasilkan kandidat nilai \(\hat{y}_i\). Confidence akhir untuk field tersebut disebut \(C_{final,i}\).

Komponen confidence yang digunakan adalah:

| Simbol | Komponen | Penjelasan |
|---|---|---|
| \(C_{ocr,i}\) | OCR quality | Kualitas hasil OCR atau parser. |
| \(C_{ext,i}\) | Extraction confidence | Keyakinan jalur ekstraksi terhadap kandidat field. |
| \(V_{schema,i}\) | Schema validation | Kesesuaian dengan tipe data, format, dan aturan wajib. |
| \(K_{context,i}\) | Context consistency | Konsistensi dengan field lain, misalnya subtotal + pajak = total. |
| \(M_{memory,i}\) | Memory support | Dukungan dari correction memory pada template yang mirip. |

Skor awal dihitung sebagai:

$$
S_i = w_1C_{ocr,i} + w_2C_{ext,i} + w_3V_{schema,i} + w_4K_{context,i} + w_5M_{memory,i}
$$

dengan syarat:

$$
w_1+w_2+w_3+w_4+w_5=1
$$

Bobot awal yang digunakan:

$$
w_1=0.25,\quad w_2=0.25,\quad w_3=0.25,\quad w_4=0.15,\quad w_5=0.10
$$

Jika terdapat konflik validasi, skor diberi penalti:

$$
C_{final,i} = \max(0,\min(1, S_i - \lambda P_i))
$$

Keterangan:

| Simbol | Keterangan |
|---|---|
| \(S_i\) | Skor awal field ke-\(i\). |
| \(P_i\) | Penalti konflik validasi. |
| \(\lambda\) | Besarnya pengaruh penalti. |
| \(C_{final,i}\) | Confidence akhir field ke-\(i\). |

Jika field gagal validasi kritis, misalnya format tanggal salah atau total lebih kecil dari subtotal, maka confidence dibatasi:

$$
C_{final,i} \leq 0.40
$$

![Gambar 3.5 Ringkasan rumus confidence](generated_figures_v4/gambar_3_4_rumus_confidence.png)

**Gambar 3.5 Ringkasan perhitungan confidence score.**

## 3.9 Keputusan Routing

Keputusan routing dibuat berdasarkan nilai \(C_{final,i}\). Sistem menggunakan tiga keputusan utama:

1. **Accept:** field diterima otomatis.
2. **Escalate:** field diproses ulang dengan jalur yang lebih kuat.
3. **Human review:** field dikoreksi manusia.

Aturan routing:

$$
R(f_i)=
\begin{cases}
\text{accept}, & C_{final,i} \geq 0.85 \text{ dan validasi kritis lolos} \\
\text{escalate}, & 0.60 \leq C_{final,i} < 0.85 \\
\text{human review}, & C_{final,i} < 0.60 \text{ atau validasi kritis gagal}
\end{cases}
$$

![Gambar 3.6 Routing decision](generated_figures_v4/gambar_3_5_routing_decision_bersih.png)

**Gambar 3.6 Keputusan routing pada level field.**

## 3.10 Template Fingerprint dan Correction Memory

Correction memory membutuhkan cara untuk mengenali dokumen yang mirip. Dalam penelitian ini, kemiripan dokumen dilihat melalui template fingerprint. Template fingerprint adalah ringkasan pola dokumen, misalnya anchor text, posisi field, struktur tabel, dan ukuran halaman.

Secara sederhana, fingerprint dokumen dapat ditulis sebagai:

$$
\phi(d) = [a(d), l(d), t(d), q(d)]
$$

dengan:

| Simbol | Keterangan |
|---|---|
| \(a(d)\) | Fitur anchor text, seperti "Invoice No" atau "Total". |
| \(l(d)\) | Fitur layout dan posisi bounding box. |
| \(t(d)\) | Fitur struktur tabel. |
| \(q(d)\) | Fitur kualitas gambar. |

Kemiripan dua dokumen dihitung dengan cosine similarity:

$$
sim(d_a,d_b)=cos(\phi(d_a),\phi(d_b))
$$

Correction memory dipakai jika:

$$
sim(d_{new},d_{memory}) \geq \tau_{memory}
$$

Jika dokumen baru mirip dengan dokumen yang pernah dikoreksi, sistem dapat memakai pola koreksi sebelumnya sebagai sinyal tambahan pada confidence score.

## 3.11 Baseline dan Metode Usulan

Metode usulan dibandingkan dengan beberapa baseline. Pemilihan baseline mengikuti dua pertimbangan: baseline klasik yang umum dipakai dalam IDP, dan baseline modern yang meniru logika hybrid serta format-aware strategy selection dari Wang dan Shen (2025).

| Kode | Metode | Tujuan Pembanding |
|---|---|---|
| B0 | Manual input | Mengukur waktu dan kesalahan input manual. |
| B1 | OCR + rule | Baseline sederhana, murah, dan cepat. |
| B2 | OCR + LLM | Menguji manfaat reasoning berbasis teks. |
| B3 | Table parser | Menguji field berbasis tabel. |
| B4 | Format-aware routing | Routing berdasarkan format dokumen, mengikuti prinsip baseline Wang dan Shen (2025). |
| B5 | Direct VLM-OCR | Baseline modern menggunakan model seperti Qwen2.5-VL atau GOT-OCR 2.0. |
| Proposed | Field-aware confidence routing | Metode utama penelitian. |

Selain baseline, dilakukan ablation study untuk melihat pengaruh masing-masing komponen. Komponen yang diuji antara lain schema validation, VLM route, correction memory, HITL, dan field-level routing. Dengan rancangan ini, penelitian dapat menguji apakah penurunan unit routing dari level format dokumen ke level field benar-benar meningkatkan akurasi field penting, menurunkan false accept rate, dan tetap menjaga latency.

## 3.12 Metrik Evaluasi

Metrik evaluasi yang digunakan adalah:

1. **Field accuracy**, untuk mengukur jumlah field yang benar.
2. **Character Error Rate (CER)**, untuk field teks.
3. **Exact match**, untuk field seperti nomor invoice dan tanggal.
4. **Numeric tolerance**, untuk field nominal.
5. **Expected Calibration Error (ECE)**, untuk melihat apakah confidence sudah sesuai dengan akurasi.
6. **False Accept Rate (FAR)**, untuk mengukur field salah yang tetap diterima otomatis.
7. **Straight-Through Processing (STP) rate**, untuk mengukur dokumen yang selesai tanpa human review.
8. **Latency dan cost proxy**, untuk mengukur waktu dan perkiraan biaya pemrosesan.

Rumus STP rate:

$$
STP = \frac{\text{jumlah dokumen selesai otomatis}}{\text{jumlah seluruh dokumen}}
$$

Rumus false accept rate:

$$
FAR = \frac{\text{field salah yang diterima otomatis}}{\text{semua field yang diterima otomatis}}
$$

## 3.13 Jadwal Penelitian

| Bulan | Aktivitas |
|---|---|
| 1 | Studi literatur, finalisasi field target, dan rancangan dataset. |
| 2 | Pengumpulan atau pembuatan data, anotasi ground truth, dan desain evaluasi. |
| 3 | Implementasi preprocessing, OCR, field candidate detection, dan baseline awal. |
| 4 | Implementasi confidence scoring, routing, dan VLM route. |
| 5 | Implementasi HITL, correction memory, dan ablation study. |
| 6 | Evaluasi akhir, analisis hasil, penulisan tesis, dan revisi. |

## 3.14 Luaran Penelitian

Luaran penelitian yang diharapkan adalah:

1. Prototipe sistem IDP berbasis field-aware confidence routing.
2. Dataset evaluasi atau sampel data teranotasi untuk dokumen administratif Indonesia.
3. Hasil evaluasi terhadap baseline dan ablation study.
4. Dokumen tesis yang menjelaskan rancangan, implementasi, dan evaluasi sistem.

---

# DAFTAR PUSTAKA SEMENTARA

Appalaraju, S., Jasani, B., Kota, B. U., Xie, Y., & Manmatha, R. (2021). DocFormer: End-to-end transformer for document understanding. *Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV)*.

Auer, C., et al. (2024). *Docling technical report*. arXiv. https://arxiv.org/abs/2408.09869

Bai, S., et al. (2025). *Qwen2.5-VL technical report*. arXiv. https://arxiv.org/abs/2502.13923

Blecher, L., Cucurull, G., Scialom, T., & Stojnic, R. (2023). *Nougat: Neural optical understanding for academic documents*. arXiv. https://arxiv.org/abs/2308.13418

Devlin, J., Chang, M.-W., Lee, K., & Toutanova, K. (2019). BERT: Pre-training of deep bidirectional transformers for language understanding. *Proceedings of NAACL-HLT*.

Du, Y., Li, C., Guo, R., Yin, X., Liu, W., Zhou, J., Bai, Y., Yu, Z., Yang, Y., Dang, Q., & Wang, H. (2020). *PP-OCR: A practical ultra lightweight OCR system*. arXiv. https://arxiv.org/abs/2009.09941

Geifman, Y., & El-Yaniv, R. (2017). Selective classification for deep neural networks. *Advances in Neural Information Processing Systems (NeurIPS)*.

Guo, C., Pleiss, G., Sun, Y., & Weinberger, K. Q. (2017). On calibration of modern neural networks. *Proceedings of the International Conference on Machine Learning (ICML)*.

Hendrycks, D., & Gimpel, K. (2017). A baseline for detecting misclassified and out-of-distribution examples in neural networks. *International Conference on Learning Representations (ICLR)*.

Huang, Y., Lv, T., Cui, L., Lu, Y., & Wei, F. (2022). LayoutLMv3: Pre-training for document AI with unified text and image masking. *Proceedings of ACM Multimedia*.

Huang, Z., et al. (2019). ICDAR2019 competition on scanned receipt OCR and information extraction. *Proceedings of ICDAR*.

Jaume, G., Ekenel, H. K., & Thiran, J. P. (2019). FUNSD: A dataset for form understanding in noisy scanned documents. *Proceedings of ICDAR Workshops*.

Kim, G., Hong, T., Yim, M., Nam, J., Park, J., Yim, J., Hwang, W., Yun, S., Han, D., & Park, S. (2022). OCR-free document understanding transformer. *Proceedings of the European Conference on Computer Vision (ECCV)*.

Li, M., Cui, L., Huang, S., Wei, F., Zhou, M., & Li, Z. (2020). TableBank: Table benchmark for image-based table detection and recognition. *Proceedings of LREC*.

Li, M., Lv, T., Chen, J., Cui, L., Lu, Y., Florencio, D., Zhang, C., Li, Z., & Wei, F. (2023). TrOCR: Transformer-based optical character recognition with pre-trained models. *Proceedings of AAAI*.

Li, Y., Qian, Y., Yu, Y., Qin, X., Zhang, C., Liu, Y., Yao, K., Han, J., Liu, J., & Ding, E. (2021). StructuralLM: Structural pre-training for form understanding. *Proceedings of ACL*.

Mathew, M., Karatzas, D., & Jawahar, C. V. (2021). DocVQA: A dataset for VQA on document images. *Proceedings of WACV*.

Mistral AI. (2026). *Document AI: OCR processor documentation*. https://docs.mistral.ai/

Nassar, M., Livathinos, N., Lysak, M., & Staar, P. (2022). TableFormer: Table structure understanding with transformers. *Proceedings of CVPR Workshops*.

National Institute of Standards and Technology. (2023). *Artificial intelligence risk management framework (AI RMF 1.0)*. https://doi.org/10.6028/NIST.AI.100-1

Ouyang, L., et al. (2024). *OmniDocBench: Benchmarking diverse PDF document parsing with comprehensive annotations*. arXiv. https://arxiv.org/abs/2412.07626

Park, S., Shin, S., Lee, B., Lee, J., Surh, J., Seo, M., & Lee, H. (2019). CORD: A consolidated receipt dataset for post-OCR parsing. *Proceedings of Document Intelligence Workshop at NeurIPS*.

Pfitzmann, B., Auer, C., Dolfi, M., Nassar, A. S., & Staar, P. (2022). DocLayNet: A large human-annotated dataset for document-layout segmentation. *Proceedings of KDD*.

Sculley, D., Holt, G., Golovin, D., Davydov, E., Phillips, T., Ebner, D., Chaudhary, V., Young, M., Crespo, J.-F., & Dennison, D. (2015). Hidden technical debt in machine learning systems. *Advances in Neural Information Processing Systems (NeurIPS)*.

Shen, Z., Zhang, R., Dell, M., Lee, B. C. G., Carlson, J., & Li, W. (2021). LayoutParser: A unified toolkit for deep learning based document image analysis. *Proceedings of ICDAR*.

Smith, R. (2007). An overview of the Tesseract OCR engine. *Proceedings of ICDAR*.

Smock, B., Pesala, R., & Abraham, R. (2022). PubTables-1M: Towards comprehensive table extraction from unstructured documents. *Proceedings of CVPR*.

Smock, B., Pesala, R., & Abraham, R. (2023). *Aligning benchmark datasets for table structure recognition*. arXiv. https://arxiv.org/abs/2303.00716

Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, L., & Polosukhin, I. (2017). Attention is all you need. *Advances in Neural Information Processing Systems (NeurIPS)*.

Wang, B., et al. (2024). *MinerU: An open-source solution for precise document content extraction*. arXiv. https://arxiv.org/abs/2409.18839

Wang, Z., & Shen, X. (2025). *Hybrid OCR-LLM framework for enterprise-scale document information extraction under copy-heavy task*. arXiv. https://doi.org/10.48550/arXiv.2510.10138

Wei, H., et al. (2024). *General OCR theory: Towards OCR-2.0 via a unified end-to-end model*. arXiv. https://arxiv.org/abs/2409.01704

Xu, Y., Li, M., Cui, L., Huang, S., Wei, F., & Zhou, M. (2020). LayoutLM: Pre-training of text and layout for document image understanding. *Proceedings of KDD*.

Xu, Y., Xu, Y., Lv, T., Cui, L., Wang, F., Lu, Y., Florencio, D., Zhang, C., Che, W., Zhang, M., & Wei, F. (2021). LayoutLMv2: Multi-modal pre-training for visually-rich document understanding. *Proceedings of ACL*.

Zhao, Z., Kang, H., Wang, B., & He, C. (2024). *DocLayout-YOLO: Enhancing document layout analysis through diverse synthetic data and global-to-local adaptive perception*. arXiv. https://arxiv.org/abs/2410.12628

Zhong, X., Tang, J., & Yepes, A. J. (2019). PubLayNet: Largest dataset ever for document layout analysis. *Proceedings of ICDAR*.
