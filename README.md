# Basic NLP Practice with the Bible Text

The model is trained with the Bible text(Genesis and Psalms)
This practice was conducted to raise the understanding of NLP processing steps,and the score.

### Technical Review

1. 모델 점수와 상관없이 낱개의 어휘로는 책의 예측이 불가능하다.   
    **Limitations of Unigram Prediction**: Regardless of the model's F1-score, predicting the source book based on isolated tokens is nearly impossible. The contextual overlap in biblical vocabulary makes individual words weak predictors.

2. SVC가 RandomForest에 비해 높은 점수를 보였지만, 실제로는 이게 의미가 있을 것 같지는 않다. 결국 1번 이슈로 인해 거의 완성된 문장이 아니면 맞출 수가 없으며, 이러한 구조는 기초적인 검색 엔진 코드만으로도 구현 가능하다.   
    **Model Evaluation (LinearSVC vs. RandomForest)**: While LinearSVC showed superior performance compared to RandomForest, its practical significance is debatable. The model heavily relies on near-complete sentence structures, a functionality that could arguably be replicated by a basic keyword-based search engine.

3. ngram_range의 최솟값을 2 이상으로 하면 모델 점수가 급격히 낮아진다. 왜일까?   
    **N-gram Sensitivity & Data Sparsity**: I observed a sharp decline in performance when the minimum `ngram_range` was set to 2 or higher. This is a classic case of **Data Sparsity**—specific bi-gram or tri-gram sequences in the training set are less likely to appear identically in the test set compared to flexible unigrams.

4. stratify는 정말 중요하다. 알면서도 계속 까먹는다...   
    **Importance of Stratification**: The use of `stratify=y` during the train-test split proved essential. In specialized datasets like the Bible, maintaining a consistent class distribution is the only way to ensure the model's reliability.

5. str 데이터 전처리의 경우 is_alpha, is_stop보다 품사 태깅이 장기적으로 봤을 때(다른 성경 텍스트를 추가 학습하는 경우) 정확도를 높이는데 바람직할 듯 하다.   
    **Advanced Preprocessing (POS Tagging over Regex)**: Moving forward, filtering by Part-of-Speech (POS) tags is more robust than simple `is_alpha` or `is_stop` methods. It captures the syntactic backbone of the text, which will be vital when expanding the training set to other books of the Bible.

### Future Roadmap

1. 딥 러닝으로 학습하기   
    **Transition to Deep Learning**: Implement Transformer-based models (e.g., BERT) to move beyond frequency-based statistics and capture semantic context.

2. 공백이나 없는 것으로 판단되는 단어 예외처리   
    **Robust Exception Handling**: Develop logic to handle empty strings, out-of-vocabulary (OOV) tokens, and noise-only inputs after preprocessing.

3. 성경 텍스트 구분을 range가 아닌 다른 방식으로 진행하여 사후 텍스트 추가 학습을 용이하게 하기   
    **Dynamic Data Loading**: Implement a more scalable labeling system (e.g., using Regex to parse chapter/verse patterns) instead of hard-coded index slicing, making it easier to add new biblical texts.

4. 사실상 성경 검색 엔진을 더 많은 자원으로 사용하는 방식으로 설계하는 꼴이 되지만... 그래도 연습용으로는 좋을 듯 하다.   
    **Hybrid Search Architecture**: Explore a design that combines the efficiency of a search engine (Retrieval) with the precision of a classifier, moving toward a "Search & Classify" hybrid system.