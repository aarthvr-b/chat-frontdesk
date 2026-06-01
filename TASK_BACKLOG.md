# Task Backlog

Execution tracker for the AI-first pivot.

Use this file as the working checklist.
Use `IMPLEMENTATION_PLAN.md` for full rationale, learning goals, and higher-level guidance.

## Status Guide

- `[ ]` Not started
- `[x]` Done

## Current Focus

- [ ] Choose next active task
- [ ] Define acceptance checks before implementation
- [ ] Record notes or decisions after finishing the task

## Next Up

- [ ] 1.2 Reorganize project modules for AI features
- [ ] 1.3 Extend schemas for AI workflows
- [ ] 2.1 Add model configuration layer

## Blocked / Open Questions

- None currently recorded

---

## Phase 1 - Prepare The Existing Project For The Pivot

Goal: reshape the current deterministic MVP into a clean base for AI workflows.

### 1.1 Audit And Document Current Reusable Components

Short description: document what stays, what changes, and what becomes non-core in the AI-first architecture.

- [x] Main task complete
- [x] Rewrite `README.md` for the AI-first direction
- [x] Rewrite `IMPLEMENTATION_PLAN.md` as phased roadmap
- [x] Mark reusable current components clearly
- [x] Mark deterministic router as baseline / fallback rather than core logic
- [x] Review docs for internal consistency

### 1.2 Reorganize Project Modules For AI Features

Short description: create clear homes for prompts, LLM calls, retrieval, tools, memory, and evaluation work.

- [ ] Main task complete
- [ ] Decide target folder layout for new AI modules
- [ ] Add missing top-level folders such as `prompts/` and `evals/` if needed
- [ ] Keep existing FastAPI structure clean and understandable
- [ ] Confirm each new module boundary has one responsibility
- [ ] Manually review project tree after changes

### 1.3 Extend Business Schemas Without Breaking Existing Data

Short description: preserve `GymProfile` as business truth while adding typed models for AI workflows.

- [ ] Main task complete
- [ ] Separate business schemas from LLM output schemas
- [ ] Add message/session schema models
- [ ] Add retrieval chunk / tool result schema models where needed
- [ ] Confirm current `gym_profile.json` still validates
- [ ] Manually review schema clarity and naming

---

## Phase 2 - Build A Basic LLM-Powered Assistant

Goal: replace deterministic routing as the main interaction layer with an LLM-driven assistant grounded in current gym data.

### 2.1 Add Configuration For Model Access

Short description: centralize provider settings and secrets so model integration is explicit and swappable.

- [ ] Main task complete
- [ ] Add app config for model provider settings
- [ ] Add `.env.example` or equivalent config documentation
- [ ] Keep provider-specific settings out of feature modules
- [ ] Verify startup behavior with valid config
- [ ] Verify failure behavior with missing config

### 2.2 Create A Dedicated LLM Service

Short description: isolate raw model API calls behind one service boundary.

- [ ] Main task complete
- [ ] Add `llm_service` interface
- [ ] Handle request / response parsing cleanly
- [ ] Add basic provider error handling
- [ ] Add at least one direct service test path
- [ ] Manually verify a simple prompt round trip

### 2.3 Write The First System Prompt

Short description: define the assistant’s scope, tone, grounding rules, and fallback behavior.

- [ ] Main task complete
- [ ] Create base system prompt file
- [ ] State supported assistant role clearly
- [ ] Instruct model not to invent missing business facts
- [ ] Define fallback behavior for unsupported questions
- [ ] Review prompt against realistic gym queries

### 2.4 Add Structured Output For Assistant Decisions

Short description: require typed outputs instead of relying on raw free-form responses.

- [ ] Main task complete
- [ ] Define response schema for assistant output
- [ ] Validate model output with Pydantic
- [ ] Handle malformed model output safely
- [ ] Test valid structured response path
- [ ] Test invalid structured response path

### 2.5 Build A First Assistant Orchestrator Using Only GymProfile

Short description: produce useful answers using only the current structured gym profile as grounding context.

- [ ] Main task complete
- [ ] Load `GymProfile` into assistant workflow
- [ ] Assemble prompt context from structured data
- [ ] Call LLM service and validate output
- [ ] Return final user-facing answer
- [ ] Manually test pricing, hours, classes, and location flows

