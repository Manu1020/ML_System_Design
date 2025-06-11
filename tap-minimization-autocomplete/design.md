# Tap Minimization via Autocomplete Suggestions

Design a system to reduce the number of taps a user needs to complete a message in a mobile chat application. The system offers real-time next-word or phrase suggestions based on user input, with high responsiveness and optional personalization.

---

## 1. Problem Formulation

### Use Case

Real-time autocomplete suggestions in mobile messaging apps to reduce typing effort.

### Business Objective

- Improve user experience by minimizing effort  
- Increase retention and engagement through intelligent assist

### Key Constraints

- Low latency: P99 < 100ms  
- Millions of users globally (real-time scale)  
- Must handle partial inputs, multilingual users, mobile constraints

### Data Availability

- User input logs (from users who have explicitly consented to data usage for improving suggestions)  
- Session-level context (time, recipient, history)  
- Feedback logs (accepted/ignored suggestions)

### ML Formulation

- **Task**: Next-token prediction  
- **Type**: Pointwise ranking/classification  
- **Input**: `<context tokens, user features>`  
- **Output**: ranked list of likely next tokens

---

## 2. Metrics

### Offline

- **Token Recall@K**: Is the actual next token present in the top K suggestions?  
- **Token Sequence Recall**: Fraction of the full target phrase accurately completed by the system  
- **NDCG@K**: Measures ranking quality when using ranked multi-token outputs  

> ⚠️ **Note**: Precision@K is difficult to evaluate accurately because we only observe user acceptance of one suggestion; unrejected options may still be relevant.

### Online

- **Tap Reduction %**: Keystrokes saved vs full manual typing  
- **Typing Time**: Time from first key to message send  
- **Suggestion Acceptance Rate**: How often suggestions are accepted by users  
- **P99 Latency**: Inference latency threshold compliance

---

## 3. System Architecture (MVP)

### a. Client Side

- Keystroke capture  
- Latency watchdog for fallback  
- Display & acceptance logging

### b. Server Side Inference

- Receives partial tokens + context  
- Pulls user features from feature store  
- Calls next-token prediction model

### c. On-device Fallback

- Phrase lookup from user history  
- Local cache of popular phrases by region and language preferences (locale)  
- Triggered if model doesn’t respond within latency SLA

---

## 4. Data Preparation

### Sources

- Chat logs (with consent)  
- Device context (time, locale)  
- Prior user selections / typing behavior

### Labeling

- For each user input prefix, the next token typed is treated as the label

### Augmentations

- Add noisy versions of partial messages (e.g., typos, abbreviations)

---

## 5. Feature Engineering

### User Features

- Active time of day  
- Device locale  
- Preferred token patterns (learned via embedding)

### Context Features

- Partial input (tokenized)  
- Message recipient type (personal, group)  
- Session language

### Feature Processing

- Bucketized and embedded as needed  
- On-device fallback uses raw heuristics

---

## 6. Model Development

### Model Type

- Causal Transformer (e.g., GPT-style)  
- Quantized or distilled for latency

### Training Objective

- Next-token prediction

### Label

- Next token after partial sequence

### Serving

- Batched inference with optional dynamic cache  
- P99 latency target: <100ms  
- Deployable in future to on-device setting

---

## 7. Prediction and Ranking

- Compute token probabilities from Transformer head  
- Apply optional diversity or repetition penalties  
- Return top-K sorted suggestions

---

## 8. Feedback and Deployment

- UUID-based session mapping for delayed logging  
- A/B testing of model variants  
- Periodic offline retraining based on new logs  
- Monitoring dashboard: latency, acceptance rate, fallbacks triggered
