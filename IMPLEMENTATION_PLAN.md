# IMPLEMENTATION_PLAN.md

## 1. Purpose Of This Plan

This roadmap pivots the project from a deterministic WhatsApp gym assistant into an AI-first gym assistant while preserving useful existing work.

The plan is intentionally structured as:

- progressive phases
- small, concrete tasks
- one learning session per task
- portfolio-relevant AI engineering concepts

The target is not "maximum features." The target is a clean, understandable AI application that demonstrates modern engineering patterns and remains achievable by one developer.

---

## 2. What Exists Today

Current repository already provides a solid foundation:

- FastAPI application bootstrap in [app/main.py](/Users/arthur_b/Lab/chat-frontdesk/app/main.py)
- health route in [app/routes/health.py](/Users/arthur_b/Lab/chat-frontdesk/app/routes/health.py)
- Pydantic business schemas in [app/models/schemas.py](/Users/arthur_b/Lab/chat-frontdesk/app/models/schemas.py)
- structured gym profile JSON in [app/data/gym_profile.json](/Users/arthur_b/Lab/chat-frontdesk/app/data/gym_profile.json)
- validated knowledge loading in [app/services/knowledge_service.py](/Users/arthur_b/Lab/chat-frontdesk/app/services/knowledge_service.py)
- early deterministic intent routing in [app/services/intent_router.py](/Users/arthur_b/Lab/chat-frontdesk/app/services/intent_router.py)

This means the pivot should not throw away the project. It should repurpose the current base.

---

## 3. Reuse Strategy

### Reuse Unchanged Or Nearly Unchanged

- FastAPI application structure
- route module organization
- `GymProfile` and related Pydantic models as business truth
- gym profile JSON as initial structured knowledge source
- profile loading and validation pattern
- explicit error handling discipline

### Reuse But Extend

- [app/models/schemas.py](/Users/arthur_b/Lab/chat-frontdesk/app/models/schemas.py):
  extend for richer gym facts, chat state, and structured model outputs
- [app/services/knowledge_service.py](/Users/arthur_b/Lab/chat-frontdesk/app/services/knowledge_service.py):
  keep JSON loading, then add retrieval-related helpers later
- deterministic routing:
  keep as backup behavior, baseline evaluation target, or guardrail fallback

### De-Emphasize As Core Architecture

- rule-based routing as main intelligence layer
- template-only response generation as main assistant behavior

### New Components To Introduce

- LLM service
- prompt assembly layer
- structured output schemas
- tool calling layer
- RAG ingestion and retrieval
- memory layer
- observability and tracing
- evaluation dataset and runner
- guardrail layer

---

## 4. Target End-State Architecture

```text
WhatsApp Webhook
  -> request parsing
  -> conversation state lookup
  -> retrieval of relevant gym knowledge
  -> prompt assembly
  -> LLM call with structured output / tool calling
  -> tool execution if needed
  -> validation and guardrails
  -> response persistence + tracing
  -> WhatsApp reply
```

Key architectural principle:

Use the LLM for language understanding and response reasoning, but keep trusted business data, validation, and operational actions in application code.

---

## 5. Phase Overview

### Phase 1: Prepare The Existing Project For The Pivot

AI engineering skills demonstrated:

- architecture refactoring for AI systems
- schema-first design
- separating business truth from model behavior

### Phase 2: Build A Basic LLM-Powered Assistant

AI engineering skills demonstrated:

- LLM integration
- prompt engineering
- structured outputs
- Pydantic validation

### Phase 3: Add Tool Calling And Controlled Actions

AI engineering skills demonstrated:

- tool/function calling
- model orchestration
- trusted execution boundaries

### Phase 4: Add RAG And Richer Knowledge

AI engineering skills demonstrated:

- embeddings
- chunking
- vector databases
- retrieval grounding

### Phase 5: Add Conversation Memory

AI engineering skills demonstrated:

- conversational state design
- memory windows
- summarization strategy

