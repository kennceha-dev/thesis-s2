## 1.1 Latar Belakang

[ADD IMAGE HERE: Ilustrasi konteks masalah pada BAB I. Tampilkan beberapa contoh dokumen transaksi seperti invoice, receipt/struk, kuitansi, dan laporan keuangan sederhana dengan variasi layout, stempel, tanda tangan, kualitas scan, dan format nominal. Tujuannya untuk menunjukkan bahwa input dokumen tidak seragam sebelum masuk ke sistem IDP.]

Pemrosesan dokumen transaksi seperti invoice, kuitansi, struk, laporan keuangan sederhana, dan dokumen pendukung operasional masih menjadi tantangan penting dalam proses digitalisasi organisasi. Sebagian informasi pada dokumen tersebut dapat dibaca secara cukup baik oleh OCR konvensional, terutama ketika teks tercetak jelas, posisi field stabil, dan format nilainya mengikuti pola yang umum. Namun, pada bagian lain, dokumen dapat memuat stempel, tanda tangan, coretan, tabel tidak konsisten, singkatan lokal, kualitas foto yang buruk, atau catatan tulisan tangan. Perbedaan karakteristik ini membuat satu dokumen sering kali berisi campuran field yang mudah diproses dengan metode ringan dan field yang membutuhkan pemahaman visual lebih kuat.

[ADD IMAGE HERE: Diagram evolusi pendekatan IDP. Buat alur ringkas dari OCR konvensional -> OCR + rule/template -> OCR + layout/table parsing -> OCR + LLM -> VLM/OCR-free. Tambahkan catatan bahwa setiap pendekatan memiliki trade-off akurasi, biaya, latency, dan auditability.]

Perkembangan Intelligent Document Processing (IDP) telah memperluas pendekatan ekstraksi informasi dari OCR konvensional menuju kombinasi OCR, analisis layout, rule-based extraction, model deep learning, Large Language Model (LLM), dan Vision-Language Model (VLM). VLM memberi kemampuan tambahan karena model dapat memanfaatkan konteks visual dokumen, bukan hanya teks hasil OCR. Akan tetapi, penggunaan VLM untuk seluruh dokumen tidak selalu efisien. Model tersebut umumnya membutuhkan komputasi, latency, dan biaya yang lebih tinggi dibandingkan OCR dan rule sederhana, padahal tidak semua field membutuhkan kemampuan tersebut. Kondisi ini menunjukkan perlunya mekanisme pemrosesan yang dapat mengelola kapan metode ringan sudah memadai dan kapan eskalasi ke model yang lebih kuat memang diperlukan.

[ADD IMAGE HERE: Diagram masalah utama penelitian. Tampilkan satu dokumen yang memiliki beberapa field dengan tingkat kesulitan berbeda: field mudah diterima OCR/rule, field ambigu perlu LLM, field dengan masalah visual perlu VLM crop, dan field berisiko perlu human review. Gambar ini memperjelas alasan routing dilakukan pada level field, bukan seluruh dokumen.]

Masalah utama dalam konteks ini bukan hanya memilih model ekstraksi yang paling akurat, tetapi menentukan **field mana yang cukup diproses dengan OCR/rule dan field mana yang perlu menggunakan komputasi lebih tinggi seperti LLM, VLM crop, atau human review**. Pendekatan langsung menggunakan VLM pada seluruh dokumen dapat menyederhanakan desain pipeline, tetapi berisiko memboroskan sumber daya pada field yang sebenarnya sudah dapat diekstraksi dengan metode tradisional. Sebaliknya, pipeline OCR-only atau template statis dapat gagal pada field yang ambigu secara visual atau bergantung pada konteks layout. Studi terbaru mengenai document information extraction menunjukkan bahwa pipeline berbasis OCR, OCR+MLLM, maupun image-only MLLM memiliki kekuatan dan kelemahan berbeda bergantung pada karakteristik dokumen dan jenis informasi yang diekstraksi (Shen et al., 2026). Penelitian lain juga menunjukkan bahwa kombinasi OCR dan LLM dapat meningkatkan akurasi sekaligus mengelola trade-off antara akurasi dan efisiensi melalui strategi pemrosesan yang berbeda (Wang & Shen, 2025). Hal ini menunjukkan bahwa sistem IDP perlu memiliki mekanisme penentuan rute ekstraksi yang adaptif pada level field.

