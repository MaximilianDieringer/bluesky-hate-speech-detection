# Detecting Hate Speech on Bluesky

An Apache Spark pipeline that predicts a hate speech score for Bluesky posts. The project was developed by **Maximilian Dieringer, Matthäus Engelbrecht, Martin André Sepulveda Riquelme and Borisz Gabor Sztaniszlav** as part of the course "Data Processing 2" (course 4892) in the Data Science programme at WU Vienna.

-----

# Project Overview

The goal is a tool that supports content moderation by identifying and flagging potentially harmful content in near real time. It is motivated by the growing need for effective moderation on social media and by regulation such as the European Digital Services Act. Instead of a binary label, the models predict a continuous hate speech score, which allows posts to be ranked by their hateful content potential.

* **Scalable data pipeline:** implemented with Apache Spark to handle large volumes of data.
* **Dual data sources:** an annotated hate speech corpus (Hugging Face) for training and a live Bluesky API feed for real-world inference.
* **End-to-end NLP processing:** language detection, removal of stop words, URLs and mentions, POS tagging, lemmatization and feature engineering.
* **Regression-based modeling:** Linear Regression, Random Forest and Gradient-Boosted Trees are trained and compared.
* **Ethical and legal framework:** data minimization and privacy-enhancing mechanisms aligned with GDPR principles.

-----

# Architecture

1. **Data loading:** a labeled hate speech dataset is loaded from Hugging Face. Live posts are streamed from the Bluesky "hot-classic" feed via its API.
2. **General pre-processing:** both datasets are reduced to the required columns (content, ID, hate speech score) and cleaned of nulls and duplicates. The NLP pipeline keeps English posts only, removes stop words, URLs and mentions, and applies POS tagging and lemmatization.
3. **Machine learning pre-processing:** cleaned text is tokenized and converted to numeric feature vectors with HashingTF and IDF.
4. **Training and evaluation:** the Hugging Face data is split 80/20 into training and test data. Three regression models are trained and evaluated with RMSE and R².
5. **Inference:** the best model (Linear Regression) predicts hate speech scores for pre-processed posts from the Bluesky stream.

-----

# Data

* **Training data:** [ucberkeley-dlab/measuring-hate-speech](https://huggingface.co/datasets/ucberkeley-dlab/measuring-hate-speech) (UC Berkeley, CC-BY-4.0), over 135,000 labeled posts from YouTube, Twitter and Reddit. The project uses the text and the continuous `hate_speech_score`. The notebook downloads the dataset directly, so no data file is included in this repository.
* **Inference data:** real-time posts from the Bluesky API (semi-structured JSON). The post content is used for prediction and the URI as identifier.

-----

# Results

| Model | RMSE | R² |
| :--- | :--- | :--- |
| Linear Regression | **1.687** | **0.333** |
| Gradient-Boosted Trees | 1.824 | 0.220 |
| Random Forest | 1.889 | 0.164 |

Linear Regression performed best on both metrics. The R² value shows that the model explains only part of the variance (a sign of underfitting), so it serves as a baseline. On the Bluesky data it was still able to rank posts according to their hateful content potential.

-----

# Getting Started

### Prerequisites

* Python 3.8+
* A local Apache Spark installation
* A Bluesky account and an App Password (create one in the Bluesky settings, do not use your main password)

### Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/MaximilianDieringer/bluesky-hate-speech-detection.git
   cd bluesky-hate-speech-detection
   ```

2. Install the dependencies (a virtual environment is recommended):

   ```bash
   pip install -r requirements.txt
   ```

3. Set up the Bluesky credentials: copy `credentials.example.json` to `credentials.json` and enter your handle and app password. The file `credentials.json` is listed in `.gitignore` and must never be committed.

4. Configure Spark: the notebook initializes Spark with `findspark.init("/usr/local/spark/")`. Adjust the path to your local Spark installation if needed.

### Execution

1. Make sure `preproc.py` is in the same folder as the notebook.
2. Open `bluesky-hate-speech-pipeline.ipynb` in Jupyter and run the cells sequentially.
3. One cell uninstalls and re-installs the `atproto` library and its dependencies to resolve version conflicts in some environments. Run it.

> Note: user names and IDs of Bluesky accounts in the saved notebook outputs have been replaced with placeholders (`user_001.example`, `did:plc:user001`). Running the notebook generates fresh outputs.

-----

# Repository Contents

| File | Description |
| :--- | :--- |
| `bluesky-hate-speech-pipeline.ipynb` | Main notebook with the full implementation |
| `preproc.py` | Helper module with user-defined functions for NLP pre-processing |
| `Final-Report.pdf` | Final project report |
| `credentials.example.json` | Template for the Bluesky credentials |
| `requirements.txt` | Python dependencies |
| `NOTICES.md` | Licenses of the third-party data and software (Hugging Face dataset, Bluesky SDK) |
| `LICENSE` | MIT License |

-----

# Contributors

* Maximilian Dieringer ([@MaximilianDieringer](https://github.com/MaximilianDieringer))
* Matthäus Engelbrecht
* Martin André Sepulveda Riquelme
* Borisz Gabor Sztaniszlav
