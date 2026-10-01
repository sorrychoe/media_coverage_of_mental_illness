# Media Coverage of Mental Illness and the Reproduction of Social Stigma

This repository archives the code, data, and analysis notebooks for reproducing the research and validating the results of the paper **"Media Coverage of Mental Illness and the Reproduction of Social Stigma"**.

---

## 📄 Full Paper
The PDF file of the full paper can be found at the following path:
* docs/Media Coverage of Mental Illness and the Reproduction of Social Stigma.pdf

---

## 🔍 Research Overview
* **Research Topic**: Analyzes media coverage from major Korean news outlets from 1960 to 2024 to identify changes in time-series discourse and aspects of the reproduction of social stigma regarding mental illnesses (schizophrenia, bipolar disorder, depression).
* **Target Illnesses**: Schizophrenia, Bipolar Disorder, Depression
* **Analyzed News Outlets**: Chosun Ilbo, Dong-A Ilbo, Hankyoreh, Kyunghyang Shinmun, Hankook Ilbo, Seoul Shinmun (total of 6 major daily newspapers)
* **Analysis Period**: January 1960 – December 2024 (65-year long-term time-series data)
* **Data Sources**: BigKinds (1990–2024), NAVER News Library (1960–1999, OCR text for some outlets), and web crawling of the Chosun Ilbo archive (2000–2024). Joongang Ilbo articles (1960s–1989) are collected in the preprocessing notebooks but filtered out before modeling. See the paper for details.
* **Key Methodologies**:
  * **Natural Language Preprocessing**: Morphological analysis and text cleaning using the `Mecab` morphological analyzer from `KoNLPy`
  * **Keyword Analysis**: Key keyword extraction for each illness through `TF-IDF` weight calculation
  * **Topic Modeling**: Exploration of time-series topic changes and hierarchical topic visualization using `BERTopic` (a topic modeling technique combining Transformer-based embeddings with HDBSCAN and UMAP). Embeddings use `jhgan/ko-sroberta-multitask`; UMAP is fixed with `random_state=42`.
  * **Partisanship Comparison**: Topic frequencies compared across outlet groups — progressive (Hankyoreh, Kyunghyang Shinmun), conservative (Chosun Ilbo, Dong-A Ilbo), and centrist (Hankook Ilbo, Seoul Shinmun)

---

## 📂 Directory Structure
To ensure reproducibility, this repository logically separates the data preprocessing phase from the topic modeling phase.

* docs/: Documents related to the paper
* notebook/: Data preprocessing and exploratory analysis phase
* model/: BERTopic modeling and result visualization phase

```
.
├── docs/
│   └── Media Coverage of Mental Illness and the Reproduction of Social Stigma.pdf
│
├── notebook/
│   ├── bipolar_disorder/
│   ├── depression/
│   ├── schizophrenia/
│   └── topics/
│
└── model/
    ├── bin/            # trained BERTopic models (not tracked, see below)
    ├── data/
    ├── image/
    ├── notebook/
    ├── bipolar_disorder/
    ├── depression/
    └── schizophrenia/
```

---

## 💻 Detailed Components

