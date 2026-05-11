# BAB II
# TINJAUAN PUSTAKA

## 2.1 Intelligent Document Processing

[ADD IMAGE HERE: Diagram taksonomi IDP untuk BAB II. Tampilkan komponen umum IDP: document input, preprocessing, OCR/document parsing, layout analysis, information extraction, validation, human review, dan structured output. Gambar ini berfungsi sebagai peta teori sebelum membahas KIE, validasi, dan evaluasi sistem IDP.]

Intelligent Document Processing (IDP) adalah proses mengubah dokumen tidak terstruktur atau dokumen dengan format tidak tetap menjadi data terstruktur yang dapat digunakan oleh sistem. Kajian IDP umumnya menempatkan OCR, analisis layout, ekstraksi informasi, validasi, dan ekspor data sebagai rangkaian proses yang saling bergantung. Perkembangan bidang ini menunjukkan pergeseran dari sekadar pengenalan teks menuju pemahaman struktur dokumen dan ekstraksi informasi yang sesuai dengan kebutuhan downstream system.

Key Information Extraction (KIE) merupakan salah satu task utama dalam IDP karena berfokus pada pengambilan informasi bernilai dari dokumen. Dataset seperti FUNSD, CORD, SROIE, dan Kleister menunjukkan bahwa KIE melibatkan hubungan antara teks, posisi, struktur layout, dan schema anotasi, bukan hanya hasil OCR mentah (Jaume et al., 2019; Park et al., 2019; Huang et al., 2019; Stanisławek et al., 2021). Variasi dataset tersebut juga memperlihatkan bahwa karakteristik dokumen, mulai dari form, receipt, hingga dokumen panjang, mempengaruhi pendekatan ekstraksi yang digunakan.

## 2.2 Ekstraksi Dokumen

[ADD IMAGE HERE: Diagram perbedaan OCR, layout detection, dan information extraction. Tampilkan OCR menghasilkan teks, bounding box, dan confidence; layout detection mengelompokkan baris, region, atau tabel; lalu information extraction memetakan elemen dokumen ke field terstruktur. Gambar ini membantu membedakan tiga istilah yang sering tercampur.]

Ekstraksi dokumen dalam IDP tidak hanya berarti membaca teks dari gambar, tetapi mengubah isi dokumen menjadi informasi terstruktur yang sesuai dengan kebutuhan sistem. Pada dokumen transaksi, proses ini dapat melibatkan beberapa lapisan pemrosesan: pengenalan teks, pemahaman posisi dan layout, identifikasi nilai yang relevan, normalisasi nilai, serta validasi terhadap schema. Dengan demikian, OCR, layout analysis, dan information extraction perlu dibedakan sebagai komponen yang saling berkaitan, tetapi tidak identik.

### 2.2.1 OCR sebagai Pengenalan Teks

Optical Character Recognition (OCR) adalah proses mengenali karakter atau teks dari citra dokumen sehingga informasi visual dapat direpresentasikan dalam bentuk teks digital. Dalam sistem IDP, OCR biasanya menjadi tahap awal karena banyak proses ekstraksi informasi masih bergantung pada teks yang berhasil dikenali dari dokumen. Tesseract menjadi salah satu OCR engine klasik yang banyak digunakan dan memberikan dasar penting tentang line finding, klasifikasi karakter, dan adaptive recognition (Smith, 2007).

Perkembangan OCR modern kemudian mengarah pada sistem yang lebih ringan dan praktis seperti PP-OCR, serta pendekatan berbasis Transformer seperti TrOCR (Du et al., 2020; Li et al., 2023). PP-OCR relevan karena menunjukkan bahwa sistem OCR praktis perlu mempertimbangkan efisiensi dan ukuran model, sedangkan TrOCR menunjukkan bahwa pre-trained Transformer dapat meningkatkan pengenalan teks cetak maupun tulisan tangan. Selain teks hasil pengenalan, banyak OCR engine juga menyediakan metadata seperti posisi teks dan confidence score, yang dapat membantu analisis dokumen lanjutan.

