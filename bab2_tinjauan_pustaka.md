# BAB II
# TINJAUAN PUSTAKA

## 2.1 Intelligent Document Processing

Intelligent Document Processing (IDP) adalah proses mengubah dokumen tidak terstruktur atau dokumen dengan format tidak tetap menjadi data terstruktur yang dapat digunakan oleh sistem. Kajian IDP umumnya menempatkan OCR, analisis layout, ekstraksi informasi, validasi, dan ekspor data sebagai rangkaian proses yang saling bergantung. Perkembangan bidang ini menunjukkan pergeseran dari sekadar pengenalan teks menuju pemahaman struktur dokumen dan ekstraksi informasi yang sesuai dengan kebutuhan downstream system.

Key Information Extraction (KIE) merupakan salah satu task utama dalam IDP karena berfokus pada pengambilan informasi bernilai dari dokumen. Dataset seperti FUNSD, CORD, SROIE, dan Kleister menunjukkan bahwa KIE melibatkan hubungan antara teks, posisi, struktur layout, dan schema anotasi, bukan hanya hasil OCR mentah (Jaume et al., 2019; Park et al., 2019; Huang et al., 2019; Stanisławek et al., 2021). Variasi dataset tersebut juga memperlihatkan bahwa karakteristik dokumen, mulai dari form, receipt, hingga dokumen panjang, mempengaruhi pendekatan ekstraksi yang digunakan.

Pada banyak aplikasi administratif, KIE bersifat schema-driven karena jenis informasi yang dibutuhkan telah didefinisikan sebelum proses ekstraksi. Pendekatan ini berbeda dari ekstraksi terbuka karena sistem mengevaluasi keluaran terhadap schema tertentu. Kajian terkait menunjukkan bahwa schema-driven extraction tetap memerlukan informasi layout, pola nilai, dan validasi karena label dokumen tidak selalu konsisten antar-template.

## 2.2 Ekstraksi Dokumen

Optical Character Recognition (OCR) merupakan fondasi awal dalam banyak sistem IDP. Tesseract menjadi salah satu OCR engine klasik yang banyak digunakan dan memberikan dasar penting tentang line finding, klasifikasi karakter, dan adaptive recognition (Smith, 2007). Perkembangan OCR modern kemudian mengarah pada sistem yang lebih ringan dan praktis seperti PP-OCR, serta pendekatan berbasis Transformer seperti TrOCR (Du et al., 2020; Li et al., 2023). PP-OCR relevan karena menunjukkan bahwa OCR praktis perlu mempertimbangkan efisiensi dan ukuran model, sedangkan TrOCR menunjukkan bahwa pre-trained Transformer dapat meningkatkan pengenalan teks cetak maupun tulisan tangan.

Hasil OCR tidak otomatis setara dengan struktur informasi dokumen. OCR umumnya menghasilkan token teks, posisi, dan confidence, sementara pemetaan token ke unit informasi membutuhkan pemrosesan layout dan ekstraksi kandidat. Oleh karena itu, Layout detection dan field candidate extraction dapat dipahami sebagai dua tahap yang berbeda: layout detection mengenali organisasi visual dokumen, sedangkan field candidate extraction menghubungkan elemen visual-teks tersebut dengan struktur informasi yang dievaluasi.

Layout analysis telah berkembang melalui toolkit dan dataset seperti LayoutParser, PubLayNet, DocLayNet, dan DocLayout-YOLO (Shen et al., 2021; Zhong et al., 2019; Pfitzmann et al., 2022; Zhao et al., 2024). LayoutParser menyediakan toolkit untuk document image analysis berbasis deep learning, PubLayNet menyediakan dataset layout skala besar dari artikel ilmiah, DocLayNet menyediakan anotasi layout manusia untuk ragam dokumen, sedangkan DocLayout-YOLO menekankan trade-off antara kecepatan dan akurasi pada layout analysis. Kajian ini mendukung rancangan penelitian bahwa OCR perlu dilengkapi dengan struktur posisi sebelum sistem dapat membuat keputusan pada level field.

