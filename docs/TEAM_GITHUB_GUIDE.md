# 🐙 우리 팀을 위한 GitHub 완벽 적응 가이드 (for CTR Project)

---

## 1. 우리가 왜 이걸 써야 해? (Why GitHub?)

코드를 USB나 카톡으로 주고받으면 이런 대참사가 일어납니다.
> *"야, `final_code_v2_진짜최종_수정.ipynb` 이거 네가 고친 거 맞아? 내 거랑 덮어씌워졌는데?"*

**GitHub**는 이를 방지하는 거대한 **'타임머신 + 클라우드'**입니다.

1. **누가, 언제, 무엇을** 고쳤는지 기록이 다 남습니다.
2. 실수하면 **과거로 되돌릴 수 있습니다.**
3. 서로 다른 파일을 작업하고 **자동으로 합쳐줍니다.**

---

## 2. 퀵 스타트: 우리들의 작업실 만들기

### Step 1. Organization 만들기 (팀장 수행)

개인 계정보다 '팀 계정(Organization)'을 만드는 게 깔끔합니다.

![image.png](attachment:31a494ae-f475-45e5-b008-8cfb4b1c5c73:image.png)

1. 회원가입 후 우측 상단 `+` 버튼 → `New organization`
2. Free Plan 선택 → 팀 이름(예: `Toss-CTR-Team`) 입력
3. 팀원 3명 아이디 검색해서 초대장 발송!

### Step 2. Repository(저장소) 생성

1. Organization 페이지에서 `New Repository` 클릭
2. 저장소 이름: `CTR-Prediction`
3. `Public` (공개) 혹은 `Private` (비공개) 선택
4. `Add a README file` 체크 → **Create repository**

---

## 3. 핵심 용어 사전 (비유로 이해하기)

| 용어 | 발음 | 의미 | 비유 (게임/RPG) |
| --- | --- | --- | --- |
| **Repo** | 리포 | 저장소 | 우리 팀의**공용 폴더** |
| **Remote** | 리모트 | 원격 저장소 | **깃허브 웹사이트**(서버) |
| **Local** | 로컬 | 내 컴퓨터 | **내 노트북** |
| **Commit** | 커밋 | 저장(기록) | **세이브 포인트**생성 (메모 필수) |
| **Push** | 푸시 | 업로드 | 서버에 세이브 파일**업로드** |
| **Pull** | 풀 | 다운로드 | 남들이 한 거**내려받기**(동기화) |
| **Branch** | 브랜치 | 가지치기 | **평행우주**만들기 (여기서 막 실험해도 원본은 안전함) |
| **Merge** | 머지 | 합치기 | 평행우주를 다시 본래 세계로**합침** |
| **PR** | 피알 | Pull Request | "나 다 했으니까, 내 코드 좀 합쳐줘!" (**결재 요청**) |

---

## 3-1. 파일 올리고 수정하기 (기초편)

"그래서 내 컴퓨터에 있는 파일을 깃허브에 어떻게 올려요?"

### 📂 새 파일 올리기 (Add)

내가 만든 `EDA_seunghwan.ipynb` 파일을 올리고 싶다면?

1. **파일 생성**: VS Code 탐색기에서 우클릭 → `새 파일` → 작업 후 저장.
2. **소스 제어 탭** (왼쪽 3번째 아이콘) 클릭.
3. `변경 사항` 목록에 내 파일이 `U` (Untracked)로 표시됩니다.
4. 파일 옆 `+` 버튼 누르기 (**장바구니 담기**) → `git add`와 같음.
5. 메시지 입력창에 "승환 EDA 파일 추가" 입력 후 `커밋(✓)` 클릭.
6. `변경 내용 동기화` 버튼 클릭 (**서버 업로드**).README 오타 수정"

```python
 git push origin [내브랜치명]
```

https://github.com/CTR-konkuk/CTR-Prediction

## 4. 실전! 협업 워크플로우 (이 순서만 지키자)

🚨 **절대 원칙**: `main` 브랜치에서 직접 작업하지 마세요!  
항상 **내 전용 브랜치**를 만들어서 작업하고 합쳐야 합니다.

### [1단계] 출근했다! (작업 시작 전)

남들이 밤사이 코드를 고쳐놨을 수 있습니다. 무조건 최신 버전부터 받으세요.

```bash
git checkout main      # 메인 브랜치로 이동
git pull origin main   # 최신 코드 당겨오기 (동기화)
```

### [2단계] 작업 시작 (브랜치 생성)

내가 작업할 '평행우주'를 만듭니다. 이름은 직관적으로 짓습니다.

```bash
# 형식: git checkout -b [브랜치이름]
git checkout -b feature/xgboost-tuning
```

*(이제 여기서 코드를 마음껏 수정하세요. 망쳐도 `main`은 안전합니다.)*

### [3단계] 저장하기 (Commit)

작업이 끝났으면 기록합니다.

```bash
git add .                          # 수정된 파일 전부 담기
git commit -m "XGBoost 파라미터 튜닝 완료"  # 메모 붙여서 세이브
```

### [4단계] 퇴근하기 (Push)

내 평행우주를 깃허브(Remote)에 올립니다.

```bash
# 형식: git push origin [내브랜치이름]
git push origin feature/xgboost-tuning
```

