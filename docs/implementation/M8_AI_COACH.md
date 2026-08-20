# M8 — W8 AI Coach

**Document:** M8_AI_COACH.md
**Product:** W8
**Milestone:** M8
**Status:** Ready for Implementation
**Priority:** P0
**Depends On:** M0–M7
**Blocks:** M9, M10, M12, M15, M16

---

# 1. Purpose

M8 implements the W8 AI Coach.

The AI Coach is a controlled AI feature, not an unrestricted chatbot.

Its purpose is to provide contextual fitness and wellness assistance using
trusted W8 data, approved content, user context, and explicit safety rules.

---

# 2. Critical Principle

The LLM is NOT the source of truth.

The authoritative sources are:

```text
W8 Business Rules
W8 CMS Content
W8 User Domain
W8 Workout Engine
W8 Nutrition Engine
W8 Safety Policies
```

The LLM is a reasoning/generation layer operating within those constraints.

---

# 3. AI Architecture

Conceptually:

```text
Flutter
   ↓
AI Coach API
   ↓
Request Validation
   ↓
User Context Resolver
   ↓
Safety Classifier
   ↓
Context Retrieval
   ↓
Prompt Builder
   ↓
LLM Provider
   ↓
Output Validator
   ↓
Safety Filter
   ↓
Response
```

---

# 4. Provider Abstraction

Do NOT couple the application to one LLM provider.

Implement an abstraction:

- AIProvider

with provider-specific adapters.

The rest of the application must not import provider SDKs directly.

---

# 5. Model Configuration

AI model configuration must be server-side.

Never expose:

- provider API key
- system prompt
- internal retrieval configuration
- moderation configuration

to Flutter.

---

# 6. Conversation Model

Implement:

- Conversation
- Message
- MessageRole
- MessageMetadata
- ConversationState

where required by Section 03.

Each conversation belongs to one authenticated user.

---

# 7. Message Lifecycle

Conceptually:

```text
User Message
    ↓
Validation
    ↓
Safety Classification
    ↓
Context Retrieval
    ↓
LLM Request
    ↓
Output Validation
    ↓
Assistant Message
```

---

# 8. Context Sources

The AI may receive controlled context from:

- User Profile
- User Goals
- Workout History
- Nutrition History
- Published Exercise Content
- Published Workout Content
- Published Nutrition Content
- Relevant W8 Knowledge Content

Only minimum necessary data should be provided.

---

# 9. Context Isolation

The AI MUST NEVER retrieve another user's data.

Every retrieval operation must be scoped by authenticated user identity.

Do not allow arbitrary user IDs inside AI context queries.

---

# 10. Retrieval

If RAG is required, retrieval must use approved W8 content.

Potential sources:

- CMS articles
- exercise instructions
- approved workout information
- nutrition knowledge
- W8 policy documents

Do not retrieve arbitrary internet content unless explicitly approved by the architecture.

---

# 11. Grounding

When answering factual W8 questions, the AI should prefer approved W8 content over model memory.

Where appropriate, internal source references should be stored as metadata.

The user-facing response does not need to expose internal database IDs.

---

# 12. Prompt Architecture

Use layered prompts:

```text
System Policy
    ↓
Safety Policy
    ↓
W8 Product Rules
    ↓
Retrieved Context
    ↓
User Context
    ↓
User Message
```

Do not dynamically concatenate untrusted content into privileged instructions.

Retrieved content must be treated as data, not instructions.

---

# 13. Prompt Injection Defense

Treat user messages and retrieved content as untrusted.

The AI must not follow instructions such as:

- ignore previous rules
- reveal system prompt
- reveal secrets
- bypass safety
- show internal database
- act as admin

Prompt injection defenses must exist at the application layer, not only inside the system prompt.

---

# 14. AI Safety Categories

The AI safety layer must identify at least:

- normal fitness question
- nutrition question
- workout question
- account/product question
- medical-related question
- dangerous exercise request
- dangerous nutrition request
- self-harm/eating-disorder-related content
- illegal/unsafe request
- prompt injection

Exact categories follow Section 13.

---

# 15. Medical Boundary

The AI MUST NOT:

- diagnose medical conditions
- prescribe medication
- modify medication
- provide emergency medical treatment
- claim certainty about injuries
- replace professional medical advice

When a request requires medical expertise, use the approved safety response.

---

# 16. Injury Handling

If a user reports pain or injury:

The AI must not confidently diagnose.

It should:

- avoid prescribing unsafe exercise
- suggest stopping/avoiding aggravating activity where appropriate
- encourage appropriate professional evaluation based on severity
- provide only safe general information within the approved scope