Meskipun penting, hasil OCR tidak otomatis setara dengan struktur informasi dokumen. OCR dapat mengenali teks, tetapi belum menentukan makna bisnis dari teks tersebut. Misalnya, angka pada struk dapat merepresentasikan harga satuan, jumlah barang, pajak, diskon, atau total pembayaran bergantung pada posisi dan konteksnya. Oleh karena itu, hasil OCR masih perlu diperkaya dengan pemahaman layout dan hubungan antar-elemen dokumen.

### 2.2.2 Layout Analysis dan Struktur Dokumen

Layout pada dokumen merujuk pada susunan visual elemen-elemen dokumen, termasuk posisi teks, kelompok baris, blok informasi, tabel, judul, gambar, header, footer, dan area kosong. Layout analysis adalah proses menganalisis susunan tersebut agar struktur visual dokumen dapat dikenali secara sistematis. Berbeda dari OCR yang berfokus pada pengenalan karakter atau teks, layout analysis berfokus pada lokasi, pengelompokan, dan hubungan spasial antar-elemen dokumen.

Dalam ekstraksi dokumen, informasi layout membantu menjelaskan konteks suatu teks. Sebuah angka, misalnya, dapat memiliki makna berbeda bergantung pada apakah angka tersebut berada di kolom harga, baris total, bagian pajak, atau area catatan. Dengan demikian, layout analysis menjadi penting karena banyak dokumen tidak hanya menyampaikan informasi melalui teks, tetapi juga melalui posisi dan struktur visual.

Perkembangan layout analysis terlihat dari munculnya toolkit dan dataset seperti LayoutParser, PubLayNet, DocLayNet, dan DocLayout-YOLO (Shen et al., 2021; Zhong et al., 2019; Pfitzmann et al., 2022; Zhao et al., 2024). LayoutParser menyediakan toolkit untuk document image analysis berbasis deep learning. PubLayNet menyediakan dataset layout skala besar dari artikel ilmiah, sedangkan DocLayNet menyediakan anotasi layout manusia untuk ragam dokumen. DocLayout-YOLO menekankan trade-off antara kecepatan dan akurasi pada layout analysis. Kajian-kajian tersebut menunjukkan bahwa struktur visual dokumen menjadi komponen penting dalam pemahaman dokumen modern.

Pada dokumen transaksi, layout sering membawa informasi yang tidak dapat diperoleh dari teks saja. Kedekatan antara label dan nilai, struktur baris dan kolom, serta posisi suatu teks dalam area ringkasan atau tabel dapat mempengaruhi interpretasi informasi. Dengan demikian, ekstraksi informasi tidak hanya bergantung pada teks yang terbaca, tetapi juga pada bagaimana teks tersebut ditempatkan dalam dokumen.

### 2.2.3 Information Extraction dari Dokumen

Information extraction menghubungkan hasil OCR dan struktur layout dengan informasi terstruktur yang dibutuhkan. Dalam konteks KIE, proses ini mencakup pengenalan field penting, pemetaan label dan nilai, normalisasi format, serta pemeriksaan konsistensi terhadap schema. Dataset seperti FUNSD, CORD, SROIE, dan Kleister memperlihatkan bahwa ekstraksi informasi dari dokumen dapat berbeda bergantung pada bentuk dokumen, kepadatan teks, keberadaan tabel, dan kompleksitas layout (Jaume et al., 2019; Park et al., 2019; Huang et al., 2019; Stanisławek et al., 2021).

Dengan susunan tersebut, ekstraksi dokumen dapat dipahami sebagai proses bertahap. OCR menyediakan representasi teks awal, layout analysis memberi konteks visual dan spasial, sedangkan information extraction mengubah elemen tersebut menjadi field terstruktur. Pemisahan ini penting dalam kajian IDP karena kelemahan pada satu tahap dapat mempengaruhi tahap berikutnya, terutama pada dokumen dengan kualitas visual rendah atau layout yang tidak seragam.

## 2.3 Document Understanding