### Phase 6: Add Guardrails, Observability, And Evals

AI engineering skills demonstrated:

- AI safety patterns
- tracing
- regression evaluation
- iterative quality improvement

### Phase 7: WhatsApp Integration And Demo Hardening

AI engineering skills demonstrated:

- end-to-end AI application integration
- failure handling
- portfolio-ready delivery

---

## 6. Detailed Roadmap

## Phase 1 - Prepare The Existing Project For The Pivot

Goal:
Turn the current deterministic MVP foundation into a clean base for AI workflows without yet introducing model complexity.

Why this phase exists:
If the foundation remains "deterministic assistant shaped," later AI additions will feel bolted on. This phase creates the right boundaries first.

### Task 1.1 - Audit And Document Current Reusable Components

- Objective:
  identify exactly which current modules remain business infrastructure and which ones stop being core intelligence
- Files to create/modify:
  - `README.md`
  - `IMPLEMENTATION_PLAN.md`
  - optional project notes file if desired
- Concepts to learn:
  - AI system boundaries
  - canonical data source vs generated output
  - technical debt avoidance during pivots
- Implementation guidance:
  - document current modules as reusable foundation
  - explicitly mark deterministic intent router as fallback/baseline, not future primary logic
  - define desired end-state architecture in plain language
- Expected outcome:
  clear written direction for the pivot
- Manual testing approach:
  - read docs end-to-end
  - confirm each current file has an intentional place in future architecture
- Optional stretch goals:
  - add a simple architecture diagram

### Task 1.2 - Reorganize Project Modules For AI Features

- Objective:
  create clean module boundaries for prompts, LLM calls, retrieval, tools, and memory
- Files to create/modify:
  - `app/services/`
  - `app/models/`
  - `app/routes/`
  - `prompts/`
  - `evals/`
  - `tests/`
- Concepts to learn:
  - separation of orchestration, infrastructure, and business data
  - designing maintainable AI application folders
- Implementation guidance:
  - keep existing modules in place where possible
  - add new folders only when they represent a real architectural boundary
  - avoid generic "agents" modules too early; prefer explicit services
- Expected outcome:
  project structure can support AI additions without confusion
- Manual testing approach:
  - inspect tree
  - confirm each new module has a single clear responsibility
- Optional stretch goals:
  - define naming conventions for prompts, eval fixtures, and model schemas

### Task 1.3 - Extend Business Schemas Without Breaking Existing Data

- Objective:
  evolve the schema layer so it can support AI workflows while preserving the current gym profile
- Files to create/modify:
  - `app/models/schemas.py`
  - possible new files such as `app/models/messages.py` and `app/models/llm_outputs.py`
- Concepts to learn:
  - schema design for LLM applications
  - business models vs model-output models
  - versionable knowledge structures
- Implementation guidance:
  - keep `GymProfile` as canonical business schema
  - separate business data schemas from LLM output schemas
  - prepare models for chat messages, retrieval chunks, tool results, and assistant decisions
- Expected outcome:
  typed schemas exist for both deterministic data and future AI outputs
- Manual testing approach:
  - validate current `gym_profile.json` still loads
  - review whether new schemas are easy to understand without reading implementation logic
- Optional stretch goals:
  - add model examples or field descriptions to improve self-documentation

---

## Phase 2 - Build A Basic LLM-Powered Assistant

Goal:
Replace deterministic routing as the primary interaction mechanism with a simple LLM-driven answer workflow grounded in the existing gym profile.

Why this phase exists:
This is the first real AI pivot. It demonstrates model integration, prompting, and structured outputs while staying close to the current JSON knowledge base.

### Task 2.1 - Add Configuration For Model Access

- Objective:
  create a clean configuration layer for model provider settings
- Files to create/modify:
  - `app/config.py` or equivalent
  - environment example file such as `.env.example`
  - `README.md`
- Concepts to learn:
  - API key management
  - model selection
  - development vs production config boundaries
