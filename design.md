# University Verified AI Helpdesk - System Design & Architecture

## 1. Design Overview

The University Verified AI Helpdesk is designed with a singular, overriding directive: **correctness is non-negotiable**. Unlike general-purpose conversational agents where plausibility is sufficient, this system operates in a domain where accuracy is binary and liability is real. 

The design philosophy rejects the "black box" approach to AI. Instead, it treats Large Language Models (LLMs) not as knowledge bases, but as transient processing and formatting engines. The system architecture inverts the typical AI paradigm: rather than the AI having agency to retrieve and synthesize information at will, it is strictly confined to the role of a semantic interface layer, wrapping a deterministic, rule-based core. This approach prioritizes **governance over automation** and **trust over completeness**.

## 2. Architectural Style

The system follows a **Strict Layered Architecture** with unidirectional data flow and explicit boundaries. This style was chosen to isolate non-deterministic components (AI) from deterministic business logic and authoritative data.

The architecture is composed of four distinct layers:

1.  **Client Layer**: A "dumb" interface responsible solely for capturing user intent and displaying validated responses. It possesses no business logic.
2.  **Application Layer (Orchestrator)**: The central nervous system. It manages the request lifecycle, enforces authentication, and acts as the immutable gateway between the user and the intelligence services.
3.  **Intelligence & Logic Layer**: A hybrid layer containing two parallel tracks: 
    *   **The Rule Engine**: A deterministic state machine that handles known, high-stakes, or logic-heavy queries.
    *   **The Constrained AI Service**: An isolated environment for semantic parsing and natural language formatting, receiving only whitelisted context.
4.  **Data Authority Layer**: The sealed vault of verified institutional knowledge. This layer is read-only to the application runtime and write-accessible only via strict administrative pipelines.

## 3. Component Responsibilities

### Frontend
The frontend is strictly a presentation layer. Its responsibilities are limited to:
*   Securely transmitting user queries to the backend.
*   Rendering structured responses (text, citations, action buttons) exactly as received.
*   Preventing client-side manipulation of conversation history.
*   It does **not** interpret, cache, or predict responses.

### Backend API (The Orchestrator)
The Backend API serves as the system's controller. It is responsible for:
*   Identity verification and session management.
*   Request sanitization and traffic shaping.
*   Routing queries to the appropriate handler (Rule Engine vs. AI Service).
*   Enforcing global timeouts and failure boundaries.

### Rule / Orchestration Service
This component allows for "AI-free" paths. Its responsibilities include:
*   Executing deterministic logic for specific triggers (e.g., "reset password," "emergency contacts").
*   Enforcing institutional policies that cannot be left to probabilistic interpretation.
*   Pre-empting the AI layer when a query matches a known, high-priority pattern.

### AI-Assisted Components
The AI components are stripped of long-term memory and agency. Their responsibilities are:
*   **Classification**: Mapping natural language inputs to specific intent categories.
*   **Retrieval**: Converting user intent into vector database queries.
*   **Synthesis**: Formulating a human-readable response *strictly* using the retrieved snippets provided by the Orchestrator.

### Data Storage
The data layer is the single source of truth. It manages:
*   **Vector Embeddings**: For semantic search of verified documentation.
*   **Structured Knowledge Base**: Raw text, FAQs, and policy documents.
*   **Audit Logs**: Immutable records of every query, processing step, and response.

## 4. AI Placement and Constraints

AI is deployed strictly at the **edges** of the processing pipeline, never at the decision-making core.

*   **Allowed**:
    *   **Input Processing**: converting unstructured user text into structured intents.
    *   **Output Formatting**: summarizing retrieved data chunks into coherent sentences.
*   **Forbidden**:
    *   **Decision Making**: AI cannot decide *which* policy applies; it can only suggest relevance.
    *   **Knowledge Generation**: AI is forbidden from using its pre-trained internal knowledge base. It must rely 100% on provided context.
    *   **Action Execution**: AI cannot trigger database writes, API calls, or system changes directly.

These boundaries exist to contain the "blast radius" of any hallucination. By limiting the AI to formatting functions, we reduce the risk of structural errors to near zero.

## 5. Data-Centric Design

The system treats the **Knowledge Base** as the supreme authority. Unlike systems where the model is the product, here the *verified data* is the product.

*   **Knowledge Authority**: If information is not in the vector store or SQL database, it does not exist. The system is designed to return "I don't know" rather than attempt to extrapolate.
*   **Structure**: Data is chunked, versioned, and tagged with metadata (e.g., "valid_for_freshmen", "expires_2024"). This granularity allows for precise retrieval that respects domain boundaries.
*   **Versioning**: Updates to university policy are propagated immediately. The system does not require model retraining to learn new facts; it simply retrieves from the updated index.

