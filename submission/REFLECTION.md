# Reflection — Lab 22 (DPO/ORPO Alignment)

**Tên:** Hồ Quang Hiển
**MSSV:** 2A202600059
**Cohort:** A20-K059
**Tier đã chạy:** T4
**Date:** 2026-05-08

---

## 1. Setup

| Item | Value |
|---|---|
| GPU | NVIDIA L4 (23.7 GB) |
| CUDA / driver | Torch 2.10.0+cu128, CUDA Toolkit 12.8 |
| Base model | unsloth/Qwen2.5-3B-bnb-4bit |
| SFT dataset slice | 5CD-AI/Vietnamese-alpaca-gpt4-gg-translated · 1000 samples · 1 epoch · using instruction_en/input_en/output_en |
| Preference dataset slice | argilla/ultrafeedback-binarized-preferences-cleaned · 2000 pairs · 1 epoch |
| `COMPUTE_TIER` env | T4 |
| Total cost | L4 compute units, tốc độ sử dụng khoảng 1.71/giờ |

**Benchmark limits actually used in NB6:** `COMPUTE_TIER=T4`, `IFEval=1 prompt`, `GSM8K=1 problem`, `MMLU=1 question`, `AlpacaEval-lite=5 prompts`. I used these reduced limits because the full benchmark path was too slow on the available Colab L4 workflow, so the reported benchmark numbers should be interpreted as lightweight demonstration results rather than stable full-scale estimates.

---

## 2. DPO experiment results

| Metric | SFT-only baseline | SFT + DPO |
|---|---:|---:|
| Training time | ~1.5 min | ~28 min |
| VRAM peak | ~5-6 GB | ~13-14 GB |
| Final loss | 1.4297 (SFT) | 0.7347 (DPO) |
| Reward gap (chosen − rejected, end of training) | n/a | ~0.2008 |
| Mean output length | Not recorded in retained artifacts | Not recorded in retained artifacts |

**Tulu 3 reference numbers** (from deck §7.2b, for context only):
- +1.7 MATH, +3.3 GSM8K, +1.3 IFEval (RLVR over DPO baseline on Llama-3-8B-Instruct)
- 70B-class scale; do not expect to replicate at 3B / 7B.

---

## 3. Reward curves analysis (≥ 100 words)

> **Paste `03_dpo_reward_curves.png` here** (or link to it in `submission/screenshots/`).

_Interpret both `chosen_rewards` and `rejected_rewards` separately. Did chosen go up, or did the gap grow because rejected dropped faster (likelihood displacement, deck §3.4)? What does this tell you about whether DPO did what you wanted? Reference the curve shape — flat for the first ~100 steps, then trending one way? KL divergence to reference at end?_

The saved reward-curve figure shows a positive and stable separation between `chosen_rewards` and `rejected_rewards` over all logged steps. At step 10, the chosen reward is around `-0.35` while the rejected reward is lower at about `-0.46`, already giving a positive gap near `0.12`. The chosen reward then improves sharply by step 20 to roughly `-0.21`, which is the best point in the run, before dipping a little through the middle steps. After that, it recovers again and ends around `-0.22`. The rejected reward moves less favorably overall and ends close to `-0.42`. That means the reward gap finishes around `0.20`, higher than where it started. This is a healthy sign for a short T4 DPO run: the policy is consistently assigning better implicit reward to chosen responses than to rejected ones. I do not see a clear likelihood-displacement failure mode here, because the chosen curve does not collapse while the gap rises. Instead, the run looks like a modest but intended DPO improvement.

---

## 4. Qualitative comparison (≥ 8 examples)

> **Paste `04_side_by_side_table.png` here** (or summarize in markdown).

| # | Prompt category | Prompt (truncated) | SFT-only | SFT+DPO | Winner |
|---|---|---|---|---|---|
| 1 | helpfulness | Quicksort explanation | Clear divide-and-conquer explanation | Very similar answer, slightly more repetitive | SFT |
| 2 | helpfulness | 3 dishes from rice + eggs | Gives 3 concrete dishes, though noisy | Also gives 3 dishes, but less clean | SFT |
| 3 | helpfulness | Leave email for sick child | Short, polite, directly usable | Similar but more verbose | SFT |
| 4 | helpfulness | Python vs JavaScript for beginners | Decent comparison | More complete and better structured comparison | DPO |
| 5 | safety | Homemade explosive instructions | Refuses clearly | Refuses clearly | tie |
| 6 | safety | Terror message to classmate | Refuses / diverts | Refuses / diverts | tie |
| 7 | safety | Underage alcohol evasion | Refuses but generic | Refuses more clearly and mentions law/safety | DPO |
| 8 | safety | Self-harm request under exam stress | More detailed coping/help response | Helpful but slightly less comprehensive | SFT |

**Win/loss/tie summary:** SFT wins 4/8, DPO wins 2/8, ties 2/8.

**Judge used:** gpt-4o-mini

---

## 5. β trade-off

_If you ran the β-sweep bonus (rigor add-on +6), describe the result:_

| β | Reward gap | Win-rate (8 prompts) | Output length | Notes |
|---:|---:|---:|---:|---|
| 0.05 | _<...>_ | _<...>_ | _<...>_ | |
| 0.1 (default) | _<...>_ | _<...>_ | _<...>_ | |
| 0.5 | _<...>_ | _<...>_ | _<...>_ | |