Dalam kajian dan praktik KIE, ekstraksi kandidat biasanya memanfaatkan kombinasi petunjuk berbasis teks, spasial, dan struktural. Label atau anchor text dapat memberi petunjuk awal, kedekatan spasial membantu menghubungkan label dengan nilai, pola tipe data membantu menyaring kandidat yang tidak valid, sedangkan informasi tabel diperlukan ketika data muncul dalam baris dan kolom. Pendekatan berbasis semantic similarity juga dapat digunakan untuk mengurangi ketergantungan pada kecocokan label.

## 2.3 Document Understanding

Penelitian document understanding modern banyak dipengaruhi oleh model multimodal yang menggabungkan teks, layout, dan visual. LayoutLM memperkenalkan pre-training yang menggabungkan teks dan informasi layout untuk document image understanding (Xu et al., 2020). LayoutLMv2 memperluas pendekatan tersebut dengan integrasi teks, layout, dan image dalam framework multimodal, termasuk spatial-aware self-attention dan pre-training task untuk cross-modality interaction (Xu et al., 2021). LayoutLMv3 kemudian menyederhanakan arsitektur dan tujuan pre-training melalui unified text and image masking serta word-patch alignment (Huang et al., 2022).

Di luar model LayoutLM, beberapa pendekatan lain juga relevan untuk KIE. DocFormer menggunakan multimodal self-attention untuk menggabungkan teks, visual, dan spatial features (Appalaraju et al., 2021). StructuralLM menekankan cell-level layout information untuk form understanding (Li et al., 2021). PICK menggunakan graph learning dan graph convolution untuk memanfaatkan hubungan visual dan tekstual pada dokumen kompleks (Yu et al., 2021). FormNet menunjukkan bahwa serialization token pada form-like document dapat menjadi masalah, sehingga dibutuhkan structural encoding melalui Rich Attention dan Super-Tokens (Lee et al., 2022). LiLT relevan untuk konteks multibahasa karena mencoba memisahkan layout structure dari language-specific text model (Wang et al., 2022).

Kajian sebelumnya menunjukkan bahwa pemahaman dokumen tidak dapat dilepaskan dari posisi dan struktur. Namun, fokus utama sebagian besar model masih berada pada peningkatan representasi dan akurasi ekstraksi. Aspek operasional seperti seleksi jalur pemrosesan, pengendalian risiko keluaran, dan keputusan eskalasi belum menjadi fokus utama pada kelompok penelitian ini.

## 2.4 Table Understanding

Banyak dokumen transaksi, invoice, receipt, dan laporan keuangan menyimpan informasi penting dalam bentuk tabel, misalnya daftar barang pada invoice, item pembelian pada struk, atau rincian pembayaran. Table understanding menjadi penting karena struktur baris dan kolom sering kali tidak eksplisit, terutama pada dokumen scan atau foto. TableBank menyediakan dataset besar untuk table detection dan recognition melalui weak supervision dari dokumen Word dan LaTeX (Li et al., 2020). PubTables-1M menyediakan hampir satu juta tabel dengan ground truth yang lebih lengkap dan mengatasi masalah inconsistency seperti oversegmentation (Smock et al., 2022). TableFormer menggunakan Transformer untuk table structure recognition dan melaporkan peningkatan TEDS pada tabel sederhana dan kompleks (Nassar et al., 2022). Smock et al. (2023) juga menekankan bahwa konsistensi benchmark table structure recognition sangat mempengaruhi performa dan evaluasi model.

Pada dokumen dengan format tidak tetap, table understanding membantu memisahkan informasi yang berada dalam baris item dari informasi ringkasan. Pemisahan ini penting karena tabel sering mengandung hubungan hierarkis antara item, subtotal, pajak, diskon, dan total. Dengan demikian, metode ekstraksi berbasis baris, kolom, atau table parser menjadi relevan sebagai sumber struktur sebelum informasi dievaluasi lebih lanjut.

## 2.5 Model Generatif Dokumen

