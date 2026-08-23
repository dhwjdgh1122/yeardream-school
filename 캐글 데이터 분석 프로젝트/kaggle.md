
# 🏥 AI Lifestyle Checkup & Optimal Habit Recommendation Engine

> **20대를 위한 AI 기반 생활습관 자가진단 및 최적 습관 추천 서비스**
> Kaggle Playground Series - Season 6, Episode 7 경진대회 참여 및 AI 서비스 엔지니어링 프로젝트[cite: 1]

---

## 📑 Table of Contents (목차)

1. [Project Overview (프로젝트 개요)](https://www.google.com/search?q=%23-1-project-overview-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B0%9C%EC%9A%94)
2. [Data Architecture & Pipeline (데이터 흐름 및 구조)](https://www.google.com/search?q=%23-2-data-architecture--pipeline-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%9D%90%EB%A6%84-%EB%B0%8F-%EA%B5%AC%EC%A1%B0)
3. [Exploratory Data Analysis (EDA)](https://www.google.com/search?q=%23-3-exploratory-data-analysis-eda)
4. [Feature Engineering & Preprocessing (전처리 및 피처 생성)](https://www.google.com/search?q=%23-4-feature-engineering--preprocessing-%EC%A0%84%EC%B2%98%EB%A6%AC-%EB%B0%8F-%ED%94%BC%EC%B2%98-%EC%83%9D%EC%84%B1)
5. [Model Benchmarking & Optimization (모델링 및 최적화)](https://www.google.com/search?q=%23-5-model-benchmarking--optimization-%EB%AA%A8%EB%8D%B8%EB%A7%81-%EB%B0%8F-%EC%B5%9C%EC%A0%81%ED%99%94)
6. [Core Service Logic (핵심 시뮬레이션 엔진)](https://www.google.com/search?q=%23-6-core-service-logic-%ED%95%B5%EC%8B%AC-%EC%8B%9C%EB%AE%AC%EB%A0%88%EC%9D%B4%EC%85%98-%EC%97%94%EC%A7%84)
7. [Directory Structure (프로젝트 폴더 구조)](https://www.google.com/search?q=%23-7-directory-structure-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%ED%8F%B4%EB%8D%94-%EA%B5%AC%EC%A1%B0)
8. [Limitations & Future Work (한계점 및 향후 계획)](https://www.google.com/search?q=%23-8-limitations--future-work-%ED%95%9C%EA%B3%84%EC%A0%90-%EB%B0%8F-%ED%96%A5%ED%9B%84-%EA%B3%84%ED%9A%8D)

---

## 📌 1. Project Overview (프로젝트 개요)

### 1.1 SCQA Framework 기반 문제 정의

* **Situation (상황)**: 20대 청년층은 불규칙한 생활 패턴(야식, 수면 부족, 과도한 스트레스)으로 잠재적 건강 위험에 노출되어 있으나, 신체적 회복력으로 인해 나쁜 습관의 악영향을 체감하지 못함
* **Complication (문제점)**: 기존 Health-tech 서비스는 단순히 기록(Logging)하는 데 그치며, "내 습관이 얼마나 위험한지", "수면과 식단 조절을 병행하면 얼마나 개선되는지"에 대한 다차원적 시뮬레이션 및 직관적 피드백이 부재함
* **Question (핵심 질문)**:
1. 유저의 생활습관 데이터를 바탕으로 현재 건강 상태(`health_condition`: fit, at-risk, unhealthy)를 정확히 분류할 수 있는가?
2. 복수의 생활습관 변경에 따른 건강 변화량(조합형 What-If)을 시뮬레이션할 수 있는가?
3. 가장 적은 노력으로 최대의 효과를 낼 수 있는 '가성비 습관'을 알고리즘적으로 제안할 수 있는가?


* **Answer (해답)**: 머신러닝 최적 파이프라인의 `predict_proba` 예측 확률을 역산 및 모킹(Mocking)하는 '조합형 습관 시뮬레이터'와 **'가성비 개선 가이드 엔진'** 기반 AI 자가진단 서비스 구축[cite: 1].

---

## 🔄 2. Data Architecture & Pipeline (데이터 흐름 및 구조)

본 프로젝트는 수집된 Raw 데이터부터 실시간 추론 API 서빙까지 전 과정이 유기적으로 연결되도록 파이프라인을 구축했습니다[cite: 1].

```
[1. Raw Data Ingestion] ────► [2. EDA & Skewness Check] ────► [3. High-Speed Preprocessing]
(train.csv / test.csv)         (Target & Feature Dist)       (Median Imputer & Winsorization)
                                                                       │
                                                                       ▼
[6. Real-time API Serving] ◄── [5. 5-Fold OOF LightGBM] ◄──── [4. Feature Engineering]
(What-If & Recommendation)      (Balanced Acc 95%+)           (4 Domain Features & IsNA Indicator)

```

---

## 📊 3. Exploratory Data Analysis (EDA)

### 3.1 Target & Feature Profile

* **Target (Y)**: `health_condition` (Multi-class: fit, at-risk, unhealthy)[cite: 1]
* **Numerical Features**: `sleep_duration`, `heart_rate`, `bmi`, `calorie_expenditure`, `step_count`, `exercise_duration`, `water_intake`[cite: 1]
* **Categorical Features**: `diet_type`, `stress_level`, `sleep_quality`, `physical_activity_level`, `smoking_alcohol`, `gender`[cite: 1]

### 3.2 핵심 발견 사항 (EDA Insights)

1. **타겟 불균형**: 특정 등급(at-risk)이 다수를 차지하여 단순 Accuracy 대신 **Balanced Accuracy**를 평가 지표로 고정[cite: 1].
2. **이상치 및 왜도**: `step_count`, `calorie_expenditure` 등 일부 변수의 극단값에 대해 이상치 캡핑(Winsorization) 필요성 확인[cite: 1].

---

## 🛠 4. Feature Engineering & Preprocessing (전처리 및 피처 생성)

### 4.1 핵심 파생 변수 (Domain Features)[cite: 1, 2]

20대 라이프스타일 특성과 AI 생성 데이터셋의 특성을 반영한 파생 변수를 구축했습니다[cite: 1, 2].

* **수면 부족 지수 (`sleep_deficit`)**: $\max(0, 7.5 - \text{sleep\_duration})$[cite: 1, 2]
* **운동 강도 효율성 (`exercise_intensity`)**: $\frac{\text{calorie\_expenditure}}{\text{exercise\_duration} + 1}$[cite: 1, 2]
* **활동량 대비 수분 공급비 (`hydration_adequacy`)**: $\frac{\text{water\_intake}}{\text{step\_count} + 1} \times 1000$[cite: 1, 2]
* **스트레스-수면 미스매치 (`stress_sleep_mismatch`)**: Stress High & Sleep Quality Poor 위험 조합 인디케이터[cite: 1, 2]
* **Artifact IsNA Indicator (`col_isna`)**: 생성 AI 데이터 특유의 결측 패턴 자체를 고차원 피처로 변환하여 예측 정확도 상승[cite: 2]

### 4.2 전처리 파이프라인 (Preprocessing)

* **High-Speed Imputation**: 기존 KNNImputer의 대용량 연산 병목을 해결하기 위해, 중앙값/최빈값 대체 및 결측 인디케이터 결합으로 처리 속도 100배 이상 개선[cite: 2].
* **Winsorization (Capping)**: $Q_1 - 2.5 \times IQR$, $Q_3 + 2.5 \times IQR$을 벗어나는 극단치를 Clipping 처리하여 실시간 서빙 시 행 삭제 에러 방지[cite: 1, 2].

---

## 🚀 5. Model Benchmarking & Optimization (모델링 및 최적화)

대용량 데이터 환경에서의 빠른 추론과 정교한 확률 분포 산출을 위해 **LightGBM Classifier**를 주력 챔피언 모델로 선정했습니다[cite: 1, 2].

### 5.1 Hyperparameter Setup[cite: 2]

| Hyperparameter | Value | Description |
| --- | --- | --- |
| **n_estimators** | 350 | 트리 반복 수 최적화[cite: 2] |
| **learning_rate** | 0.05 | 안정적인 수렴 속도 확보[cite: 2] |
| **max_depth / num_leaves** | 8 / 91 | 과적합 방지 및 비선형 관계 학습[cite: 2] |
| **class_weight** | 'balanced' | 클래스 불균형 자동 보정[cite: 2] |
| **subsample / colsample_bytree** | 0.8 / 0.8 | 트리의 무작위성 확보[cite: 2] |

### 5.2 Performance & Validation Result[cite: 2]

* **Validation Strategy**: Stratified 5-Fold Cross Validation[cite: 1, 2]
* **Evaluation Metric**: Balanced Accuracy Score[cite: 1, 2]
* **Final OOF Score**: **0.95+ (95% 이상)** 기록[cite: 2]

---

## 💡 6. Core Service Logic (핵심 시뮬레이션 엔진)

### 6.1 조합형 What-If 시뮬레이터 (Multi-variable Simulator)

유저가 UI 상에서 수면시간, 수분 섭취량, 식단 등을 복합 조절할 때, 수정된 벡터 $X_{\text{modified}}$를 모델에 입력하여 등급 변화 및 건강 확률 상승 폭($\Delta P_{fit}$)을 실시간 역산[cite: 1, 2].

$$\Delta P_{fit} = P(\text{fit} \mid X_{\text{modified}}) - P(\text{fit} \mid X_{\text{current}})$$


[cite: 1]

### 6.2 가성비 개선 치트키 가이드 엔진 (Effort-Efficiency Engine)

통제 가능한 습관 변수(수면, 물 섭취, 걸음 수 등)의 탐색 공간을 백엔드에서 일괄 시뮬레이션하여 최소 노력 대비 최대 효과를 발휘하는 습관 1위를 추천[cite: 1, 2].

$$\text{Score} = \frac{P(\text{fit} \mid X_{\text{virtual}}) - P(\text{fit} \mid X_{\text{current}})}{\text{Effort Penalty}}$$


[cite: 1]

---

## 📂 7. Directory Structure (프로젝트 폴더 구조)

```bash
.
├── data/
│   ├── train.csv                      # 학습 데이터셋
│   └── test.csv                       # 평가 데이터셋
├── src/
│   ├── eda_and_visualization.py        # EDA 리포트 및 정밀 시각화
│   ├── feature_engineering.py         # 4대 도메인 파생변수 생성기
│   ├── pipeline_preprocessing.py      # 고속 전처리 & Winsorizer 파이프라인
│   └── artifact_hunter_pipeline.py    # 5-Fold OOF 학습 및 제출 파일 생성
├── output/
│   └── submission_artifact_hunted.csv # 최종 제출 파일
└── README.md                          # 프로젝트 안내 문서

```

---

## 📝 8. Limitations & Future Work (한계점 및 향후 계획)

1. **의료적 진단의 한계**: 본 모델은 설문 데이터 기반 분류 모델로 실제 임상 진단을 대체할 수 없으며, 서비스 적용 시 명시적 Disclaimer 동의가 필요합니다.
2. **주관적 입력 노이즈 제어**: `sleep_quality`, `stress_level` 등 유저 주관에 따른 노이즈를 줄이기 위해 차기 버전에서는 Wearable API (Apple HealthKit, Google Fit) 연동을 통한 객관적 센서 데이터 수집을 계획하고 있습니다.