# Event-Driven Song Recommendation Flow
Design an event-driven system that recommends music to users in real time, with the ability to detect disengagement early (e.g., skips, pauses, search, or exit) and trigger relevant recommendations before users explicitly request them.

## 📄 Table of Contents

1. [User Interaction Flow](##user-interaction-flow)
2. [High-Level System Architecture](##high-level-system-architecture-textual-diagram)
3. [Disengagement Prediction Model (Deep Dive)](##disengagement-prediction-model-deep-dive)
4. [Recommendation Model (Two-Tower; Deep Dive)](##recommendation-model-two-tower-deep-dive)

---

## User Interaction Flow

### 1. User Listens

#### Continues Listening (Happy Path)
- Maintain current flow
    - No intervention required
    - Monitor engagement passively

#### Disengages (Unhappy Path)
- Prediction model detects intent shift
    - Indicators: sudden stop, skips, lack of interaction
- Recommender is triggered
    - Based on predicted disengagement
    - Personalized suggestions pushed in real-time
- Outcome:
    - User re-engages through recommendation
    - OR user performs a manual search
 
## High-Level System Architecture

| Component                   | Purpose                                 |
|----------------------------|-----------------------------------------|
| Event Stream               | Captures play, pause, skip, search logs |
| Session Tracker            | Maintains short-term behavior history   |
| Disengagement Predictor    | Flags intent shift                      |
| Recommender Engine         | Suggests songs based on dynamic context |
| Real-Time Serving          | Delivers low-latency responses          |
| Feedback Collector         | Captures interaction signals            |



### 1. User Interaction Stream
- **Raw events:** play, skip, pause, search, exit

### 2. Event Ingestion & Logging
- Collects and buffers events for:
  - **Online processing** (real-time prediction)
  - **Offline training** (batch jobs)

### 3. Session State Tracker
- Maintains short-term window (e.g., last 30 min)
- Tracks engagement patterns:
  - Skips
  - Dwell time
  - Pause/play behavior

### 4. Disengagement Prediction Model
- **Input:** Session state + user profile + song metadata
- **Output:** Disengagement score (0 to 1)
- Uses threshold to determine whether to trigger recommendations

### 5. Recommendation Engine

#### a. Candidate Generation (Two-Tower Retrieval Model)
- Static **song embeddings**
- Dynamic **user embeddings** (based on context and history)
- Uses **ANN index** for fast retrieval

#### b. Ranking Model *(optional)*
- Re-ranks top-K candidates using recent context

### 6. Post-Processing Layer
- Applies filters for:
  - **Diversity**
  - **Freshness**
  - **Explicit content**

### 7. Real-Time Serving Layer
- Returns recommendations with **<200ms latency**
- Includes:
  - Caching
  - Fallback strategies

### 8. Feedback Collector
- Tracks user actions on recommendations:
  - Listens
  - Skips
  - Searches
- Logs positive and negative feedback

### 9. Training and Monitoring Pipelines
- **Offline training jobs:**
  - Retrain prediction and recommendation models regularly (e.g., daily)
- **Online monitoring:**
  - Engagement drift
  - Score distribution
  - Model latency
## Disengagement Prediction Model (Deep Dive)
> "I included a prediction model to proactively detect early signs of disengagement—like skips or hesitations—so we can trigger timely, relevant recommendations before the user manually searches or exits."

### Labeling Strategy
- **Positive label:** Exit, search, or skip burst within the next 30 minutes  
- **Negative label:** Continuous play

### Features
- **Session behavior:** Sequence of (song ID, skip flag, dwell time)
- **User profile:** Past skips, preferred genres/artists
- **Song metadata:** Genre, artist, language, popularity

### Architecture
- Transformer encoder for session sequence
- Concatenate with user profile vector
- MLP + sigmoid head for binary classification

### Training
- **Loss:** Binary cross-entropy
- **Class balancing:** Adjusted for rare events like skip/search
- **Frequency:** Daily or every few days

### Evaluation
- **Offline metrics:** AUC, Precision@K
- **Online metrics:** Precision of trigger, false positive rate


## Recommendation Model (Two-Tower; Deep Dive)
> "I chose a two-tower architecture because it generalizes matrix factorization by allowing us to incorporate dynamic context, user history, and metadata, while still supporting scalable, real-time retrieval."

### Input Format
- **Positive pairs:** (User, song) with engagement

### Features
#### User Tower
- Contextual features (e.g., time of day, device)
- Session embedding (Transformer over past songs)

#### Song Tower
- Audio features (e.g., CNN-extracted), genre, popularity

### Loss Function
- **Contrastive loss**
  - Maximize similarity with positives
  - Minimize similarity with in-batch negatives

### Training
- In-batch negative sampling
- Separate training for user and song towers
- At inference:
  - Serve static **item embeddings**
  - Serve dynamic **user embeddings**

### Evaluation
- **Offline:** Recall@K, NDCG, MRR
- **Online:** CTR, dwell time, skip rate, A/B test results





