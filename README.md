# GCP_IITM_workshop
# ☁️ GCP Serverless ML Inference Pipeline
## 🚀 Overview
This repository contains the code and architecture details for a serverless Machine Learning inference pipeline built on Google Cloud Platform (GCP). The objective of this project was to deploy a trained Scikit-Learn model, process incoming datasets, compute model accuracy, and trigger automated notifications using GCP's event-driven architecture. 

## 🏗️ Architecture & Workflow
The pipeline leverages the following GCP services:
1. **Google Cloud Storage (GCS):** Acts as the central data lake. Stores the trained ML model (`model.joblib`), target labels (`test_targets.csv`), and the incoming inference data (`test.csv`).
2. **Cloud Run Functions (formerly Cloud Functions):** A serverless Python backend function that gets triggered to perform the heavy lifting.
3. **Google Cloud Pub/Sub:** A messaging service used to publish the final computed accuracy and trigger a downstream bot notification (G-Space).

## 🛠️ Tech Stack
- **Language:** Python 3.12
- **Libraries:** `pandas`, `scikit-learn`, `joblib`, `numpy`
- **GCP Services:** Cloud Storage, Cloud Functions, Pub/Sub, IAM

## 🧠 Key Learnings & Problem Solving
Building this pipeline wasn't just about writing code; it involved navigating real-world cloud infrastructure challenges:
* **Region Constraints & IAM:** Encountered strict IAM policies restricting bucket deletions. Learned how region mismatches (e.g., `asia` vs `us-central1`) can silently fail Eventarc triggers.
* **Architecture Bypassing:** When default Eventarc triggers failed due to organizational policy restrictions, I successfully bypassed the limitation by converting the event-driven function into an **HTTP-triggered Cloud Function**, allowing manual invocation while maintaining the exact same pipeline flow and successfully hitting the Pub/Sub topic.
* **Serverless Execution:** Handled file streams (`io.BytesIO`) directly in memory within the serverless environment to load the ML model and CSV files without relying on local disk storage.

## 💻 Core Logic (`main.py`)
The main serverless function performs the following steps:
1. Validates the presence of `test.csv` in the GCS bucket.
2. Reads the CSV and generates summary statistics, saving them to a shared output bucket.
3. Loads the serialized `model.joblib` and `test_targets.csv` from GCS.
4. Runs predictions using the Scikit-Learn model and calculates the accuracy score.
5. Packages the accuracy and Roll Number into a JSON payload and publishes it to a Pub/Sub topic.

## 🚀 How to Run
1. Ensure your GCP project has Cloud Storage, Cloud Functions, and Pub/Sub APIs enabled.
2. Create a GCS bucket and upload `model.joblib` and `test_targets.csv`.
3. Deploy the code in `main.py` and `requirements.txt` to a Cloud Run Function (Python 3.12).
4. Upload `test.csv` to your bucket.
5. Trigger the HTTP endpoint to execute the inference pipeline.

## 🙏 Acknowledgments
A huge shoutout and special thanks to **Sai Sir (Saikiran Puvvada)** for conducting this incredible GCP Workshop. The foundational architecture, guidance, and hands-on challenges provided in the session were instrumental in making this learning experience possible! 
GCP Project : https://console.cloud.google.com/welcome?project=iitm-gcp-workshop
