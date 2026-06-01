# Chat Frontdesk

AI-first WhatsApp gym assistant built as a realistic portfolio project.

This project started as a deterministic gym front desk assistant: structured gym data, intent routing, and controlled replies. The new direction keeps that useful foundation, but pivots the product into a modern AI application that demonstrates practical LLM engineering skills without turning into vague chatbot complexity.

The goal is still simple:

- receive WhatsApp messages from gym prospects or members
- answer common front desk questions accurately
- stay grounded in real gym data
- guide the user toward useful next steps like visiting, trialing, or contacting the gym

The difference is architectural:

- old direction: deterministic router as core intelligence
- new direction: LLM-centered assistant with structured control, retrieval, validation, tools, memory, tracing, and evaluations

## Why This Project Exists

This repo is meant to teach and demonstrate how to build an AI application, not just how to call an API.

The project is intentionally designed to showcase interview-relevant and CV-relevant skills such as:

- LLM integration
- prompt design
- structured outputs
- Pydantic validation
- retrieval-augmented generation (RAG)
- embeddings and vector search
- tool/function calling
- conversation memory
- guardrails
- observability and tracing
- evaluation-driven iteration

The target outcome is a portfolio project that is:

- small enough for one developer
- concrete enough to demo
- modern enough to discuss in interviews
- structured enough to extend phase by phase

## Product Vision

Build an AI gym assistant for WhatsApp that behaves like a reliable digital front desk.

It should handle questions like:

- "What are your opening hours?"
- "How much is the monthly membership?"
- "Do you offer a free trial?"
- "What classes do you have?"
- "Where are you located?"
- "I want to join"
- follow-up questions that depend on previous messages

Unlike the original deterministic MVP, the assistant should be able to:

- understand more natural language variation
- respond in a more conversational way
- use retrieval for richer gym knowledge
- call tools when structured business facts are needed
- maintain short conversation context
- stay auditable and safe through validation and guardrails

## Architecture Direction

The new architecture should remain clean and understandable.

### Core Idea

Use the LLM as the reasoning and response layer, but keep important business constraints outside the model when possible.

That means:

- structured gym facts remain in owned application data
- Pydantic models validate both business data and model outputs
- retrieval supplies grounded context
- tools expose trusted operations
- memory is scoped and intentional
- tracing and evals make behavior observable

### Proposed High-Level Flow

1. WhatsApp webhook receives user message.
2. Backend loads conversation context and relevant gym knowledge.
3. Assistant workflow builds a prompt with:
   - system instructions
   - business rules
   - retrieved context
   - recent conversation memory
4. LLM produces either:
   - a structured direct answer
   - a tool call request
   - a guarded fallback / escalation response
5. Application validates outputs with Pydantic.
6. Response is logged with traces and evaluation-friendly metadata.
7. Reply is sent back through WhatsApp.

## What Should Be Reused

Large parts of current project still matter.

### Reuse Mostly Unchanged

- FastAPI app structure in [app/main.py](/Users/arthur_b/Lab/chat-frontdesk/app/main.py)
- route organization such as [app/routes/health.py](/Users/arthur_b/Lab/chat-frontdesk/app/routes/health.py)
- Pydantic schema approach in [app/models/schemas.py](/Users/arthur_b/Lab/chat-frontdesk/app/models/schemas.py)
- JSON-based gym data in [app/data/gym_profile.json](/Users/arthur_b/Lab/chat-frontdesk/app/data/gym_profile.json)
- profile loading and validation pattern in [app/services/knowledge_service.py](/Users/arthur_b/Lab/chat-frontdesk/app/services/knowledge_service.py)
- explicit error handling around malformed or missing knowledge data

### Reuse But Evolve

- `GymProfile` schema:
  keep as canonical structured business profile, then extend for richer knowledge fields
- knowledge loading:
  keep JSON loading, then add document ingestion and chunking for retrieval
- deterministic routing:
  keep as fallback or safety layer, not primary intelligence

### Replace As Primary Mechanism

- rule-based intent routing in [app/services/intent_router.py](/Users/arthur_b/Lab/chat-frontdesk/app/services/intent_router.py)

The current router is useful as:

- a baseline
- a safety fallback
- a comparison point during evaluation

But it should no longer be the main product architecture.

## New Components To Introduce

These are the new AI-first building blocks and why they exist.

### LLM Client Layer

Purpose:
- central place to call the model
- manage prompts, model settings, retries, and response parsing