- Implementation guidance:
  - centralize model name, API key, and optional temperature/token settings
  - avoid scattering provider-specific config across services
  - keep config minimal at first
- Expected outcome:
  LLM-related settings are explicit, secure, and easy to change
- Manual testing approach:
  - start app with config present
  - start app with config missing and confirm failure is understandable
- Optional stretch goals:
  - support switching between two model options for experimentation

### Task 2.2 - Create A Dedicated LLM Service

- Objective:
  isolate raw model API interaction behind one service
- Files to create/modify:
  - `app/services/llm_service.py`
  - tests for service behavior
- Concepts to learn:
  - model request lifecycle
  - retry/error handling for LLM calls
  - keeping provider code isolated
- Implementation guidance:
  - create one small interface responsible only for model calls
  - do not mix prompt assembly or business logic into this module
  - return typed results or clearly parseable payloads
- Expected outcome:
  app has one authoritative integration point for model usage
- Manual testing approach:
  - send a simple known prompt
  - verify service returns expected raw/parsed structure
  - simulate provider error handling
- Optional stretch goals:
  - add latency logging

### Task 2.3 - Write The First System Prompt

- Objective:
  define the assistant's behavior, scope, tone, and grounding rules
- Files to create/modify:
  - `prompts/system_prompt.md`
  - optional `prompts/answer_prompt.md`
- Concepts to learn:
  - system prompting
  - instruction hierarchy
  - grounding constraints
- Implementation guidance:
  - tell the assistant it is a gym front desk assistant
  - explicitly forbid inventing unavailable gym facts
  - instruct it to rely on provided structured gym profile
  - define desired tone and response style
- Expected outcome:
  assistant behavior is controlled by explicit prompt rules rather than hidden assumptions
- Manual testing approach:
  - read prompt and challenge it with edge-case questions
  - verify prompt clearly says what to do when data is missing
- Optional stretch goals:
  - maintain prompt versions for comparison

### Task 2.4 - Add Structured Output For Assistant Decisions

- Objective:
  require the model to return a predictable response schema instead of free-form text only
- Files to create/modify:
  - `app/models/llm_outputs.py`
  - `app/services/llm_service.py`
  - `app/services/assistant_service.py`
- Concepts to learn:
  - structured outputs
  - schema-guided prompting
  - validation of model responses
- Implementation guidance:
  - define a small response model such as:
    - answer text
    - confidence or reasoning category
    - whether fallback is needed
    - referenced knowledge fields
  - validate every model response with Pydantic before using it
- Expected outcome:
  model outputs become easier to debug, test, and constrain
- Manual testing approach:
  - run valid prompt flows
  - simulate malformed model output and verify safe failure
- Optional stretch goals:
  - compare free-form vs structured output quality

### Task 2.5 - Build A First Assistant Orchestrator Using Only GymProfile

- Objective:
  create the first end-to-end assistant workflow using current structured gym data as context
- Files to create/modify:
  - `app/services/assistant_service.py`
  - `app/services/knowledge_service.py`
  - `tests/`
- Concepts to learn:
  - orchestration layer design
  - prompt context assembly
  - grounding from owned data
- Implementation guidance:
  - load `GymProfile`
  - convert relevant facts into prompt context
  - call LLM service
  - validate structured output
  - return final assistant response
- Expected outcome:
  deterministic intent routing is no longer necessary for the main happy path
- Manual testing approach:
  - ask about pricing, opening hours, classes, and location
  - verify answers stay consistent with JSON data
  - verify missing data triggers a safe answer
- Optional stretch goals:
  - log which `GymProfile` fields were used in each answer

---

## Phase 3 - Add Tool Calling And Controlled Actions

Goal:
Move from "LLM answers from context" to "LLM can request trusted structured operations."

Why this phase exists:
Tool calling is one of the clearest modern AI engineering patterns. It teaches how to keep important logic and data access outside the model.

### Task 3.1 - Identify The First Trusted Tools

- Objective:
  define a small set of business-safe tools the assistant can use
