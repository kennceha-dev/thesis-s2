# BAB II
# TINJAUAN PUSTAKA

## 2.1 Intelligent Document Processing dan Key Information Extraction

Intelligent Document Processing (IDP) adalah proses mengubah dokumen tidak terstruktur atau semi-terstruktur menjadi data terstruktur yang dapat digunakan oleh sistem. Dalam konteks dokumen administratif, IDP tidak cukup hanya membaca teks melalui OCR. Sistem juga perlu memahami field yang ingin diambil, seperti nomor dokumen, tanggal transaksi, nama vendor, subtotal, pajak, total pembayaran, dan daftar item. Karena itu, penelitian ini memandang IDP sebagai workflow ekstraksi dan pengambilan keputusan, bukan hanya pipeline OCR.

Key Information Extraction (KIE) menjadi bagian penting dari IDP karena berfokus pada pengambilan informasi penting dari dokumen. Dataset seperti FUNSD, CORD, SROIE, dan Kleister menunjukkan bahwa KIE bukan hanya masalah membaca teks, tetapi juga memahami hubungan antara teks, posisi, struktur layout, dan schema field (Jaume et al., 2019; Park et al., 2019; Huang et al., 2019; Stanisławek et al., 2021). FUNSD menekankan form understanding pada dokumen scan yang noisy, CORD berfokus pada post-OCR parsing untuk struk, SROIE memperkenalkan task OCR dan information extraction pada scanned receipt, sedangkan Kleister menunjukkan pentingnya layout pada dokumen panjang dan kompleks.

Dalam penelitian ini, KIE dipahami sebagai proses schema-driven. Sistem sudah mengetahui field target sebelum ekstraksi dilakukan, misalnya `total_amount`, `tax`, atau `transaction_date`. Pendekatan ini lazim pada dokumen administratif karena organisasi biasanya memiliki kebutuhan data yang jelas. Namun, schema-driven extraction tidak berarti sistem hanya mencari label secara kaku. Sistem tetap perlu menggabungkan OCR, layout, pola nilai, tabel, semantic similarity, dan validasi untuk menghasilkan kandidat field yang dapat dipercaya.

## 2.2 OCR, Layout Detection, dan Candidate Field Extraction

Optical Character Recognition (OCR) merupakan fondasi awal dalam banyak sistem IDP. Tesseract menjadi salah satu OCR engine klasik yang banyak digunakan dan memberikan dasar penting tentang line finding, klasifikasi karakter, dan adaptive recognition (Smith, 2007). Perkembangan OCR modern kemudian mengarah pada sistem yang lebih ringan dan praktis seperti PP-OCR, serta pendekatan berbasis Transformer seperti TrOCR (Du et al., 2020; Li et al., 2023). PP-OCR relevan karena menunjukkan bahwa OCR praktis perlu mempertimbangkan efisiensi dan ukuran model, sedangkan TrOCR menunjukkan bahwa pre-trained Transformer dapat meningkatkan pengenalan teks cetak maupun tulisan tangan.

Walaupun OCR penting, hasil OCR tidak otomatis sama dengan field. OCR menghasilkan token teks, bounding box, dan confidence; sedangkan field extraction membutuhkan proses tambahan untuk menentukan apakah token tertentu merupakan tanggal, total, pajak, nama vendor, atau bagian dari line item. Oleh karena itu, layout detection dan candidate field extraction menjadi tahap yang berbeda. Layout detection bertujuan mengenali struktur dokumen seperti baris, blok key-value, area tabel, header, footer, dan region tanda tangan atau stempel. Candidate field extraction bertujuan memetakan teks dan posisi tersebut ke schema field yang ditargetkan.

Layout analysis telah berkembang melalui toolkit dan dataset seperti LayoutParser, PubLayNet, DocLayNet, dan DocLayout-YOLO (Shen et al., 2021; Zhong et al., 2019; Pfitzmann et al., 2022; Zhao et al., 2024). LayoutParser menyediakan toolkit untuk document image analysis berbasis deep learning, PubLayNet menyediakan dataset layout skala besar dari artikel ilmiah, DocLayNet menyediakan anotasi layout manusia untuk ragam dokumen, sedangkan DocLayout-YOLO menekankan trade-off antara kecepatan dan akurasi pada layout analysis. Literatur ini mendukung rancangan penelitian bahwa OCR perlu dilengkapi dengan struktur posisi sebelum sistem dapat membuat keputusan pada level field.

