# Workshop Flows — Quick Start

These flows run on our own AI server using a custom **LiteLLM Proxy** component. Whatever flow you import, the setup is the same idea:

> **Plug your pre-seeded variables into the matching fields.** You don't type API keys or connection strings — they're already in your account.

## Before you start

- Import a flow: drag its JSON into the panel on [flow.paip.nl](https://flow.paip.nl).
- Your account already has the variables below loaded. In a field, click the **variable icon** (🌐) and pick the one you need.

A flow is just components wired together — here a Chat Input feeds an LLM, which feeds a Chat Output:

![A simple flow: Chat Input → LLM → Chat Output](img/Example3.png)

## Available flows and components

It contains two folders:

- `template-flows/` — two example flows you can use as a starting point:
  - `Starter.json` — simple chat: Chat Input → LiteLLM Proxy → Chat Output
  - `RAG.json` — full RAG pipeline: load a file into a vector database, then answer questions from it
- `template-components/` — one custom component:
  - `gemma4.json` — a LiteLLM Proxy component for making calls to our AI server via a proxy with virtual key authentication

## The variables you'll use

| Variable | What it is | Where it goes |
|---|---|---|
| `LiteLLM_URL` | URL of our LiteLLM proxy server | **LiteLLM Proxy** → `LiteLLM Proxy URL` |
| `GEMMA_APIKEY` | Virtual key for the proxy | **LiteLLM Proxy** → `Virtual Key` |
| `Gemma` | Our text/OCR model | **LiteLLM Proxy** → `Model Name` |
| `embeddings` | Our embedding model | **OpenAI Embeddings** → `Model` |
| `DATABASE` | PostgreSQL + pgvector connection string | **PGVector** → `PostgreSQL Server Connection String` |

> Only the RAG flow needs `embeddings` and `DATABASE`. The Starter flow only needs `LiteLLM_URL`, `GEMMA_APIKEY`, and `Gemma`.

## The rule for wiring any flow

| Field (on any component) | What to put |
|---|---|
| `LiteLLM Proxy URL` | `LiteLLM_URL` |
| `Virtual Key` | `GEMMA_APIKEY` |
| `Model Name` | `Gemma` |
| `Model` (Embeddings) | `embeddings` |
| `PostgreSQL Server Connection String` | `DATABASE` |
| `Temperature` | `0`–`1` — lower = more factual, higher = more creative |
| `System Message` | *(optional)* instructions for tone/role |
| `Template` (Prompt) | Keep the `{variables}` in `{curly braces}` — they're filled automatically |

Here the Starter flow is fully configured — `LiteLLM_URL` and `GEMMA_APIKEY` filled in as variables, and a model name typed into the Model Name field:

![The Starter flow: Chat Input wired to LiteLLM Proxy with LiteLLM_URL and GEMMA_APIKEY filled in, connected to Chat Output](img/Example1.png)

Standard Langflow components (Chat Input/Output, Split Text, If-Else, Parser, etc.) need no credentials — just connect them and set their own options.

Then open the **Playground** (top right) to run the flow.

## RAG flow

The RAG flow has two parts and **must be run in order**:

1. **Load Data** — reads a file, splits it into chunks, embeds them, and stores them in your database. Upload your file in **Read File**, then click **Run** on the **PGVector** component.

   > **Maximum file size: 1 MB.**

2. **Retriever** — answers questions from the stored data via the Playground.

Both parts are in the same flow file. Here is an overview of the full RAG flow:

![Overview of the full RAG flow showing the Load Data and Retriever parts](img/Example2.png)

For these to work:

- Use the **same embedding model** (`Text-embedding-3-small`) in both parts.
- Use the **same `Table` name** (e.g. `rag_table`) in both PGVector components.
- Point both at the same `DATABASE`.

## Tips

- **Flow not responding?** Check that `LiteLLM_URL` and `GEMMA_APIKEY` are filled in on every LiteLLM Proxy component.
- **RAG returns nothing?** Run the Load Data part first, and confirm both parts share the same `DATABASE`, table name, and embedding model.
- **Build your own components.** Click the **Code** button (`</>`) on any component to open its Python source and edit the logic. You can also paste that code into an LLM to help you modify or create components from scratch.