- Files to create/modify:
  - `README.md`
  - design notes or `app/services/tool_service.py`
- Concepts to learn:
  - function calling use cases
  - choosing tool boundaries
  - keeping tool surface area small
- Implementation guidance:
  - start with simple read-oriented tools:
    - get opening hours
    - get pricing
    - get class list
    - get contact details
  - optionally add one action-oriented tool later, such as lead capture
- Expected outcome:
  clear first tool set with well-defined inputs and outputs
- Manual testing approach:
  - review tool definitions and confirm each represents a trusted business action or lookup
- Optional stretch goals:
  - classify tools into read tools and action tools

### Task 3.2 - Implement The Tool Execution Layer

- Objective:
  create backend functions the model may call
- Files to create/modify:
  - `app/services/tool_service.py`
  - `app/services/knowledge_service.py`
  - tool-related tests
- Concepts to learn:
  - tool schema design
  - application-controlled execution
  - typed tool results
- Implementation guidance:
  - tools should return validated structured data
  - avoid directly exposing raw file structures to the model
  - keep tools small and composable
- Expected outcome:
  backend has a safe, typed interface for business lookups
- Manual testing approach:
  - call each tool directly
  - verify outputs match `GymProfile` data
  - verify invalid inputs fail safely
- Optional stretch goals:
  - add usage logging per tool

### Task 3.3 - Let The LLM Choose Between Direct Answer And Tool Use

- Objective:
  integrate tool calling into assistant workflow
- Files to create/modify:
  - `app/services/llm_service.py`
  - `app/services/assistant_service.py`
  - `app/models/llm_outputs.py`
- Concepts to learn:
  - tool choice orchestration
  - iterative LLM workflows
  - model execution loops
- Implementation guidance:
  - let model either answer directly or request a tool
  - if tool requested, execute tool and re-prompt with result
  - cap loop depth to avoid runaway behavior
- Expected outcome:
  assistant uses tools for trusted facts instead of only prompt stuffing
- Manual testing approach:
  - ask questions that should trigger tool use
  - inspect whether final answers reflect tool outputs accurately
  - test unsupported questions and confirm fallback
- Optional stretch goals:
  - compare answer quality before and after tool calling

### Task 3.4 - Add A Simple Lead Capture Tool

- Objective:
  demonstrate an action-oriented AI tool beyond factual lookup
- Files to create/modify:
  - `app/services/tool_service.py`
  - `app/models/`
  - local storage file or simple persistence layer
- Concepts to learn:
  - tool calling for business workflows
  - structured extraction from chat
  - safe action confirmation
- Implementation guidance:
  - keep persistence simple, such as JSON or local file storage
  - require structured data for captured lead intent
  - define when the assistant should ask clarifying questions before saving a lead
- Expected outcome:
  assistant can do something operationally meaningful, not just answer questions
- Manual testing approach:
  - simulate a user asking to join or be contacted
  - verify captured data is stored in expected structure
  - verify incomplete user data triggers clarification rather than bad records
- Optional stretch goals:
  - prepare interface for future CRM integration

---

## Phase 4 - Add RAG And Richer Knowledge

Goal:
Expand from one JSON profile to a retrieval-backed assistant that can answer from richer gym documentation.

Why this phase exists:
RAG is one of the most recognizable AI application patterns. This phase teaches how to ground the assistant in more than one small structured file.

### Task 4.1 - Define Additional Knowledge Sources

- Objective:
  identify what information belongs in retrieval documents rather than the core profile JSON
- Files to create/modify:
  - `app/data/knowledge_docs/`
  - `README.md`
- Concepts to learn:
  - structured vs unstructured knowledge
  - data modeling for RAG
  - source-of-truth boundaries
- Implementation guidance:
  - keep stable business facts in `gym_profile.json`
  - move richer narrative content into docs, such as:
    - class descriptions
    - membership FAQs
    - onboarding steps
    - trainer bios
    - policy notes