[ADD IMAGE HERE: Timeline atau peta model document understanding. Urutkan LayoutLM, LayoutLMv2, LayoutLMv3, DocFormer, StructuralLM, PICK, FormNet, dan LiLT berdasarkan pendekatan utama: text-layout, multimodal, graph/structure, dan language-independent layout. Gambar ini membantu pembaca melihat perkembangan model.]

Penelitian document understanding modern banyak dipengaruhi oleh model multimodal yang menggabungkan teks, layout, dan visual. LayoutLM memperkenalkan pre-training yang menggabungkan teks dan informasi layout untuk document image understanding (Xu et al., 2020). LayoutLMv2 memperluas pendekatan tersebut dengan integrasi teks, layout, dan image dalam framework multimodal, termasuk spatial-aware self-attention dan pre-training task untuk cross-modality interaction (Xu et al., 2021). LayoutLMv3 kemudian menyederhanakan arsitektur dan tujuan pre-training melalui unified text and image masking serta word-patch alignment (Huang et al., 2022).

Di luar model LayoutLM, beberapa pendekatan lain juga relevan untuk KIE. DocFormer menggunakan multimodal self-attention untuk menggabungkan teks, visual, dan spatial features (Appalaraju et al., 2021). StructuralLM menekankan cell-level layout information untuk form understanding (Li et al., 2021). PICK menggunakan graph learning dan graph convolution untuk memanfaatkan hubungan visual dan tekstual pada dokumen kompleks (Yu et al., 2021). FormNet menunjukkan bahwa serialization token pada form-like document dapat menjadi masalah, sehingga dibutuhkan structural encoding melalui Rich Attention dan Super-Tokens (Lee et al., 2022). LiLT relevan untuk konteks multibahasa karena mencoba memisahkan layout structure dari language-specific text model (Wang et al., 2022).

## 2.4 Table Understanding

[ADD IMAGE HERE: Ilustrasi table understanding pada invoice atau receipt. Tampilkan area tabel dengan baris item dan bagian ringkasan, lalu tunjukkan table detection, row/column structure recognition, dan pemisahan informasi detail vs ringkasan. Gambar ini menjelaskan peran TableBank, PubTables-1M, dan TableFormer.]

Banyak dokumen transaksi, invoice, receipt, dan laporan keuangan menyimpan informasi penting dalam bentuk tabel, misalnya daftar barang pada invoice, item pembelian pada struk, atau rincian pembayaran. Table understanding menjadi penting karena struktur baris dan kolom sering kali tidak eksplisit, terutama pada dokumen scan atau foto. TableBank menyediakan dataset besar untuk table detection dan recognition melalui weak supervision dari dokumen Word dan LaTeX (Li et al., 2020). PubTables-1M menyediakan hampir satu juta tabel dengan ground truth yang lebih lengkap dan mengatasi masalah inconsistency seperti oversegmentation (Smock et al., 2022). TableFormer menggunakan Transformer untuk table structure recognition dan melaporkan peningkatan TEDS pada tabel sederhana dan kompleks (Nassar et al., 2022). Smock et al. (2023) juga menekankan bahwa konsistensi benchmark table structure recognition sangat mempengaruhi performa dan evaluasi model.

## 2.5 Pendekatan LLM dan VLM untuk Ekstraksi Dokumen

[ADD IMAGE HERE: Diagram perbandingan pendekatan OCR-based, OCR+LLM, OCR-free, dan VLM. Tampilkan input dan output masing-masing: OCR-based memakai teks+bbox, OCR+LLM memakai hasil OCR sebagai konteks, OCR-free/VLM memakai gambar langsung. Tambahkan trade-off auditability, biaya, dan kemampuan visual.]

