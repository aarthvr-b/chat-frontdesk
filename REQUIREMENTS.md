# Gym WhatsApp Assistant MVP — Requirements Document

## 1. Project Overview

This project is an MVP for a WhatsApp-based assistant for local gyms.
The assistant should act as a lightweight digital front desk that replies to common customer questions on WhatsApp, such as pricing, opening hours, free trial availability, and basic service information. Its main goal is to respond instantly, reduce missed leads, and guide interested users toward visiting the gym or contacting the owner.
This MVP is intended as a demo-ready product that can be shown to a real gym owner and tested in a realistic scenario.

---

## 2. Product Goal

Build a working MVP that can:

- receive WhatsApp messages from users
- understand a limited set of common gym-related intents
- return correct, controlled, and useful answers
- guide users toward conversion-oriented next actions
- be demoed live to a gym owner

The product is **not** meant to be a fully autonomous chatbot.  
It is a controlled assistant for a narrow use case.

---

## 3. MVP Scope

### In Scope

The MVP must support the following capabilities:

1. Answer opening hours questions
2. Answer pricing questions
3. Answer questions about free trial availability
4. Answer basic gym information questions
   - address
   - available services
   - courses/classes
5. Respond to general interest in joining
6. Guide users toward a next step
   - visit the gym
   - contact the gym
   - request more information
7. Provide a safe fallback when the request is outside supported scope

### Out of Scope

The following are explicitly out of scope for the first MVP:

- payment handling
- full booking/reservation system
- CRM integrations
- calendar integrations
- human handoff system
- analytics dashboard
- multi-tenant support
- advanced retrieval / RAG pipelines
- voice messages
- image understanding
- multilingual support beyond the initial chosen language
- full production-grade admin panel

---

## 4. Target User

### Business User
The first business user is a gym owner or gym manager.

### End User
The end user is a person messaging the gym on WhatsApp with questions such as:

- "What are your opening hours?"
- "How much is the monthly membership?"
- "Can I do a free trial?"
- "What classes do you offer?"
- "Where are you located?"
- "I want to join"

---

## 5. Core Value Proposition

The MVP should demonstrate the following business value:

- instant replies to common WhatsApp questions
- fewer missed leads
- reduced manual interruption for staff
- more consistent answers
- better conversion from inquiry to visit/trial

The product should be framed as:

> A WhatsApp assistant that helps gyms respond faster and convert more interested people into visitors or members.

---

## 6. Functional Requirements

### 6.1 Message Reception
The system must be able to receive incoming WhatsApp messages through a webhook.

**Requirement**
- Expose an endpoint that can receive webhook payloads from WhatsApp Cloud API.

**Acceptance Criteria**
- Incoming user messages are received and parsed successfully.
- Relevant message text can be extracted from the payload.

---

### 6.2 Message Processing
The system must process incoming text messages and determine the best response based on supported intents.

**Supported intents for MVP**
- opening_hours
- pricing
- free_trial
- location
- services
- classes
- join_interest
- fallback

**Acceptance Criteria**
- The system can classify or route incoming text into one of the supported intents.
- Unsupported requests trigger a fallback response.

---

### 6.3 Controlled Responses
The system must answer only within a controlled and predefined scope.

**Requirement**
- Responses must be based only on known gym information.
- The assistant must avoid inventing information.

**Acceptance Criteria**
- If data is available, the assistant uses it.
- If data is not available, the assistant responds with a safe fallback.
- No unsupported claims should be generated.

---

### 6.4 Gym Knowledge Base
The system must use a structured knowledge source for the gym's information.

**Knowledge areas**
- gym name
- opening hours
- address
- contact details
- services
- classes
- pricing
- free trial information
- conversion messages / CTAs

**Acceptance Criteria**
- Gym data can be loaded from a simple structured file, such as JSON.
- Updating the file changes the assistant behavior without changing business logic code.

---

### 6.5 Outbound Messaging
The system must be able to send a WhatsApp message back to the user.

**Acceptance Criteria**
- After receiving and processing an inbound message, the system can send back a response through WhatsApp Cloud API.

---

### 6.6 Fallback Behavior
The system must handle unsupported requests safely.

**Requirement**
- When the system does not understand or support a request, it should respond politely and redirect the user toward supported topics.

**Example fallback behavior**
- "I can help with prices, opening hours, free trial information, and classes."

**Acceptance Criteria**
- Unsupported input never results in an empty response.
- Unsupported input does not produce fabricated information.

---

### 6.7 Conversion-Oriented Replies
Whenever appropriate, responses should move the user toward a business goal.

**Examples**
- invite the user to visit the gym
- invite the user to try a free session
- ask whether they want more information
- encourage contacting the gym directly

**Acceptance Criteria**
- At least pricing, free-trial, and join-interest flows should include a conversion-oriented next step.

---

## 7. Non-Functional Requirements

### 7.1 Simplicity
The MVP should prioritize simplicity over scalability.

**Requirement**
- The architecture must stay easy to understand, easy to run locally, and easy to demo.

---

### 7.2 Reliability
The demo must feel stable.

**Requirement**
- The system should return correct answers for the supported cases consistently.
- The system should fail safely for unsupported cases.

---

### 7.3 Maintainability
The codebase should be organized clearly enough that it can be extended after the MVP.