Learning value:
- production-style LLM integration
- request/response handling

### Structured Output Models

Purpose:
- force model outputs into predictable shapes
- separate "reasoning result" from final message text

Learning value:
- schema-driven prompting
- output validation with Pydantic

### Retrieval Layer

Purpose:
- answer beyond the limited JSON profile
- ground replies in richer business documents such as class descriptions, FAQ, policies, trainer notes, onboarding info

Learning value:
- embeddings
- chunking
- vector similarity
- context assembly

### Tool Calling Layer

Purpose:
- let the model request trusted operations like:
  - fetch gym profile facts
  - get pricing
  - get class list
  - create a lead / contact request
  - hand off to human flow later if added

Learning value:
- function calling
- separating reasoning from execution

### Conversation Memory

Purpose:
- preserve short context across turns
- support follow-up questions without re-sending everything

Learning value:
- session state
- memory window design
- summarization tradeoffs

### Guardrails

Purpose:
- prevent fabricated business claims
- constrain unsupported actions
- handle out-of-scope questions safely

Learning value:
- application-level safety
- validation and fallback design

### Observability

Purpose:
- trace prompt inputs, retrieval context, tool calls, outputs, and failures

Learning value:
- debugging AI systems
- prompt iteration with evidence

### Evaluations

Purpose:
- measure whether system improves over time
- compare deterministic baseline vs AI workflow

Learning value:
- dataset-based evaluation
- regression testing for LLM apps

## Suggested AI-First Stack

- Python
- FastAPI
- Pydantic
- WhatsApp Cloud API
- JSON gym profile data
- LLM API
- embeddings model
- vector database
- tracing / observability tooling

Keep stack choices pragmatic. Prefer tools that are easy to run and easy to explain.

Good project principles:

- one backend service
- minimal infrastructure at first
- local-first development
- typed interfaces everywhere possible
- deterministic controls around model behavior

## Recommended Target Architecture

```text
app/
  main.py
  routes/
    health.py
    webhook.py
  models/
    schemas.py              # business schemas
    llm_outputs.py          # structured model outputs
    messages.py             # webhook / chat state models
  services/
    knowledge_service.py    # existing JSON profile loader
    retrieval_service.py    # embeddings + vector search
    llm_service.py          # model calls
    prompt_service.py       # prompt assembly
    tool_service.py         # tool definitions / execution
    memory_service.py       # session memory
    assistant_service.py    # orchestration workflow
    whatsapp_service.py     # outbound messages
    guardrail_service.py    # validation / safe fallback
    eval_service.py         # local evaluation runner
  data/
    gym_profile.json
    knowledge_docs/
  prompts/
    system_prompt.md
    answer_prompt.md
  tests/
  evals/
  scripts/
```

## Learning Outcomes By Stage

### Stage 1: Controlled LLM Assistant

You learn:

- how to wrap an LLM in a clean service layer
- how to use structured outputs
- how to keep responses grounded in known gym data

### Stage 2: Tool-Using Assistant

You learn:

- how function/tool calling changes architecture
- how to keep trusted business facts outside the model

### Stage 3: RAG Assistant

You learn:

- document ingestion
- embeddings
- chunking
- vector retrieval
- prompt grounding

### Stage 4: Stateful Assistant

You learn:

- conversation memory design
- session context handling
- follow-up question support

### Stage 5: Production-Style AI App

You learn:

- tracing
- evaluations
- guardrails
- failure analysis

## Current Status

Current repository already contains a useful non-AI base:

- FastAPI app bootstrapping
- health route
- `GymProfile` schemas
- JSON profile data
- validated knowledge loading
- initial deterministic intent router

This means the project does not need a restart. It needs a pivot:

- preserve the backend skeleton
- preserve the structured gym knowledge layer
- preserve validation discipline
- replace deterministic routing as the center of the system
- add modern AI workflow components incrementally

## Design Principles For The Pivot

- Reuse existing code when it still provides clarity.
- Add AI components only when they teach something meaningful.
- Prefer explicit workflows over "magic agent" behavior.
- Keep business facts source-controlled and inspectable.
- Treat validation, tracing, and evaluation as first-class features.
- Build progressively from simple assistant to richer AI system.

## Roadmap

Detailed implementation roadmap lives in [IMPLEMENTATION_PLAN.md](/Users/arthur_b/Lab/chat-frontdesk/IMPLEMENTATION_PLAN.md).

The roadmap is organized as learning phases so each task can be completed as a focused development session.
