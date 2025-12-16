# Code Similarity Detection

> 기간: 2025.10.17 ~ 2025.10.23  
> 형태: 팀 프로젝트 (모델 5종 비교 실험)  
> 키워드: CodeNet, Pretraining, Fine-tuning, Code Similarity, Tokenizer, Preprocessing

## 0. 한 줄 요약
CodeNet으로 사전학습(Pretraining)한 코드 모델을 데이콘 데이터로 파인튜닝(Fine-tuning)하여 **두 코드의 유사도/동일 문제 풀이 여부**를 판별하는 모델을 구축하고, 전처리·토크나이저·학습전략에 따른 성능 영향을 비교/분석했습니다.

---

## 1. 문제 정의
- 입력: 코드 쌍 (A, B)
- 출력: 두 코드가 “유사(동일 문제 풀이)”한지에 대한 예측(대회 포맷 기준)
- 핵심 난점
  - 코드 길이/형식 다양성 (주석, 들여쓰기, 리터럴, 변수명 등)
  - 토크나이저/전처리 전략에 따라 성능 편차가 크게 발생

---

## 2. 접근 방법
### (1) 데이터 전처리 파이프라인
- 주석 제거
- 식별자 정규화(변수/함수/클래스명 익명화)
- 공백/개행 정규화
- 코드 포맷팅(black 기준)
- 중복 제거(SHA1)

### (2) 토크나이저 설계
- Unigram 기반 토크나이저(32k vocab)
- 구조/리터럴 마커 도입: `<indent>`, `<dedent>`, `<str>`, `<num>` 등  
→ 희귀 식별자/리터럴에 대한 안정성 확보

### (3) 모델링/학습
- CodeNet으로 사전학습 모델 구축 후, 대회 데이터로 파인튜닝
- 표준 학습 전략(AdamW, Warmup Scheduler) 기반으로 하이퍼파라미터 튜닝

---

## 3. 성과 (팀 실험 결과)
| Model | Approach | Public | Private |
|---|---|---:|---:|
| RoleBERT | Role Embedding | 93.17% | 93.18% |
| CodeBERT | microsoft/codebert-base | 92.84% | 92.86% |
| **RoBERTa-small (Me)** | **Custom Implementation** | **91.67%** | **91.64%** |
| Custom BERT | From Scratch | 86.33% | 86.32% |
| Contrastive | Dual Encoder + AST | 62.18% | 62.03% |

---

## 4. My Contribution
### 4.1 RoBERTa-small 파이프라인 구축
- RoBERTa 최적화 기법을 적용한 **RoBERTa-small 커스텀 구현**
- **Unigram(32k) 토크나이저 학습 및 특수 토큰/마커 설계**
- 전처리 전략(특히 **Left Truncation + 최소화**)을 중심으로 성능 개선

### 4.2 결과/임팩트
- 팀 내 Custom 계열 모델 중 최고 성능 달성 (Public 91.67 / Private 91.64)
- 전처리 전략 개선으로 성능 **+4.5%p 수준 개선 인사이트** 도출 및 공유

---

## 5. Tech Stack
- Python, HuggingFace Transformers
- Tokenizers (Unigram/BPE/SentencePiece 실험)
- 데이터 전처리: regex, black, hashing(SHA1)
- 실험/재현: 스크립트 기반 학습·추론 파이프라인

---

## 6. Reproducibility
### 6.1 Preprocess
```bash
python preprocessing/preprocess_corpus.py
```
### 6.2 Train / Fine-tune (RoBERTa-small, Me)
```
# 1) Unigram tokenizer
python tokenizers/unigram_tokenizer_HwangHosung.py

# 2) Pretrain
python train/pretrain_roberta_HwangHosung.py --mode train

# 3) Finetune (local or huggingface)
python train/finetune_roberta_HwangHosung.py --model local
python train/finetune_roberta_HwangHosung.py --model huggingface
```

## 7. Links

- Original Project Repo: https://github.com/horaeng1/code-similarity

## 8. Lessons Learned

사전학습 모델(Transfer Learning) 효과가 가장 크며, 그 다음은 전처리/토크나이저 전략의 영향이 큼

“과도한 정제”보다 “목표 지표에 맞춘 최소화 + 안정적 학습 전략”이 재현성과 성능을 동시에 확보