**Requirement**
- Separate webhook handling, message processing, and knowledge/config loading into distinct modules.

---

### 7.4 Fast Feedback During Development
The system should be easy to test manually.

**Requirement**
- Local testing must be straightforward.
- It should be possible to test message handling without full production deployment.

---

## 8. Suggested Technical Direction

### Recommended Stack
- Python
- FastAPI
- WhatsApp Cloud API
- JSON file for gym data
- Optional LLM layer only for response polishing, not core decision-making

### Architecture Principles
- one backend service
- simple synchronous flow for MVP
- no microservices
- no unnecessary infrastructure complexity
- deterministic logic first, LLM second

---

## 9. Suggested Project Structure

This is only a suggested direction and may evolve.

```text
app/
  main.py
  routes/
    webhook.py
  services/
    message_processor.py
    intent_router.py
    knowledge_service.py
    whatsapp_service.py
    response_builder.py
  data/
    gym_profile.json
  models/
    schemas.py
  utils/
    logging.py
tests/
  test_message_processor.py
  test_intents.py
README.md
REQUIREMENTS.md
10. Gym Data Requirements

The MVP should support a structured data file with fields similar to:

{
  "gym_name": "Example Gym",
  "opening_hours": {
    "monday_friday": "07:00-22:00",
    "saturday": "09:00-18:00",
    "sunday": "Closed"
  },
  "address": "Via Example 10",
  "contact_phone": "+39XXXXXXXXXX",
  "services": [
    "Gym floor",
    "Personal training",
    "Group classes"
  ],
  "classes": [
    "Yoga",
    "Functional training",
    "Spinning"
  ],
  "pricing": {
    "monthly": "60 EUR",
    "annual": "500 EUR"
  },
  "free_trial": "One free trial day is available",
  "cta_messages": {
    "pricing": "Would you like to come visit the gym?",
    "trial": "Would you like to book your free trial visit?"
  }
}
11. MVP User Flows
Flow 1 — Opening Hours

User input

"What time do you open?"

Expected behavior

system identifies opening_hours intent
system returns the correct opening hours
optional CTA if appropriate
Flow 2 — Pricing

User input

"How much is the monthly membership?"

Expected behavior

system identifies pricing intent
system returns known pricing
system follows up with a conversion-oriented CTA
Flow 3 — Free Trial

User input

"Can I try the gym first?"

Expected behavior

system identifies free_trial intent
system returns free trial information
system encourages next step
Flow 4 — Classes

User input

"What classes do you offer?"

Expected behavior

system identifies classes intent
system returns known class information
Flow 5 — Join Interest

User input

"I want to join"

Expected behavior

system identifies join_interest intent
system returns a friendly next step
response should move the user toward visiting or contacting the gym
Flow 6 — Unsupported Input

User input

something unrelated or unsupported

Expected behavior

system returns fallback
response remains polite and useful
12. MVP Success Criteria

The MVP should be considered successful if all of the following are true:

A live WhatsApp message can reach the backend.
The backend can extract and process the text correctly.
The system can answer at least the 5 main gym use cases:
hours
prices
free trial
services/classes
joining
Unsupported requests get a safe fallback.
The end-to-end flow can be demoed live to a gym owner.
The demo makes the business value obvious.
13. Demo Requirements

The MVP demo should be able to show the following scenario live:

A user sends "What are your opening hours?"
The assistant replies correctly.
A user sends "How much is the monthly membership?"
The assistant replies correctly and invites the user to visit.
A user sends "Can I do a free trial?"
The assistant replies positively and guides the next step.
A user sends something unsupported.
The assistant handles it safely.

The demo should emphasize:

speed of response
consistency
conversion potential
reduced manual effort for gym staff
14. Constraints
The MVP should be built fast.
The MVP should be simple enough to understand fully.
The MVP should not depend on complex infrastructure.
The MVP should be designed for one gym only.
The MVP should prioritize working software over perfect architecture.
15. Risks and Mitigations
Risk 1 — Overengineering

Risk
Building too much infrastructure too early.

Mitigation
Keep one service, one gym, simple data source, limited intents.

Risk 2 — Hallucinated or incorrect answers

Risk
Assistant invents information.

Mitigation
Use deterministic logic and structured gym data as the source of truth.

Risk 3 — Weak perceived value in demo

Risk
The gym owner sees it as "just a chatbot."

Mitigation
Frame the demo around:

faster replies
fewer missed leads
more visits/trials
reduced interruptions
Risk 4 — Scope creep

Risk
Trying to add booking, CRM, dashboard, and admin tools too early.

Mitigation
Keep the MVP narrow and demo-oriented.

16. Future Extensions (Not for MVP)

Possible future versions may include:

appointment booking
CRM integration
analytics dashboard
lead tracking
multi-gym multi-tenant support
admin panel
calendar integration
richer knowledge base management
multilingual responses
escalation to human operator
17. Development Guidance for Planning Agent

The planning agent that reads this document should produce a step-by-step implementation plan with:

small, clear development phases
one concrete objective per phase
expected deliverable for each phase
key takeaways / learning goals per phase
test criteria for each phase
clear separation between must-have MVP work and optional improvements

The plan should optimize for:

speed to first working demo
simplicity
high confidence in each completed step
minimal technical risk

The first implementation target is:

a working local MVP that can receive a WhatsApp message and send back a correct gym-related response.
