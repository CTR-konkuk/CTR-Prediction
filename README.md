
# 🎯 Toss External AD CTR Prediction (Team Project)

> **광고 클릭률(CTR) 예측을 위한 머신러닝/딥러닝 앙상블 프로젝트**  
> Konkuk Univ. Graduate School Data Mining Project

![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)
![Status](https://img.shields.io/badge/Status-Active-green.svg)

## 👥 Team Members (Contributors)
| 이름 | 역할 | GitHub ID |
| :--- | :--- | :--- |
| **김승환** | Data Sampling & EDA | `@Seunghwan` |
| **박근우** | Feature Engineering | `@Geunwoo` |
| **홍수호** | CatBoost & Modeling | `@Sooho` |
| **박상민** | XGBoost & Tuning, PM | `@Sangmin` |

---

## 📂 Repository Structure
```bash
├── 📁 docs/                 # 팀 협업 가이드 & 워크스루
│   ├── TEAM_GITHUB_GUIDE.md
│   └── PROJECT_WALKTHROUGH.md
├── 📁 models/               # 모델링 & 앙상블 노트북
│   ├── 03_Model_CatBoost.ipynb
│   ├── 04_Model_XGBoost.ipynb
│   ├── 05_Model_FiBiNet.ipynb
│   └── 06_Final_Ensemble.ipynb
├── 📁 rawdata/              # (로컬 전용) 데이터 경로
├── 01_Data_Sampling.ipynb   # 대용량 데이터 샘플링
├── 02_EDA.ipynb             # 탐색적 데이터 분석
├── requirements.txt         # 필요 라이브러리 목록
└── README.md                # Main Documentation
```

---

## 🚀 How to Run (Execution Flow)

이 프로젝트는 **순차적 실행**을 권장합니다. 모든 데이터 경로는 상대 경로(`../rawdata` 등)로 설정되어 있습니다.

### 1. 사전 준비 (Prerequisites)
```bash
# 필수 라이브러리 설치
pip install -r requirements.txt
```
* `rawdata` 폴더에 원본 데이터(`train.parquet`)가 있어야 합니다.

### 2. 실행 순서
1. **`01_Data_Sampling.ipynb`**: 대용량 데이터를 분석 가능한 크기로 샘플링합니다.
2. **`02_EDA.ipynb`**: 데이터 분포를 확인하고 `high_click_numbers.xlsx` (클릭 확률 정보)를 생성합니다.
3. **`models/03 ~ 05`**: 각 모델(CatBoost, XGBoost, FiBiNet)을 학습하고 개별 예측값을 생성합니다.
4. **`models/06_Final_Ensemble.ipynb`**: 개별 모델의 결과를 가중 평균하여 최종 `06_Final_Submission.csv`를 생성합니다.

---

## 📚 Documentation
팀원들을 위한 상세 가이드가 `docs/` 폴더에 준비되어 있습니다.
* **[GitHub 협업 가이드 (TEAM_GITHUB_GUIDE.md)](docs/TEAM_GITHUB_GUIDE.md)**: 깃허브 사용법 및 워크플로우
* **[프로젝트 워크스루 (PROJECT_WALKTHROUGH.md)](docs/PROJECT_WALKTHROUGH.md)**: 전체 분석 로직 수도코드 설명

---

## 🛠️ Tech Stack
* **Language**: Python 3.9
* **Libraries**: Pandas, Scikit-learn, XGBoost, CatBoost, PyTorch(FiBiNet), Optuna
* **Collaboration**: GitHub