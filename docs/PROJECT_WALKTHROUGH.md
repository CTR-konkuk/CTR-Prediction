
# 📘 CTR 예측 프로젝트 워크스루 (for Data Analysis Beginners)

이 문서는 **광고 클릭률(CTR) 예측 프로젝트**의 전체 흐름을 **수도코드(Pseudo-code)** 형식으로 정리한 가이드입니다.  

---

## 🏗️ 전체 파이프라인 요약
1. **데이터 준비**: 대용량 데이터를 다루기 쉽게 작게 자르기 (Sampling)
2. **데이터 분석**: 데이터의 생김새와 패턴 파악하기 (EDA)
3. **피처 엔지니어링**: 모델이 학습하기 좋은 형태로 변수 가공하기 (Feature Eng.)
4. **모델링**: 서로 다른 장점을 가진 3가지 모델 학습하기 (CatBoost, XGBoost, FiBiNet)
5. **앙상블**: 모델들의 예측값을 섞어서 성능 높이기 (Ensemble)

---

## Phase 1. 데이터 준비 (Data Preparation)
> **목표**: 220만 개가 넘는 대용량 원본 데이터를 10%로 줄여서 가볍게 분석할 준비를 합니다.

```python
# 01_Data_Sampling.ipynb

FUNCTION 데이터_샘플링():
    원본_데이터 = Load("train.parquet")
    
    # 전체를 다 쓰면 너무 느리니까 1/10만 랜덤하게 뽑자!
    샘플_데이터 = 원본_데이터.sample(frac=0.1, random_state=42)
    
    Save(샘플_데이터, "train_sample_10pct.parquet")
    print("분석용 가벼운 데이터 준비 완료!")
```

---

## Phase 2. 탐색적 데이터 분석 (EDA & Pre-calculation)
> **목표**: 데이터의 분포를 눈으로 확인하고, 모델에 힌트로 줄 **'클릭 확률 사전'**을 미리 만듭니다.

```python
# 02_EDA.ipynb

FUNCTION 데이터_탐색_및_통계추출():
    데이터 = Load("train_sample_10pct.parquet")
    
    # Q. 클릭한 사람(1)과 안 한 사람(0) 비율은?
    Plot_BarChart(데이터['clicked'])  # 결과: 클릭한 사람이 1.8% 밖에 안됨 (클래스 불균형 심각!)
    
    # Q. 과거 행동 패턴('seq')에서 힌트 찾기
    # 'seq' 컬럼: 유저가 이전에 봤던 아이템 번호들 (예: "123, 456, 789")
    
    클릭_확률_사전 = {} # 빈 노트 준비
    
    FOR 각_행 IN 데이터:
        시퀀스 = 행['seq']
        클릭여부 = 행['clicked']
        
        FOR 아이템_번호 IN 시퀀스:
            # 이 아이템이 등장했을 때 유저가 결국 클릭을 했나? 카운팅
            클릭_확률_사전[아이템_번호].등장횟수 += 1
            IF 클릭여부 == 1:
                클릭_확률_사전[아이템_번호].클릭횟수 += 1
                
    # 계산: 아이템별 클릭 확률 = (클릭횟수 / 등장횟수)
    # 이 정보는 나중에 모델에게 "이 아이템은 평소에 인기가 많아!"라고 알려주는 힌트가 됨
    Save(클릭_확률_사전, "high_click_numbers.xlsx")
```

---

## Phase 3. 모델링 (Modeling Strategy)
> **전략**: **"다양성(Diversity)"**이 핵심입니다. 서로 다른 성격의 모델 3개를 각각 학습시킵니다.
> 모든 모델은 공통적으로 **3가지 시드(Seed)**로 반복 학습하여 운에 의한 성과를 배제합니다.

### 공통 전처리 함수 (Common Preprocessing)
모든 모델이 공통으로 사용하는 데이터를 다듬는 과정입니다.

```python
FUNCTION 피처_엔지니어링(데이터):
    # 1. 시퀀스 변수 (Sequence Features)
    # 아까 만든 '클릭_확률_사전'을 보고, 이 유저가 본 아이템들의 평균 인기도를 계산
    데이터['평균_클릭_확률'] = Mean(Map(데이터['seq'], 클릭_확률_사전))
    데이터['시퀀스_길이'] = Length(데이터['seq'])
    
    # 2. 상호작용 변수 (Interaction Features)
    # 두 변수를 합쳐서 새로운 정보를 만듦 (예: "20대" + "남성" -> "20대_남성")
    데이터['성별_연령대'] = 데이터['gender'] + "_" + 데이터['age_group']
    
    # 3. 통계형 변수 (Group Statistics)
    # 특정 광고지면(inventory_id) 별로 평균적으로 얼마나 클릭됐는지 통계 내기
    데이터['지면별_평균_히스토리'] = GroupBy('inventory_id').Mean('history_a_1')
    
    RETURN 가공된_데이터
```

