# The $100,000 Software Renewal Story - Workflow Diagram

Here is the visual representation of how the Termora architecture handles the "$100,000 Software Renewal" scenario.

```mermaid
sequenceDiagram
    autonumber
    
    actor CFO
    participant Frontend as Termora UI (Next.js)
    participant API as API Gateway (FastAPI)
    participant AI as AI Agent (LangGraph)
    participant DB as Vector DB (ChromaDB)
    participant Policy as Policy Engine (Deterministic)
    participant MCP as MCP Layer (Slack/Jira)

    Note over API: 1. THE SETUP
    API->>API: User uploads Vendor Software PDF
    API->>DB: Store contract as Vector Embeddings

    Note over AI,DB: 2. AI UNDERSTANDS & DETECTS
    API->>AI: Trigger Contract Analysis Workflow
    AI->>DB: Query for "Renewal" & "Penalty" clauses
    DB-->>AI: Returns "Clause 7.2: 30% price jump if not canceled"
    AI->>AI: Calculate Financial Impact: $100,000
    AI->>API: AI Recommendation: "Draft Cancellation Notice"

    Note over API,Policy: 3. POLICY ENGINE GOVERNS (CRITICAL STEP)
    API->>Policy: Send AI Recommendation for Validation
    Policy->>Policy: Check Rule: "If Impact > $10k -> Require CFO Approval"
    Policy->>Policy: Math Check: $100,000 > $10,000 == TRUE
    Policy-->>API: Action BLOCKED 🛑. Status changed to PENDING_APPROVAL.

    Note over CFO,Frontend: 4. HUMAN DECIDES
    API-->>Frontend: Display PENDING_APPROVAL alert on Dashboard
    CFO->>Frontend: Logs in, reviews AI risk vs Policy rule
    CFO->>Frontend: Clicks "APPROVE" ✅
    Frontend->>API: Submit Approval

    Note over API,MCP: 5. SYSTEM EXECUTES
    API->>MCP: Trigger external actions via Model Context Protocol
    MCP->>MCP: Draft Slack msg to Legal Team
    MCP->>MCP: Create Jira Ticket for Procurement
```

### Key Takeaways from the Diagram:
* **Steps 2 to 3:** Notice how the AI does NOT talk directly to the execution layer (MCP). It *must* pass through the Policy Engine. This is the core of your deterministic governance.
* **Step 4:** The human (CFO) is kept in the loop specifically because the mathematical rule in Step 3 required it.