---

## Phase 3 - Add Tool Calling And Controlled Actions

Goal: let the assistant request trusted business operations instead of depending only on prompt context.

### 3.1 Identify The First Trusted Tools

Short description: define a small, safe tool surface for business fact lookups and later actions.

- [ ] Main task complete
- [ ] List first read-oriented tools
- [ ] Define tool inputs and outputs
- [ ] Keep tool scope intentionally small
- [ ] Review whether each tool represents trusted business logic

### 3.2 Implement The Tool Execution Layer

Short description: create backend functions the assistant can call safely.

- [ ] Main task complete
- [ ] Add tool service module
- [ ] Return validated structured results from each tool
- [ ] Reuse `GymProfile` / knowledge service where possible
- [ ] Add direct tests for each tool path
- [ ] Manually verify invalid input handling

### 3.3 Let The LLM Choose Between Direct Answer And Tool Use

Short description: support a simple execution loop where the model may answer directly or request a tool.

- [ ] Main task complete
- [ ] Extend assistant workflow for tool requests
- [ ] Execute tool calls safely
- [ ] Re-prompt model with tool results when needed
- [ ] Add loop limits or equivalent guard against runaway execution
- [ ] Manually test direct-answer and tool-using scenarios

### 3.4 Add A Simple Lead Capture Tool

Short description: demonstrate a practical action workflow, not only informational answers.

- [ ] Main task complete
- [ ] Define lead capture schema
- [ ] Choose simple local persistence for leads
- [ ] Add clarification behavior for incomplete lead data
- [ ] Test successful lead capture path
- [ ] Test incomplete or ambiguous lead path

---

## Phase 4 - Add RAG And Richer Knowledge

Goal: expand beyond one JSON profile into retrieval-backed grounded answers.

### 4.1 Define Additional Knowledge Sources

Short description: separate structured business facts from richer unstructured reference content.

- [ ] Main task complete
- [ ] Create `knowledge_docs` area or equivalent
- [ ] Decide what stays in `gym_profile.json`
- [ ] Add first unstructured business docs
- [ ] Review whether knowledge boundaries are still clear

### 4.2 Build A Document Ingestion Pipeline

Short description: transform raw docs into retrieval-ready chunks with useful metadata.

- [ ] Main task complete
- [ ] Choose source document format
- [ ] Implement chunking strategy
- [ ] Preserve source metadata
- [ ] Inspect generated chunks manually
- [ ] Adjust chunking if chunks are too broad or too thin

### 4.3 Add Embeddings And Vector Storage

Short description: make knowledge searchable by semantic similarity.

- [ ] Main task complete
- [ ] Choose embeddings model and simple vector store
- [ ] Store text plus metadata plus embedding references
- [ ] Add retrieval query path
- [ ] Manually inspect relevance of top matches
- [ ] Review poor-match cases

### 4.4 Inject Retrieved Context Into Assistant Answers

Short description: feed relevant retrieved chunks into the answer workflow.

- [ ] Main task complete
- [ ] Retrieve top relevant chunks per user query
- [ ] Add retrieved context to prompt assembly
- [ ] Keep prompt context bounded
- [ ] Test doc-backed questions end to end
- [ ] Test irrelevant questions and safe fallback

### 4.5 Evaluate Retrieval Quality Separately From Answer Quality

Short description: distinguish retrieval failures from generation failures.

- [ ] Main task complete
- [ ] Define retrieval-focused evaluation questions
- [ ] Record expected source docs or topics
- [ ] Review retrieval hits manually
- [ ] Note false positives and missing matches
- [ ] Update retrieval strategy if needed

---

## Phase 5 - Add Conversation Memory

Goal: support short multi-turn conversations and follow-up questions.

### 5.1 Define Conversation State Schema

Short description: create explicit types for messages, sessions, and stored context.

- [ ] Main task complete
- [ ] Define message models
- [ ] Define session / memory models
- [ ] Keep schema minimal but useful
- [ ] Review whether follow-up question support is covered

### 5.2 Add A Simple Memory Store

Short description: persist recent state between requests for the same user/session.

- [ ] Main task complete
- [ ] Choose simple persistence approach
- [ ] Store recent turns with session identity
- [ ] Add load/save interface
- [ ] Test state persistence across multiple requests
- [ ] Review retention limits