Perkembangan LLM dan VLM memperluas pendekatan ekstraksi dokumen dari pipeline berbasis OCR menuju pemrosesan yang lebih kontekstual dan multimodal. Pada pendekatan berbasis OCR, teks hasil pengenalan digunakan sebagai input utama untuk ekstraksi informasi. Pada pendekatan OCR+LLM, hasil OCR tetap dipakai, tetapi LLM membantu memahami konteks, melakukan normalisasi, atau menyusun output sesuai schema. Sementara itu, pendekatan OCR-free dan VLM mencoba memahami dokumen langsung dari gambar sehingga model dapat memanfaatkan informasi visual yang tidak selalu tertangkap oleh OCR.

Pendekatan OCR-free menjadi salah satu arah awal dalam pemahaman dokumen berbasis gambar. Donut menunjukkan bahwa document understanding dapat dilakukan dengan Transformer tanpa OCR engine tradisional, sedangkan Nougat memperluas gagasan tersebut untuk menghasilkan markup dari halaman ilmiah (Kim et al., 2022; Blecher et al., 2023). Perkembangan VLM dan MLLM kemudian memperluas kemampuan ini ke pengenalan visual, localization, document parsing, structured extraction, OCR post-correction, dan NER pada berbagai jenis dokumen (Wei et al., 2024; Bai et al., 2025; Greif et al., 2025).

Di sisi lain, LLM berbasis teks tetap relevan ketika hasil OCR tersedia dan cukup informatif. Beberapa studi menunjukkan bahwa language model dapat digunakan untuk document information extraction, localization, post-OCR correction, prompt-based extraction, dan key information extraction pada tabel tidak standar (Perot et al., 2023; Gupta et al., 2021; Chen et al., 2025; Hu et al., 2025). Namun, penggunaan language model juga perlu memperhatikan grounding dan risiko unconstrained generation, terutama pada dokumen yang membutuhkan ketelitian tinggi.

Kajian terbaru mulai membandingkan kebutuhan OCR dalam era MLLM. Shen et al. (2026) menunjukkan bahwa image-only pipeline dapat bersaing dengan pendekatan OCR-enhanced pada model tertentu, tetapi OCR tetap bernilai karena menyediakan teks, posisi, dan confidence yang berguna untuk audit serta integrasi dengan proses yang lebih ringan. Literatur ini menunjukkan bahwa pendekatan berbasis OCR, OCR+LLM, dan VLM memiliki trade-off berbeda dari sisi akurasi, kemampuan visual, auditability, latency, dan biaya komputasi.

## 2.6 Confidence dan Selective Processing

[ADD IMAGE HERE: Diagram konsep confidence dan selective processing. Tampilkan hubungan antara confidence calibration, selective prediction, uncertainty, coverage, computational cost, dan human validation. Gunakan ilustrasi prediksi diterima jika confidence tinggi dan ditolak atau divalidasi jika confidence rendah.]

Confidence score banyak digunakan untuk menyatakan tingkat keyakinan model, tetapi skor tersebut tidak selalu setara dengan probabilitas kebenaran. Guo et al. (2017) menunjukkan bahwa neural network modern sering kali tidak terkalibrasi dengan baik, sehingga confidence tinggi belum tentu mencerminkan peluang benar yang tinggi. Konsep selective classification dari Geifman dan El-Yaniv (2017) memberikan dasar teoretis bahwa sistem dapat menerima prediksi pada kasus yang cukup yakin dan menolak prediksi pada kasus tertentu untuk menukar coverage dengan risiko kesalahan yang lebih rendah.

Dalam konteks KIE, Rombach dan Mehdiyev (2026) membahas penggunaan conformal prediction untuk mengukur uncertainty pada ekstraksi informasi. Studi tersebut menekankan bahwa alur kerja bisnis membutuhkan ukuran kepercayaan yang dapat diinterpretasikan, terutama ketika kesalahan ekstraksi dapat berdampak pada proses finansial atau administratif. Temuan ini menunjukkan bahwa evaluasi KIE perlu mempertimbangkan reliability, coverage, dan kebutuhan validasi manusia, bukan hanya akurasi rata-rata.

