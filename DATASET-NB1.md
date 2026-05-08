# NB1 Dataset Update

Notebook `colab/Lab22_DPO_T4.ipynb` ở phần `NB1 — SFT-mini: Build the Lab 21 SFT checkpoint inline` đã được đổi sang dataset:

- `5CD-AI/Vietnamese-alpaca-gpt4-gg-translated`

NB1 hiện dùng cặp cột tiếng Anh:

- `instruction_en`
- `input_en`
- `output_en`

## Schema đang dùng

Theo dataset viewer, tập `train` có khoảng `52k rows` và các cột chính nhìn thấy gồm:

| Cột | Vai trò trong NB1 |
| --- | --- |
| `input_en` | Ngữ cảnh bổ sung, nối thêm vào prompt nếu có |
| `input_vi` | Không dùng trong NB1 |
| `instruction_vi` | Không dùng trong NB1 |
| `output_vi` | Không dùng trong NB1 |
| `output_en` | Câu trả lời assistant |
| `instruction_en` | Prompt user chính |

## Mapping trong notebook

NB1 format dữ liệu theo ChatML như sau:

```text
user      <- instruction_en + "\n\n" + input_en (nếu input_en tồn tại)
assistant <- output_en
```

## Preview dữ liệu giống ảnh tham chiếu

| input_en | input_vi | instruction_vi | output_vi | output_en | instruction_en |
| --- | --- | --- | --- | --- | --- |
| `Canada` | `Canada` | `Kể 3 sự kiện lịch sử liên quan đến đất nước...` | `1. Liên bang và mở rộng...` | `1. Confederation and Expansion (1867)...` | `List 3 historical events related to the...` |
| `` | `` | `Nghĩ ra một từ có vần với từ 'fine'` | `Một từ có vần với 'ổn' là 'của tôi'.` | `One word that rhymes with 'fine' is 'mine'.` | `Come up with a word that rhymes with...` |
| `` | `` | `Kể tên ba hoạt động của con người tạo ra...` | `1. Sản xuất công nghiệp...` | `1. Industrial manufacturing...` | `Name three human activities that...` |
| `` | `` | `Chủ đề bài hát 'The Ride' của David Allan...` | `Chủ đề của “The Ride”...` | `The theme of "The Ride" by David Allan...` | `What is the theme of the song 'The Ride'...` |
| `` | `` | `Thuật ngữ địa chất cho một vùng đất bao gồm...` | `Thuật ngữ địa chất để chỉ một vùng đất...` | `The geological term for an area of land...` | `What is the geological term for an area of...` |

## Ghi chú

- Notebook vẫn giữ `SFT_SLICE = 1000` để tương thích flow lab cũ.
- Nếu muốn huấn luyện thuần tiếng Việt sau đó, có thể đổi mapping sang `instruction_vi` và `output_vi`.
- Nếu Colab báo lỗi `tokenizer.chat_template is not set`, notebook hiện đã có fallback ChatML template để `apply_chat_template(...)` vẫn chạy ổn khi tokenizer/adapters bị mất metadata này.
