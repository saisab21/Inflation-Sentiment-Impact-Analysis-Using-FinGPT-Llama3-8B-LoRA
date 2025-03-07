# Inflation Sentiment & Impact Analysis
# **Inflation Sentiment & Impact Analysis Using FinGPT-Llama3-8B LoRA**

## **🚀 Project Overview**
This project aims to analyze financial news articles to determine **how they impact inflation**.  
We utilize **FinGPT-Llama3-8B LoRA**, an open-source financial Large Language Model (FinLLM), to:
1. **Classify the article’s inflation sentiment** (Positive Inflation / Negative Inflation / Neutral Inflation).
2. **Identify key words contributing to inflation sentiment** and assign them **importance scores**.
3. **Compute an overall Inflation Impact Score (0 to 1)** to determine how much the article affects inflation.
4. **Store results in JSON format** for further analysis.

---

## **🎯 Objectives**
- **Classify Inflation Sentiment** → Does the news predict inflation will rise, fall, or remain neutral?
- **Identify Key Contributing Words** → Extract the top 10 most influential words.
- **Compute Inflation Impact Score (0-1)** → How much does the article impact inflation?
- **Optimize Model Execution** → Ensure efficient inference using 8-bit quantization.
- **Batch Process Large Datasets** → Efficiently process 20,000+ articles.

---

## **📌 Why Use `FinGPT-Llama3-8B LoRA`?**
- ✅ **Finance-specific LLM** → Pre-trained on financial data.
- ✅ **Low-Rank Adaptation (LoRA) Fine-tuned** → Efficient memory usage.
- ✅ **Optimized for inference** → Works well on a 20GB GPU.
- ✅ **Supports interpretability** → Can analyze attention & word attributions.

---

## **🔄 Pipeline Workflow**
### **1️⃣ Load FinGPT-Llama3-8B LoRA Efficiently**
- Use **8-bit quantization** to run efficiently on a **20GB GPU**.
- Load the model and tokenizer.

### **2️⃣ Preprocess Each Article**
- **If ≤ 4096 tokens** → Process directly.
- **If > 4096 tokens** → Split into **3072-token chunks with 1024-token overlap**.
- Aggregate sentiment & impact across chunks.

### **3️⃣ Classify Inflation Sentiment**
- Prompt the model:
  ```plaintext
  "Does this article indicate that inflation will rise, fall, or remain neutral?"
  ```
- Expected outputs:
  - **Positive Inflation** → Inflation **will increase**.
  - **Negative Inflation** → Inflation **will decrease**.
  - **Neutral Inflation** → No strong effect on inflation.

### **4️⃣ Extract Word-Level Contributions**
- Use **SHAP (Shapley Additive Explanations)** or **Attention Weights**.
- Identify **Top 10 words** that influence the sentiment.
- Assign **each word an importance score (0 to 1)**.

#### **Example Output:**
```json
{
    "word_attributions": {
        "interest rate hike": 0.92,
        "supply chain disruptions": 0.85,
        "currency depreciation": 0.81
    },
    "most_influential_word": "interest rate hike"
}
```

### **5️⃣ Compute Overall Inflation Impact Score (0-1)**
- **Ask the Model:**
  ```plaintext
  "Does this article have a strong, moderate, weak, or no effect on inflation?"
  ```
- Map responses to numeric values:

| **Model Response**  | **Numeric Mapping** |
|---------------------|--------------------|
| **Strong Effect**   | **0.9 - 1.0**      |
| **Moderate Effect** | **0.5 - 0.8**      |
| **Weak Effect**     | **0.1 - 0.4**      |
| **No Effect**       | **0.0 - 0.1**      |

- **Compute Model Confidence:**
  - Extract **logits** from the model.
  - Apply **softmax to get probabilities**.
  - Highest probability → **Model confidence**.

- **Final Calculation:**
  ```plaintext
  Inflation Impact Score = (Model-Predicted Effect) × (Model Confidence)
  ```

#### **Example Calculation:**
```plaintext
If model predicts "Strong Effect" (0.9) with 80% confidence (0.8):
Inflation Impact Score = 0.9 × 0.8 = 0.72
```

---

### **6️⃣ Save Results in JSON Format**
Each article’s output will be stored as JSON:

```json
{
    "article_id": 1234,
    "sentiment": "positive inflation",
    "word_attributions": {
        "interest rate hike": 0.92,
        "supply chain disruptions": 0.85,
        "currency depreciation": 0.81
    },
    "most_influential_word": "interest rate hike",
    "inflation_impact_score": 0.94,
    "model_confidence": 0.92
}
```

---

## **⚡ Installation & Usage**
### **🛠️ Install Dependencies**
```bash
pip install -r requirements.txt
```

### **🚀 Run Sentiment Analysis**
```bash
python run_sentiment_analysis.py --input data/articles.csv --output results.json
```

---

## **📌 Folder Structure**
```plaintext
Inflation-Sentiment-Impact-Analysis
│── data/
│   ├── articles.csv        # Input dataset
│── models/
│   ├── model_weights/      # (Optional) Model weights for FinGPT
│── outputs/
│   ├── results.json        # Processed output
│── scripts/
│   ├── run_sentiment_analysis.py  # Main execution script
│── README.md
│── requirements.txt
```

---

## **📝 Future Improvements**
- 🔹 **Fine-tune `FinGPT-Llama3-8B LoRA` further** on inflation-specific datasets.
- 🔹 **Add visualizations** for word attributions.
- 🔹 **Improve confidence calibration** using **Bayesian techniques**.

---

## **📌 Summary**
This project classifies financial news articles for **inflation sentiment**, extracts **key words influencing inflation predictions**, and computes an **Overall Inflation Impact Score (0-1)** using **FinGPT-Llama3-8B LoRA**.  
The output is stored in JSON format for **further financial analysis**.