Candidate field extraction dalam penelitian ini menggunakan beberapa sinyal yang saling melengkapi. Label alias digunakan untuk mengenali anchor seperti `Total`, `PPN`, atau `Tanggal`. Spatial proximity digunakan untuk mencari nilai di kanan, bawah, atau dekat dengan anchor tersebut. Regex dan schema validation digunakan untuk memeriksa apakah kandidat sesuai dengan tipe field, misalnya nominal rupiah atau tanggal. Table position digunakan untuk membedakan line item dari summary field. Semantic similarity dapat membantu ketika label tidak persis sama dengan alias, misalnya `Jumlah Pembayaran` dipetakan ke `total_amount`.

## 2.3 Structured Document Understanding Berbasis Multimodal dan Layout

Penelitian document understanding modern banyak dipengaruhi oleh model multimodal yang menggabungkan teks, layout, dan visual. LayoutLM memperkenalkan pre-training yang menggabungkan teks dan informasi layout untuk document image understanding (Xu et al., 2020). LayoutLMv2 memperluas pendekatan tersebut dengan integrasi teks, layout, dan image dalam framework multimodal, termasuk spatial-aware self-attention dan pre-training task untuk cross-modality interaction (Xu et al., 2021). LayoutLMv3 kemudian menyederhanakan arsitektur dan tujuan pre-training melalui unified text and image masking serta word-patch alignment (Huang et al., 2022).

Selain keluarga LayoutLM, beberapa pendekatan lain juga relevan untuk KIE. DocFormer menggunakan multimodal self-attention untuk menggabungkan teks, visual, dan spatial features (Appalaraju et al., 2021). StructuralLM menekankan cell-level layout information untuk form understanding (Li et al., 2021). PICK menggunakan graph learning dan graph convolution untuk memanfaatkan hubungan visual dan tekstual pada dokumen kompleks (Yu et al., 2021). FormNet menunjukkan bahwa serialization token pada form-like document dapat menjadi masalah, sehingga dibutuhkan structural encoding melalui Rich Attention dan Super-Tokens (Lee et al., 2022). LiLT relevan untuk konteks multibahasa karena mencoba memisahkan layout structure dari language-specific text model (Wang et al., 2022).

Literatur tersebut menunjukkan bahwa pemahaman dokumen administratif tidak dapat dilepaskan dari posisi dan struktur. Namun, sebagian besar model tersebut berfokus pada peningkatan representasi atau akurasi ekstraksi, bukan pada keputusan operasional apakah suatu field cukup aman diterima otomatis atau perlu dieskalasi. Penelitian ini mengambil posisi berbeda: model OCR, layout, table parser, LLM, dan VLM diperlakukan sebagai komponen dalam workflow, sedangkan kontribusi utamanya adalah mekanisme confidence-aware routing pada level field.

## 2.4 Table Understanding dan Dokumen Semi-Terstruktur

Banyak dokumen administratif menyimpan informasi penting dalam bentuk tabel, misalnya daftar barang pada invoice, item pembelian pada struk, atau rincian pembayaran. Table understanding menjadi penting karena struktur baris dan kolom sering kali tidak eksplisit, terutama pada dokumen scan atau foto. TableBank menyediakan dataset besar untuk table detection dan recognition melalui weak supervision dari dokumen Word dan LaTeX (Li et al., 2020). PubTables-1M menyediakan hampir satu juta tabel dengan ground truth yang lebih lengkap dan mengatasi masalah inconsistency seperti oversegmentation (Smock et al., 2022). TableFormer menggunakan Transformer untuk table structure recognition dan melaporkan peningkatan TEDS pada tabel sederhana dan kompleks (Nassar et al., 2022). Smock et al. (2023) juga menekankan bahwa konsistensi benchmark table structure recognition sangat mempengaruhi performa dan evaluasi model.