---

### Model A. CatBoost (범주형 데이터 강자)
```python
# 03_Model_CatBoost.ipynb

ALGORITHM 캣부스트_학습():
    SEED_LIST = [42, 106, 1031] # 시드 3개 사용 (안정성 확보)
    예측결과_리스트 = []
    
    FOR seed IN SEED_LIST:
        # 데이터 불균형 해결 (Down-Sampling)
        # 클릭 안 한 데이터(0)가 너무 많으니, 클릭 한 데이터(1) 비율만큼만 랜덤하게 뽑아서 1:1로 맞춤
        학습_데이터 = DownSample(데이터, ratio=1:1, seed=seed)
        
        # 학습
        모델 = CatBoostClassifier(random_state=seed)
        모델.fit(학습_데이터)
        
        # 예측 및 저장
        결과 = 모델.predict(테스트_데이터)
        예측결과_리스트.append(결과)
        
    # Soft Voting (3개 결과의 평균 내기)
    최종_CatBoost_점수 = Mean(예측결과_리스트)
    Save(최종_CatBoost_점수, "03_CatBoost_SoftVoting.csv")
```

### Model B. XGBoost (전통의 강호, 속도/성능 밸런스)
```python
# 04_Model_XGBoost.ipynb

ALGORITHM XGBoost_학습():
    # CatBoost와 비슷하지만, 계산 방식이 다름 (Histogram 기반 알고리즘 사용)
    # 피처 엔지니어링 후, 결측치를 좀 더 세밀하게 처리
    
    FOR seed IN [42, 106, 1031]:
        학습_데이터 = DownSample(데이터, ratio=1:1)
        
        모델 = XGBClassifier(tree_method='hist') # 속도 최적화 모드
        모델.fit(학습_데이터)
        
        결과 = 모델.predict(테스트_데이터)
        저장(결과)
        
    최종_XGBoost_점수 = Mean(3개_시드_결과)
    Save(최종_XGBoost_점수, "04_XGBoost_SoftVoting.csv")
```

### Model C. FiBiNet (딥러닝, 미세한 패턴 발견)
```python
# 05_Model_FiBiNet.ipynb

ALGORITHM FiBiNet_학습():
    # 트리가 못 보는 복잡한 관계를 딥러닝으로 찾는다!
    # 핵심 기술: SENet (변수 중요도 자동 조절), Bilinear Interaction (변수 간 곱하기)
    
    FOR seed IN [42, 106, 1031]:
        # 딥러닝은 데이터가 좀 더 많아도 됨 (부정 데이터 비율 1:5로 설정)
        학습_데이터 = DownSample(데이터, ratio=1:5)
        
        # 신경망 모델 정의
        모델 = NeuralNetwork(Embedding_Layer + SENet_Layer + Deep_Layer)
        
        # 학습 (Epochs)
        For epoch in 1..N:
            모델.train(학습_데이터)
            
        결과 = 모델.predict(테스트_데이터)
        저장(결과)
        
    최종_FiBiNet_점수 = Mean(3개_시드_결과)
    Save(최종_FiBiNet_점수, "05_FiBiNet_SoftVoting.csv")
```

---

## Phase 4. 최종 앙상블 (Final Ensemble)
> **전략**: 각 모델이 잘 맞추는 영역이 다릅니다. 이들을 적절한 비율로 섞으면(가중 평균) 혼자일 때보다 더 강력해집니다.

```python
# 06_Final_Ensemble.ipynb

FUNCTION 최종_제출파일_생성():
    # 3모델 불러오기
    CatBoost_점수 = Load("03_CatBoost_SoftVoting.csv")
    XGBoost_점수  = Load("04_XGBoost_SoftVoting.csv")
    FiBiNet_점수  = Load("05_FiBiNet_SoftVoting.csv")
    
    # 🌟 황금 비율로 섞기 (가중 평균)
    # CatBoost가 성능이 제일 좋아서 가중치를 많이 줌 (40%)
    # XGBoost (35%), FiBiNet (25%)
    
    최종_점수 = (CatBoost_점수 * 0.40) + 
                (XGBoost_점수  * 0.35) + 
                (FiBiNet_점수  * 0.25)
                
    # 최종 제출
    Save(최종_점수, "06_Final_Submission.csv")
    print("프로젝트 완료! 제출 준비 끝! 🚀")
```