- Expected outcome:
  clear distinction between core business schema and retrieval documents
- Manual testing approach:
  - inspect docs and confirm each document has a purpose
  - confirm no critical structured field is accidentally moved into only unstructured text
- Optional stretch goals:
  - add metadata fields such as topic, source, and update date

### Task 4.2 - Build A Document Ingestion Pipeline

- Objective:
  convert raw knowledge docs into chunked retrieval-ready records
- Files to create/modify:
  - `scripts/` or `app/services/ingestion_service.py`
  - `app/models/`
  - `app/data/knowledge_docs/`
- Concepts to learn:
  - chunking strategy
  - metadata enrichment
  - ingestion repeatability
- Implementation guidance:
  - choose a simple document format such as Markdown
  - split content into useful chunks
  - preserve metadata for source tracing
  - keep ingestion deterministic and rerunnable
- Expected outcome:
  documents can be transformed into retrieval records consistently
- Manual testing approach:
  - ingest a few docs
  - inspect produced chunks manually
  - check whether chunks are too large, too small, or semantically mixed
- Optional stretch goals:
  - compare multiple chunking strategies

### Task 4.3 - Add Embeddings And Vector Storage

- Objective:
  store semantic representations of knowledge chunks for retrieval
- Files to create/modify:
  - `app/services/retrieval_service.py`
  - vector store config or local storage setup
  - ingestion-related scripts
- Concepts to learn:
  - embeddings
  - vector databases
  - semantic search tradeoffs
- Implementation guidance:
  - choose a simple vector store appropriate for solo development
  - store chunk text plus metadata plus embedding references
  - keep local setup understandable
- Expected outcome:
  knowledge docs become searchable by meaning, not only keywords
- Manual testing approach:
  - query for semantically similar content
  - inspect top matches for relevance
  - verify irrelevant queries do not retrieve nonsense
- Optional stretch goals:
  - compare vector-only search vs keyword-plus-vector search

### Task 4.4 - Inject Retrieved Context Into Assistant Answers

- Objective:
  use retrieved knowledge in the answer workflow
- Files to create/modify:
  - `app/services/retrieval_service.py`
  - `app/services/prompt_service.py`
  - `app/services/assistant_service.py`
- Concepts to learn:
  - retrieval-augmented generation
  - context packing
  - grounding discipline
- Implementation guidance:
  - retrieve only top relevant chunks
  - include source metadata in prompt context
  - instruct the model to prefer retrieved content and avoid unsupported extrapolation
  - keep context size bounded
- Expected outcome:
  assistant can answer richer questions than the base JSON supports
- Manual testing approach:
  - ask questions only answerable from docs
  - verify answers cite or clearly reflect retrieved material
  - test irrelevant questions and confirm graceful fallback
- Optional stretch goals:
  - return source references in debug mode

### Task 4.5 - Evaluate Retrieval Quality Separately From Answer Quality

- Objective:
  start measuring retrieval quality as its own system component
- Files to create/modify:
  - `evals/`
  - `app/services/eval_service.py`
- Concepts to learn:
  - retrieval evaluation
  - component-level AI testing
  - failure decomposition
- Implementation guidance:
  - define questions with expected source docs/topics
  - check whether retrieval returns relevant chunks before judging final wording
  - track false positives and missing context cases
- Expected outcome:
  you can tell whether failures come from retrieval or generation
- Manual testing approach:
  - run a small evaluation set manually
  - review mismatches and categorize failure types
- Optional stretch goals:
  - add simple relevance scoring rubric

---

## Phase 5 - Add Conversation Memory

Goal:
Allow the assistant to handle follow-up questions and short multi-turn conversations more naturally.

Why this phase exists:
A front desk assistant should remember nearby context. This phase introduces practical state management without jumping into full autonomous agents.

### Task 5.1 - Define Conversation State Schema

- Objective:
  create typed models for session history and assistant memory
- Files to create/modify:
  - `app/models/messages.py`
  - `app/models/schemas.py`