Dalam penelitian ini, table understanding digunakan terutama untuk membedakan line item dan summary field. Sebagai contoh, baris `Nasi Goreng 15,000` dan `Es Teh 5,000` dapat diperlakukan sebagai line item, sedangkan baris `Subtotal 20,000`, `PPN 2,000`, dan `Total 22,000` diperlakukan sebagai field summary. Pembedaan ini penting karena field summary seperti `total_amount` memiliki risiko lebih tinggi dibanding teks item biasa. Dengan demikian, table parser atau row-based extraction menjadi salah satu sumber kandidat field, tetapi keputusan akhir tetap dilakukan melalui confidence scoring dan routing.

## 2.5 OCR-Free, VLM, dan LLM untuk Document Understanding

Pendekatan OCR-free mencoba memahami dokumen langsung dari gambar tanpa menjadikan OCR sebagai tahap terpisah. Donut merupakan salah satu model penting karena melakukan document understanding dengan Transformer tanpa OCR engine tradisional dan menunjukkan bahwa OCR dependency dapat menyebabkan biaya komputasi, error propagation, serta keterbatasan bahasa atau domain (Kim et al., 2022). Nougat memperluas gagasan OCR-free untuk dokumen akademik dengan menghasilkan markup dari halaman ilmiah (Blecher et al., 2023).

Perkembangan Vision-Language Model (VLM) dan Multimodal Large Language Model (MLLM) memperkuat arah ini. GOT-OCR 2.0 memperkenalkan gagasan OCR-2.0 melalui model unified end-to-end sekitar 580 juta parameter yang dapat menangani teks, formula, tabel, chart, dan region-level recognition (Wei et al., 2024). Qwen2.5-VL menunjukkan kemampuan visual recognition, object localization, document parsing, dan structured data extraction dari dokumen seperti invoice, form, tabel, dan layout kompleks (Bai et al., 2025). Greif et al. (2025) juga menunjukkan bahwa MLLM dapat digunakan untuk OCR, OCR post-correction, dan NER pada dokumen historis, bahkan memperlihatkan potensi multimodal post-correction ketika OCR konvensional gagal.

Di sisi lain, LLM berbasis teks tetap relevan untuk ekstraksi dari OCR output. LMDX menunjukkan penggunaan language model untuk document information extraction dan localization dengan perhatian pada grounding (Perot et al., 2023). Gupta et al. (2021) menunjukkan bahwa language model dapat membantu post-OCR correction dengan multi-view OCR, tetapi juga menekankan risiko unconstrained generation pada skenario yang membutuhkan ketelitian. Chen et al. (2025) mengevaluasi prompt engineering untuk document information extraction dan menunjukkan bahwa rancangan prompt, schema, dan post-processing berpengaruh terhadap hasil ekstraksi. Hu et al. (2025) menunjukkan pemanfaatan LLM untuk key information extraction pada tabel tidak standar.

Literatur terbaru mulai membandingkan apakah OCR masih diperlukan. Shen et al. (2026) melakukan benchmarking MLLM pada business-document information extraction dan menemukan bahwa image-only pipeline dapat mencapai performa yang sebanding dengan OCR-enhanced setup pada model kuat, terutama jika schema, exemplar, dan instruksi dirancang dengan baik. Namun, temuan tersebut tidak berarti OCR harus selalu ditinggalkan. Untuk sistem operasional, OCR masih berguna karena murah, cepat, dan menyediakan evidence berupa teks serta bounding box. Penelitian ini mengambil posisi hybrid: OCR/rule digunakan untuk field yang mudah, text-only LLM digunakan untuk ketidakpastian semantik, VLM crop digunakan untuk ketidakpastian visual, dan human review digunakan untuk field yang tetap tidak meyakinkan.

## 2.6 Confidence Calibration, Selective Prediction, dan Human-in-the-Loop

Confidence score merupakan elemen penting dalam penelitian ini karena routing dilakukan berdasarkan tingkat keyakinan field. Namun, confidence tidak selalu sama dengan probabilitas kebenaran. Guo et al. (2017) menunjukkan bahwa neural network modern sering kali tidak terkalibrasi dengan baik, sehingga skor confidence tinggi belum tentu mencerminkan peluang benar yang tinggi. Konsep selective classification dari Geifman dan El-Yaniv (2017) relevan karena memberi dasar bahwa sistem dapat menolak membuat prediksi ketika risiko terlalu tinggi, sehingga performa dapat ditukar dengan coverage.

