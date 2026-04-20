# Compute & Model Options for Phase 0 Experiments

**Date:** 2026-04-19  
**Status:** Planning note (non-registered, exploratory)  
**Context:** Phase 0 환경 세팅 전 대안 검토  
**Last updated:** 2026-04-19 — v1.3 모델 결정 반영

---

## 배경

사전등록(v1.3)의 기본 설정은 RTX 4090 로컬 환경 + Qwen2.5-1.5B-Instruct.  
무료 클라우드 GPU 활용 및 모델 구성을 검토.

---

## 등록된 모델 구성 (v1.3 기준)

| 모델 | 파라미터 | 아키텍처 | GRPO 추정 VRAM | Kaggle T4 적합도 | 역할 |
|------|---------|---------|---------------|----------------|------|
| Qwen2.5-1.5B-Instruct | 1.5B | Dense | ~8–10GB | ✅ 가능 | Primary |
| Qwen2.5-1.5B (base) | 1.5B | Dense | ~8–10GB | ✅ 가능 | H2 Secondary A (same-family) |
| Qwen2.5-3B | 3B | Dense | ~12–14GB | ✅ gradient_checkpointing | H2 Secondary A (size-scaled) |
| google/gemma-2-2b-it | 2B | Dense | ~8–10GB | ✅ 가능 | H2 Secondary B (cross-family) |

모든 모델은 dense 아키텍처로 통일 — SVD 분석 및 H2 transfer 비교의 일관성 확보.

### google/gemma-2-2b-it 선택 이유

- Dense 아키텍처 — Qwen2.5와 직접 비교 가능
- Gemma Scope SAE 공개 — H1c(interpretable features 정렬) 탐색 실험에 바로 활용 가능
- 2B로 Kaggle T4 16GB 여유 있음, GRPO gradient_checkpointing 없이 가능
- Gemma 4는 전 모델 MoE — update vector 정의 불일치로 제외

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

---

## 결정 보류 사항

- [ ] Phase 1 전체를 Kaggle으로 진행할지 vs 로컬 RTX 4090 병행할지
- [ ] 체크포인트 저장 전략 (Kaggle Dataset vs Google Drive vs HuggingFace Hub)
