# Tabel Validasi Referensi BAB II

File ini berisi daftar referensi yang digunakan pada `bab2_tinjauan_pustaka.md`, link pengecekan, status sumber, dan tujuan penggunaannya dalam tesis.

| No | Referensi | Link Tepercaya | Kategori | Status Sumber | Tujuan/Purpose dalam Paper |
|---:|---|---|---|---|---|
| 1 | Smith (2007), *An Overview of the Tesseract OCR Engine* | https://research.google/pubs/an-overview-of-the-tesseract-ocr-engine/ | OCR klasik | Conference paper, Google Research | Dasar historis OCR dan baseline OCR engine klasik. |
| 2 | Du et al. (2020), *PP-OCR* | https://arxiv.org/abs/2009.09941 | OCR praktis | arXiv preprint, open-source ecosystem | Menjelaskan OCR ringan dan efisien sebagai kandidat engine awal. |
| 3 | Li et al. (2023), *TrOCR* | https://dblp.org/rec/conf/aaai/LiLC0LFZ0W23 | OCR Transformer | AAAI paper, DBLP verified | Menunjukkan perkembangan OCR berbasis pre-trained Transformer. |
| 4 | Wei et al. (2024), *GOT-OCR 2.0* | https://arxiv.org/abs/2409.01704 | OCR-2.0 / VLM OCR | arXiv preprint | SOTA high-tier OCR/VLM untuk region-level recognition dan OCR modern. |
| 5 | Xu et al. (2020), *LayoutLM* | https://www.kdd.org/kdd2020/accepted-papers/view/layoutlm-pre-training-of-text-and-layout-for-document-image-understanding.html | Multimodal document understanding | KDD paper | Dasar model yang menggabungkan teks dan layout. |
| 6 | Xu et al. (2021), *LayoutLMv2* | https://aclanthology.org/2021.acl-long.201/ | Multimodal document understanding | ACL Anthology | Menjelaskan integrasi teks, layout, dan image dengan spatial-aware attention. |
| 7 | Huang et al. (2022), *LayoutLMv3* | https://www.microsoft.com/en-us/research/publication/layoutlmv3-pre-training-for-document-ai-with-unified-text-and-image-masking/ | Multimodal document understanding | ACM MM paper, Microsoft Research | SOTA representasi dokumen dengan unified text-image masking. |
| 8 | Appalaraju et al. (2021), *DocFormer* | https://www.amazon.science/publications/docformer-end-to-end-transformer-for-document-understanding | Multimodal document understanding | ICCV paper, Amazon Science | Menunjukkan multimodal self-attention untuk document understanding. |
| 9 | Li et al. (2021), *StructuralLM* | https://aclanthology.org/2021.acl-long.493/ | Form understanding | ACL Anthology | Mendukung pentingnya cell-level layout pada form. |
| 10 | Yu et al. (2021), *PICK* | https://doi.org/10.1109/ICPR48806.2021.9412927 | KIE graph learning | ICPR paper DOI | Referensi graph-based KIE yang menggabungkan teks dan visual/layout. |
| 11 | Lee et al. (2022), *FormNet* | https://aclanthology.org/2022.acl-long.260/ | Form/KIE | ACL Anthology | Mendukung kebutuhan structural encoding karena serialization token dokumen bermasalah. |
| 12 | Wang et al. (2022), *LiLT* | https://arxiv.org/abs/2202.13669 | Multilingual document understanding | arXiv / ACL-related | Mendukung isu language-independent layout, relevan untuk dokumen Indonesia. |
| 13 | Kim et al. (2022), *Donut* | https://www.ecva.net/papers/eccv_2022/papers_ECCV/html/8042_ECCV_2022_paper.php | OCR-free document understanding | ECCV paper | SOTA OCR-free baseline dan argumen OCR error propagation. |
| 14 | Blecher et al. (2023), *Nougat* | https://arxiv.org/abs/2308.13418 | OCR-free / document markup | arXiv preprint | Contoh OCR-free untuk dokumen akademik dan structured markup. |
| 15 | Bai et al. (2025), *Qwen2.5-VL Technical Report* | https://arxiv.org/abs/2502.13923 | VLM / MLLM | arXiv technical report | SOTA VLM untuk document parsing, localization, invoice/form/table extraction. |
| 16 | Auer et al. (2024), *Docling Technical Report* | https://arxiv.org/abs/2408.09869 | PDF/document conversion | arXiv technical report, IBM-linked toolkit | Baseline/toolkit praktis untuk PDF conversion, layout, dan table recognition. |
| 17 | Ouyang et al. (2025), *OmniDocBench* | https://doi.org/10.1109/CVPR52734.2025.02313 | Document parsing benchmark | CVPR 2025 DOI | SOTA benchmark untuk evaluasi PDF parsing dan perbandingan pipeline vs VLM. |
| 18 | Zhao et al. (2024), *DocLayout-YOLO* | https://arxiv.org/abs/2410.12628 | Layout analysis | arXiv preprint | SOTA layout detection cepat sebagai konteks layout module. |
| 19 | Shen et al. (2021), *LayoutParser* | https://arxiv.org/abs/2103.15348 | Layout toolkit | arXiv / ICDAR toolkit | Mendukung implementasi/layout analysis berbasis toolkit. |
| 20 | Zhong et al. (2019), *PubLayNet* | https://arxiv.org/abs/1908.07836 | Layout dataset | arXiv / ICDAR | Dataset besar untuk document layout analysis. |
| 21 | Pfitzmann et al. (2022), *DocLayNet* | https://arxiv.org/abs/2206.01062 | Layout dataset | arXiv / KDD dataset | Dataset layout human-annotated, juga digunakan oleh Docling. |
| 22 | Li et al. (2020), *TableBank* | https://www.microsoft.com/en-us/research/publication/tablebank-table-benchmark-for-image-based-table-detection-and-recognition/ | Table detection | LREC paper, Microsoft Research | Dasar table detection/recognition dataset. |
| 23 | Smock et al. (2022), *PubTables-1M* | https://www.microsoft.com/en-us/research/publication/pubtables-1m/ | Table extraction | CVPR paper, Microsoft Research | Dataset table extraction skala besar dan isu ground truth table. |
| 24 | Nassar et al. (2022), *TableFormer* | https://research.ibm.com/publications/tableformer-table-structure-understanding-with-transformers | Table structure recognition | CVPR paper, IBM Research | SOTA table parser; relevan untuk line item dan table route. |
| 25 | Smock et al. (2023), *Aligning benchmark datasets for table structure recognition* | https://arxiv.org/abs/2303.00716 | Table benchmark alignment | arXiv / ICDAR 2023 | Mendukung argumen pentingnya konsistensi anotasi/evaluasi table. |
| 26 | Park et al. (2019), *CORD* | https://github.com/clovaai/cord | Receipt dataset | NeurIPS workshop dataset repo | Dataset utama kandidat untuk struk, termasuk konteks Indonesia/post-OCR parsing. |
| 27 | Huang et al. (2019/2021), *SROIE* | https://arxiv.org/abs/2103.10213 | Receipt OCR/KIE benchmark | ICDAR competition report | Baseline receipt OCR dan key information extraction. |
| 28 | Jaume et al. (2019), *FUNSD* | https://research.itu.edu.tr/en/publications/funsd-a-dataset-for-form-understanding-in-noisy-scanned-documents/ | Form understanding dataset | ICDARW / university publication page | Dataset form understanding noisy scanned document. |
| 29 | Stanisławek et al. (2021), *Kleister* | https://arxiv.org/abs/2105.05796 | Long document KIE | arXiv preprint | Menunjukkan KIE pada dokumen panjang/layout kompleks. |
| 30 | Gupta et al. (2021), *Unsupervised Multi-View Post-OCR Error Correction* | https://aclanthology.org/2021.emnlp-main.680/ | Post-OCR correction | EMNLP, ACL Anthology | Dasar OCR+LM correction dan risiko unconstrained generation. |
| 31 | Perot et al. (2023), *LMDX* | https://arxiv.org/abs/2309.10952 | LLM document IE/localization | arXiv preprint | Mendukung LLM extraction dengan grounding/localization. |
| 32 | Wang & Shen (2025), *Hybrid OCR-LLM Framework* | https://arxiv.org/abs/2510.10138 | Hybrid OCR+LLM routing | arXiv preprint | Related work paling dekat untuk adaptive OCR+LLM; baseline format/document-level routing. |
| 33 | Hu et al. (2025), LLM-driven KIE for nonstandardized tables | https://www.nature.com/articles/s41598-025-15627-z | LLM table KIE | Scientific Reports, peer-reviewed | Mendukung penggunaan LLM untuk table/key information extraction. |
| 34 | Chen et al. (2025), Prompt Engineering for LLM Document IE | https://www.mdpi.com/2079-9292/14/11/2145 | LLM prompt/document IE | Peer-reviewed open-access article | Mendukung pentingnya prompt, schema, JSON output, dan post-processing. |
| 35 | Greif et al. (2025), *Multimodal LLMs for OCR, OCR Post-Correction, and NER* | https://arxiv.org/abs/2504.00414 | MLLM OCR/post-correction | arXiv preprint | Mendukung VLM untuk OCR dan visual post-correction. |
| 36 | Shen et al. (2026), *OCR or Not?* | https://arxiv.org/abs/2603.02789 | OCR vs MLLM document IE | arXiv preprint, latest benchmark | SOTA terbaru untuk membandingkan OCR-only, image-only, dan image+OCR. |
| 37 | Guo et al. (2017), *On Calibration of Modern Neural Networks* | https://proceedings.mlr.press/v70/guo17a.html | Confidence calibration | ICML/PMLR | Landasan bahwa confidence model perlu dikalibrasi. |
| 38 | Geifman & El-Yaniv (2017), *Selective Classification* | https://papers.neurips.cc/paper/7073-selective-classification-for-deep-neural-networks | Selective prediction | NeurIPS | Dasar accept/reject/review berdasarkan risiko dan coverage. |
| 39 | Rombach & Mehdiyev (2026), Confidence in KIE with Conformal Prediction | https://doi.org/10.1007/s10032-026-00572-y | Confidence-aware KIE | IJDAR, peer-reviewed open access | Referensi utama confidence-aware KIE dan human validation. |
| 40 | NIST (2023), *AI RMF 1.0* | https://doi.org/10.6028/NIST.AI.100-1 | AI risk management | Government standard/framework | Mendukung auditabilitas, risk control, dan human review pada workflow AI. |

## Catatan Validitas

- Referensi peer-reviewed utama: LayoutLM, LayoutLMv2, LayoutLMv3, Donut, FormNet, StructuralLM, TableFormer, PubTables-1M, FUNSD, SROIE, TrOCR, Guo et al., Geifman & El-Yaniv, Gupta et al., Rombach & Mehdiyev.
- Referensi SOTA terbaru yang masih preprint/technical report: Qwen2.5-VL, GOT-OCR 2.0, Docling, DocLayout-YOLO, Hybrid OCR-LLM, OCR or Not. Referensi ini berguna untuk posisi SOTA, tetapi sebaiknya tidak menjadi satu-satunya fondasi teoretis.
- Referensi dataset/tooling: CORD, PubLayNet, DocLayNet, TableBank, LayoutParser, Docling. Referensi ini berguna untuk dataset, baseline, atau rancangan implementasi.
- Referensi paling dekat dengan novelty tesis: Wang & Shen (2025), Shen et al. (2026), Rombach & Mehdiyev (2026), Gupta et al. (2021), dan FormNet/LayoutLMv3 sebagai fondasi document understanding.
