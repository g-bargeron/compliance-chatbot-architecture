# Internal Compliance Chatbot (PDF-Grounded RAG)

Internal web application for answering questions strictly from a fixed set of compliance PDFs. Responses are generated using retrieval-augmented generation (RAG) and always include document and page citations. If the information is not present in the PDFs, the system is designed to say so.

This repository shows the overall structure and key design decisions.

## Implementation Repository
The full implementation (backend service, indexing pipeline, and runtime logic) is maintained separately:

https://github.com/g-bargeron/internal-compliance-chatbot/tree/main

Access available on request. 

## Problem This Solves
Compliance documents tend to be large, static, and hard to search in a reliable way. This tool makes them queryable while keeping answers deterministic and traceable back to the source material.

## Architecture (High Level)
The backend is a small FastAPI service that loads a pre-built PDF embedding index into memory at startup. PDFs are processed offline (text extraction only, no OCR), chunked with document and page tags, embedded, and stored in a simple vector index.

At runtime, user questions are embedded and matched against the index using cosine similarity. Only the retrieved chunks are passed to the language model, which is instructed to return structured answers with citations. The frontend is a static single-page UI served separately behind a reverse proxy.

## Tech Stack
- Python / FastAPI
- OpenAI embeddings and chat models
- Static HTML, CSS, and JavaScript
- Reverse proxy (e.g., Nginx)

## Constraints and Non-Goals
- The model has no access to information outside the retrieved PDF excerpts
- Documents are not modified or re-indexed at runtime
- The embedding index is treated as sensitive data, equivalent to the source PDFs
- Built for internal use; authentication and multi-tenant support were intentionally left out

## API Overview
- `GET /health` — basic health check  
- `GET /pdfs_list` — list of available source documents  
- `POST /chat` — question + history → citation-backed answer  

## Implementation
Designed to be used internally as a read-only compliance reference tool. It is not intended to function as a general-purpose chatbot.

## Notes
A simple deterministic extractor is included as a fallback for structured data (emails, phone numbers, URLs) when semantic retrieval is not sufficient.