- Concepts to learn:
  - message schemas
  - chat session boundaries
  - ephemeral vs persisted memory
- Implementation guidance:
  - define user messages, assistant messages, timestamps, and session IDs
  - decide what minimum state is needed for useful follow-up support
- Expected outcome:
  memory has explicit structure instead of ad hoc strings
- Manual testing approach:
  - create example sessions by hand
  - verify schema supports common follow-up flows cleanly
- Optional stretch goals:
  - tag messages with derived topics or intents

### Task 5.2 - Add A Simple Memory Store

- Objective:
  persist recent conversation state between requests
- Files to create/modify:
  - `app/services/memory_service.py`
  - local file store or lightweight database setup
- Concepts to learn:
  - state persistence
  - session key design
  - bounded history retention
- Implementation guidance:
  - choose a simple persistence layer first
  - store recent turns, not infinite conversation history
  - design for easy replacement later if needed
- Expected outcome:
  assistant can retrieve recent context for the same WhatsApp user/session
- Manual testing approach:
  - send two or three linked example messages
  - verify state is stored and loaded correctly
- Optional stretch goals:
  - add expiry or cleanup strategy

### Task 5.3 - Use Memory In Prompt Assembly

- Objective:
  include relevant recent turns in assistant reasoning
- Files to create/modify:
  - `app/services/prompt_service.py`
  - `app/services/assistant_service.py`
  - `app/services/memory_service.py`
- Concepts to learn:
  - context window budgeting
  - selective memory inclusion
  - multi-turn prompt design
- Implementation guidance:
  - include only recent or relevant turns
  - do not dump entire conversation blindly
  - continue grounding on structured data and retrieval outputs
- Expected outcome:
  assistant handles follow-up questions like "and the annual one?" or "where is it?" after a prior exchange
- Manual testing approach:
  - test short conversational sequences
  - verify follow-ups work better with memory than without
- Optional stretch goals:
  - add topic-aware memory filtering

### Task 5.4 - Add Conversation Summarization For Longer Threads

- Objective:
  prevent memory growth from bloating prompts
- Files to create/modify:
  - `app/services/memory_service.py`
  - `app/services/llm_service.py`
- Concepts to learn:
  - summarization memory
  - context compression
  - prompt cost management
- Implementation guidance:
  - summarize older turns into compact structured memory
  - keep raw recent turns plus one short summary
  - ensure summaries preserve business-relevant facts
- Expected outcome:
  memory remains useful as conversations grow
- Manual testing approach:
  - simulate longer chats
  - inspect whether summary preserves relevant context
- Optional stretch goals:
  - compare manual summarization rules vs LLM-generated summaries

---

## Phase 6 - Add Guardrails, Observability, And Evals

Goal:
Make the assistant inspectable, safer, and easier to improve.

Why this phase exists:
An AI project becomes much stronger as a portfolio piece when it shows not only generation but control, measurement, and debugging discipline.

### Task 6.1 - Add Output Guardrails

- Objective:
  validate final responses against business and safety constraints
- Files to create/modify:
  - `app/services/guardrail_service.py`
  - `app/models/llm_outputs.py`
  - `app/services/assistant_service.py`
- Concepts to learn:
  - post-generation validation
  - safe fallback behavior
  - application-level guardrails
- Implementation guidance:
  - check for missing required response fields
  - detect unsupported claims where possible
  - fail closed to safe fallback rather than sending uncertain content
- Expected outcome:
  assistant has a protection layer beyond prompt wording alone
- Manual testing approach:
  - test malformed output cases
  - test out-of-scope questions
  - verify fallback message is always available
- Optional stretch goals:
  - add lightweight policy checks for prohibited claims

### Task 6.2 - Add Prompt / Retrieval / Tool Tracing

- Objective:
  make assistant runs inspectable for debugging and improvement
- Files to create/modify:
  - `app/services/assistant_service.py`
  - `app/services/llm_service.py`
  - `app/services/retrieval_service.py`
  - tracing config or logging modules