Di sisi lain, eskalasi ke model yang lebih kuat juga perlu dikendalikan. LLM dapat membantu memperbaiki hasil OCR, menormalkan format, atau memahami konteks field, tetapi model ini juga berpotensi menghasilkan koreksi yang tidak sepenuhnya sesuai dengan isi dokumen asli. VLM dapat membantu ketika informasi bergantung pada bukti visual, tetapi penggunaannya untuk setiap field dapat meningkatkan beban komputasi tanpa selalu memberi peningkatan akurasi yang sepadan. Penelitian mengenai post-OCR correction menekankan bahwa post-OCR correction menggunakan language model perlu dikendalikan (Gupta et al., 2021) karena koreksi teks yang terlalu bebas dapat berisiko pada skenario yang membutuhkan ketelitian tinggi. Dalam dokumen transaksi dan keuangan, kesalahan kecil pada nominal, tanggal, nomor identitas, atau nama pihak dapat menimbulkan konsekuensi operasional dan finansial.

Dengan demikian, sistem IDP perlu memperhatikan pengelolaan sumber daya komputasi dan risiko kesalahan secara bersamaan, bukan hanya akurasi rata-rata. Routing pada level field memungkinkan sistem menerima hasil OCR/rule ketika kandidat field sudah kuat, mengirim field yang membutuhkan konteks bahasa ke text-LLM, menggunakan VLM hanya pada bagian dokumen yang memang bermasalah secara visual, serta menandai kasus berisiko untuk human review. Dalam mekanisme tersebut, confidence score berperan sebagai sinyal pendukung untuk memperkirakan kualitas kandidat dan tingkat ketidakpastian. Kajian confidence-aware KIE menunjukkan bahwa uncertainty pada Key Information Extraction dapat digunakan (Rombach & Mehdiyev, 2026) untuk memproses prediksi berkepercayaan tinggi secara otomatis dan menandai kasus ambigu untuk validasi manusia.

Karakteristik dokumen lokal juga perlu diperhatikan karena format penulisan, singkatan, stempel, tanda tangan, dan elemen non-teks dapat mempengaruhi kualitas OCR dan ekstraksi. Dataset CORD menunjukkan bahwa struk berbahasa Indonesia memiliki variasi struktur dan anotasi yang relevan untuk studi post-OCR parsing (Park et al., 2019). Hal ini memberi dasar bahwa konteks bahasa dan format lokal dapat menjadi faktor penting dalam perancangan sistem ekstraksi dokumen.

[ADD IMAGE HERE: Diagram konsep solusi penelitian. Tampilkan ringkasan input dokumen -> field candidate -> confidence score -> routing field -> output terstruktur. Beri label route: OCR/rule, text-LLM, VLM crop, human review. Cocok sebagai gambar penutup latar belakang sebelum masuk rumusan masalah.]

Berdasarkan permasalahan tersebut, penelitian ini diarahkan pada perancangan sistem IDP berbasis **field-level adaptive routing** untuk mengelola pemilihan antara OCR/rule, text-LLM, VLM crop, dan human review. Fokusnya adalah mekanisme orkestrasi ekstraksi yang menentukan rute pemrosesan berdasarkan karakteristik field, kualitas kandidat hasil OCR, risiko kesalahan, serta confidence sebagai salah satu indikator keputusan. Dengan fokus tersebut, penelitian tidak diarahkan untuk membangun OCR, LLM, atau VLM baru, melainkan untuk mengevaluasi workflow ekstraksi yang lebih efisien, adaptif, dan dapat diaudit.

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

Gupta, H., Del Corro, L., Broscheit, S., Hoffart, J., & Brenner, E. (2021). Unsupervised multi-view post-OCR error correction with language models. *Proceedings of the 2021 Conference on Empirical Methods in Natural Language Processing*, 8647–8657. https://aclanthology.org/2021.emnlp-main.680/

Park, S., Shin, S., Lee, B., Lee, J., Surh, J., Seo, M., & Lee, H. (2019). CORD: A consolidated receipt dataset for post-OCR parsing. *Proceedings of the Workshop on Document Intelligence at NeurIPS 2019*. https://github.com/clovaai/cord

Rombach, A., & Mehdiyev, N. (2026). Beyond accuracy: Understanding model confidence in key information extraction with conformal prediction. *International Journal on Document Analysis and Recognition*. https://doi.org/10.1007/s10032-026-00572-y

Shen, J., Yuan, P., Ghosh, A., Mai, Y., & Dahlmeier, D. (2026). *OCR or not? Rethinking document information extraction in the MLLMs era with real-world large-scale datasets*. arXiv. https://arxiv.org/abs/2603.02789

Wang, Z., & Shen, X. (2025). *Hybrid OCR-LLM framework for enterprise-scale document information extraction under copy-heavy task*. arXiv. https://arxiv.org/abs/2510.10138




