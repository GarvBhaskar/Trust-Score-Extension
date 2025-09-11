# Trust Score – AI-Powered Shopping Assistant

The **Trust Score Extension** helps you shop smarter on **Amazon** by automatically analyzing product listings with AI.  
It detects signs of **counterfeit products, fake reviews, misleading descriptions, and suspicious return patterns** — then gives you a simple **Trust Score (%)** directly on the product page.

---

## Features

| Feature | Description |
|---------|-------------|
| **Trust Score Badge** | Instant percentage score on every product |
| **Image-Text Consistency** | Uses CLIP + BLIP to validate alignment between product images and descriptions |
| **Review Authenticity** | Uses BART-MNLI to detect templated, bot-like, or suspicious reviews |
| **Brand Verification** | Logo and branding cross-checks (heuristics + vision models) |
| **Return Feedback Analysis** | Uses DistilBERT to classify customer complaints (e.g., fake, wrong item, poor quality) |
| **Lightweight & Fast** | Works seamlessly while browsing |
| **Privacy Friendly** | Sends only essential product data to the AI API |
---

## Architecture

<p align="center">
  <img src="https://github.com/GarvBhaskar/Trust-Score-Extension/blob/main/Architecture%20TSE.png" alt="Screenshot 1" width="900"/>
</p>

---

## Machine Learning Models Used

| Task | Model | Purpose |
|------|-------|---------|
| Image–Text Similarity | [openai/clip-vit-base-patch32](https://huggingface.co/openai/clip-vit-base-patch32) | Embeds product images & text, measures alignment |
| Image Captioning | [Salesforce/blip-image-captioning-base](https://huggingface.co/Salesforce/blip-image-captioning-base) | Generates captions from product images to validate against product title/description |
| Review Authenticity | [facebook/bart-large-mnli](https://huggingface.co/facebook/bart-large-mnli) | Zero-shot classification of reviews to detect authenticity & coherence |
| Returns Feedback | [distilbert/distilbert-base-uncased](https://huggingface.co/distilbert/distilbert-base-uncased) | Sentiment & intent analysis on return complaints |

---

## Installation

### 1. Download the Extension
1. Go to the **Releases** section of this repo.
2. Download the latest `trust-score-extension.zip`.
3. Extract the zip file to a folder on your computer.

### 2. Load in Chrome
1. Open **Chrome** (or any Chromium-based browser).
2. Go to `chrome://extensions/`.
3. Enable **Developer Mode** (top-right).
4. Click **Load unpacked** and select the "extension" folder.  
Done — the extension runs automatically on Amazon product pages!

<p align="center">
  <img src="https://github.com/user-attachments/assets/4dd950da-c6b7-4a2f-8e41-2a308ab7ac89" alt="Screenshot 2" width="400"/>
</p>

---

## How to Use

1. Visit any **Amazon product page**.  
2. Wait a moment while the extension analyzes the product.  
3. A **Trust Score button** will appear at the bottom-left.  
4. Hover or click to see a detailed modal with analysis.

### Example Trust Scores

| Score | Meaning |
|-------|---------|
| **95%** | Reliable listing |
| **60%** | Some risks detected |
| **30%** | High chance of counterfeit or misleading info |

<p align="center">
  <img src="https://github.com/user-attachments/assets/1f20e871-40ab-4fb4-94c4-87746493b2c1" alt="Screenshot 1" width="400"/>
</p>

---

## Technical Architecture

1. **Chrome Extension (Frontend)**  
   - Extracts product info (title, description, images, reviews).  
   - Sends JSON payload to backend (`/trust_score`).  
   - Renders Trust Score badge + modal UI via Shadow DOM.

2. **Backend (FastAPI-based Orchestrator)**  
   - Receives product data.  
   - Runs multiple ML modules: CLIP + BLIP for images, BART-MNLI for reviews, DistilBERT for returns.  
   - Aggregates module scores into a final Trust Score.  
   - Returns JSON with per-module details & explanations.

3. **ML Models (Hugging Face)**  
   - CLIP: image-text similarity  
   - BLIP: image captioning  
   - BART-MNLI: zero-shot review authenticity  
   - DistilBERT: return sentiment classification  

4. **Response Returned to Extension**  
   ```json
   {
     "trust_score": 0.82,
     "details": {
       "image_text_alignment": {"score":0.9,"model":"clip-vit-base-patch32","summary":"Images match description"},
       "review_authenticity": {"score":0.7,"model":"bart-large-mnli","summary":"Some templated reviews detected"},
       "logo_verification": {"score":0.95,"summary":"Brand logo verified"},
       "returns_feedback": {"score":0.5,"model":"distilbert-base-uncased","summary":"Multiple returns cite authenticity issues"}
     }
   }
