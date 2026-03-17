# AI-Powered Identity Verification & KYC Suite

This repository features a modular, high-security pipeline for automated identity verification. The system is designed to process official documents (optimized for Moroccan CIN and Passports), perform biometric validation, and detect sophisticated fraud like deepfakes and document inconsistencies.

---

## 🧠 Core Methodology: Multi-Layered Trust
The suite operates on a **Defense-in-Depth** model. Instead of trusting a single scan, it validates identity through five independent verification pillars:

1.  **Semantic Document Extraction:** Using VLMs to understand documents as a human would.
2.  **Biometric Core:** Mathematical face-matching between live and document portraits.
3.  **Forensic Meta-Analysis:** Scanning for digital artifacts left by AI generators.
4.  **Cross-Document Integrity:** Comparing multiple ID types (Passport vs. CIN) for "unconstintous" data.
5.  **Social Proofing:** Verifying real-world digital footprints via OSINT.

---

## 🛠 Project Modules

### 1. Document OCR & Extraction (`Offline_V2.ipynb`)
* **Model:** Qwen2.5-VL-7B-Instruct.
* **Approach:** Multimodal reasoning. It extracts structured data (Name, CIN, DOB) from both sides of a Moroccan ID simultaneously without needing hard-coded templates.
* **Input:** Front (recto) and Back (verso) images of the ID.
* **Output:** Structured JSON data.

### 2. Biometric KYC & Age Estimation (`KYC.ipynb`)
* **Model:** `DeepFace` (Facial Recognition) & `GPT-4o` (Visual Analysis).
* **Approach:** * **Matching:** Calculates cosine similarity between a live selfie and the ID photo.
    * **Heuristics:** Uses OpenAI Vision to analyze skin texture and facial structure for age estimation to cross-reference with the ID's birth date.
* **Input:** ID portrait and live user selfie.

### 3. AI-Generated Image (Deepfake) Detector (`AI_check.ipynb`)
* **Approach:** Forensic Metadata Scan. It inspects image headers for signatures and EXIF tags specific to AI generators like Midjourney, DALL-E, and Stable Diffusion.
* **Input:** User-submitted selfie.
* **Output:** `is_ai_likely` flag with specific evidence tags.

### 4. Integrity & Anomaly Check (`Anomalies_check_1.ipynb`)
* **Model:** **Qwen2.5-VL:3b** (via Ollama).
* **Approach:** **Cross-Document Forensic Validation**. This module takes a Passport and a CIN image and performs a comparative analysis. It looks for "unconstintous" (inconsistent) information between the two documents.
    * **Deterministic Logic:** Uses `temperature: 0` to ensure exact data extraction and zero "hallucinations."
    * **Validation Checks:** Compares Full Name, DOB, and ID numbers across both documents. It also calculates if either document is expired based on a reference date (e.g., March 2026).
* **Input:** Passport image and CIN front image.
* **Output:** Detailed JSON report identifying `anomalies_found` and a final `verification_status` (Pass/Fail).

### 5. Social Footprint Validator (`Social_Network (1).ipynb`)
* **Approach:** OSINT (Open Source Intelligence). It queries public APIs to find social media profiles (LinkedIn, Instagram) matching the identity.
* **Input:** Name and City from the document.
* **Output:** List of digital footprint URLs and snippets.

---

## 🔬 Model Spotlight: Qwen2.5-VL (3B & 7B)
The project heavily utilizes **Vision-Language Models (VLM)**. 
* **Type:** These models are "multimodal," meaning they interpret pixels and text in a shared space. 
* **Local Execution:** The 3B version is served locally via **Ollama**, ensuring that sensitive personal data (Passports/CINs) never leaves the local environment.
* **Zero-Shot Learning:** The model does not need training on specific Moroccan ID layouts; it understands the *concept* of an identity document and can adapt to variations in document design.

---

## 📦 Requirements & Installation

### Dependencies
```bash
# General AI and Vision
pip install transformers torch qwen_vl_utils
pip install deepface tf-keras opencv-python

# Local Model Serving
# Install Ollama ([https://ollama.com](https://ollama.com)) and run:
# ollama pull qwen2.5vl:3b

# Reporting and Search
pip install langchain-openai pillow pandas requests tavily-python