### 1. Data Analysis and Preprocessing (notebook/)
* **bipolar_disorder/**
  *(Note: The preprocessing notebooks read raw article files (BigKinds exports, NAVER News Library text, crawled Chosun Ilbo articles) that are **not included** in this repository, so they cannot be re-run as-is. Their output is provided in `model/data/`.)*
  * bipolar_preprocessing.ipynb: Data cleaning and morphological analysis (`Mecab` utilized) for news article data related to Bipolar Disorder.
  * bipolar_topic_analysis.ipynb: Basic statistics and topic trend analysis for Bipolar Disorder text.
* **depression/**
  * depression_preprocessing.ipynb: Data preprocessing for news articles related to Depression.
  * depression_topic_analysis.ipynb: Basic statistics and analysis for Depression text.
* **schizophrenia/**
  * schizophrenia_preprocessing.ipynb: Data preprocessing for news articles related to Schizophrenia.
  * schizo_topic_analysis.ipynb: Text analysis for news articles related to Schizophrenia.
* **topics/**
  * all_text_analysis.ipynb: Integrated comparative analysis of news articles across all three mental illnesses and extraction of word frequency rankings based on TF-IDF weights.
  * all_topic_analysis.ipynb: Integrated analysis of time-series topic trends.
  * data/: Collection of keywords and topic data for each illness (`bipolar_topics.xlsx`, `depression_topics.xlsx`, `schizo_topics.xlsx`).

### 2. Topic Modeling and Visualization (model/)
* **model/notebook/**
  * Bertopic_bipolar.ipynb, Bertopic_depression.ipynb, Bertopic_schizo.ipynb: Main modeling notebooks that construct and train BERTopic models using news articles for each illness as input.
  * Visualization_bipolar.ipynb, Visualization_depression.ipynb, Visualization_schizo.ipynb: Notebooks that load the trained BERTopic results, build hierarchical topic structures (`*_hierarchical_topics.xlsx`), and render interactive visualizations. The images in `model/image/` were exported from these visualizations manually (the notebooks contain no image-saving code).
* **model/bin/** *(not included in this repository)*
  * Trained BERTopic models saved by the `Bertopic_*.ipynb` notebooks (`bipolar_model.bin`, `depression_model.bin`, `schizo_model.bin`) and loaded by the `Visualization_*.ipynb` notebooks. See [Trained Models](#-trained-models-not-included).
* **model/data/**
  * Cleaned datasets used as modeling inputs: `bipolar_disorder_all.xlsx`, `depression_all.xlsx`, `schizophrenia_all.xlsx`
* **model/image/**
  * Visualizations of topic structures based on hierarchical clustering: bipolar_hierarchy.png, depression_hierarchy.png, schizo_hierarchy.png
* **model/bipolar_disorder/**, **model/depression/**, **model/schizophrenia/**
  * Contains BERTopic results and the files derived from them.
  * `bertopic_[illness_name].xlsx`: Topic summary table, one row per topic including the outlier topic `-1` (`Topic`, `Count`, `Name`, `Top Words`). Generated by `Bertopic_*.ipynb`.
  * `doc_topics_[illness_name].xlsx`: Topic assignment for each article (`name`, `Year`, `Document`, `Topic`). Topic probabilities are not saved. Generated by `Bertopic_*.ipynb`.
  * `df_[illness_name].xlsx`: Articles from the six target outlets used as modeling input. Generated by `Bertopic_*.ipynb`.
  * `[illness_name]_hierarchical_topics.xlsx`: Hierarchical topic structure. Generated by `Visualization_*.ipynb`.
  * `topic_name_[illness_name].xlsx`: **Manually authored** topic labels (`Group`, and `Topic Name` for bipolar disorder) assigned by the researchers to each topic; no notebook generates this file. The `Group` column is used by the analysis notebooks in `notebook/` to merge topics, so analyses that depend on it reflect researcher judgment.

---

## ⚙️ Environment Setup

### Key Dependencies
The following libraries are required to reproduce this analysis pipeline:

* Python 3.10–3.11
* BERTopic (Topic modeling)
* KoNLPy (Korean morphological analysis, `Mecab` supported environment recommended)
* [pyBigKinds](https://pypi.org/project/pyBigKinds/) (News data integration and preprocessing utility library, published by this repo's author — not a general-purpose third-party package)
* Pandas, OpenPyXL (Data processing)
* Matplotlib, Seaborn (Data visualization)

```bash
# Install required packages
pip install -r requirements.txt
```

*(Note: For KoNLPy Mecab, additional pre-built installations (mecab-ko, mecab-ko-dic) may be required depending on your operating system environment.)*

*(Note: `requirements.txt` lists the packages required to run these notebooks, but does not pin the exact versions originally used in the research — the original environment was not preserved. Pin versions yourself if you need to reproduce results exactly.)*

### Running the Notebooks
Each notebook expects to be run from the directory it lives in (e.g. open `notebook/depression/depression_preprocessing.ipynb` in Jupyter and run it with that as the working directory). Notebooks under `model/notebook/` that reference `model/` data use paths relative to `model/`.

---

## 🗃️ Data Files (Git LFS)
The `.xlsx` data files under `model/` and `notebook/topics/data/` are stored using [Git LFS](https://git-lfs.com/). After cloning, run:

```bash
git lfs install
git lfs pull
```

to download the actual file contents (a plain `git clone` alone will only fetch small pointer files).

---

## 🧠 Trained Models (Not Included)
The trained BERTopic models (`model/bin/*.bin`, ~2.1 GB in total) are excluded from this repository via `.gitignore` because they exceed GitHub's Git LFS storage quota.

To regenerate them, run the modeling notebooks in `model/notebook/` (`Bertopic_bipolar.ipynb`, `Bertopic_depression.ipynb`, `Bertopic_schizo.ipynb`). Each notebook saves its model to `model/bin/[illness_name]_model.bin`, which the corresponding `Visualization_*.ipynb` notebook then loads. Create the `model/bin/` directory first if it does not exist.

*(Note: Although UMAP is fixed with `random_state=42`, package versions were not pinned and embedding computation can differ across hardware, so retrained models may not exactly match the ones used in the paper. The topic assignment results used in the paper are preserved in the `.xlsx` files under `model/bipolar_disorder/`, `model/depression/`, and `model/schizophrenia/`.)*

---

## 📜 License
* **Code** (the `*.ipynb` notebooks in this repository) is licensed under the MIT License — see `LICENSE`.
* **The paper PDF** under `docs/` is copyrighted by its author(s)/journal and is **not** covered by the MIT License; it is included here for reference only.
* **The processed data files** (`.xlsx` under `model/` and `notebook/topics/data/`) are derived from news articles obtained from BigKinds, the NAVER News Library, and the Chosun Ilbo website, and are **not** covered by the MIT License. They are shared for research reproducibility; any further redistribution should follow the terms of use of each source and the copyright of the original publishers.

---

## ✉️ Contact & Citation
If you wish to use this code or research data to conduct subsequent research or cite them, please refer to the paper located in `docs/`. For other inquiries related to the research, please refer to the corresponding author information in the paper.