Pendekatan OCR-free mencoba memahami dokumen langsung dari gambar tanpa menjadikan OCR sebagai tahap terpisah. Donut merupakan salah satu model penting karena melakukan document understanding dengan Transformer tanpa OCR engine tradisional dan menunjukkan bahwa OCR dependency dapat menyebabkan biaya komputasi, error propagation, serta keterbatasan bahasa atau domain (Kim et al., 2022). Nougat memperluas gagasan OCR-free untuk dokumen akademik dengan menghasilkan markup dari halaman ilmiah (Blecher et al., 2023).

Perkembangan Vision-Language Model (VLM) dan Multimodal Large Language Model (MLLM) memperkuat arah ini. GOT-OCR 2.0 memperkenalkan gagasan OCR-2.0 melalui model unified end-to-end sekitar 580 juta parameter yang dapat menangani teks, formula, tabel, chart, dan region-level recognition (Wei et al., 2024). Qwen2.5-VL menunjukkan kemampuan visual recognition, object localization, document parsing, dan structured data extraction dari dokumen seperti invoice, form, tabel, dan layout kompleks (Bai et al., 2025). Greif et al. (2025) juga menunjukkan bahwa MLLM dapat digunakan untuk OCR, OCR post-correction, dan NER pada dokumen historis, bahkan memperlihatkan potensi multimodal post-correction ketika OCR konvensional gagal.

Di sisi lain, LLM berbasis teks tetap relevan untuk ekstraksi dari OCR output. LMDX menunjukkan penggunaan language model untuk document information extraction dan localization dengan perhatian pada grounding (Perot et al., 2023). Gupta et al. (2021) menunjukkan bahwa language model dapat membantu post-OCR correction dengan multi-view OCR, tetapi juga menekankan risiko unconstrained generation pada skenario yang membutuhkan ketelitian. Chen et al. (2025) mengevaluasi prompt engineering untuk document information extraction dan menunjukkan bahwa rancangan prompt, schema, dan post-processing berpengaruh terhadap hasil ekstraksi. Hu et al. (2025) menunjukkan pemanfaatan LLM untuk key information extraction pada tabel tidak standar.

Kajian terbaru juga mulai membandingkan kebutuhan OCR dalam era MLLM. Shen et al. (2026) melakukan benchmarking pada business-document information extraction dan menunjukkan bahwa image-only pipeline dapat bersaing dengan pendekatan OCR-enhanced pada model tertentu, terutama ketika schema, exemplar, dan instruksi dirancang dengan baik. Namun, OCR tetap memiliki nilai praktis karena menyediakan teks, posisi, dan confidence yang dapat digunakan untuk audit serta integrasi dengan proses ekstraksi yang lebih ringan.

## 2.6 Confidence-Aware Processing

Confidence score banyak digunakan untuk menyatakan tingkat keyakinan model, tetapi skor tersebut tidak selalu setara dengan probabilitas kebenaran. Guo et al. (2017) menunjukkan bahwa neural network modern sering kali tidak terkalibrasi dengan baik, sehingga confidence tinggi belum tentu mencerminkan peluang benar yang tinggi. Konsep selective classification dari Geifman dan El-Yaniv (2017) memberikan dasar teoretis bahwa sistem dapat menolak prediksi pada kasus tertentu untuk menukar coverage dengan risiko kesalahan yang lebih rendah.

Dalam konteks KIE, Rombach dan Mehdiyev (2026) membahas penggunaan conformal prediction untuk mengukur uncertainty pada ekstraksi informasi. Studi tersebut menekankan bahwa workflow bisnis membutuhkan ukuran kepercayaan yang dapat diinterpretasikan, terutama ketika kesalahan ekstraksi dapat berdampak pada proses finansial atau administratif. Temuan ini menunjukkan bahwa evaluasi KIE perlu mempertimbangkan reliability, coverage, dan kebutuhan validasi manusia, bukan hanya akurasi rata-rata.

