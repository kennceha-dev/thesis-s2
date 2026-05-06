## 1.1 Latar Belakang

[ADD IMAGE HERE: Ilustrasi konteks masalah pada BAB I. Tampilkan beberapa contoh dokumen transaksi seperti invoice, receipt/struk, kuitansi, dan laporan keuangan sederhana dengan variasi layout, stempel, tanda tangan, kualitas scan, dan format nominal. Tujuannya untuk menunjukkan bahwa input dokumen tidak seragam sebelum masuk ke sistem IDP.]

Pemrosesan dokumen transaksi seperti invoice, kuitansi, struk, laporan keuangan sederhana, dan dokumen pendukung operasional masih menjadi tantangan penting dalam proses digitalisasi organisasi. Dokumen-dokumen tersebut sering kali tidak memiliki format yang seragam, memiliki kualitas scan atau foto yang bervariasi, serta mengandung elemen visual seperti stempel, tanda tangan, coretan, singkatan lokal, tabel tidak konsisten, dan catatan tulisan tangan. Kondisi ini membuat proses ekstraksi informasi tidak cukup hanya mengandalkan OCR konvensional atau aturan berbasis template yang statis.

[ADD IMAGE HERE: Diagram evolusi pendekatan IDP. Buat alur ringkas dari OCR konvensional -> OCR + rule/template -> OCR + layout/table parsing -> OCR + LLM -> VLM/OCR-free. Tambahkan catatan bahwa setiap pendekatan memiliki trade-off akurasi, biaya, latency, dan auditability.]

Perkembangan Intelligent Document Processing (IDP) telah memperluas pendekatan ekstraksi informasi dari OCR konvensional menuju kombinasi OCR, analisis layout, rule-based extraction, model deep learning, Large Language Model (LLM), dan Vision-Language Model (VLM). Namun, penggunaan model yang lebih kuat untuk seluruh dokumen tidak selalu efisien karena tingkat kesulitan informasi di dalam dokumen dapat berbeda. Kondisi ini menunjukkan perlunya mekanisme pemrosesan yang lebih adaptif daripada pipeline tunggal pada level dokumen.

[ADD IMAGE HERE: Diagram masalah utama penelitian. Tampilkan satu dokumen yang memiliki beberapa field dengan tingkat kesulitan berbeda: field mudah diterima OCR/rule, field ambigu perlu LLM, field dengan masalah visual perlu VLM crop, dan field berisiko perlu human review. Gambar ini memperjelas alasan routing dilakukan pada level field, bukan seluruh dokumen.]

Masalah utama dalam konteks ini bukan hanya memilih model ekstraksi yang paling akurat, tetapi menentukan **field mana yang cukup aman diterima otomatis dan field mana yang perlu dieskalasi ke proses yang lebih kuat**. Studi terbaru mengenai document information extraction menunjukkan bahwa pipeline berbasis OCR, OCR+MLLM, maupun image-only MLLM memiliki kekuatan dan kelemahan berbeda bergantung pada karakteristik dokumen dan jenis informasi yang diekstraksi [1]. Penelitian lain juga menunjukkan bahwa kombinasi OCR dan LLM dapat meningkatkan akurasi sekaligus mengelola trade-off antara akurasi dan efisiensi melalui strategi pemrosesan yang berbeda [2]. Hal ini menunjukkan bahwa sistem IDP perlu memiliki mekanisme penentuan rute ekstraksi yang adaptif, terutama ketika kualitas kandidat hasil ekstraksi berbeda antar-field.

Di sisi lain, pemanfaatan LLM dalam document processing juga membawa tantangan baru. LLM dapat membantu memperbaiki hasil OCR, menormalkan format, atau memahami konteks field, tetapi model ini juga berpotensi menghasilkan koreksi yang tidak sepenuhnya sesuai dengan isi dokumen asli. Gupta et al. [3] menekankan bahwa post-OCR correction menggunakan language model perlu dikendalikan karena koreksi teks yang terlalu bebas dapat berisiko pada skenario yang membutuhkan ketelitian tinggi. Dalam dokumen transaksi dan keuangan, kesalahan kecil pada nominal, tanggal, nomor identitas, atau nama pihak dapat menimbulkan konsekuensi operasional dan finansial.

Sistem IDP juga perlu memperhatikan pengendalian risiko, bukan hanya akurasi rata-rata. Pendekatan confidence-aware menjadi penting karena ukuran ketidakpastian dapat digunakan untuk membedakan hasil yang layak diproses otomatis dari hasil yang memerlukan validasi tambahan. Rombach dan Mehdiyev [4] menunjukkan bahwa uncertainty pada Key Information Extraction dapat digunakan untuk memproses prediksi berkepercayaan tinggi secara otomatis dan menandai kasus ambigu untuk validasi manusia.

Karakteristik dokumen lokal juga perlu diperhatikan karena format penulisan, singkatan, stempel, tanda tangan, dan elemen non-teks dapat mempengaruhi kualitas OCR dan ekstraksi. Dataset CORD menunjukkan bahwa struk berbahasa Indonesia memiliki variasi struktur dan anotasi yang relevan untuk studi post-OCR parsing [5]. Hal ini memberi dasar bahwa konteks bahasa dan format lokal dapat menjadi faktor penting dalam perancangan sistem ekstraksi dokumen.

