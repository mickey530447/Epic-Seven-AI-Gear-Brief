# Epic Seven Gear AI

Hệ thống AI chạy **100% local** giúp đánh giá và quản lý gear (trang bị) trong game Epic Seven (Smile Gate), sử dụng [Ollama](https://ollama.com/) làm AI runtime.

> 📄 Tài liệu thiết kế chi tiết (domain knowledge, kiến trúc, prompt templates...) được giữ riêng tư, dùng làm context cho Claude Code CLI khi implement.

## Vấn đề cần giải quyết

1. **Đánh giá gear +15 tồn kho** — batch evaluate toàn bộ gear đã max từ file export của Fribbels Epic 7 Optimizer, xếp hạng và recommend keep/salvage/reforge.
2. **Lọc gear mới đáng upgrade** — đánh giá tiềm năng gear mới drop dựa trên substats ban đầu, rank, set, level.
3. **Hỗ trợ quyết định khi enhance** — tại các mốc +3/+6/+9/+12/+15, đánh giá nên tiếp tục hay dừng (đặc biệt quan trọng: speed check).

## Kiến trúc

Hai workflow chính, đều đổ về một AI Engine (Ollama) cho ra **verdict** (Keep / Salvage / Reforge / Stop):

- **Workflow 1 — Batch evaluate:** `Fribbels gear.txt → JSON Parser → Gear Score Engine → Local AI Engine`
- **Workflow 2 — Realtime evaluate:** `Screenshot (Vision Model) hoặc Manual Input → Parse → Local AI Engine`

### Tech stack

| Component | Technology |
|-----------|-----------|
| AI Runtime | Ollama |
| Vision Model | Qwen3-VL 2B |
| Text/Reasoning Model | Qwen3.5 7B (Q4) |
| Backend/CLI | Python |
| UI (optional) | Streamlit hoặc Gradio |
| Data format | JSON (Fribbels compatible) |

## Specs máy phát triển

- CPU: AMD Ryzen AI 9 HX 370 w/ Radeon 890M
- RAM: 32 GB
- GPU: NVIDIA GeForce RTX 5060 Laptop GPU (8 GB VRAM)

## Implementation phases

1. **Phase 1 — Deterministic Scoring Engine** (không cần AI): parse Fribbels JSON, tính Gear Score, roll efficiency, surplus detection, phân loại Keep/Salvage/Swap/Reforge. Làm trước vì hoàn toàn deterministic và test được.
2. **Phase 2 — Vision Pipeline**: Ollama + Qwen3-VL 2B đọc screenshot gear → structured data.
3. **Phase 3 — AI-Powered Evaluation**: Ollama + Qwen3.5 7B reasoning, đưa ra verdict + giải thích.
4. **Phase 4 — Web UI** (optional): Streamlit/Gradio app với batch upload, screenshot evaluate, manual input, dashboard.