- Concepts to learn:
  - AI observability
  - trace design
  - prompt debugging
- Implementation guidance:
  - record inputs, retrieved chunks, tool calls, structured outputs, and final responses
  - avoid logging sensitive secrets
  - keep traces readable for local analysis
- Expected outcome:
  you can inspect why a response happened, not just what happened
- Manual testing approach:
  - run sample conversations
  - review traces and confirm each stage is visible
- Optional stretch goals:
  - integrate a dedicated tracing platform

### Task 6.3 - Build A Small Evaluation Dataset

- Objective:
  create repeatable test cases for the assistant
- Files to create/modify:
  - `evals/queries.json` or similar
  - `README.md`
- Concepts to learn:
  - evaluation set design
  - representative edge cases
  - regression mindset for AI apps
- Implementation guidance:
  - include:
    - simple factual questions
    - follow-up questions
    - retrieval-based questions
    - out-of-scope questions
    - lead capture scenarios
  - define expected behavior, not necessarily one exact wording
- Expected outcome:
  assistant improvements can be checked against a fixed benchmark set
- Manual testing approach:
  - review dataset manually
  - confirm each test case represents a realistic user message
- Optional stretch goals:
  - label each case by feature area and difficulty

### Task 6.4 - Add An Evaluation Runner

- Objective:
  make evaluations easy to run after prompt or workflow changes
- Files to create/modify:
  - `app/services/eval_service.py`
  - `scripts/`
  - `evals/`
- Concepts to learn:
  - offline evaluation workflow
  - rubric-based judging
  - regression detection
- Implementation guidance:
  - run assistant against stored prompts
  - collect outputs, trace metadata, and pass/fail notes
  - prefer simple local evaluation workflow before advanced auto-judging
- Expected outcome:
  changes can be validated systematically instead of only by intuition
- Manual testing approach:
  - run evaluation suite before and after a prompt tweak
  - compare outcomes and inspect regressions
- Optional stretch goals:
  - add an LLM-as-judge experiment with human spot-checking

### Task 6.5 - Compare AI Assistant Against Deterministic Baseline

- Objective:
  use the old router-based approach as a benchmark
- Files to create/modify:
  - `app/services/intent_router.py`
  - `evals/`
  - comparison notes in `README.md`
- Concepts to learn:
  - baseline comparisons
  - measuring AI value over deterministic systems
  - honest portfolio framing
- Implementation guidance:
  - evaluate both systems on overlapping supported questions
  - document where AI improves flexibility and where deterministic logic is still safer
  - use findings to justify final architecture
- Expected outcome:
  project can explain the pivot with evidence, not hype
- Manual testing approach:
  - run the same question set through both flows
  - compare correctness, flexibility, and failure behavior
- Optional stretch goals:
  - add a simple scorecard table in the README

---

## Phase 7 - WhatsApp Integration And Demo Hardening

Goal:
Connect the AI assistant to the real external channel and prepare a credible demo.

Why this phase exists:
Portfolio value increases sharply when the AI system works end to end in a realistic interface like WhatsApp.

### Task 7.1 - Implement Webhook Message Intake

- Objective:
  receive WhatsApp webhook events and normalize inbound text into internal message models
- Files to create/modify:
  - `app/routes/webhook.py`
  - `app/models/messages.py`
  - `app/main.py`
- Concepts to learn:
  - external event normalization
  - thin route design
  - request validation
- Implementation guidance:
  - keep route small
  - parse only the message types you intend to support
  - transform inbound payload into internal typed message format
- Expected outcome:
  backend can accept real inbound messages cleanly
- Manual testing approach:
  - post sample WhatsApp payloads locally
  - verify normalized message objects are correct
- Optional stretch goals:
  - store raw payload snapshots for debugging

### Task 7.2 - Connect Webhook To Assistant Workflow

- Objective:
  wire inbound messages into the full assistant pipeline
