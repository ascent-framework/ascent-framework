# Compute & Model Options for Phase 0 Experiments

**Date:** 2026-04-19  
**Status:** Planning note (non-registered, exploratory)  
**Context:** Phase 0 환경 세팅 전 대안 검토  
**Last updated:** 2026-04-20 — VRAM 수치 근거 명시, T4/P100 분리, 체크포인트 전략 확정

---

## 배경

사전등록(v1.3)의 기본 설정은 RTX 4090 로컬 환경 + Qwen2.5-1.5B-Instruct.  
무료 클라우드 GPU 활용 및 모델 구성을 검토.

---

## 등록된 모델 구성 (v1.3 기준)

| 모델 | 파라미터 | 아키텍처 | GRPO 추정 VRAM¹ | Kaggle T4 적합도 | 역할 |
|------|---------|---------|----------------|----------------|------|
| Qwen2.5-1.5B-Instruct | 1.5B | Dense | ~8–10GB | ✅ 가능 | Primary |
| Qwen2.5-1.5B (base) | 1.5B | Dense | ~8–10GB | ✅ 가능 | H2 Secondary A (same-family) |
| Qwen2.5-3B | 3B | Dense | ~12–14GB | ✅ gradient_checkpointing 필요² | H2 Secondary A (size-scaled) |
| google/gemma-2-2b-it | 2B | Dense | ~8–10GB | ✅ 가능 | H2 Secondary B (cross-family) |

모든 모델은 dense 아키텍처로 통일 — SVD 분석 및 H2 transfer 비교의 일관성 확보.

**¹ VRAM 수치 근거:**  
모든 추정치는 다음 설정을 기준으로 함: LoRA (r=16, 7개 타겟 모듈), GRPO (num_generations=4, batch_size=2), AdamW optimizer state 포함, bfloat16 혼합 정밀도. Full fine-tuning이나 QLoRA 설정에서는 수치가 달라짐. 실제 실행 시 런 로그에 정확한 peak VRAM 값을 기록할 것.

**² Qwen2.5-3B + T4 주의:**  
gradient_checkpointing 활성화 시 T4 16GB에서 이론적으로 가능하지만 검증되지 않음. Phase 0 파일럿 대상이 아니며, Phase 1 이전에 별도 smoke test 필요.

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

### T4 vs P100 하드웨어 분리 정책

Kaggle은 T4와 P100 중 하나를 할당하며 사용자가 선택할 수 없다. 두 GPU는 특성이 다르다:

| 항목 | T4 (Turing) | P100 (Pascal) |
|------|-------------|---------------|
| VRAM | 16GB GDDR6 | 16GB HBM2 |
| bfloat16 지원 | ✅ 네이티브 | ❌ 미지원 (float16으로 폴백) |
| 메모리 대역폭 | ~320 GB/s | ~720 GB/s |
| GRPO 훈련 속도 | 기준값 | T4보다 느릴 수 있음 (bfloat16 폴백 영향) |

**운영 체크리스트 항목 (런 로그 필수 기록):**
- 세션에서 할당된 GPU 모델 (`nvidia-smi` 출력 첫 줄)
- 실제 사용된 precision (`torch.cuda.is_bf16_supported()` 결과)
- 훈련 1 step당 소요 시간 (하드웨어 간 비교 기준)

런 로그에 GPU 모델을 명시하지 않으면 실험 시간과 재현 조건이 불명확해진다.

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

## 확정된 결정 사항

**체크포인트 저장 전략 (Phase 0 고정):**  
주 저장소 **Kaggle Dataset**, 백업 **Google Drive**. HuggingFace Hub는 Phase 1 이후 공개 배포 필요 시점까지 사용하지 않음.  
옵션을 여러 개 열어두면 파일럿 단계에서 운영 복잡도가 증가하므로, Phase 0 동안은 위 두 경로만 사용한다. 변경 시 이 문서에 명시적으로 기록할 것.
