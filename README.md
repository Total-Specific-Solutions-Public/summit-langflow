# Workshop Flows — Quick Start

These flows run on our own AI server using two custom components: an **AI LLM** component and an **embedding** component. Whatever flow you import, the setup is the same idea:

> **Plug your pre-seeded variables into the matching fields.** You don't type API keys or connection strings — they're already in your account.

## Before you start

- Import a flow: drag its JSON into the panel on [flow.paip.nl](https://flow.paip.nl).
- Your account already has the variables below loaded. In a field, click the **variable icon** (🌐) and pick the one you need.

## The variables you'll use

| Variable | What it is | Where it goes |
|---|---|---|
| `APIKEY` | API key for our AI LLM + embedding server | **Every** `OpenAI API Key` field (LLM and Embeddings) |
| `kimi-k2.6` / `gpt-5.3-codex` | LLM model name (pick one) | **OpenAI** → `Model Name` |
| `Text-embedding-3-small` | Embedding model name | **OpenAI Embeddings** → `Model` |
| `DATABASE` | PostgreSQL + pgvector connection string | **PGVector** → `PostgreSQL Server Connection String` |

> Only flows that use a vector database (RAG) need `DATABASE` and `Text-embedding-3-small`. A plain LLM flow only needs `APIKEY` and a model name.

## The rule for wiring any flow

No matter which components a flow has, fill the fields the same way:

| Field (on any component) | What to put |
|---|---|
| `OpenAI API Key` | `APIKEY` |
| `Model Name` (LLM) | `kimi-k2.6` or `gpt-5.3-codex` |
| `Model` (Embeddings) | `Text-embedding-3-small` |
| `PostgreSQL Server Connection String` | `DATABASE` |
| `Temperature` | `0`–`1` — lower = more factual, higher = more creative |
| `System Message` | *(optional)* instructions for tone/role |
| `Template` (Prompt) | Keep the `{variables}` in `{curly braces}` — they're filled automatically |

Standard Langflow components (Chat Input/Output, Split Text, If-Else, Parser, etc.) need no credentials — just connect them and set their own options.

Then open the **Playground** (top right) to run the flow.

## If your flow uses a vector database (RAG)

RAG flows come in two parts and **must be run in order**:

1. **Load Data Flow** — reads a file, splits it into chunks, embeds them, and stores them in your database. Upload your file in **Read File**, then click **Run** on the **PGVector** component.
2. **Retriever Flow** — answers questions from the stored data via the Playground.

For these to work:

- Use the **same embedding model** (`Text-embedding-3-small`) in both flows.
- Use the **same `Table` name** (e.g. `rag_table`) in both PGVector components.
- Point both at the same `DATABASE`.

## Tips

- **API key field empty?** Almost every error is a missing `APIKEY` — check each LLM and Embeddings component.
- **RAG returns nothing?** Run the Load Data Flow first, and confirm both flows share the same `DATABASE`, table name, and embedding model.