## 6. Query Handling Design

The lifecycle of a query emphasizes classification before retrieval:

1.  **Ingestion & Sanitization**: The raw query is stripped of malicious patterns.
2.  **Intent Classification**: Specialized, small-model classifiers determine *what* the user wants (e.g., "General Info", "Personal Data Access", "Out of Scope").
3.  **Routing**:
    *   If "Personal Data": The request is routed to a deterministic API with strict auth checks.
    *   If "General Info": The system searches the Knowledge Base.
4.  **Retrieval**: The system fetches the top-k relevant fragments solely based on semantic similarity.
5.  **Synthesis**: The LLM is invoked with a rigid prompt: "Answer the user query using ONLY these provided fragments. If the fragments are insufficient, refuse to answer."

This sequence ensures that rule evaluation and security checks happen *before* the expensive and less predictable generation step.

## 7. Response Design

Responses are **structured objects**, not simple strings. A response payload includes:
*   **Content**: The synthesized text.
*   **Citations**: Direct links to the source documents used.
*   **Confidence**: A metric indicating retrieval quality.
*   **Follow-up Actions**: Deterministic buttons or links (e.g., "Go to Portal").

Free-text answers are avoided where possible in favor of selecting pre-verified snippets. When text is generated, it is constrained by low-temperature settings and strict system prompts to prevent "creative" writing. This creates clarity and ensures that every claim is inextricably linked to a verifiable source.

## 8. Failure and Fallback Design

The system treats refusal as a successful outcome. It is better to remain silent than to misinform.

*   **Explicit Refusal**: If the semantic search returns low-confidence scores (below a configured threshold), the system defaults to a canned refusal message: "I cannot find verified information regarding your query in the university handbook."
*   **Safe Fallbacks**: In the event of AI service latency or failure, the system falls back to a traditional keyword search or a directory listing.
*   **Partial Answers**: These are strictly forbidden. If the system cannot answer the *entire* question with verification, it directs the user to a human support channel.

## 9. Security and Control Considerations

Control is enforced through **Domain-Locking** and **Administrative Gates**.

*   **Admin-Only Modification**: Students and faculty constitute a read-only population. Only designated administrators can add or modify the Knowledge Base.
*   **No External Data**: The system has no internet access. It cannot browse the web to answer questions. This prevents injection of unverified external data.
*   **Prompt Injection Defense**: The system uses separate channels for system instructions and user input, combined with heuristic analysis to detect and block "jailbreak" attempts before they reach the model.

## 10. Scalability by Configuration

The system scales horizontally across institutions without retraining.

*   **Multi-Tenancy**: The application logic is generic; specific knowledge is isolated in tenant-specific database partitions (namespaces).
*   **Configuration over Training**: Deploying to a new university is a data engineering task (ingesting their PDFs/Website), not a machine learning task.
*   **Resource Isolation**: Rule sets and vector indices are distinct per tenant, ensuring that policies from University A never leak into responses for University B.

## 11. Design Trade-offs

**We purposefully sacrificed flexibility for safety.**

*   **Conversational Fluidity**: The chatbot may feel "stiff" or "bureaucratic." This is intentional. We prioritize accurate dissemination of policy over engaging conversation.
*   **Broad General Knowledge**: The system cannot answer questions about general world events, math, or coding, even if the underlying model is capable. This prevents user distraction and maintains domain focus.
*   **Speed of Implementation**: The rigorous data ingestion and verification pipeline takes longer to set up than simply pointing a bot at a website. This upfront cost is the price of reliability.

## 12. Design Limitations

*   **Data Latency**: While faster than retraining, there is still a nonzero delay between a policy change and its ingestion into the vector store.
*   **Nuance Gap**: The semantic search may miss queries that rely on deep, unspoken cultural context or highly specific slang that doesn't semantically map to formal documentation.
*   **Admin Bottleneck**: The reliance on verified data creates a dependency on administrative staff to maintain the Knowledge Base. If the data is poor, the system is poor.

## 13. Design Summary

The University Verified AI Helpdesk is an exercise in restraint. It harnesses the linguistic power of modern AI but cages it within a lattice of strict logic and verified data. By placing **data as the single authority** and **treating failure as a valid state**, the design ensures that the system serves as a reliable institutional tool rather than an experimental novelty. It is built to be trusted, not just to be talked to.
