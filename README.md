# Artificial_Intelligence_Portfolio

이 저장소는 제가 수행한 **AI/NLP 프로젝트 포트폴리오 허브**입니다.  
각 프로젝트는 단순 실험이 아니라, **문제 정의 → 데이터 파이프라인 → 모델링/최신 기법 적용 → 평가/개선 → 재현 가능한 실행/데모**까지 연결해 *목표 성능과 시연 가능한 결과물*을 만드는 데 초점을 두었습니다.

프로젝트별 상세 내용(배경, 접근, 아키텍처, 실험/지표, 회고)은 **각 폴더의 README**에서 확인하실 수 있고, 메인 페이지에는 **핵심 지표와 데모 링크**만 간단히 정리했습니다.  
포트폴리오는 지속적으로 업데이트됩니다. 문의/피드백은 **호성**에게 이메일로 부탁드립니다: **hhs6228@gmail.com**


---
## Projects Index

- [NowYouSeeMe – 한국어 자동 화면 해설(AD) 웹 서비스](./NowYouSeeMe/README.md)
- [Grammatical Error Correction – 영어 문법/오타 자동 교정](./GrammaticalErrorCorrection/README.md)
- [Code Similarity Detection – 코드 유사도 판별](./CodeSimilarityDetection/README.md)
- [AI vs Human Paragraph – 약라벨 기반 AI 생성 문단 탐지](./AiHumanParagraph/README.md)


---

## Projects Summary
> 각 프로젝트의 상세(문제정의/아키텍처/실험/회고)는 폴더 README에 있습니다.  


### 1) [NowYouSeeMe – 한국어 자동 화면 해설(AD) 웹 서비스](./NowYouSeeMe/README.md) (or [Full repository](https://github.com/horaeng1/Nowyouseeme))
https://github.com/user-attachments/assets/dbece2b3-b80f-413a-bcae-63b16dae8a87

https://github.com/user-attachments/assets/57276b3f-09ed-4e88-9db5-1a480c01e13e
- **문제:** 시각장애인은 영상 시청 시 시각 정보(인물 행동, 장면 전환 등)를 인지하기 어려움. 기존 AD 제작은 전문 인력이 수작업으로 진행하며, 비용과 시간이 많이 소요됨
- **목적:** 영상을 입력하면 **대사와 겹치지 않는 무음 구간에 맥락을 반영한 한국어 AD를 자동 생성·삽입**하는 엔드투엔드 웹 서비스 구축
- **핵심 접근:** VAD+ASR 기반 대사 보호 타임라인 / 최근 대사·이전 AD·키프레임을 묶는 컨텍스트 윈도우로 문맥 일관성 확보 / Gemini·GPT·로컬 VLM 멀티모델 파이프라인 통합
- **성과:** 5분 영상 기준 1~20분 처리, BERTScore 0.88+, 30편 테스트로 대사 보호·연속성 개선 확인


### 2) [Grammatical Error Correction – 영어 문법/오타 자동 교정](./GrammaticalErrorCorrection/README.md) (or [Full repository](https://github.com/horaeng1/GEC))
https://github.com/user-attachments/assets/f7994a93-9fe0-4fba-9020-d531c2f827f6
- **문제:** 영어 학습자나 비원어민이 작성한 문장에는 문법 오류·오타가 포함됨. 단순 맞춤법 검사기로는 문맥을 고려한 정교한 교정이 어려움
- **목적:** 입력 문장의 **문법 오류/오타를 자동 교정**하고, 수정 구간을 하이라이트로 보여주는 GEC 시스템 구축
- **핵심 접근:** 2,000,000 sentence-pair 규모 데이터 가공·정제 / Copy-heavy 특성을 반영한 편집 중심 설계 / F0.5(Precision 가중) 기준 최적화
- **성과:** 팀 내 최종 우수 성능 모델 확보, Gradio 데모 배포 모델로 채택


### 3) [Code Similarity Detection – 코드 유사도 판별](./CodeSimilarityDetection/README.md) (or [Full repository](https://github.com/horaeng1/code-similarity))
- **문제:** 대규모 코드 저장소에서 표절/클론 코드를 탐지하거나, 동일 알고리즘을 다르게 구현한 코드를 찾는 것은 변수명·주석·포맷 차이로 인해 단순 텍스트 비교로는 어려움
- **목적:** 두 소스코드가 **동일 문제를 풀이하는 유사 코드인지 판별**하는 학습·추론 파이프라인 구축
- **핵심 접근:** CodeNet 사전학습 + 대회 데이터 파인튜닝 / Unigram(32k) 토크나이저 + 구조 마커(`<indent>`, `<str>` 등) 설계 / 전처리 전략(Left Truncation + 최소화)으로 성능 개선
- **성과:** RoBERTa-small 커스텀 모델 Public 91.67% / Private 91.64%, 전처리 개선으로 +4.5%p 인사이트 도출


### 4) [AI vs Human Paragraph – 약라벨 기반 AI 생성 문단 탐지](./AiHumanParagraph/README.md)
- **문제:** AI 생성 텍스트 탐지에서 **문서 단위 라벨만 제공**(문단별 정답 없음)되는 약라벨(Weak Label) 환경. 직접 문단 분류기를 학습하면 신호가 희석되고, 불균형 문제도 심각함
- **목적:** 문서 라벨만으로 학습하여 **문단 단위 AI 작성 확률(0~1)**을 예측하는 모델 설계
- **핵심 접근:** 약라벨 제약을 **MIL(Multiple Instance Learning)** 로 재정의(Document=Bag, Paragraph=Instance) / LogSumExp pooling + top-k 보조 손실 + hard-positive 샘플링으로 핵심 문단 신호 증폭 / LoRA로 제한된 GPU에서 실험 효율 확보
- **성과:** Public AUC 0.864 / Private AUC 0.869, 리더보드 87/929위(연습 참가)


---

## Tech
Python · PyTorch · HuggingFace · LLM/VLM Prompting  
STT/VAD/TTS · Docker · Web MVP(React/Node)  
(프로젝트별 상세 스택은 각 README 참고)

---

