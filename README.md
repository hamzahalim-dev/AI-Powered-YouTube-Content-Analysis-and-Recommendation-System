# AI-Powered YouTube Content Analysis and Recommendation System Using NLP

An end-to-end Python project that collects publicly accessible YouTube video metadata and comments, analyzes textual content, recommends similar videos, explores comment topics with zero-shot classification, and organizes findings into an Excel insights report.

## Overview

YouTube videos generate useful data through titles, descriptions, view counts, likes, and user comments. This project builds a small data-analysis pipeline to organize that information and explore it using Natural Language Processing (NLP).

The system uses **TF-IDF and cosine similarity** to recommend textually similar videos and a pretrained **BART zero-shot classification model** to explore topics discussed in comments.

> **Project status:** Exploratory academic project. Results are useful for demonstrating the workflow, but comment labels and recommendation quality still require formal validation.

## Key Features

- Collects accessible video metadata and comments through the **YouTube Data API v3**.
- Saves raw and processed datasets as CSV files.
- Cleans video and comment text for downstream analysis.
- Generates content-based video recommendations using TF-IDF and cosine similarity.
- Produces five recommendations for each source video.
- Explores comment relevance and topic categories using zero-shot classification.
- Compares available views, likes, and comment counts.
- Generates a structured Excel insights report with summaries, rankings, and visualizations where supported by the reporting code.
- Stores project data and outputs in Google Drive so saved files persist beyond a Colab runtime session.

## Recorded Results

The project report records the following results from the latest documented run:

| Measure | Recorded result |
|---|---:|
| Videos collected | 30 |
| Comments collected | 2,299 |
| Recommendation records | 150 |
| TF-IDF matrix in an earlier run | 30 × 325 |
| Recommendations per source video | 5 |

The comment-classification output contained broad categories, including general AI discussion, benefits and applications, education, privacy, jobs, AI safety, copyright, generic/off-topic comments, and uncertain predictions.

**Important:** These are recorded experimental results, not a guarantee that a fresh run will produce identical counts. The category counts are model predictions, not verified ground-truth labels.

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Main programming language |
| Google Colab | Notebook-based development and execution |
| Google Drive | Persistent storage for datasets and reports |
| YouTube Data API v3 | Collection of accessible YouTube metadata and comments |
| Pandas | Data cleaning, manipulation, and analysis |
| NumPy | Numerical operations |
| Scikit-learn | TF-IDF vectorization and cosine similarity |
| Hugging Face Transformers | Loading and running the pretrained classification model |
| `facebook/bart-large-mnli` | Zero-shot comment classification |
| OpenPyXL | Excel workbook generation and formatting |

## How It Works

```text
YouTube Data API v3
        |
        v
Collect video metadata and accessible comments
        |
        v
Save raw CSV datasets in Google Drive
        |
        v
Clean and prepare video and comment text
        |
        +-------------------------------+
        |                               |
        v                               v
Video recommendation pipeline      Comment analysis pipeline
        |                               |
        v                               v
TF-IDF text vectors                Zero-shot classification
        |                           with BART-large-MNLI
        v                               |
Cosine similarity ranking              v
        |                         Relevance/topic labels
        v                               |
Five recommendations per video          |
        |                               |
        +---------------+---------------+
                        |
                        v
          CSV outputs and Excel insights report
```

### 1. Data Collection

The YouTube Data API v3 is used to retrieve accessible video information, such as:

- Video ID, title, channel, publication date, and description
- Available view, like, and comment counts
- Video duration
- Accessible comments

Comment availability depends on video settings, API permissions, resource availability, and quota limits. The collected data should not be assumed to include every YouTube comment.

### 2. Data Preprocessing

Raw video and comment data are stored separately. Text fields are prepared for analysis, with cleaned fields such as `clean_title` and `clean_comment` used in subsequent steps.

### 3. Content-Based Recommendations

The recommendation pipeline:

1. Combines relevant video text, including titles and descriptions.
2. Converts the text into numerical features using TF-IDF.
3. Calculates pairwise cosine similarity between video vectors.
4. Ranks candidate videos by similarity.
5. Selects the five highest-ranked alternatives for each source video.

Cosine similarity is used as a measure of textual similarity. A higher score does not necessarily mean that a recommendation is useful or semantically related to a human viewer.

### 4. Comment Topic Analysis

The project uses the pretrained `facebook/bart-large-mnli` model for zero-shot classification. It compares comments against candidate topic labels without training a new classifier on this project's comments.

Explored topics include:

- Benefits and useful applications of AI
- Education and learning
- Jobs and workplace automation
- Privacy and surveillance
- Copyright and training data
- AI safety, misinformation, deepfakes, and harmful uses
- General AI discussion
- Generic, off-topic, or uncertain comments

The later recorded workflow used a relevance cutoff of `0.60` and a topic cutoff of `0.40`. These are decision thresholds applied to model scores, not guarantees of correctness or automatically calibrated probabilities.

### 5. Video Performance Analysis and Reporting

The reporting workflow compares available views, likes, and reported comment counts. It can rank videos by these measures and calculate a project-defined engagement-rate proxy:

\[
\text{Engagement Rate (\%)} =
\frac{\text{Likes}+\text{Reported Comments}}{\text{Views}}\times100
\]

This is a project-defined metric, not an official or universal YouTube engagement measure. The Excel workbook is intended to organize summary statistics, rankings, topic and comment analysis, methodology, and recommendation results where the corresponding data is available.

## Project Structure

