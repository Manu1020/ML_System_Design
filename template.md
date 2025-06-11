# ML System Design Flow Template

Use this template to structure responses in interviews or projects involving applied machine learning system design. Adapt section depth depending on the complexity and time available.

---

## 1. Problem Formulation

* **Use Case**: What is the product or problem you're solving?
* **Business Objective**: What outcome are we optimizing (e.g., engagement, conversion, cost)?
* **Key Constraints**: Latency, scale, cost, privacy, etc.
* **Clarifying Assumptions**: Personalization? Language? Geography? Device type?

## 2. Success Metrics

### Offline Metrics

* Recall\@K / Precision\@K / NDCG / Sequence Accuracy
* Log-likelihood / token-level loss

### Online Metrics

* Acceptance rate / CTR / Conversion
* Latency (P95/P99), Tap reduction, Session engagement

## 3. System Architecture (High-Level)

* **Client-side logic**: Input capture, fallback logic
* **Inference API**: What is called, what does it return?
* **Fallback/Heuristics**: On-device logic, cache, rule-based logic

## 4. Data and Labeling

* **Data Sources**: User logs, content data, metadata, feedback
* **Label Strategy**: Clicks, purchases, next-token, engagement window
* **Augmentation**: Noisy data handling, partial examples, simulation

## 5. Features

* **User Features**: Profile, history, segments
* **Item/Context Features**: Text, metadata, timestamps
* **Interaction Features**: User-item or user-query interactions

## 6. Model Design

* **Objective**: Classification / Ranking / Generation
* **Model Type**: GBDT, Two-tower, Transformer, LLM
* **Serving Format**: Quantized? On-device? Real-time?
* **Training Considerations**: Loss function, imbalance, retraining cycle

## 7. Prediction and Ranking

* How are scores computed?
* Do you re-rank or diversify?
* Caching, thresholding, result filtering

## 8. Feedback, Monitoring, and Deployment

* **User Feedback Logging**: Accept/ignore, true vs false positives
* **Monitoring**: Accuracy drift, latency, system health
* **Deployment Strategy**: A/B testing, rollback, retraining triggers

---

Use this as a thinking scaffold — not all sections are needed every time, but practicing this structure helps you speak and write like a system thinker.