Human-in-the-loop (HITL) sering digunakan sebagai mekanisme pengendalian risiko pada sistem otomatis. Pada alur ekstraksi dokumen, validasi manusia dapat ditempatkan pada kasus yang tidak memenuhi ambang kepercayaan atau gagal aturan validasi setelah proses otomatis dijalankan. Kerangka ini sejalan dengan prinsip AI risk management yang menekankan pengendalian, transparansi, dan monitoring pada sistem AI yang digunakan dalam proses penting (NIST, 2023).

## 2.7 State of the Art

[ADD IMAGE HERE: Peta SOTA berdasarkan area. Buat matriks atau bubble chart dengan area OCR modern, layout parsing, table understanding, OCR-free/VLM, OCR+LLM hybrid, dan confidence-aware KIE. Letakkan paper utama seperti LayoutLMv3, Donut, Qwen2.5-VL, GOT-OCR 2.0, Docling, OCR or Not, Hybrid OCR-LLM, dan Rombach & Mehdiyev.]

Tabel berikut merangkum pendekatan state of the art (SOTA) pada beberapa area yang berkaitan dengan pemrosesan dokumen, yaitu OCR modern, document layout parsing, OCR-free/VLM, OCR+LLM hybrid, dan confidence-aware KIE.

| No. | Literatur | Metode | Dataset | Hasil |
|---|---|---|---|---|
| 1 | Huang et al. (2022) | LayoutLMv3; representasi multimodal teks, layout, dan visual untuk document understanding. | FUNSD, CORD, PubLayNet, DocVQA | FUNSD F1 92,08 dan CORD F1 97,46; menunjukkan performa tinggi pada KIE berbasis teks-layout-visual. |
| 2 | Kim et al. (2022) | Donut; document understanding tanpa OCR engine tradisional. | RVL-CDIP, CORD | RVL-CDIP accuracy 95,30% vs LayoutLMv2 95,25%; waktu 752 ms/gambar vs 1.489 ms/gambar, sekitar 2x lebih cepat. |
| 3 | Bai et al. (2025) | Qwen2.5-VL; VLM umum untuk OCR, document parsing, visual localization, dan structured extraction. | DocVQA, InfoVQA, CC-OCR, OCRBenchV2 | Qwen2.5-VL-72B melaporkan DocVQA 96,4%, InfoVQA 87,3%, dan CC-OCR 79,8%; menunjukkan kemampuan kuat pada parsing dan ekstraksi visual. |
| 4 | Wei et al. (2024) | GOT-OCR 2.0; unified OCR-2.0 untuk text, formula, tabel, chart, dan region-level recognition. | Benchmark OCR umum, dokumen, chart, formatted OCR | Model sekitar 580M parameter dan dilaporkan unggul atas baseline pada beberapa benchmark OCR dan dokumen. |
| 5 | Auer et al. (2024); Nassar et al. (2022) | Docling + TableFormer; parsing dokumen dan table structure recognition. | DocLayNet, PubTables-1M | TableFormer mencapai TEDS 93,6 untuk all tables, lebih tinggi dari Camelot 73,0 dan EDD 88,3. |
| 6 | Ouyang et al. (2025) | OmniDocBench; benchmark parsing dokumen multi-domain. | OmniDocBench | Membandingkan komponen parsing: RapidTable sekitar 82,5 TEDS untuk tabel, sedangkan DocLayout-YOLO sekitar 48,7 mAP pada layout detection. |
| 7 | Wang & Shen (2025) | Hybrid OCR-LLM; strategy selection untuk enterprise document information extraction. | Dataset enterprise copy-heavy lintas PNG, DOCX, XLSX, PDF | F1 1,000 dengan latency 0,97 detik pada dokumen terstruktur; F1 0,997 dengan latency 0,6 detik pada input gambar menantang; peningkatan hingga 54x dibanding pendekatan multimodal/naive tertentu. |
| 8 | Shen et al. (2026) | OCR-only, image-only, dan image+OCR untuk business-document IE dengan MLLM. | Dataset bisnis C1 dan C2 | Pilihan modality mempengaruhi F1: Gemini 1.5 Pro image-only 76,8 vs OCR-only 74,1; Nova Pro image+OCR 72,1 vs OCR-only 66,9. |
| 9 | Rombach & Mehdiyev (2026) | Confidence-aware KIE dengan Split Conformal Prediction. | Receipt KIE | Marginal coverage 98,3%; sekitar 70% prediksi menjadi high-confidence singleton dan 94% memiliki set size maksimal 2. |