- Files to create/modify:
  - `app/routes/webhook.py`
  - `app/services/assistant_service.py`
  - `app/services/memory_service.py`
- Concepts to learn:
  - end-to-end orchestration
  - workflow integration boundaries
  - operational error handling
- Implementation guidance:
  - route should call one orchestration entrypoint
  - session identification should connect webhook user identity to memory
  - failures should return safe operational responses
- Expected outcome:
  one inbound message can produce one assistant response from the AI pipeline
- Manual testing approach:
  - simulate inbound messages end to end
  - inspect traces for each stage
- Optional stretch goals:
  - add idempotency handling for duplicate webhook events

### Task 7.3 - Implement Outbound WhatsApp Replies

- Objective:
  send assistant replies back to the user
- Files to create/modify:
  - `app/services/whatsapp_service.py`
  - `app/routes/webhook.py`
  - config files
- Concepts to learn:
  - external API integration
  - delivery error handling
  - separating channel logic from assistant logic
- Implementation guidance:
  - create one outbound service wrapper
  - do not let assistant code depend on WhatsApp payload formats directly
  - log send failures with enough detail to debug
- Expected outcome:
  real round-trip conversation becomes possible
- Manual testing approach:
  - test with sandbox or live setup
  - verify outbound text matches assistant output
- Optional stretch goals:
  - support typing indicators or richer message templates later

### Task 7.4 - Prepare A Demo-Ready Scenario Set

- Objective:
  harden the most important portfolio and interview demo flows
- Files to create/modify:
  - `README.md`
  - `evals/`
  - optional demo notes
- Concepts to learn:
  - scenario-driven validation
  - presenting AI systems honestly
  - demo reliability
- Implementation guidance:
  - prepare a small set of demo conversations:
    - pricing
    - classes
    - follow-up pricing question
    - retrieval-based FAQ
    - lead capture / join intent
    - out-of-scope fallback
  - ensure traces and eval notes can explain what happened
- Expected outcome:
  project is ready to show as a complete AI application
- Manual testing approach:
  - run each demo scenario start to finish
  - verify both reply quality and internal observability
- Optional stretch goals:
  - record before/after comparison of deterministic vs AI-first version

---

## 7. Suggested Build Order

If you want the most practical sequence, follow this order:

1. Phase 1
2. Phase 2
3. Phase 7.1 and 7.2 for a local end-to-end loop
4. Phase 3
5. Phase 4
6. Phase 5
7. Phase 6
8. Phase 7.3 and 7.4

Reason:

- you get visible AI behavior early
- you avoid premature RAG complexity
- you keep each layer understandable
- you reach demo value before optimization work

---

## 8. What Makes This A Strong Portfolio Project

By the end of this roadmap, the project can demonstrate:

- API-backed LLM integration
- schema-validated model outputs
- prompt engineering with explicit guardrails
- tool/function calling
- RAG with embeddings and vector search
- short-term conversation memory
- AI observability and tracing
- evaluation-driven improvement
- end-to-end WhatsApp deployment architecture

Just as important, it demonstrates engineering judgment:

- reusing existing deterministic infrastructure where it still adds value
- not overusing agents where explicit workflows are clearer
- keeping structured business truth outside the model
- making AI behavior inspectable and testable

---

## 9. Anti-Goals

Do not add these early unless they serve a clear learning purpose:

- multi-agent complexity without a concrete need
- autonomous planning loops for simple front desk tasks
- heavy infrastructure before local workflows work
- admin dashboards before assistant quality is strong
- multi-tenant support before single-gym flow is solid
- vague "AI magic" features that are hard to explain or test

---

## 10. Definition Of Success

The pivot is successful when this repo becomes a working AI-first gym assistant that:

- answers real gym questions through a modern LLM workflow
- remains grounded in trusted gym data
- uses validation, retrieval, tools, and memory intentionally
- is observable and evaluable
- is realistic for one developer to build
- is strong enough to discuss in interviews as an example of practical AI engineering