### 5.3 Use Memory In Prompt Assembly

Short description: include relevant recent context instead of treating each message as isolated.

- [ ] Main task complete
- [ ] Add recent-turn retrieval to assistant workflow
- [ ] Include only relevant or recent context
- [ ] Keep prompt size controlled
- [ ] Test follow-up question flows
- [ ] Compare behavior with and without memory

### 5.4 Add Conversation Summarization For Longer Threads

Short description: compress older context so memory remains useful without prompt bloat.

- [ ] Main task complete
- [ ] Decide when summarization should happen
- [ ] Keep recent raw turns plus compact summary
- [ ] Preserve business-relevant context in summary
- [ ] Test longer conversations
- [ ] Review whether summaries lose critical facts

---

## Phase 6 - Add Guardrails, Observability, And Evals

Goal: make the assistant safer, easier to debug, and easier to improve.

### 6.1 Add Output Guardrails

Short description: validate final answers against business and safety constraints before sending them.

- [ ] Main task complete
- [ ] Add post-generation validation checks
- [ ] Define safe fallback path on invalid output
- [ ] Test malformed output handling
- [ ] Test unsupported-question behavior

### 6.2 Add Prompt / Retrieval / Tool Tracing

Short description: capture enough trace data to inspect why a response happened.

- [ ] Main task complete
- [ ] Log prompt inputs at useful level
- [ ] Log retrieved chunks and tool calls
- [ ] Log structured outputs and final response
- [ ] Review trace readability manually
- [ ] Ensure secrets are not logged

### 6.3 Build A Small Evaluation Dataset

Short description: create a repeatable benchmark of realistic user messages.

- [ ] Main task complete
- [ ] Add simple factual questions
- [ ] Add follow-up and retrieval-backed questions
- [ ] Add out-of-scope and lead-capture questions
- [ ] Define expected behavior per case
- [ ] Review realism of evaluation set

### 6.4 Add An Evaluation Runner

Short description: make it easy to rerun a fixed benchmark after prompt or workflow changes.

- [ ] Main task complete
- [ ] Build local eval runner
- [ ] Record outputs and pass/fail notes
- [ ] Re-run after at least one prompt or workflow change
- [ ] Review regressions manually

### 6.5 Compare AI Assistant Against Deterministic Baseline

Short description: use the old router-based flow as a benchmark rather than deleting it blindly.

- [ ] Main task complete
- [ ] Run overlapping scenarios through both systems
- [ ] Compare correctness and flexibility
- [ ] Compare failure behavior
- [ ] Record conclusions in docs or notes

---

## Phase 7 - WhatsApp Integration And Demo Hardening

Goal: connect the assistant to the real user channel and prepare a strong portfolio demo.

### 7.1 Implement Webhook Message Intake

Short description: parse inbound WhatsApp messages into internal typed message objects.

- [ ] Main task complete
- [ ] Add webhook route
- [ ] Parse supported inbound payload shape
- [ ] Normalize inbound data into internal models
- [ ] Test valid sample payloads
- [ ] Test malformed payload handling

### 7.2 Connect Webhook To Assistant Workflow

Short description: wire inbound messages into the AI assistant pipeline.

- [ ] Main task complete
- [ ] Route inbound message to assistant entrypoint
- [ ] Connect session identity to memory layer
- [ ] Return safe operational response on failures
- [ ] Test local end-to-end message processing

### 7.3 Implement Outbound WhatsApp Replies

Short description: send final assistant replies back through the WhatsApp API.

- [ ] Main task complete
- [ ] Add outbound WhatsApp service wrapper
- [ ] Keep channel-specific logic separate from assistant logic
- [ ] Test successful outbound send path
- [ ] Test provider failure handling

### 7.4 Prepare A Demo-Ready Scenario Set

Short description: harden the most important product and interview flows.

- [ ] Main task complete
- [ ] Define core demo conversations
- [ ] Verify traces are usable during demo review
- [ ] Re-run key evaluation scenarios
- [ ] Record demo notes or walkthrough points

---

## Completed Milestones

- [x] AI-first `README.md` created
- [x] phased `IMPLEMENTATION_PLAN.md` created
- [x] dedicated `TASK_BACKLOG.md` created