Dalam konteks KIE, Rombach dan Mehdiyev (2026) secara langsung membahas confidence pada Key Information Extraction dengan conformal prediction. Mereka menekankan bahwa sistem KIE pada workflow bisnis perlu memberikan kepercayaan yang dapat dipahami karena kesalahan ekstraksi, terutama pada dokumen finansial, dapat berdampak serius. Pendekatan post-hoc uncertainty quantification tersebut relevan dengan penelitian ini karena menunjukkan bahwa evaluasi KIE tidak cukup hanya menggunakan akurasi, tetapi juga perlu melihat reliability, coverage, dan kebutuhan human validation.

Human-in-the-loop (HITL) dalam penelitian ini dipahami sebagai mekanisme pengendalian risiko, bukan pengganti sistem otomatis. Field yang confidence-nya rendah atau gagal validasi dapat ditandai untuk review manusia. Jika koreksi manusia disimpan, sistem dapat menggunakan correction memory sebagai sinyal tambahan pada dokumen dengan template serupa. Kerangka ini sejalan dengan prinsip AI risk management yang menekankan pengendalian, transparansi, dan monitoring pada sistem AI yang digunakan dalam proses penting (NIST, 2023). Dalam ruang lingkup tesis, HITL dapat dievaluasi sebagai flag atau simulasi review agar kontribusi utama tetap berada pada field-aware confidence routing.

## 2.7 State of the Art dan Posisi Penelitian

Tabel berikut merangkum beberapa pendekatan SOTA yang paling relevan dengan penelitian ini. SOTA tidak dipahami sebagai satu model tunggal, tetapi sebagai beberapa arah terbaru: OCR modern, document layout parsing, OCR-free/VLM, OCR+LLM hybrid, dan confidence-aware KIE.

| Area SOTA | Paper/Sistem | Ringkasan Metode | Hasil Utama yang Dilaporkan | Keterbatasan/Future Work | Relevansi dengan Penelitian |
|---|---|---|---|---|---|
| Multimodal document representation | LayoutLMv3 (Huang et al., 2022) | Unified text-image masking dan word-patch alignment untuk pre-training Document AI. | Mencapai SOTA pada text-centric task seperti form/receipt understanding dan image-centric task seperti layout analysis. | Tetap membutuhkan OCR/token input pada banyak task dan berfokus pada model representation. | Dasar bahwa teks, layout, dan visual perlu digabungkan. |
| OCR-free document understanding | Donut (Kim et al., 2022) | Transformer OCR-free yang memetakan gambar dokumen langsung ke output terstruktur. | Menunjukkan performa kuat pada beberapa VDU task dan mengurangi OCR error propagation. | Membutuhkan training/fine-tuning dan belum tentu efisien untuk semua field. | Menjadi pembanding untuk jalur VLM/OCR-free. |
| Unified OCR/VLM | GOT-OCR 2.0 (Wei et al., 2024) | Unified end-to-end model untuk plain text, formula, tabel, chart, dan region-level recognition. | Mendukung OCR-2.0 dan region-level recognition dengan prompt. | Masih preprint; evaluasi operasional pada dokumen administratif lokal perlu diuji. | Relevan sebagai high-tier VLM/OCR route. |
| General VLM document parsing | Qwen2.5-VL (Bai et al., 2025) | VLM dengan visual recognition, object localization, document parsing, dan structured extraction. | Menunjukkan kemampuan kuat pada parsing dokumen, form, invoice, tabel, dan visual localization. | Model besar dapat mahal dan latency tinggi jika dipakai untuk semua field. | Mendukung jalur VLM crop untuk field visual-uncertain. |
| PDF/document conversion pipeline | Docling (Auer et al., 2024) | Toolkit PDF conversion dengan layout analysis DocLayNet dan table structure recognition TableFormer. | Menyediakan pipeline praktis open-source untuk konversi dokumen terstruktur. | Bukan sistem field-level routing dan tidak fokus pada confidence per field. | Relevan untuk baseline parsing dan preprocessing. |
| Benchmark parsing modern | OmniDocBench (Ouyang et al., 2025) | Benchmark PDF parsing multi-source dengan anotasi layout dan atribut komprehensif. | Menunjukkan evaluasi pipeline dan end-to-end VLM pada dokumen beragam; menyoroti limitasi tiap paradigma. | Fokus pada parsing PDF umum, bukan field-level routing administratif Indonesia. | Mendukung kebutuhan evaluasi multi-level dan hybrid approach. |
| OCR+LLM hybrid | Wang & Shen (2025) | Framework OCR+LLM dengan strategy selection untuk dokumen enterprise copy-heavy. | Melaporkan F1 sangat tinggi dan latency sub-second pada konfigurasi tertentu; menunjukkan manfaat format-aware routing. | Routing masih berbasis karakteristik dokumen/format, bukan keputusan per field. | Baseline paling dekat untuk adaptive routing, tetapi penelitian ini turun ke level field. |
| OCR vs MLLM input modality | Shen et al. (2026) | Benchmark OCR-only, image-only, dan image+OCR untuk business-document IE. | Menunjukkan image-only MLLM dapat sebanding dengan OCR-enhanced setup pada model kuat; schema dan instruksi penting. | Dataset industri tidak selalu publik; belum fokus pada field-level cost/risk routing. | Menguatkan perlunya membandingkan OCR+LLM dan VLM. |
| Confidence-aware KIE | Rombach & Mehdiyev (2026) | Split conformal prediction untuk uncertainty quantification pada KIE. | Melaporkan coverage ketat dan sebagian prediksi menjadi high-confidence singleton. | Fokus pada uncertainty quantification, bukan orkestrasi OCR/LLM/VLM. | Landasan utama confidence-aware dan human review. |