Ringkasan SOTA menunjukkan bahwa perkembangan terbaru bergerak ke arah model yang lebih kuat, benchmark yang lebih luas, dan pipeline multimodal yang semakin fleksibel. Namun, sebagian besar pendekatan masih menilai performa pada level task atau dokumen, sementara hubungan antara akurasi, risiko kesalahan, dan biaya pemrosesan pada unit informasi yang lebih kecil masih belum banyak dibahas.

## Daftar Pustaka

Appalaraju, S., Jasani, B., Kota, B. U., Xie, Y., & Manmatha, R. (2021). DocFormer: End-to-end transformer for document understanding. *ICCV 2021*. https://www.amazon.science/publications/docformer-end-to-end-transformer-for-document-understanding

Auer, C., Lysak, M., Nassar, A., Dolfi, M., Livathinos, N., Vagenas, P., Ramis, C. B., Omenetti, M., Lindlbauer, F., Dinkla, K., Mishra, L., Kim, Y., Gupta, S., de Lima, R. T., Weber, V., Morin, L., Meijer, I., Kuropiatnyk, V., & Staar, P. W. J. (2024). *Docling technical report*. arXiv. https://arxiv.org/abs/2408.09869

Bai, S., Chen, K., Liu, X., Wang, J., Ge, W., Song, S., Dang, K., Wang, P., Wang, S., Tang, J., Zhong, H., Zhu, Y., Yang, M., Li, Z., Wan, J., Wang, P., Ding, W., Fu, Z., Xu, Y., Ye, J., Zhang, X., Xie, T., Cheng, Z., Zhang, H., Yang, Z., Xu, H., & Lin, J. (2025). *Qwen2.5-VL technical report*. arXiv. https://arxiv.org/abs/2502.13923

Blecher, L., Cucurull, G., Scialom, T., & Stojnic, R. (2023). *Nougat: Neural optical understanding for academic documents*. arXiv. https://arxiv.org/abs/2308.13418

Chen, L.-C., Weng, H.-T., Pardeshi, M. S., & Chen, C.-M. (2025). Evaluation of prompt engineering on the performance of a large language model in document information extraction. *Electronics, 14*(11), 2145. https://www.mdpi.com/2079-9292/14/11/2145

Du, Y., Li, C., Guo, R., Yin, X., Liu, W., Zhou, J., Bai, Y., Yu, Z., Yang, Y., Dang, Q., & Wang, H. (2020). *PP-OCR: A practical ultra lightweight OCR system*. arXiv. https://arxiv.org/abs/2009.09941

Geifman, Y., & El-Yaniv, R. (2017). Selective classification for deep neural networks. *NeurIPS 2017*. https://papers.neurips.cc/paper/7073-selective-classification-for-deep-neural-networks

Greif, G., Griesshaber, N., & Greif, R. (2025). *Multimodal LLMs for OCR, OCR post-correction, and named entity recognition in historical documents*. arXiv. https://arxiv.org/abs/2504.00414

Guo, C., Pleiss, G., Sun, Y., & Weinberger, K. Q. (2017). On calibration of modern neural networks. *ICML 2017*. https://proceedings.mlr.press/v70/guo17a.html

Gupta, H., Del Corro, L., Broscheit, S., Hoffart, J., & Brenner, E. (2021). Unsupervised multi-view post-OCR error correction with language models. *EMNLP 2021*. https://aclanthology.org/2021.emnlp-main.680/

Hu, R., Yang, Y., Liu, S., Li, Z., Liu, J., Ding, X., Sun, H., & Ren, L. (2025). Large language model driven transferable key information extraction mechanism for nonstandardized tables. *Scientific Reports, 15*, 29802. https://www.nature.com/articles/s41598-025-15627-z

