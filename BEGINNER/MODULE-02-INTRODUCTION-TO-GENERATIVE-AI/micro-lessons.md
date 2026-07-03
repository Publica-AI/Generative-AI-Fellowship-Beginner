# Module 2 - Introduction to Generative AI: Micro Lessons
> For use with AI practice question generation. Beginner level only.

---

## Topic 1: What are Large Language Models (LLMs)

**Title:** What are Large Language Models (LLMs)

**Programming Language:** Python

**Coding Exercise:** Yes

**Learning Objective:**
Students will understand what LLMs are, how they are trained, what tokens and parameters are, and how LLMs differ from traditional search — and make their first API call to an LLM.

**Description for AI:**
This topic introduces Large Language Models conceptually and practically. Students learn that LLMs work by predicting the next token one at a time, are trained in three stages (pre-training, fine-tuning, RLHF), and store their knowledge in billions of parameters. Key concepts: tokens (chunks of text, ~4 characters each), context window (maximum tokens per call), temperature (controls randomness — 0 is deterministic, 1 is creative), and the difference between LLMs (which generate answers) and search engines (which retrieve documents). The practical component is making a first API call using the OpenAI Python library, loading an API key from an environment variable using python-dotenv, and reading the response object including token usage. Nigerian examples used throughout: asking about Lagos industries, Nigerian startups, and CBN policy.

**Common Mistakes:**
- Hardcoding the API key directly in the Python file instead of using an environment variable
- Forgetting to call `load_dotenv()` before `os.getenv()` — returns None silently
- Accessing the response text as `response` instead of `response.choices[0].message.content`
- Confusing temperature=0 (deterministic) with temperature=1 (creative) — higher is not always better

---

## Topic 2: Overview of Popular LLMs (GPT, Claude, Gemini, LLaMA)

**Title:** Overview of Popular LLMs

**Programming Language:** Python

**Coding Exercise:** Yes

**Learning Objective:**
Students will identify the four major LLM families, compare their strengths, access multiple LLM APIs from Python, and make informed model selection decisions based on task, cost, and privacy requirements.

**Description for AI:**
This topic covers the four main LLM families: GPT (OpenAI) — best for code and general tasks; Claude (Anthropic) — best for long documents and writing, 200K token context window, Constitutional AI safety approach; Gemini (Google) — best for very long context (1M tokens) and multimodal tasks, free tier available; LLaMA (Meta) — open source, runs locally, best for privacy-sensitive applications and custom fine-tuning. Students call GPT, Claude, and Gemini APIs from Python, send the same prompt to all three, and compare outputs. Pricing comparison shows cost differences at scale (1,000 calls/day scenario using Nigerian fintech context). Key decision framework: use LLaMA when data cannot leave the organisation (Nigerian hospitals, banks, government), Claude for long documents, GPT for code, Gemini for free tier or multimodal.

**Common Mistakes:**
- Assuming the most expensive model is always best — a smaller well-prompted model often outperforms a larger poorly-prompted one
- Not considering token pricing when choosing a model for production
- Forgetting that sending Nigerian personal data to US-based APIs may have NDPA 2023 compliance implications
- Mixing up client libraries — each provider has a different import and client structure

---

## Topic 3: Accessing LLMs via API

**Title:** Accessing LLMs via API

**Programming Language:** Python

**Coding Exercise:** Yes

**Learning Objective:**
Students will set up API keys securely, make structured API calls with system and user messages, read the full response object, build multi-turn conversations, handle API errors with retry logic, and stream responses.

**Description for AI:**
This topic covers the full API access workflow. Students learn: secure API key management using `.env` files and `python-dotenv` (never hardcode keys — GitHub bots steal exposed keys within seconds); the anatomy of an API request — model, messages array (system/user/assistant roles), temperature, max_tokens; reading the response object — text at `response.choices[0].message.content`, token usage at `response.usage`, finish reason at `response.choices[0].finish_reason` (stop=natural end, length=cut off, content_filter=blocked); building multi-turn conversations by manually maintaining the messages array (LLMs have no memory — you must pass full history every call); error handling with try/except for AuthenticationError, RateLimitError, APIError with exponential backoff; streaming responses using `stream=True` and iterating over chunks. Nigerian business context used throughout.

