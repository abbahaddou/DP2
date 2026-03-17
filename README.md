# AI-Powered Identity Verification & KYC Suite

This repository contains a modular, multi-layered identity verification pipeline. It is designed to automate the onboarding process by extracting data from identity documents (optimized for Moroccan CIN), performing biometric face matching, estimating age via visual heuristics, and detecting fraudulent AI-generated images or synthetic identities.

---

## General Methodology
The system follows a **Defense-in-Depth** architecture. Instead of relying on a single data point, it validates identity through five independent pillars:
1.  **Multimodal Extraction:** Using Vision-Language Models (VLM) to understand document context.
2.  **Biometric Verification:** Mathematical comparison of facial embeddings.
3.  **Heuristic Validation:** Cross-referencing visual physical traits (age) against extracted document data.
4.  **Digital Forensics:** Scanning file metadata for traces of AI manipulation.
5.  **Social Proofing:** Verifying the "Proof of Existence" through public digital footprints (OSINT).

---

## Project Modules

### 1. Document OCR & Data Extraction
* **File:** `Offline_V2.ipynb`
* **Model/Approach:** **Qwen2.5-VL-7B-Instruct** (Vision-Language Model). Unlike traditional OCR, this approach uses a multimodal transformer to interpret the document layout semantically.
* **Inputs:** * `recto_path`: Image of the ID front.
    * `verso_path`: Image of the ID back.
* **Outputs:** Structured JSON including Full Name, CIN Number, Birth Date, and Address.

### 2. Biometric KYC & Age Verification
* **File:** `KYC.ipynb`
* **Model/Approach:** * **Face Matching:** `DeepFace` library (VGG-Face/Facenet) to calculate cosine similarity between faces.
    * **Age Estimation:** **GPT-4o (OpenAI Vision)**. An LLM analyzes visual cues (skin texture, facial hair, structure) to estimate age range.
* **Inputs:** * `image_path`: Portrait extracted from the ID.
    * `selfie_img`: A live user selfie.
* **Outputs:** * `verification_result`: Boolean (Match/No Match).
    * `age_report`: JSON containing `estimated_age_range` and `confidence_level`.

### 3. AI-Generated Image Detector
* **File:** `AI_check.ipynb`
* **Model/Approach:** **Forensic Metadata Analysis**. Scans image headers and EXIF data for keywords and software signatures associated with generative AI (Midjourney, Stable Diffusion, DALL-E).
* **Inputs:** User-submitted selfie image.
* **Outputs:** * `is_ai_likely`: Boolean flag.
    * `evidence`: Specific tags found (e.g., "AI Keyword found in EXIF").

### 4. Social Network Existence Validator
* **File:** `Social_Network (1).ipynb`
* **Model/Approach:** **OSINT (Open Source Intelligence)** via Search APIs (Tavily/Forum Search). It automates the search for a digital footprint to confirm the user is a real person.
* **Inputs:** `full_name` and `city` (extracted from the ID).
* **Outputs:** A list of verified social profile snippets (LinkedIn, Instagram, etc.).

### 5. Integrity & Anomaly Check
* **File:** `Anomalies_check_1.ipynb`
* **Model/Approach:** **Visual Forensic Analysis**. Checks for digital inconsistencies or manipulation markers within the document image files.
* **Inputs:** Document images.
* **Outputs:** Detection report of potential image tampering.

---

## 🚀 Execution Workflow
To achieve the highest security, execute the modules in this order:
1.  **AI Check:** Ensure the selfie isn't a deepfake.
2.  **Extraction:** Pull data from the ID card.
3.  **KYC:** Match the selfie to the ID and estimate age.
4.  **Cross-Check:** Compare the AI-estimated age against the document's birth date.
5.  **Social Validation:** Confirm the person exists in public records.

---

## 📦 Requirements & Setup

### Dependencies
```bash
pip install transformers torch qwen_vl_utils  # Extraction
pip install deepface tf-keras opencv-python   # Biometrics
pip install langchain-openai pillow pandas    # Reporting/Logic
pip install tavily-python requests            # OSINT