Huang, Y., Lv, T., Cui, L., Lu, Y., & Wei, F. (2022). LayoutLMv3: Pre-training for document AI with unified text and image masking. *ACM Multimedia 2022*. https://www.microsoft.com/en-us/research/publication/layoutlmv3-pre-training-for-document-ai-with-unified-text-and-image-masking/

Huang, Z., Chen, K., He, J., Bai, X., Karatzas, D., Lu, S., & Jawahar, C. V. (2019). ICDAR2019 competition on scanned receipt OCR and information extraction. *ICDAR 2019*. https://arxiv.org/abs/2103.10213

Jaume, G., Ekenel, H. K., & Thiran, J. P. (2019). FUNSD: A dataset for form understanding in noisy scanned documents. *ICDARW 2019*. https://research.itu.edu.tr/en/publications/funsd-a-dataset-for-form-understanding-in-noisy-scanned-documents/

Kim, G., Hong, T., Yim, M., Nam, J., Park, J., Yim, J., Hwang, W., Yun, S., Han, D., & Park, S. (2022). OCR-free document understanding transformer. *ECCV 2022*. https://www.ecva.net/papers/eccv_2022/papers_ECCV/html/8042_ECCV_2022_paper.php

Lee, C.-Y., Li, C.-L., Dozat, T., Perot, V., Su, G., Hua, N., Ainslie, J., Wang, R., Fujii, Y., & Pfister, T. (2022). FormNet: Structural encoding beyond sequential modeling in form document information extraction. *ACL 2022*. https://aclanthology.org/2022.acl-long.260/

Li, C., Bi, B., Yan, M., Wang, W., Huang, S., Huang, F., & Si, L. (2021). StructuralLM: Structural pre-training for form understanding. *ACL 2021*. https://aclanthology.org/2021.acl-long.493/

Li, M., Cui, L., Huang, S., Wei, F., Zhou, M., & Li, Z. (2020). TableBank: Table benchmark for image-based table detection and recognition. *LREC 2020*. https://www.microsoft.com/en-us/research/publication/tablebank-table-benchmark-for-image-based-table-detection-and-recognition/

Li, M., Lv, T., Chen, J., Cui, L., Lu, Y., Florencio, D., Zhang, C., Li, Z., & Wei, F. (2023). TrOCR: Transformer-based optical character recognition with pre-trained models. *AAAI 2023*. https://dblp.org/rec/conf/aaai/LiLC0LFZ0W23

Nassar, A., Livathinos, N., Lysak, M., & Staar, P. (2022). TableFormer: Table structure understanding with transformers. *CVPR 2022*. https://research.ibm.com/publications/tableformer-table-structure-understanding-with-transformers

National Institute of Standards and Technology. (2023). *Artificial intelligence risk management framework (AI RMF 1.0)*. https://doi.org/10.6028/NIST.AI.100-1

Ouyang, L., Qu, Y., Zhou, H., Zhu, J., Zhang, R., Lin, Q., Wang, B., Zhao, Z., Jiang, M., Zhao, X., Shi, J., Wu, F., Chu, P., Liu, M., Li, Z., Xu, C., Zhang, B., Shi, B., Tu, Z., & He, C. (2025). OmniDocBench: Benchmarking diverse PDF document parsing with comprehensive annotations. *CVPR 2025*. https://doi.org/10.1109/CVPR52734.2025.02313

Park, S., Shin, S., Lee, B., Lee, J., Surh, J., Seo, M., & Lee, H. (2019). CORD: A consolidated receipt dataset for post-OCR parsing. *Document Intelligence Workshop at NeurIPS 2019*. https://github.com/clovaai/cord

Perot, V., Kang, K., Luisier, F., Su, G., Sun, X., Boppana, R. S., Wang, Z., Mu, J., Zhang, H., & Hua, N. (2023). *LMDX: Language model-based document information extraction and localization*. arXiv. https://arxiv.org/abs/2309.10952