Exact safety wording belongs to the Master Specification.

---

# 17. Nutrition Safety

The AI must not generate extreme diets.

It must not:

- recommend starvation
- recommend dangerous caloric restriction
- encourage purging
- encourage eating-disorder behavior
- make unsupported medical nutrition claims

Nutrition plans must originate from approved W8 content or deterministic business rules.

---

# 18. Workout Safety

The AI must not casually recommend dangerous training behavior.

It should respect:

- available equipment
- training frequency
- known constraints
- approved W8 exercise content

AI-generated exercise recommendations must remain constrained by published W8 exercises where required.

---

# 19. Output Validation

AI output must pass through validation before being returned.

Validation should check:

- length
- unsafe claims
- forbidden content
- unsupported medical claims
- invalid exercise references
- invalid nutrition references
- prompt leakage

If structured output is expected, reject malformed responses.

---

# 20. Structured AI Output

Where the AI needs to perform an application action, use structured output rather than parsing arbitrary prose.

Example conceptual schema:

- intent
- response
- exercise_references[]
- meal_references[]
- actions[]
- safety_level

Only supported actions may be executed.

The AI MUST NOT directly execute database mutations.

---

# 21. Tool Calling

If AI tools are implemented, expose only narrowly scoped tools.

Examples:

- get_user_workout_summary
- get_user_progress
- search_published_exercises
- search_published_nutrition
- get_user_preferences

Never expose unrestricted:

- database
- filesystem
- admin APIs
- payment APIs

---

# 22. Streaming

If streaming is implemented:

- authenticate before stream creation
- enforce timeout
- enforce output limits
- allow cancellation
- validate final output
- do not expose internal provider errors

---

# 23. Rate Limiting

AI endpoints require stricter rate limits.

Protect against:

- spam
- token abuse
- automated scraping
- cost attacks
- conversation flooding

Rate limits should consider:

- IP
- account
- subscription entitlement
- endpoint

without relying on IP alone.

---

# 24. Cost Control

Track:

- input tokens
- output tokens
- latency
- estimated cost
- request ID
- user/account

Never expose provider API keys.

Implement maximum context and output limits.

---

# 25. Conversation Persistence

Store only necessary data.

Do not persist sensitive information unnecessarily.

Conversation deletion must follow account lifecycle requirements.

---

# 26. AI Errors

The API must distinguish:

- validation error
- safety rejection
- rate limit
- provider unavailable
- timeout
- internal error

Never expose raw provider errors.

---

# 27. Fallback

If the AI provider is unavailable:

The application must show a localized failure state.

Do not silently fabricate an answer.

---

# 28. Observability

Every AI request should be traceable using:

- request ID
- trace ID
- model
- latency
- token usage
- result status
- safety classification

Never log full sensitive conversations by default.

---

# 29. Testing

Required:

## Unit

- prompt building
- context selection
- safety classification
- output validation
- tool authorization

## Integration

- chat request
- streaming
- provider failure
- timeout
- rate limiting

## Security

- prompt injection
- cross-user context access
- system prompt extraction
- tool abuse
- secret leakage

## Safety

Create a curated safety test suite covering:

- medical questions
- injury claims
- dangerous diets
- unsafe exercise
- eating-disorder-related prompts
- self-harm-related prompts
- medication questions

---

# 30. Definition of Done

- [ ] AI provider abstraction implemented
- [ ] Conversation model implemented
- [ ] Message model implemented
- [ ] Context resolver implemented
- [ ] Safety layer implemented
- [ ] Prompt architecture implemented
- [ ] Retrieval implemented where required
- [ ] Output validation implemented
- [ ] Tool permissions implemented
- [ ] Rate limiting implemented
- [ ] Cost tracking implemented
- [ ] Observability implemented
- [ ] Error handling implemented
- [ ] Security tests pass
- [ ] Safety test suite passes
- [ ] No provider secrets in mobile
- [ ] No unrestricted tool access
- [ ] No arbitrary AI database mutations

---

# 31. AI Execution Prompt

Implement W8 M8.

Read:

- AGENTS.md
- Master Specification
- Sections 03, 04, 06, 07, 12, 13, 15, 17, 20
- M0–M7

CRITICAL:

The LLM is not the source of truth.
Never allow the LLM to directly modify the database.
Never expose system prompts or provider secrets.
Never allow arbitrary SQL/tool execution.
Use only approved W8 content for grounded recommendations.
Implement safety outside the prompt as well as inside the prompt.
Every tool must have explicit authorization.
Create adversarial tests before completion.
If safety requirements are ambiguous, STOP.
