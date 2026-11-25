# Giai đoạn 5b: AI / LLM System Design

[← Quay lại Roadmap tổng](../roadmap.md)

> Phần này hợp với hướng **RAG / LangChain** bạn đang làm.

## 1. Thời lượng
- **2 tuần** (Tuần 19–20), **~18 giờ** (~9 giờ/tuần).

## 2. Nguồn tài liệu
- **`[W]`** Bài hướng dẫn trả lời đề AI system design trên **KDNuggets** *(AI chatbot, hệ thống hỏi đáp tài liệu/RAG, AI coding agent, voice agent)*
- **`[W]`** Hướng dẫn GenAI/LLM system design 2026 trên [dev.to/PracHub](http://dev.to/PracHub) *(token cost, semantic caching, streaming, guardrails, tracing)*
- **`[W]`** Ghi chú *"System Design for AI Interviews"* của **bhaskarjha-com** *(evaluation, observability, safety boundaries, cost)*
- **`[W]`** Tài liệu **LangChain**, vector database (**pgvector**, **Qdrant**), và phần **Evaluation** của framework bạn đang dùng

---

## Tài liệu bài học chi tiết trong Giai đoạn 5b

| Phần | Nội dung | File bài học |
| :---: | :--- | :--- |
| **3** | **Concepts:** Kiến trúc RAG (Chunking, Hybrid Search, Reranking), Serving LLM (Streaming, Batching, Semantic Cache, Token Rate Limit, Fallback), AI Agent & Guardrails, Evaluation (`Recall@K`, `Faithfulness`, Regression Test), Tối ưu Chi phí & Độ trễ | [01-concepts.md](./01-concepts.md) |
| **4** | **Thực hành:** 2 bản thiết kế trên giấy kèm ước lượng Token Cost (*Chatbot tài liệu 100k users* & *RAG CSKH TechStore*) + code mẫu nâng cấp RAG lên Production (Semantic Cache, SSE Streaming & Metrics, `Recall@K`/`MRR` Evaluator) | [02-thuc-hanh-rag-production.md](./02-thuc-hanh-rag-production.md) |
| **5** | **Mục tiêu & Mốc Tuần 20:** Kịch bản thuyết trình RAG + Chi phí 10 phút, Playbook 5 tầng Debug khi RAG trả lời sai, Tư duy KISS & Checklist Portfolio tổng kết 20 tuần | [03-muc-tieu-va-check-tuan20.md](./03-muc-tieu-va-check-tuan20.md) |

---

## 3. Concepts (Tóm tắt checklist)
- [ ] **Kiến trúc RAG:** chunking, embedding, vector store, hybrid search, reranking
- [ ] **Serving LLM:** streaming, batching, semantic cache, rate limit theo token, fallback model
- [ ] **Agent:** orchestrator, tool calling, sandbox, policy/guardrails
- [ ] **Đánh giá:** retrieval quality, answer faithfulness, tracing, regression test cho prompt
- [ ] **Chi phí và độ trễ:** chọn model, cache, giới hạn context

## 4. Thực hành
- [ ] Thiết kế trên giấy: *"Chatbot hỏi đáp tài liệu cho 100k người dùng"* (có sơ đồ, ước lượng chi phí token).
- [ ] Thiết kế trên giấy: *"Hệ thống RAG hỗ trợ khách hàng"* (có sơ đồ, ước lượng chi phí token).
- [ ] Nâng **RAG hiện có** lên mức production: thêm semantic cache, streaming, đo latency/chi phí mỗi request, bộ test đánh giá retrieval.

## 5. Mục tiêu đạt được
- [ ] Vẽ và giải thích được kiến trúc RAG trong 10 phút, có phần chi phí
- [ ] Nêu được cách debug khi câu trả lời sai dù tài liệu đúng có tồn tại
- [ ] Biết giữ thiết kế đơn giản trước, rồi mới thêm thành phần khi có lý do

## Mốc kiểm tra liên quan
- [ ] **Tuần 20:** Mock tổng hợp gồm 1 đề AI — Sản phẩm: *Hồ sơ portfolio và bản thiết kế RAG*

---

## Ghi chú & Bản thiết kế RAG
<!-- Ghi lại ước lượng token cost, kết quả benchmark TTFT/cost và Recall@5 sau nâng cấp tại đây -->
