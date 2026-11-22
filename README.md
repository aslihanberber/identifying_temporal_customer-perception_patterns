# Master Thesis Analysis Code

## Thesis

**Identifying Temporal Customer-Perception Patterns Associated with Business Closure: An LLM-Based Review Analysis with a Multimodal Extension**

**Author:** Aslihan Berber  
**Matriculation Number:** 03798114  
**Program:** Master in Management and Technology  
**University:** Technical University of Munich (TUM)


## Overview

This repository contains the analysis code used for the Master's thesis.

The study examines whether temporal patterns in Yelp reviews and LLM-derived customer-perception indicators are associated with Yelp endpoint closure among Italian restaurants in Pennsylvania.

The analysis combines conventional Yelp review variables with customer-perception constructs extracted from review text using **Qwen2.5-VL-7B-Instruct**. A separate multimodal extension applies the same model to customer-uploaded Yelp images.

The LLM is used only for annotation and feature extraction. It does not directly predict restaurant closure. Closure associations are examined separately using statistical models.

The main analysis is implemented in the Jupyter/Google Colab notebook:

`aslihanberber_identifying_temporal_customer-perception_patterns.ipynb`

A Python export of the notebook is also provided:

`aslihanberber_identifying_temporal_customer-perception_patterns.py`

The notebook is the primary version of the analysis and is intended to be run in Google Colab.


## Project Folder Structure

The shared Google Drive project folder is organized approximately as follows:


TUM_thesis_digital_marketing/
├── README.md
├── aslihanberber_03798114_master_thesis_digital_marketing.ipynb
├── aslihanberber_03798114_master_thesis_digital_marketing.py
├── raw/
│   ├── json/
│   │   ├── Yelp-JSON.zip
│   │   ├── zip_extracted/
│   │   └── tar_extracted/
│   └── photos/
│       ├── Yelp-Photos.zip
│       ├── zip_extracted/
│       ├── tar_extracted/
│       ├── sampled_images_for_llm/
│       └── selected category-diverse image files/folder
└── processed/
    └── intermediate and final datasets generated during the analysis


The `raw` folder contains the original Yelp dataset archives together with the extracted files used by the notebook.

The `processed` folder contains intermediate and final datasets generated during filtering, LLM annotation, feature construction, aggregation, validation, and statistical analysis.

Some folders inside `raw/photos/`, such as the sampled or selected image folders, were generated during the study and are not part of the original Yelp download.


## Data Source

The analysis uses the **Yelp Open Dataset**, downloaded in **May 2026**.

The exact dataset files used for this thesis are included in the shared Google Drive project folder under:

`raw/`

The original downloaded archives are retained as:

- `raw/json/Yelp-JSON.zip`
- `raw/photos/Yelp-Photos.zip`

The extracted JSON and photo archives used during the analysis are also included in the shared folder.

The JSON archive contains the Yelp dataset documentation and `yelp_dataset.tar`, while the photo archive contains the corresponding documentation and `yelp_photos.tar`.

The analysis uses:

- business metadata;
- review text and review metadata;
- photo metadata; and
- customer-uploaded restaurant images.

Keeping the original archives and extracted files in the shared project folder allows the analysis to be reproduced using the same Yelp dataset version used in the thesis rather than a later release.

The final analytical sample contains **957 Italian restaurants in Pennsylvania** that had at least one review during the first pre-COVID observation period.

The Yelp `is_open` variable is used to construct the endpoint closure indicator:

- `closed = 0`: restaurant marked open at the Yelp dataset endpoint;
- `closed = 1`: restaurant marked closed at the Yelp dataset endpoint.

The dataset does not provide an exact closure date or reason for closure. Therefore, the outcome represents Yelp endpoint closure status rather than legal bankruptcy or a directly observed failure event.


## Temporal Design

The analysis uses four equal nine-month periods covering 36 months:

| Period | Dates |
|---|---|
| Early pre-COVID | 2018-09-01 to 2019-05-31 |
| Late pre-COVID | 2019-06-01 to 2020-02-29 |
| COVID shock | 2020-03-01 to 2020-11-30 |
| Adaptation/recovery | 2020-12-01 to 2021-08-31 |

The restaurant sample is fixed using the early pre-COVID period. Restaurants that first appear in later periods are not added to the sample.

This design allows the analysis to distinguish:

- pre-existing development before COVID-19;
- change during the COVID shock;
- adaptation or recovery after the initial shock; and
- longer-term development across the complete observation period.


## Notebook Structure

The notebook is organized into eight main sections.

### 1. Environment and Data Setup

This section:

- mounts Google Drive;
- defines project directories;
- locates and extracts the Yelp JSON and photo archives;
- loads the required libraries; and
- verifies that the raw dataset files are available.

The code assumes the following main project directory in Google Drive:

`/content/drive/MyDrive/TUM_thesis_digital_marketing`

If the shared folder is copied or mounted at a different location, this base directory must be updated near the beginning of the notebook.

### 2. Business Dataset Exploration

This section explores the Yelp business dataset and constructs increasingly specific restaurant subsets:

- all Yelp businesses;
- all restaurants;
- all Italian restaurants; and
- Pennsylvania Italian restaurants.

It also examines endpoint open/closed status, geographic coverage, star ratings, review counts, and category information.

### 3. Final Restaurant Sample Construction

This section loads reviews belonging to Pennsylvania Italian restaurants and examines their review coverage.

The fixed restaurant sample is subsequently defined using restaurants with at least one review in the early pre-COVID period.

### 4. Four-Period Review Dataset & Review-Based Feature Construction

This section creates the four-period analytical design and constructs conventional Yelp variables.

Period-level variables include:

- review count;
- average star rating;
- standard deviation of star ratings;
- low-star review share;
- high-star review share;
- average useful votes;
- average funny votes; and
- average cool votes.

Additional dynamic variables describe temporal change, including:

- rating changes between periods;
- changes in low-star review share;
- review-volume changes and ratios;
- repeated high low-star-share periods; and
- review-activity continuity.

A period with a high low-star share is defined as a period in which at least 30% of reviews have one or two stars.

The section also prepares the review-level dataset later used for LLM annotation.

### 5. Yelp Photo Dataset Preparation

This section loads and processes Yelp photo metadata and image files.

Menu images are excluded because they do not primarily represent the customer experience examined in this thesis.

To reduce strong differences in photo availability across restaurants, a maximum of five photos per restaurant is selected. The sampling procedure aims to retain category diversity across available photo types.

The resulting image sample contains **1,957 images from 639 restaurants**.

Because Yelp photo metadata does not provide upload timestamps, image-derived indicators are treated as static restaurant-level variables rather than temporal variables.

### 6. LLM Annotation

This section loads **Qwen2.5-VL-7B-Instruct** and defines the final review-text and image annotation procedures.

The model is used for structured feature extraction only.

#### Review-Text Indicators

The nullable review constructs are:

- food quality;
- price-value;
- cleanliness;
- service reliability;
- atmosphere;
- environment;
- warmth; and
- visual appeal.

These constructs are scored between `-1` and `1` when supported by the review text and are returned as `null` when no meaningful evidence is present.

The following variables are always numeric:

- overall sentiment: `[-1, 1]`;
- complaint intensity: `[0, 1]`; and
- confidence: `[0, 1]`.

The final review annotation dataset contains **32,500 unique reviews**.

#### Image Indicators

The image annotation procedure extracts:

- visual food appeal;
- presentation quality;
- portion signal;
- visual cleanliness;
- visual atmosphere;
- visual environment;
- exterior condition;
- crowding signal;
- negative visual signals; and
- annotation confidence.

Indicators that cannot be meaningfully assessed from a particular image are returned as `null`.

#### Output Validation

LLM outputs are automatically checked for:

- valid JSON parsing;
- presence of all required fields;
- absence of unexpected fields;
- correct data types;
- valid score ranges; and
- correct handling of nullable indicators.

Invalid outputs are retried during annotation. Checkpoints are saved during large annotation runs so the process can be resumed after interruption.

The final annotation files are also checked for expected row counts, duplicate identifiers, validity flags, and out-of-range values.

Manual validation samples are additionally created for both review and image annotations.

Inference is performed with random sampling disabled (`do_sample=False`) to reduce output variation across repeated runs.


### 7. LLM Feature Aggregation and Modeling Dataset

This section aggregates review-level annotations into restaurant-period features.

For nullable constructs, averages are calculated only from reviews where the construct is actually observed. Missing evidence is therefore not treated as a neutral value of zero.

The code also calculates:

- evidence counts;
- evidence shares;
- period-specific construct levels; and
- temporal changes in LLM-derived constructs.