[ADD IMAGE HERE: Diagram konsep solusi penelitian. Tampilkan ringkasan input dokumen -> field candidate -> confidence score -> routing field -> output terstruktur. Beri label route: OCR/rule, text-LLM, VLM crop, human review. Cocok sebagai gambar penutup latar belakang sebelum masuk rumusan masalah.]

Berdasarkan permasalahan tersebut, penelitian ini diarahkan pada perancangan sistem IDP berbasis **field-level confidence-aware adaptive routing**. Fokusnya adalah mekanisme orkestrasi ekstraksi yang menentukan rute ekstraksi berdasarkan kualitas kandidat, risiko, dan confidence pada unit informasi yang diekstraksi. Dengan fokus tersebut, penelitian tidak diarahkan untuk membangun OCR baru, melainkan untuk mengevaluasi workflow ekstraksi yang lebih adaptif dan dapat diaudit.
## 1.2 Perumusan Masalah

Berdasarkan latar belakang tersebut, permasalahan penelitian dirumuskan sebagai berikut:

1. Bagaimana merancang sistem IDP yang dapat melakukan routing ekstraksi pada level field untuk dokumen transaksi berbahasa Indonesia?
2. Bagaimana menghitung confidence score field berdasarkan kualitas OCR, kecocokan label, posisi, validasi schema, dan konsistensi konteks?
3. Bagaimana menentukan route yang sesuai untuk setiap field, seperti OCR/rule, text-LLM, VLM crop, atau human review?
4. Bagaimana performa metode field-level confidence-aware routing dibandingkan dengan baseline OCR-only, OCR+rule, OCR+LLM, direct VLM, dan document-level routing?

## 1.3 Tujuan Penelitian

Tujuan penelitian ini adalah:

1. Merancang workflow IDP untuk ekstraksi informasi pada dokumen transaksi berbahasa Indonesia.
2. Mengembangkan mekanisme confidence scoring pada level field.
3. Mengembangkan routing ekstraksi yang memilih route pemrosesan berdasarkan confidence dan jenis ketidakpastian field.
4. Mengevaluasi metode usulan terhadap beberapa baseline menggunakan metrik akurasi, false accept rate, escalation rate, latency, dan Straight-Through Processing rate.

## 1.4 Manfaat Penelitian

Manfaat penelitian ini adalah:

1. Memberikan rancangan workflow IDP yang lebih adaptif untuk dokumen transaksi dengan format tidak tetap.
2. Membantu mengurangi penggunaan model besar pada field yang dapat diproses dengan metode ringan.
3. Memberikan mekanisme evaluasi yang mempertimbangkan akurasi, risiko salah terima, latency, dan kebutuhan human review.
4. Menjadi referensi bagi pengembangan sistem ekstraksi dokumen yang membutuhkan audit trail pada level field.

## 1.5 Ruang Lingkup Penelitian

Ruang lingkup penelitian ini dibatasi sebagai berikut:

1. Dokumen yang dikaji adalah dokumen transaksi berbahasa Indonesia, seperti receipt, invoice, kuitansi, atau laporan keuangan sederhana.
2. Penelitian berfokus pada routing ekstraksi level field, bukan pada pengembangan OCR, LLM, atau VLM baru.
3. Field yang dievaluasi mengikuti target schema yang ditentukan pada tahap metodologi.
4. Human review diposisikan sebagai flag atau simulasi evaluasi, bukan sebagai antarmuka koreksi penuh.
5. Evaluasi dilakukan menggunakan dataset yang memiliki ground truth terstruktur.

## Referensi

[1] Shen, J., Yuan, P., Ghosh, A., Mai, Y., & Dahlmeier, D. (2026). *OCR or Not? Rethinking Document Information Extraction in the MLLMs Era with Real-World Large-Scale Datasets*. https://papers.cool/arxiv/2603.02789

[2] Wang, Z., & Shen, X. (2025). *Hybrid OCR-LLM Framework for Enterprise-Scale Document Information Extraction Under Copy-heavy Task*. arXiv:2510.10138. https://huggingface.co/papers/2510.10138

[3] Gupta, H., Del Corro, L., Broscheit, S., Hoffart, J., & Brenner, E. (2021). *Unsupervised Multi-View Post-OCR Error Correction With Language Models*. EMNLP 2021. https://aclanthology.org/2021.emnlp-main.680/

[4] Rombach, A., & Mehdiyev, N. (2026). *Beyond Accuracy: Understanding Model Confidence in Key Information Extraction with Conformal Prediction*. International Journal on Document Analysis and Recognition. https://link.springer.com/article/10.1007/s10032-026-00572-y

[5] Park, S., Shin, S., Lee, B., Lee, J., Surh, J., Seo, M., & Lee, H. (2019). *CORD: A Consolidated Receipt Dataset for Post-OCR Parsing*. https://github.com/clovaai/cord