### [5단계] 결재 요청 (Pull Request) ⭐

이게 제일 중요합니다!

1. **GitHub 웹사이트**에 들어갑니다.
2. 노란 박스가 뜨면서 `Compare & pull request` 버튼이 보입니다. 클릭!
3. 제목과 내용을 적습니다. (예: "XGBoost 속도 10배 개선")
4. **Reviewers**에 팀원(승환, 근우 등)을 태그합니다.
5. `Create Pull Request` 클릭

### [6단계] 합치기 (Merge)

팀원들이 코드를 보고 승인하면 → `Merge` 버튼을 눌러 `main`에 합칩니다.

---

## 5. 상황극으로 보는 시뮬레이션

**상황**: 승환이가 `04_Model_XGBoost.ipynb`의 학습 횟수를 1000회에서 100회로 줄이려고 한다.

1. 승환: (터미널 켬) "일단 최신 버전 받자."
    - `git pull origin main`
2. 승환: "Branch를 따자. 이름은.. `fix/xgb-speed`로 해야지."
    - `git checkout -b fix/xgb-speed`
3. 승환: (VS Code에서 코드 수정 후 저장) "수정 끝!"
4. 승환: "깃허브에 올릴 준비."
    - `git add .`
    - `git commit -m "XGBoost 학습횟수 100회로 감소"`
    - `git push origin fix/xgb-speed`
5. 승환: (웹사이트 접속) "PR 날립니다~ 확인해주세요!"
6. **근우/수호**: (알림 옴) "어디 보자... 코드 문제없네. 승인(Approve)!"
7. **수호/팀장**: (웹사이트에서) `Merge Pull Request` 버튼 클릭! → **끝**

---

## 6. 자주 쓰는 명령어 요약표 (Cheat Sheet)

| 상황 | 명령어 |
| --- | --- |
| 처음 복제해올 때 | `git clone [주소]` |
| 현재 상태 확인 | `git status`(수시로 확인 추천) |
| 브랜치 목록 보기 | `git branch` |
| 브랜치 이동하기 | `git checkout [브랜치명]` |
| 브랜치 삭제하기 | `git branch -d [브랜치명]` |
| 변경사항 취소하기 | `git restore [파일명]`(조심해서 사용!) |

---

## 💡 Tip for VS Code 사용자

터미널 명령어가 무섭다면?
VS Code 왼쪽 메뉴의 **세 번째 아이콘(소스 제어)**을 누르면, 버튼 클릭만으로 `Commit`, `Push`, `Pull`을 다 할 수 있습니다!

1. `+` 버튼: `git add`와 같음
2. 메시지 입력 후 `커밋(✓)`: `git commit`과 같음
3. `변경 내용 동기화` 버튼: `git push/pull`과 같음

그래도 충돌(Conflict)이 나거나 꼬이면, **팀장(저)을 부르세요!** 🚑

---

## 7. 🧪 연구실 서버 동시 접속 가이드 (중요!)

"하나의 서버(컴퓨터)를 4명이 같이 쓰는데, 어떻게 작업해요?"

🚨 **절대 금지**: **하나의 폴더를 4명이 같이 쓰지 마세요!**
(승환이가 브랜치를 바꾸면, 작업 중이던 근우의 파일도 바뀌어 버립니다.)

### ✅ 1인 1폴더 원칙 (독립된 작업 공간)
서버 안에 각자의 방(폴더)을 만들고, **각자 프로젝트를 Clone** 해야 합니다.

```bash
# 예시 구조 (서버)
/home/lab_user/workspace/
├── seunghwan/CTR-Prediction/  (승환의 작업실)
├── geunwoo/CTR-Prediction/    (근우의 작업실)
├── sooho/CTR-Prediction/      (수호의 작업실)
└── sangmin/CTR-Prediction/    (상민의 작업실)
```

### ✅ 데이터는 '바로가기'로 공유 (용량 절약)
데이터(`rawdata`)까지 4개나 복사하면 용량이 터집니다.
한곳에 데이터를 두고, 각자 폴더에는 **바로가기(Symlink)**를 만드세요.

```bash
# 1. 데이터는 공용 폴더에 둡니다.
/home/lab_user/data/rawdata/  <-- 실제 데이터 위치

# 2. 내 폴더에서 바로가기 생성 명령어 (터미널)
cd ~/workspace/seunghwan/CTR-Prediction
ln -s /home/lab_user/data/rawdata ./rawdata
# (해석: 저쪽 데이터를 내 폴더의 'rawdata'인 것처럼 연결해줘!)
```

### ✅ Git 계정 각자 설정하기
공용 아이디(예: `root`)를 쓰더라도, **누가 커밋했는지**는 구별해야겠죠?
각자의 프로젝트 폴더 안에서 아래 명령어를 한 번만 쳐주세요.

```bash
# 내 폴더 안에서 실행
git config user.name "Seunghwan Kim"
git config user.email "내이메일@example.com"
```
이렇게 하면 공용 서버에서도 **"승환이 커밋했다"**는 기록이 정확히 남습니다!

> **요약**: 
> 1. 서버에 **내 폴더** 만들기 
> 2. 거기서 `git clone` 하기
> 3. 데이터는 `ln -s` 로 연결하기
> 4. `git config`로 내 이름표 붙이기
