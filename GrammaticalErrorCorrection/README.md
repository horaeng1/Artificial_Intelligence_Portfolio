# GEC: 영어 문법/오타 자동 교정 (Gradio Demo)

영어 문장 내 문법 오류/오타를 자동 교정하고, 수정 구간을 하이라이트로 보여주는 GEC(Grammatical Error Correction) 데모 프로젝트입니다.  
**2,000,000 sentence-pair** 규모 데이터 기반으로 모델을 학습하고, **F0.5(precision 가중)** 지표로 성능을 검증했습니다.

- Code Repository: https://github.com/horaeng1/GEC

## Demo
[🔗 데모 영상 보기](https://github.com/user-attachments/assets/f7994a93-9fe0-4fba-9020-d531c2f827f6)

---

## 프로젝트 개요
| 항목 | 내용 |
|---|---|
| 프로젝트명 | GrammaticalErrorCorrection |
| 기간 | 2025.10.24 ~ 2025.11.03 |
| 팀 | 5명 |
| 문제 | 영어 문장에 포함된 문법 오류/오타를 자동 교정 |
| 데이터 | 2,000,000 sentence-pair (학습/검증 구성) |
| 모델 | RoBERTa 기반 GEC 파인튜닝(데모 적용 모델) |
| 평가 | F0.5 기반 (교정 품질에서 Precision 비중 강화) |
| 산출물 | Gradio 데모, 학습/추론 파이프라인, 실행 가이드 |

---

## 해결 과제 및 접근 방식
### 1) 데이터/오류 특성 기반 전략 수립
- **수정량이 크지 않은 편(Copy-heavy)**인 GEC 특성을 반영해 “대규모 생성”보다 **편집(Edit) 중심**으로 설계
- 오류 분포/동시발생 패턴을 분석해 **데이터 가공 및 증강 방향**을 결정(자주 같이 등장하는 오류쌍 기반)

### 2) 모델링 방향
- 입력 문장을 “정답 문장으로 생성”하기보다, 토큰 단위 편집 관점으로 학습(교정/치환/삭제/삽입 등)
- 여러 백본/전략을 비교 실험한 뒤, **RoBERTa 기반 최종 우수 모델을 데모에 채택**

### 3) 평가 기준
- **F0.5**로 평가 (교정 태스크 특성상 *불필요한 수정(오탐)* 비용이 커서 Precision을 더 중시)
- 모델 선택/튜닝은 F0.5 개선을 목표로 운영


---

## My Contribution 
### 데이터 파이프라인 오너십
- **2,000,000 sentence-pair** 데이터 수집/다운로드 후, 학습 가능한 형태로 가공
- 팀이 바로 실험에 투입할 수 있도록 **공용 포맷/가이드**로 정리해 공유(협업 효율 및 재현성 확보)

### 모델 성능 리드 및 데모 적용
- RoBERTa 기반으로 GEC 학습/튜닝을 수행해 **팀 내 최종 우수 성능 모델을 확보**
- 최종적으로 **데모(Gradio) 배포 모델로 내 모델이 채택**되도록 성능/안정성을 책임지고 정리

---

## 실행 방법(Quickstart)
포트폴리오 허브에는 요약만 유지하고, 실행/설치는 코드 리포지토리를 단일 소스로 관리합니다.

```bash
git clone https://github.com/horaeng1/GEC.git
cd GEC
pip install -r requirements.txt
python app.py
```
---
## Tech Stack

- Python 
- Transformers (RoBERTa)
- Gradio
---
## Credits / Notes

본 프로젝트는 오픈소스/레퍼런스 접근을 포함하며, 원본 라이선스를 준수합니다.

상세 실험 설정/데이터 구성/모델 파일은 코드 리포지토리에서 관리합니다.