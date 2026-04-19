# Compute & Model Options for Phase 0 Experiments

**Date:** 2026-04-19  
**Status:** Planning note (non-registered, exploratory)  
**Context:** Phase 0 환경 세팅 전 대안 검토

---

## 배경

사전등록(v1.2)의 기본 설정은 RTX 4090 로컬 환경 + Qwen2.5-1.5B-Instruct.  
무료 클라우드 GPU 활용 및 경량 모델 대체 가능성을 검토.

---

## 모델 대안 검토

| 모델 | 파라미터 | GRPO 추정 VRAM | Colab T4 적합도 | 사전등록 상태 |
|------|---------|---------------|----------------|-------------|
| Qwen2.5-1.5B-Instruct | 1.5B | ~8–10GB | ✅ 가능 | Primary (등록됨) |
| TinyLlama-1.1B-Chat | 1.1B | ~6–8GB | ✅ 여유 있음 | Secondary H2 (등록됨) |
| Gemma 4 1B | 1B | ~5–7GB | ✅ 가능 | 미등록 → 수정 필요 |
| Gemma 4 4B | 4B | ~12–14GB | ⚠️ gradient_checkpointing 필수 | 미등록 → 수정 필요 |

### TinyLlama 1.1B

- v1.2 §4.1에 H2 cross-family secondary 모델로 이미 포함
- H2 transfer 실험 대상으로만 등록됨 — primary 분석 경로(H1a/H1b)는 Qwen2.5-1.5B-Instruct
- **하네스 smoke test 용도로만 사용 가능**: TinyLlama 파일럿 통과 ≠ 등록된 primary 모델 준비 완료

### Gemma 4

- v1.2에 미포함 → OSF 등록 **전**에 추가하면 pre-data amendment로 처리 가능 (§11 정책 준수)
- OSF 등록 후에는 post-registration amendment로 기록, 해당 분석은 "non-pre-registered" 레이블 필요
- 1B 변종은 TinyLlama보다 VRAM 효율적이며 Gemma 생태계(SAE 등) 활용 가능성 있음

---

## Google 무료 GPU 옵션 비교

| 플랫폼 | GPU | 무료 한도 | 세션 안정성 | 추천도 |
|--------|-----|---------|------------|------|
| Google Colab 무료 | T4 16GB | 불규칙 (~12h/세션) | ⚠️ 낮음 | 비추천 (본실험) |
| **Kaggle** | T4 / P100 16GB | **30h/주 (확정)** | ✅ 안정적 | ✅ 추천 |
| Colab Pro ($10/월) | A100 40GB | 컴퓨팅 유닛 제한 | ✅ 안정적 | ✅ 파일럿 이후 |
| Vast.ai | A100/H100 선택 | 종량제 (~$0.3–1/h) | ✅ 안정적 | Phase 2 참조 (ROADMAP.md §Phase 2 예산; 실행 전 별도 로그 필요) |

### Google Colab 무료의 실제 문제

- GRPO 1 task ≈ 2–4h (1.5B, T4 기준)
- 10 tasks = 20–40h 총 소요 → 12h 세션 내 완료 불가
- 연속 사용 시 GPU 할당 블락 (불규칙)
- 체크포인트 미저장 시 진행 데이터 유실 위험

### Kaggle 추천 이유

- 주 30h 무료 GPU 확정 제공 (T4 또는 P100)
- 세션 최대 12h이지만 주 30h 한도 내 재시작 자유
- 체크포인트를 Kaggle Dataset에 저장하면 세션 간 지속 가능
- 1 task/세션 전략으로 10 tasks 완료 가능 (3–4주 소요)

---

## Phase 0 파일럿 권장 전략

**목표:** GSM8K × 1 task end-to-end 파이프라인 검증

| 항목 | 선택 | 이유 |
|------|------|------|
| 모델 | **Qwen2.5-1.5B-Instruct** | 등록된 primary 모델 — 파일럿도 primary로 검증해야 함 |
| 플랫폼 | Kaggle 무료 | 안정적 30h/주, 비용 0 |
| 저장 | Kaggle Dataset / Google Drive | 세션 간 체크포인트 지속 |
| 목표 | train → extract → SVD 1회 통과 | Phase 0 deliverable |

### TinyLlama smoke test (선택)

TinyLlama-1.1B-Chat으로 먼저 하네스 동작 확인을 원할 경우:

- **smoke test 목적에 한정**: 모듈명·토크나이저·체크포인트 저장 경로 확인
- **Phase 0 완료 조건으로 인정 불가** — Qwen2.5-1.5B-Instruct end-to-end 통과가 반드시 뒤따라야 함
- 결과 문서에 "TinyLlama smoke test only, not a Phase 0 gate" 명시 필요

---

## 사전등록 수정이 필요한 경우

Gemma 4를 primary 또는 secondary 모델로 추가하려면 **아래 모든 항목**을 수정해야 함.  
§4.1만 수정하면 base-model 목록에는 등록된 것처럼 보이지만 H2 transfer 분석 경로에서 미등록 상태가 됨.

**OSF 등록 전 (pre-data amendment):**

- [ ] `docs/preregistration/v1.2.md` §4.1 — base model 목록에 추가
- [ ] `docs/preregistration/v1.2.md` §3 H2 — transfer pair 명시 (`Gemma 4 XB → ?` 형태로 추가)
- [ ] `docs/preregistration/v1.2.md` §7 — interpretation rules에 새 모델 영향 여부 검토
- [ ] `docs/preregistration/v1.2.md` §11 — version history에 v1.3 changelog 기록
- [ ] 버전을 v1.3으로 업데이트

**OSF 등록 후 (post-registration amendment):**

- 위 항목 동일하게 수정 + amendment log 파일 별도 작성
- 영향 받은 모든 분석에 "non-pre-registered" 레이블 명시

---

## 결정 보류 사항

- [ ] Gemma 4를 secondary 모델로 추가할지 여부 (OSF 등록 전에 결정 필요)
- [ ] Phase 1 전체를 Kaggle으로 진행할지 vs 로컬 RTX 4090 병행할지
- [ ] 체크포인트 저장 전략 (Kaggle Dataset vs Google Drive vs HuggingFace Hub)