Image annotations are aggregated separately to one row per restaurant.

The conventional Yelp features, review-based LLM features, and image-based features are then merged into the final restaurant-level modeling dataset.

The final modeling dataset contains:

- **957 restaurants**
- **274 variables**

Restaurants without eligible annotated images remain in the main dataset, while their image-derived scores remain missing.

### 8. Statistical Analysis & Results

This section contains the statistical analyses reported in the thesis.

The main outcome is Yelp endpoint closure status.

Continuous predictors are standardized within the relevant estimation sample before modeling.

The analysis includes:

- conventional Yelp benchmark models;
- temporal LLM-only models;
- Yelp-adjusted LLM models;
- period-specific LLM level models;
- review continuity and repetition analysis; and
- post-hoc image-extension models.

Standard logistic regression is used where the number of closure events provides sufficient estimation support.

For sparse temporal specifications:

- cases with 5-9 closure events are examined using exploratory L2-penalized logistic regression;
- cases with fewer than 5 closure events are retained for descriptive comparison only.

These sparse analyses are treated as supplementary and are not used for the main inferential conclusions.

Reported model statistics include, where applicable:

- odds ratios;
- 95% confidence intervals;
- p-values;
- average marginal effects;
- Akaike Information Criterion (AIC);
- McFadden pseudo-R²; and
- in-sample AUC.

All reported relationships are interpreted as associations rather than causal effects.


## Main Generated Data Files

The notebook creates several intermediate and final files in the project's `processed` directory.

Important final outputs include:

- `review_text_annotations_final.csv`  
  Final review-level LLM annotations.

- `photo_annotations_final.csv`  
  Final image-level LLM annotations.

- `review_llm_restaurant_period_final.csv`  
  Review-based LLM indicators aggregated by restaurant and period.

- `review_llm_temporal_features_final.csv`  
  Restaurant-level temporal LLM features.

- `image_llm_restaurant_features_final.csv`  
  Restaurant-level image features.

- `final_restaurant_modeling_dataset.csv`  
  Final merged dataset used for statistical modeling.

Checkpoint files are also created during review and image annotation so interrupted runs can be resumed.


## Reproducibility Notes

The notebook was developed and executed in **Google Colab Pro**.

Several cells contain Colab-specific functionality, including:

- Google Drive mounting;
- shell installation commands using `!pip`; and
- GPU-based inference.

For this reason, the `.ipynb` notebook should be treated as the main executable version.

The `.py` file is an export of the notebook for easier inspection and archival purposes. Some Colab-specific commands may require modification before the Python file can be executed directly in a standard local Python environment.

The LLM used in the study is:

`Qwen/Qwen2.5-VL-7B-Instruct`

The model requires substantially more computational resources than the non-LLM sections of the notebook. A GPU-enabled Colab runtime is therefore recommended for annotation.

Large-scale review and image annotation may take considerable time. The checkpointing logic allows annotation to continue from previously completed outputs rather than restarting from the beginning.

If final annotation files and processed datasets are already available, the later aggregation and statistical-analysis sections can be run without repeating the complete LLM annotation process.



## Recommended Execution Order

For a complete reproduction, run the notebook from top to bottom using the Yelp dataset files already included in the shared `raw` folder.

The code is written for the following project location in Google Drive:

`/content/drive/MyDrive/TUM_thesis_digital_marketing`

If the shared folder is copied or mounted at a different location, the base directory defined near the beginning of the notebook must be updated accordingly.

If the final annotation files and processed datasets are already available in the `processed` folder, the computationally expensive LLM annotation steps do not need to be repeated. The saved annotation files can instead be loaded directly before rerunning feature aggregation and statistical analysis.

Care should be taken not to overwrite completed annotation files unless a full re-annotation is intentionally required.


## Software and Main Python Packages

The notebook uses standard Python data-analysis and machine-learning libraries, including:

- `pandas`
- `numpy`
- `matplotlib`
- `statsmodels`
- `scikit-learn`
- `torch`
- `transformers`
- `accelerate`
- `bitsandbytes`
- `qwen-vl-utils`

Additional standard-library modules are used for JSON processing, file handling, archive extraction, timing, and checkpoint management.

Package installation commands are included directly in the notebook where required.


For questions regarding the analysis or thesis code:

**Aslihan Berber**  
Technical University of Munich