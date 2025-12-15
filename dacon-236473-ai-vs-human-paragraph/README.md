# DACON 236473 | LLM vs Human 문단(Paragraph) 판별 (MIL + LoRA)

문서 단위 라벨(weak label)만 제공되는 환경에서, **문단 단위 AI 작성 확률(0~1)** 을 예측하는 모델을 설계/개선했습니다.

- 참가 형태: **연습 참가(대회 종료 후 개인 학습 목적)**
- 리더보드(연습): **87 / 929**
- Metric: ROC-AUC
- Public AUC: **0.8638700593**
- Private AUC: **0.8693396251**
- Env: Google Colab / NVIDIA L4 22GB

---

## TL;DR
- 문서 라벨만 존재하는 약라벨 제약을 **MIL(Multiple Instance Learning)** 로 재정의해 문단 예측 문제로 전환
- **LogSumExp(τ) pooling + top-k instance 보조 손실 + hard-positive 샘플링**으로 “핵심 문단 신호”를 증폭
- 제한된 GPU에서 **LoRA**로 실험 회전율을 확보하고 제출 파이프라인을 정리

---

## 문제 정의
- Input: `paragraph_text`
- Output: `P(AI-generated | paragraph)` in [0, 1]
- Constraint: 학습은 **문서 라벨만 제공(문단 GT 없음)**, 평가는 **문단 단위 확률 제출**

---

## 접근 전략 (Model)
### 1) MIL Formulation
- Document = Bag, Paragraph = Instance
- 문단별 로짓 `s_i` → pooling으로 문서 로짓 `S` 구성 → 문서 라벨로 학습
- Inference: `sigmoid(s_i)` 를 문단 확률로 제출

### 2) Key Techniques
- Pooling: **LogSumExp(τ)**
  - `LSE_TAU = 12.0`
- Instance auxiliary loss (**top-k**)
  - `INST_TOPK = 2`, `INST_LAMBDA = 0.15`
- Hard-positive subsampling (**cache top-k**)
  - `HARD_CACHE_TOPK = 2`
- LoRA
  - `r=8`, `lora_alpha=16`, `lora_dropout=0.1`
  - `target_modules=["query","value"]`

### 3) Training Settings (notebook 기준)
- `MAX_LEN = 192`
- `TRAIN_PARAS_PER_DOC = 12`
- `BATCH_BAGS = 8`
- imbalance: `POS_WEIGHT = (num_neg/num_pos) * 1.3`
- Asymmetric focal: `GAMMA_POS=0.0`, `GAMMA_NEG=2.0`
- OOM 방지: `VAL_BATCH1 = True`

---

## 리포지토리 구성
> **중요:** 대회 데이터(train/test 등)는 이 저장소에 포함하지 않습니다.

```text
.
├─ notebooks/
│  ├─ 00_EDA.ipynb
│  ├─ 01_Baseline_TFIDF_XGB.ipynb
│  ├─ 02_Attention-MIL_LoRA6_Final.ipynb   # 최종(주력)
│  └─ 03_Inference.ipynb
├─ models/
│  └─ README.md
├─ data/
│  └─ README.md
├─ assets/
│  ├─ eda_length.png
│  ├─ eda_punct_ratio.png
│  └─ flow.png
├─ requirements.txt
├─ .gitignore
└─ README.md
```
## 데이터 준비

1. DACON 대회 페이지에서 데이터 다운로드
- https://dacon.io/competitions/official/236473/data
2. 아래 구조로 로컬/Colab에 배치
```
data/
  train.csv
  test.csv
  sample_submission.csv
```

주의: 대회 데이터는 재배포하지 않습니다(이 저장소에는 포함하지 않음).

## 실행 방법 (Colab 권장)
1. notebooks/02_Attention-MIL_LoRA6_Final.ipynb 실행
2. 실행 결과로 submission.csv가 생성됩니다.
3. 생성된 submission.csv를 DACON에 제출합니다.

## 실험 변천사
- Baseline: TF-IDF + XGB
- MIL(Attention): 문서=bag / 문단=instance + attention pooling
- MIL + LoRA: 효율 튜닝 및 안정화
- Final: LSE(τ=12) + top-k aux + hard-positive + pos_weight/ASL

## 트러블슈팅
- 문단 확률이 0.5 근처로 몰림 → LSE + top-k aux로 로짓 분리 강화
- 양성 문서에서 증거 문단 누락 → hard-positive 캐시로 항상 포함
- OOM/검증 불안정 → MAX_LEN, VAL_BATCH1 등 정책으로 안정화