Human-in-the-loop (HITL) sering digunakan sebagai mekanisme pengendalian risiko pada sistem otomatis. Pada workflow ekstraksi dokumen, validasi manusia dapat ditempatkan pada kasus yang tidak memenuhi ambang kepercayaan atau gagal aturan validasi. Jika koreksi manusia disimpan, koreksi tersebut juga dapat menjadi sumber informasi untuk dokumen dengan template serupa. Kerangka ini sejalan dengan prinsip AI risk management yang menekankan pengendalian, transparansi, dan monitoring pada sistem AI yang digunakan dalam proses penting (NIST, 2023).

## 2.7 State of the Art

Tabel berikut merangkum pendekatan state of the art (SOTA) pada beberapa area yang berkaitan dengan pemrosesan dokumen, yaitu OCR modern, document layout parsing, OCR-free/VLM, OCR+LLM hybrid, dan confidence-aware KIE.

| Paper | Ringkasan | Hasil | Limitasi |
|---|---|---|---|
| LayoutLMv3 (Huang et al., 2022) | Multimodal document representation untuk form, receipt, layout, dan VQA. | LayoutLMv3-large melaporkan FUNSD F1 = 92,08 dan CORD F1 = 97,46. LayoutLMv3-base melaporkan PubLayNet mAP = 95,1 dan DocVQA ANLS = 78,76. Nilai ini menunjukkan bahwa kombinasi teks, layout, dan visual masih sangat kuat untuk document understanding. | Masih bergantung pada OCR/token dan bounding box pada banyak task. Fokus utama adalah representasi model, bukan keputusan operasional apakah field harus diterima, dieskalasi, atau direview. |
| Donut (Kim et al., 2022) | OCR-free document understanding. | Pada RVL-CDIP, Donut melaporkan accuracy = 95,30% dengan waktu 752 ms per gambar pada P40 GPU, sedikit di atas LayoutLMv2 95,25% dengan 1.489 ms. Pada implementasi resmi CORD document parsing, model Donut melaporkan score sekitar 91,3 dengan sekitar 0,7 detik per gambar. | Performa tinggi membutuhkan fine-tuning dan data pretraining/sintetis. Output end-to-end lebih sulit diaudit dibanding pipeline yang menyimpan OCR token, bounding box, dan confidence per field. |
| Qwen2.5-VL (Bai et al., 2025) | General VLM untuk OCR, document parsing, visual localization, dan structured extraction. | Untuk Qwen2.5-VL-72B, laporan benchmark menunjukkan DocVQA = 96,4%, InfoVQA = 87,3%, CC-OCR = 79,8%, dan OCRBenchV2 = 61,5/63,7. Beberapa ringkasan benchmark juga mencatat OCRBench sekitar 885 dan ChartQA sekitar 89,5. | Model besar dapat mahal dan latency tinggi jika digunakan untuk seluruh field. Evaluasi benchmark umum belum otomatis membuktikan efisiensi pada workflow IDP field-level. |
| GOT-OCR 2.0 (Wei et al., 2024) | Unified OCR-2.0 untuk text, formula, tabel, chart, sheet music, geometry, dan region-level recognition. | Model berukuran sekitar 580M parameter dan mendukung whole-page OCR, sliced OCR, multi-page OCR, serta region-level recognition berbasis koordinat atau warna. Paper melaporkan hasil unggul pada beberapa benchmark OCR umum, dokumen, chart, dan formatted OCR dibanding baseline yang diuji. | Banyak hasil dilaporkan sebagai preprint dan benchmark lintas task tidak langsung setara dengan KIE administratif. Untuk tesis ini, GOT-OCR lebih cocok sebagai high-tier OCR/VLM route daripada fondasi utama. |
| Docling + TableFormer (Auer et al., 2024; Nassar et al., 2022) | Pipeline PDF/document conversion dengan layout analysis dan table structure recognition. | Model TableFormer yang digunakan dalam ekosistem Docling melaporkan TEDS 95,4 pada simple table, 90,1 pada complex table, dan 93,6 untuk all tables; lebih tinggi dari Camelot 73,0 dan EDD 88,3 pada all tables. Untuk layout, model Docling melaporkan skor agregat sekitar 72,4 sampai 76,8 pada beberapa konfigurasi DocLayNet/model. | Kuat untuk parsing dokumen dan tabel, tetapi bukan sistem confidence-aware field routing. Hasil parsing tetap perlu dipetakan ke field bisnis dan divalidasi. |
| OmniDocBench (Ouyang et al., 2025) | Benchmark SOTA untuk PDF/document parsing multi-domain. | OmniDocBench mengevaluasi text OCR, table recognition, formula recognition, layout detection, dan reading order. Ringkasan benchmark melaporkan contoh hasil: MinerU text edit distance 0,180 untuk English text, Mathpix 0,384 untuk Chinese text, RapidTable sekitar 82,5 TEDS, general VLM sekitar 71-74 TEDS, dan DocLayout-YOLO mAP sekitar 48,7 pada evaluasi layout. | Fokus pada document parsing umum, bukan field-level key information extraction. Benchmark ini lebih cocok sebagai referensi evaluasi parsing, bukan pembanding langsung untuk routing field administratif. |
| Hybrid OCR-LLM Framework (Wang & Shen, 2025) | Hybrid OCR+LLM dan strategy selection untuk enterprise document information extraction. | Pada copy-heavy identity extraction lintas PNG, DOCX, XLSX, dan PDF, framework ini melaporkan F1 = 1,000 dengan latency 0,97 detik untuk dokumen terstruktur, serta F1 = 0,997 dengan latency 0,6 detik pada input gambar menantang ketika memakai PaddleOCR. Paper juga melaporkan peningkatan performa 54x dibanding pendekatan multimodal/naive tertentu. | Routing masih berbasis karakteristik format/dokumen dan strategi extraction, bukan field-level risk routing. Paper masih preprint, sehingga sebaiknya dipakai sebagai baseline/inspirasi, bukan satu-satunya fondasi teoretis. |
| OCR or Not? (Shen et al., 2026) | Benchmark OCR-only, image-only, dan image+OCR untuk business-document IE dengan MLLM. | Pada dua dataset bisnis C1 dan C2, paper melaporkan F1 mean: Gemini 1.5 Pro image-only = 76,8, OCR-only = 74,1, image+OCR = 75,6; GPT-4o image-only = 70,1, OCR-only = 72,8, image+OCR = 73,0; Nova Pro image-only = 71,5, OCR-only = 66,9, image+OCR = 72,1. Paper juga melaporkan latency/cost per page, misalnya Gemini 2.5 Flash sekitar 1,4 detik dan $0,0025 per page, GPT-4o sekitar 2,2 detik dan $0,006 per page. | Dataset industri tidak sepenuhnya publik. Fokusnya membandingkan input modality pada level dokumen, belum membahas accept/escalate/review pada level field. |
| Rombach & Mehdiyev (2026) | Confidence-aware KIE dengan Split Conformal Prediction. | Pada receipt KIE, conformal prediction mencapai marginal coverage 98,3% untuk α = 0,02. Sekitar 70% prediksi menjadi high-confidence singleton; 94% prediksi memiliki set size maksimal 2; average prediction set width = 1,38. Field terstruktur seperti `Prod_price_v` memiliki average set size 1,13 dan coverage 99,51%, sedangkan field lebih ambigu seperti `Tips_v` memiliki average set size 2,24 dan coverage 93,1%. | Fokus pada uncertainty quantification, bukan orkestrasi OCR/rule/LLM/VLM. Namun, paper ini sangat relevan sebagai dasar bahwa confidence perlu dipakai untuk selective automation dan human review. |

Ringkasan SOTA menunjukkan bahwa perkembangan terbaru bergerak ke arah model yang lebih kuat, benchmark yang lebih luas, dan pipeline multimodal yang semakin fleksibel. Namun, sebagian besar pendekatan masih menilai performa pada level task atau dokumen. Ruang kontribusi yang masih terbuka adalah mekanisme yang mengatur jalur pemrosesan pada unit informasi yang lebih kecil dengan mempertimbangkan confidence, risiko, dan biaya pemrosesan.

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