The report documents the following intended directory structure. Exact files may differ depending on which notebook cells have been executed.

```text
AI_YouTube_Project/
├── data/
│   ├── youtube_videos.csv
│   ├── youtube_comments.csv
│   ├── youtube_videos_cleaned.csv
│   ├── youtube_comments_cleaned.csv
│   ├── youtube_comments_topics.csv
│   ├── youtube_comments_improved_analysis.csv
│   ├── youtube_two_stage_checkpoint.csv
│   ├── youtube_comments_validated_v2.csv
│   ├── topic_summary.csv
│   ├── uncertain_topic_comments.csv
│   ├── uncertain_relevance_comments.csv
│   ├── generic_comments.csv
│   ├── relevant_comments.csv
│   ├── video_recommendations.csv
│   └── top_comment_words.csv
└── output/
    └── AI_YouTube_Insights_Report.xlsx
```

Some files represent intermediate or earlier experimental classification runs. Do not combine their results without checking their label definitions and versions.

## Getting Started

The project was implemented in **Google Colab** and uses Google Drive for persistent storage.

### Prerequisites

- A Google account with access to Google Colab and Google Drive
- A Google Cloud project with the YouTube Data API v3 enabled
- A YouTube Data API key
- Python packages used by the notebook

### 1. Configure the API key securely

1. Create or select a project in the [Google Cloud Console](https://console.cloud.google.com/).
2. Enable the [YouTube Data API v3](https://console.cloud.google.com/apis/library/youtube.googleapis.com).
3. Create an API key and apply appropriate restrictions.
4. In Google Colab, add the key to **Secrets** and grant the notebook access to it.
5. Read the key from Colab Secrets in the notebook.

**Never commit your API key to GitHub or hard-code it in a shared notebook.** If a key has been exposed, rotate it in Google Cloud Console.

### 2. Open the notebook

Upload or open the project's `.ipynb` notebook in [Google Colab](https://colab.research.google.com/). The report describes the notebook workflow, but the notebook itself should be included in this repository for others to reproduce the project.

### 3. Connect Google Drive

Run the notebook's Drive-mounting setup cell and configure the project directory. Save datasets and reports to Drive so they remain available after the Colab runtime disconnects.

### 4. Install dependencies

Install the packages required by your notebook. A typical starting set for the technologies described in the report is:

```bash
pip install pandas numpy scikit-learn transformers torch openpyxl
```

The exact package versions should be pinned in a `requirements.txt` file after checking the environment used by the notebook.

### 5. Run the workflow

Execute the notebook cells in order:

1. Configure the environment and connect Google Drive.
2. Collect video metadata and accessible comments.
3. Save the raw CSV files.
4. Preprocess the datasets.
5. Generate TF-IDF vectors and video recommendations.
6. Run zero-shot comment classification.
7. Calculate video statistics and summaries.
8. Export the Excel insights report.

API quota, unavailable comments, and model download or runtime constraints can affect execution.

## Outputs

Depending on the notebook run, outputs may include:

- Raw and cleaned video metadata CSV files
- Raw and cleaned comment CSV files
- Comment topic and relevance-classification results
- CSV files containing ranked video recommendations and similarity scores
- Topic summaries and lists of uncertain or generic comments
- `AI_YouTube_Insights_Report.xlsx`

The report-generation code determines which worksheets and charts are actually included in the workbook.

## Limitations

- The documented dataset contains only 30 videos, so it is limited in size and diversity.
- Some videos do not allow comment retrieval, and API quota limits may restrict collection.
- Collected comments may not represent the wider YouTube audience.
- TF-IDF measures vocabulary-based similarity and may miss deeper semantic relationships.
- Recommendations are not personalized and do not use an individual's watch history.
- Similarity scores do not measure video quality, credibility, or usefulness.
- Manual sample checks found comment-classification errors.
- Many comments were assigned broad or uncertain labels.
- The classifier has not been benchmarked against a representative manually labeled dataset.
- Recommendation relevance has not been formally evaluated.
- Video statistics can change over time.

## Future Improvements

- Collect a larger and more diverse sample of videos using a reproducible sampling strategy.
- Compare TF-IDF with semantic text embeddings.
- Manually evaluate a sample of recommendations and measure top-result relevance.
- Build a manually labeled comment benchmark.
- Evaluate comment classification with precision, recall, and F1-score.
- Improve handling of uncertain, generic, and off-topic comments.
- Record collection dates, model settings, and package versions.
- Improve reporting visualizations and support evidence-based interpretation.

## Security and Data Handling

- Keep API credentials in Colab Secrets or environment variables.
- Never commit API keys, tokens, or other credentials.
- Check whether collected data and generated outputs should be included in a public repository before uploading them.
- Follow the YouTube API terms and applicable privacy requirements when collecting, storing, and sharing data.

## References

- [YouTube Data API documentation](https://developers.google.com/youtube/v3)
- [Scikit-learn: TfidfVectorizer](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html)
- [Scikit-learn: cosine similarity](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.pairwise.cosine_similarity.html)
- [Hugging Face Transformers documentation](https://huggingface.co/docs/transformers/index)
- [BART paper — Lewis et al. (2020)](https://aclanthology.org/2020.acl-main.703/)

## Author

**Hamza Halim**  
BS Artificial Intelligence  
University of Management and Technology (UMT)

---

*This project was developed for academic work in Big Data Analysis. It demonstrates an introductory data collection, NLP, recommendation, classification, and reporting pipeline; its outputs should be interpreted as exploratory until validated.*