Berdasarkan ringkasan tersebut, posisi penelitian ini adalah menggabungkan kekuatan beberapa arah SOTA, tetapi dengan unit keputusan yang berbeda. Sebagian besar penelitian SOTA berfokus pada model yang lebih kuat, benchmark yang lebih luas, atau pipeline OCR+LLM pada level dokumen. Penelitian ini berfokus pada field-level routing: satu dokumen dapat memiliki beberapa keputusan berbeda. Field yang mudah diproses melalui OCR/rule, field ambigu diproses ulang melalui text-only LLM, field dengan indikasi visual error diproses melalui VLM crop, dan field berisiko tinggi dikirim ke human review.

## 2.8 Gap Penelitian

Berdasarkan tinjauan pustaka, terdapat beberapa gap yang menjadi dasar penelitian ini.

1. Banyak penelitian document understanding berfokus pada peningkatan akurasi model, tetapi belum menjadikan keputusan accept, escalate, dan review pada level field sebagai pusat workflow.
2. Pendekatan VLM dan OCR-free semakin kuat, tetapi penggunaan model besar untuk semua field belum tentu efisien dari sisi latency, biaya, dan auditabilitas.
3. Pendekatan OCR+LLM hybrid sudah mulai muncul, tetapi sebagian besar routing masih dilakukan pada level format dokumen atau strategi pipeline, bukan pada level field yang memiliki risiko berbeda.
4. Confidence calibration dan selective prediction sudah banyak dibahas dalam machine learning, tetapi penerapannya pada IDP operasional masih perlu disesuaikan dengan validasi skema, konteks bisnis, dan kebutuhan human review.
5. Dokumen administratif berbahasa Indonesia memiliki karakteristik lokal seperti format rupiah, PPN, NPWP, singkatan, stempel, tanda tangan, serta variasi layout yang belum banyak menjadi fokus evaluasi.

Dengan gap tersebut, penelitian ini mengusulkan field-aware confidence routing untuk IDP dokumen administratif berbahasa Indonesia. Kontribusi penelitian bukan membangun OCR baru atau VLM baru, melainkan merancang mekanisme orkestrasi ekstraksi yang menentukan jalur pemrosesan berdasarkan confidence, risiko, dan jenis ketidakpastian setiap field.

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
