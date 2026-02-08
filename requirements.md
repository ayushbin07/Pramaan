# University Verified AI Helpdesk: Requirements Summary

## 1. Vision & Core Philosophy
*   **Mission:** Bridge the gap between student inquiry volume and staff capacity.
*   **Refusal as a Feature:** In this domain, "I don't know" is safer than a plausible guess. The system prioritizes institutional trust over helpfulness.
*   **Data as Authority:** The verified knowledge base is the primary intelligence; AI is merely a semantic interface.
*   **Zero Tolerance:** Unlike consumer apps, academic advice has real financial/academic consequences. Accuracy must be 100%.

## 2. Problem Statement
*   **Student Confusion:** Critical info (deadlines, policies) is scattered across PDFs and sites.
*   **AI Risks:** Generic chatbots hallucinate policies, causing students to miss deadlines or lose aid.
*   **Staff Overload:** Administrators waste hours on repetitive faqs instead of complex student needs.

## 3. System Capabilities & Scope
### Included (In-Scope)
*   **Academic Policies:** Grading, withdrawal, academic standing.
*   **Procedures:** Registration steps, form submission handling.
*   **Deadlines:** Financial aid, drop/add dates (context-aware).
*   **Eligibility Criteria:** Rules for programs (but not personal decisions).

### Explicitly Excluded (Out-of-Scope)
*   **Personal Advice:** No career counseling, mental health support, or academic path planning.
*   **Decision Making:** The system never determines student eligibility or waives requirements.
*   **Private Data:** No access to student grades, transcripts, or financial balances.
*   **Chatting:** No small talk or personality simulation.

## 4. Key Functional Requirements
*   **Intent Classification:** AI identifies *what* the student wants (e.g., "deadline", "procedure") without generating facts.
*   **Deterministic Retrieval:**
    *   Query parameters (intent + entities) map to specific knowledge base entries.
    *   No "fuzzy" logic for policy application.
*   **Response Generation:**
    *   AI formats retrieved text into natural language.
    *   Must cite sources (e.g., "According to the 2024 Handbook...").
    *   Must include caveats (e.g., "This applies to undergraduates only.").
*   **Clarification:** If a query is ambiguous, the system asks for details instead of guessing.

## 5. Non-Functional Requirements
*   **Reliability:** Consistent answers for the same query, regardless of phrasing.
*   **Transparency:** Users must know data comes from a database, not an "AI mind."
*   **Availability:** 24/7 access to policy info (even if staff are offline).
*   **Auditability:** Every interaction is logged to trace *why* an answer was given.

## 6. Data Governance
*   **Human Verification:** No automated web scraping. Every fact is entered/approved by staff.
*   **Ownership Model:** Specific university depts own specific knowledge chunks (e.g., Registrar owns deadlines).
*   **Versioning:** Updates tracked with audit trails (who changed what, when).
*   **Metadata:** Data tagged with applicability (e.g., "Valid for Fall 2025").

## 7. AI Governance & Risk Control
*   **Constrained Role:** AI is strictly for:
    1.  Understanding user input (Parsing).
    2.  Formatting system output (Phrasing).
*   **No Fact Generation:** The model is prohibited from using its internal training data to answer.
*   **Tone Control:** Professional, neutral, institutional. No empathy simulation.

## 8. Failure Handling (Safe Fallbacks)
*   **Low Confidence:** If AI isn't sure of intent -> Ask for clarification.
*   **No Data:** If knowledge base has no answer -> Explicit refusal ("I cannot answer this.").
*   **Out of Scope:** If user asks for advice -> "Please contact an advisor."
*   **Technical Error:** If system fails -> "Service unavailable, please contact [Phone]."

## 9. Scalability & Adaptability
*   **Configuration over Code:** Adapt to new universities by changing data, not code.
*   **Terminologies:** Configurable synonyms (e.g., "Module" vs "Course").
*   **Multilingual Ready:** Architecture supports templated translation.
