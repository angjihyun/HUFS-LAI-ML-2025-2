# 5th Assignment: Model Training and Evaluation  
**Project: 일정 문장 기반 카테고리 및 우선순위 자동 분류 시스템**

이 프로젝트는 일정 문장을 입력하면 해당 일정의 카테고리(과제, 시험, 발표, 기타)와 우선순위(High, Medium, Low)를 자동으로 분류하는 모델을 구축하는 것을 목표로 한다. 텍스트 정보뿐만 아니라 남은 일수(days_left), 중요 키워드 포함 여부(contains_keyword)와 같은 수치형 특성을 함께 사용하여 예측 정확도를 향상시키고자 하였다.

---

## 1. 모델 아키텍처

Assignment 3에서는 Logistic Regression과 RandomForest 모델을 모두 고려하였으나, 데이터의 양이 많지 않고 텍스트 기반 분류 문제라는 점을 고려해 최종적으로 **TF-IDF + Logistic Regression** 조합을 사용하였다.

### Input Features
- **text**: 일정 문장  
  - TF-IDF 벡터화 (ngram_range = 1–2, max_features = 1500)
- **days_left**: 마감일까지 남은 일수  
- **contains_keyword**: “제출”, “발표”, “시험”, “대비” 등의 핵심 단어 포함 여부(0/1)

텍스트 특징(TF-IDF 1500차원)과 numeric feature(2개)를 hstack으로 결합하여 모델 입력으로 사용하였다.

### Category Model
- Logistic Regression  
- 출력: 과제 / 시험 / 발표 / 기타  
- class_weight='balanced' 적용

### Priority Model
- Logistic Regression  
- 출력: High / Medium / Low  
- class_weight='balanced' 적용

### Vectorizer 및 Scaler
- TF-IDF Vectorizer
- StandardScaler

---

## 2. Training

전체 50개의 일정 데이터를 다음과 같이 분리하였다.

- Train: 70%  
- Validation: 15%  
- Test: 15%  

카테고리 모델과 우선순위 모델을 각각 학습하였고, Validation 성능(Macro F1)을 기준으로 최종 모델을 선정하였다.

RandomForestClassifier도 실험하였으나 Logistic Regression이 더 안정적인 Validation 성능을 보였다.

모델 및 전처리 도구는 다음 파일로 저장하였다.
- `category_model.pkl`  
- `priority_model.pkl`  
- `tfidf_vectorizer.pkl`  
- `feature_scaler.pkl`

---

## 3. Evaluation

Test set은 전체 데이터의 15%를 사용하였다.  
문제 특성상 **Macro F1**과 **분류 보고서(classification_report)**를 중점적으로 분석하였다.

### Category 모델 성능
- Precision, Recall, F1-score(클래스별) 확인  
- 발표/과제처럼 문장 구조가 비슷한 일정에서 일부 오분류가 발생  

### Priority 모델 성능
- High 클래스는 비교적 명확하게 예측됨  
- Medium과 Low 클래스에서 약간의 혼동이 존재  
- days_left와 contains_keyword가 높은 설명력을 가짐  

### 종합 평가 지표  
평가 결과는 다음과 같은 지표를 기반으로 정리하였다.  
(아래 수치는 evaluation.ipynb 실행 결과를 기반으로 기입)

- **Accuracy:** (evaluation 결과 입력)  
- **Macro Precision:** (evaluation 결과 입력)  
- **Macro Recall:** (evaluation 결과 입력)  
- **Macro F1:** (evaluation 결과 입력)  
- **Weighted F1:** (evaluation 결과 입력)

---

## 4. 특이사항

- **days_left**는 Priority 모델에서 매우 강력한 feature로 작동하였다. High 일정은 남은 일수가 짧고 Low는 여유가 많은 패턴이 뚜렷하게 나타났다.  
- **contains_keyword**는 단순한 일정 문장의 모호함을 보완하는 데 중요한 역할을 했다.  
- Category 모델보다 Priority 모델이 전반적으로 더 높은 Macro F1을 기록했는데, 이는 우선순위 라벨이 수치형 특성에 더 직접적으로 연결되기 때문으로 보인다.

---

## 5. 한계

- 데이터 수가 약 50개로 매우 적어 모델의 일반화 성능이 충분하지 않다.  
- TF-IDF는 단어 기반 특징이라 문맥 이해 능력이 부족하다.  
- contains_keyword는 단순 이진 변수라 정보량이 제한적이다.  
- 장기적으로는 KoBERT, Sentence-BERT 등 문장 임베딩 기반 모델로 확장할 경우 성능 향상이 가능하다.

---

## 6. 모델 가중치 저장 위치

모델 가중치는 Google Drive에 업로드하였다.

- `category_model.pkl`  
- `priority_model.pkl`  
- `tfidf_vectorizer.pkl`  
- `feature_scaler.pkl`  