Pfitzmann, B., Auer, C., Dolfi, M., Nassar, A. S., & Staar, P. (2022). DocLayNet: A large human-annotated dataset for document-layout segmentation. *KDD 2022*. https://arxiv.org/abs/2206.01062

Rombach, A., & Mehdiyev, N. (2026). Beyond accuracy: Understanding model confidence in key information extraction with conformal prediction. *International Journal on Document Analysis and Recognition*. https://doi.org/10.1007/s10032-026-00572-y

Shen, J., Yuan, P., Ghosh, A., Mai, Y., & Dahlmeier, D. (2026). *OCR or not? Rethinking document information extraction in the MLLMs era with real-world large-scale datasets*. arXiv. https://arxiv.org/abs/2603.02789

Shen, Z., Zhang, R., Dell, M., Lee, B. C. G., Carlson, J., & Li, W. (2021). LayoutParser: A unified toolkit for deep learning based document image analysis. *ICDAR 2021*. https://arxiv.org/abs/2103.15348

Smith, R. (2007). An overview of the Tesseract OCR engine. *ICDAR 2007*. https://research.google/pubs/an-overview-of-the-tesseract-ocr-engine/

Smock, B., Pesala, R., & Abraham, R. (2022). PubTables-1M: Towards comprehensive table extraction from unstructured documents. *CVPR 2022*. https://www.microsoft.com/en-us/research/publication/pubtables-1m/

Smock, B., Pesala, R., & Abraham, R. (2023). Aligning benchmark datasets for table structure recognition. *ICDAR 2023*. https://arxiv.org/abs/2303.00716

Stanisławek, T., Graliński, F., Wróblewska, A., Lipiński, D., Kaliska, A., Rosalska, P., Topolski, B., & Biecek, P. (2021). *Kleister: Key information extraction datasets involving long documents with complex layouts*. arXiv. https://arxiv.org/abs/2105.05796

Wang, J., Jin, L., & Ding, K. (2022). LiLT: A simple yet effective language-independent layout transformer for structured document understanding. *ACL 2022*. https://arxiv.org/abs/2202.13669

Wang, Z., & Shen, X. (2025). *Hybrid OCR-LLM framework for enterprise-scale document information extraction under copy-heavy task*. arXiv. https://arxiv.org/abs/2510.10138

Wei, H., Liu, C., Chen, J., Wang, J., Kong, L., Xu, Y., Ge, Z., Zhao, L., Sun, J., Peng, Y., Han, C., & Zhang, X. (2024). *General OCR theory: Towards OCR-2.0 via a unified end-to-end model*. arXiv. https://arxiv.org/abs/2409.01704

Xu, Y., Li, M., Cui, L., Huang, S., Wei, F., & Zhou, M. (2020). LayoutLM: Pre-training of text and layout for document image understanding. *KDD 2020*. https://www.kdd.org/kdd2020/accepted-papers/view/layoutlm-pre-training-of-text-and-layout-for-document-image-understanding.html

Xu, Y., Xu, Y., Lv, T., Cui, L., Wang, F., Lu, Y., Florencio, D., Zhang, C., Che, W., Zhang, M., & Wei, F. (2021). LayoutLMv2: Multi-modal pre-training for visually-rich document understanding. *ACL 2021*. https://aclanthology.org/2021.acl-long.201/

Yu, W., Lu, N., Qi, X., Gong, P., & Xiao, R. (2021). PICK: Processing key information extraction from documents using improved graph learning-convolutional networks. *ICPR 2020*. https://doi.org/10.1109/ICPR48806.2021.9412927

Zhao, Z., Kang, H., Wang, B., & He, C. (2024). *DocLayout-YOLO: Enhancing document layout analysis through diverse synthetic data and global-to-local adaptive perception*. arXiv. https://arxiv.org/abs/2410.12628

Zhong, X., Tang, J., & Yepes, A. J. (2019). PubLayNet: Largest dataset ever for document layout analysis. *ICDAR 2019*. https://arxiv.org/abs/1908.07836