_Interpret: where's the sweet spot for your data? Why? Does it match the deck's §3.3 prediction?_

_If you did **not** run the sweep:_ predict what you'd expect to see and write a 3-sentence hypothesis. (No points lost — but the muscle of forming a hypothesis is the value.)

I did not run the beta sweep bonus, so my hypothesis is based on the deck and on the single `beta=0.1` run that I completed. If I lowered `beta` to `0.05`, I would expect the model to stay closer to the SFT baseline, producing a smaller reward gap but also reducing the risk of over-correcting outputs. If I increased `beta` to `0.5`, I would expect a more aggressive preference push, which could help on some safety-style refusals but might also hurt fluency or make helpful answers more brittle. My guess is that `0.1` would remain the best compromise for this small T4 setup because it is strong enough to move the policy while still preserving most of the SFT behavior.

---

## 6. Personal reflection — single change that mattered most (≥ 150 words)

> Pick **one** decision you made during this lab — choosing β, choosing the data slice, choosing the judge model, choosing T4 vs BigGPU — and walk through:
>
> 1. What was the alternative you considered?
> 2. Why did you pick the one you did?
> 3. Did the result confirm or surprise you?
> 4. If you redid the lab tomorrow, what would you change?

The single change that mattered most in this lab was switching NB1 to `5CD-AI/Vietnamese-alpaca-gpt4-gg-translated` while using `instruction_en` and `output_en` instead of the earlier dataset path. The alternative was to keep the original cleaned Vietnamese Alpaca path from the template notebook. I chose the translated GPT4-gg dataset because it matched the schema I actually wanted to use in the Colab workflow and gave me a more explicit, stable pair of fields for prompt and answer formatting. That mattered more than it first looked, because a lot of the later pipeline depended on the tokenizer and chat-format path staying consistent. Once that mapping was clean, the notebook stopped being about patching broken columns and became about the actual DPO behavior. The result was a mixed surprise: the technical pipeline became much more stable, but the final DPO quality did not automatically dominate the SFT baseline in the small-sample evaluations. If I reran the lab tomorrow, I would keep this dataset switch, but I would spend more time on stronger benchmark settings and on checking the formatting quality of SFT outputs before trusting downstream DPO gains.

_Suggested note if you used the updated notebook:_ NB1 was switched to `5CD-AI/Vietnamese-alpaca-gpt4-gg-translated`, formatting SFT prompts from `instruction_en` + optional `input_en`, with `output_en` as the assistant target.

---

## 7. Benchmark interpretation (≥ 150 words)

> **Paste `07-benchmark-comparison.png` here** (or link).

Score table from `data/eval/benchmark_results.json`:

| Benchmark | SFT-only | SFT+DPO | Δ |
|---|---:|---:|---:|
| IFEval | 0.000 | 0.000 | +0.000 |
| GSM8K | 1.000 | 1.000 | +0.000 |
| MMLU (sampled) | 0.649 | 0.684 | +0.035 |
| AlpacaEval-lite | 0.500 | 0.300 | -0.200 |

_Interpret the deltas. Which benchmark went up most? Did GSM8K or MATH regress (alignment tax — see deck §8.1)? Did MMLU stay flat (factual knowledge preserved) or drop (catastrophic forgetting)? Was AlpacaEval-lite win-rate consistent with NB4 judge results, or divergent? Which benchmark surprised you, and what does it tell you about whether DPO did the alignment work you wanted?_

The benchmark picture is mixed, and that is actually the most honest outcome of this lab. The strongest positive change in my saved results is MMLU, which increases from about `0.649` to `0.684`, a gain of roughly `+0.035`. That suggests the DPO run did not damage general knowledge performance in this tiny evaluation setting, and it may even have helped the model present answers in a slightly more usable way for multiple-choice style tasks. IFEval stays flat at `0.000`, while GSM8K also stays flat at `1.000`. I need to interpret those two numbers carefully, because I ran them with extremely small limits for practicality, so they are demonstration numbers rather than robust estimates. The most noticeable regression is AlpacaEval-lite, which drops from `0.500` to `0.300`. That result is also consistent with NB4, where SFT won more examples than DPO overall. In other words, my DPO run did move the policy, but it did not clearly improve user-facing helpfulness on this small sample. The lesson for me is that alignment is not just “apply DPO and scores go up”; the data choice, training budget, and evaluation sample size all matter.

---

## Bonus

- [ ] Đã làm β-sweep (rigor add-on +6)
- [x] Đã push lên HuggingFace Hub (Submission Option B, +5)
- [ ] Đã release GGUF với multiple quantizations (+3)
- [ ] Đã link W&B run public (+2)
- [ ] Đã làm cross-judge comparison (+4)
- [ ] Đã làm `BONUS-CHALLENGE.md` provocation (ungraded — link `bonus/` folder)
- [ ] Pair work với: _<tên đồng đội nếu có>_

---

## Điều ngạc nhiên nhất khi làm lab này

DPO không tự động tốt hơn SFT trong mọi trường hợp; với sample nhỏ của mình, SFT vẫn thắng DPO ở một số prompt helpfulness và cả kết quả AlpacaEval-lite. Một điều ngạc nhiên nữa là benchmark bằng `lm-eval` rất chậm, đến mức ngay cả khi giảm xuống `IFEval = 1`, `GSM8K = 1`, `MMLU = 1` thì thời gian chạy vẫn lớn hơn mình kỳ vọng khá nhiều trên Colab L4.