**Common Mistakes:**
- Not including conversation history in follow-up calls — the model has no memory, each call starts fresh
- Not setting `max_tokens` — responses can be very long and expensive without a cap
- Not checking `finish_reason` — if it says "length", the response was cut off and max_tokens needs increasing
- Using Python's `and`/`or` instead of catching specific exception types
- Forgetting `flush=True` when streaming — output may not appear in real time without it

---

## Topic 4: Introduction to Prompt Engineering

**Title:** Introduction to Prompt Engineering

**Programming Language:** Python

**Coding Exercise:** Yes

**Learning Objective:**
Students will apply zero-shot, one-shot, few-shot, role, and chain-of-thought prompting techniques, control output format for application development, and build a simple prompt chain.

**Description for AI:**
This topic teaches core prompt engineering techniques. Zero-shot: task without examples — works for common well-defined tasks. Few-shot: include 1–5 examples to show exact format or style — use for domain-specific tasks or when consistency matters. Role prompting: assign a persona in the system message ("You are a Nigerian tax lawyer who...") — dramatically changes tone, vocabulary, and cultural relevance. Chain-of-thought: add "Think through this step by step" — improves accuracy on multi-step reasoning and maths problems. Output format control: always specify format for production code — JSON output, numbered lists, strict bullet formats. Prompt chaining: break complex tasks into sequential simple prompts, each using the output of the previous. Nigerian examples: complaint classification, English-to-Pidgin translation, Lagos trader profit/loss calculation, CV screening for Nigerian tech companies.

**Common Mistakes:**
- Using zero-shot for domain-specific Nigerian tasks — few-shot with local examples dramatically improves results
- Not specifying output format — leads to inconsistent outputs that cannot be parsed by application code
- Putting multiple unrelated tasks in one prompt — chain them instead
- Not testing prompts with varied inputs — a prompt that works once may fail 30% of the time
- Writing system messages that are too vague — every unstated rule is filled in by the model's defaults

---

## Topic 5: Prompt Design Best Practices & Limitations

**Title:** Prompt Design Best Practices & Limitations

**Programming Language:** Python

**Coding Exercise:** Yes

**Learning Objective:**
Students will build production-ready system messages, identify and mitigate LLM hallucination, handle knowledge cutoff for Nigerian applications, defend against prompt injection, and test prompt consistency.

**Description for AI:**
This topic covers professional prompt design discipline and LLM limitations. Best practices: be specific (every vague word is a decision delegated to the model); iterate and test on 5+ varied inputs; build comprehensive system messages with role, rules, constraints, and uncertainty handling; always include "say when you do not know" for factual applications. Limitations: hallucination (model generates plausible-sounding wrong facts — mitigate with grounding data or RAG); knowledge cutoff (GPT-4o cut off ~Oct 2023 — provide current Nigerian data like exchange rates in the prompt); prompt injection (malicious users craft inputs to override system instructions — defend with explicit resistance instructions and input validation); bias (models have less African training data — be explicit about Nigerian context); no memory (each API call is stateless — manage conversation history explicitly). Nigerian examples: QuickPay fintech customer service bot, Nigerian legal advisor, CBN exchange rate, Lagos court cases.

**Common Mistakes:**
- Building factual applications without uncertainty handling — model will hallucinate confidently
- Asking LLMs for current Nigerian exchange rates or inflation figures without providing the data
- Building public-facing applications without prompt injection defences
- Testing a prompt once and shipping it — always test 5+ times with varied inputs
- Treating LLM statistics as verified facts without checking sources
