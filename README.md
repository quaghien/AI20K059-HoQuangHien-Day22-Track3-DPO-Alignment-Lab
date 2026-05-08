# Day 22 Submission Repo

**Họ tên:** Hồ Quang Hiển  
**MSSV:** 2A202600059

Repo này là bài nộp Lab Day 22 về DPO/ORPO Alignment. Mình chạy theo Colab path với notebook chính:

- [colab/Lab22_DPO_T4.ipynb](/home/quanghien/day22/AI20K059-HoQuangHien-Day22-Track3-DPO-Alignment-Lab/colab/Lab22_DPO_T4.ipynb)

## Nội dung nộp

- Notebook đã chạy: `colab/Lab22_DPO_T4.ipynb`
- Reflection: [submission/REFLECTION.md](/home/quanghien/day22/AI20K059-HoQuangHien-Day22-Track3-DPO-Alignment-Lab/submission/REFLECTION.md)
- Screenshots: [submission/screenshots](/home/quanghien/day22/AI20K059-HoQuangHien-Day22-Track3-DPO-Alignment-Lab/submission/screenshots)
- Preference + evaluation artifacts: [data](/home/quanghien/day22/AI20K059-HoQuangHien-Day22-Track3-DPO-Alignment-Lab/data)
- Rubric tham chiếu: [rubric.md](/home/quanghien/day22/AI20K059-HoQuangHien-Day22-Track3-DPO-Alignment-Lab/rubric.md)

## Các file chính trong repo

- `data/pref/train.parquet`, `data/pref/eval.parquet`
- `data/eval/judge_results.json`
- `data/eval/side_by_side.jsonl`
- `data/eval/benchmark_results.json`
- `data/eval/deploy_meta.json`
- `submission/screenshots/01-setup-gpu.png`
- `submission/screenshots/02-sft-loss.png`
- `submission/screenshots/03-dpo-reward-curves.png`
- `submission/screenshots/04-side-by-side-table.png`
- `submission/screenshots/05-judge-output.png`
- `submission/screenshots/06-gguf-smoke.png`
- `submission/screenshots/07-benchmark-comparison.png`

## Hugging Face artifacts

Do weights lớn không đưa vào git, mình host trên Hugging Face:

- SFT-mini adapter: <https://huggingface.co/wanhin/lab22-sft-mini>
- DPO adapter: <https://huggingface.co/wanhin/lab22-dpo-vn>
- Merged FP16 model: <https://huggingface.co/wanhin/lab22-dpo-vn-merged>
- GGUF Q4_K_M: <https://huggingface.co/wanhin/lab22-dpo-vn-gguf>

Chi tiết link cũng được ghim tại:

- [adapters/README.md](/home/quanghien/day22/AI20K059-HoQuangHien-Day22-Track3-DPO-Alignment-Lab/adapters/README.md)
- [gguf/README.md](/home/quanghien/day22/AI20K059-HoQuangHien-Day22-Track3-DPO-Alignment-Lab/gguf/README.md)

## Checklist tự kiểm

- Có notebook Colab đã chạy và giữ output
- Có đủ screenshots theo rubric
- Có `data/pref/` và `data/eval/`
- Có `submission/REFLECTION.md`
- Có link HF cho các model artifacts lớn

## Ghi chú

- Repo này ưu tiên giữ phần cần chấm và phần dễ review trên GitHub.
- Các artifact model nặng được thay bằng link Hugging Face để repo gọn hơn.
