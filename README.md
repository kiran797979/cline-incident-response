# Team Cline — Agentic Incident Response  
### IBM watsonx Orchestrate Proof of Concept

Cline is a proof-of-concept **agentic incident response system** built using **IBM watsonx Orchestrate**.  
It demonstrates how enterprise teams can safely use AI to investigate production incidents, make confidence-based decisions, execute approved remediations, and maintain full auditability.

---

## 🚨 Problem

Modern incident response is slow, manual, and risky.

Site Reliability Engineers (SREs) and DevOps teams must analyze logs, understand recent deployments, decide on corrective actions, and execute fixes under extreme pressure. This often results in:

- High Mean Time To Repair (MTTR)
- Risky automation or manual execution errors
- Low trust in AI-driven decisions
- Poor governance and limited auditability

As systems grow more complex, teams need **safe, confidence-aware automation**—not blind auto-fixes.

---

## 💡 Solution

Cline addresses this challenge by orchestrating **multiple specialized AI agents** using **IBM watsonx Orchestrate**, following real-world enterprise SRE practices.

Instead of relying on a single chatbot, Cline uses **agentic AI** with clear responsibility boundaries, decision policies, and safety controls. Automation is applied only when confidence is high and actions are proven safe.

---

## 🤖 Agents & Responsibilities

### 🔍 InvestigatorAgent
Defined and configured inside **IBM watsonx Orchestrate**.

Responsibilities:
- Analyze incident context (service, symptoms, logs, recent deployments)
- Produce:
  - Root cause hypothesis
  - Confidence score (0–1)
  - Ranked remediation options with idempotency metadata
- Explicitly avoids speculation when required inputs are incomplete

---

### 🧠 CoordinatorAgent
Acts as the orchestration and decision layer in **IBM watsonx Orchestrate**.

Decision rules:
- **Confidence ≥ 0.8** → Auto-remediate (only if action is idempotent)
- **Confidence 0.5 – 0.79** → Create suggested remediation ticket
- **Confidence < 0.5** → Escalate to human SRE triage

This ensures automation is fast **only when safe** and mirrors real enterprise operating models.

---

### 🛠 RemediationAgent
Managed within **IBM watsonx Orchestrate**.

Responsibilities:
- Execute only **whitelisted, idempotent actions**
- Block unsafe or non-approved operations
- Return structured audit records (action, status, timestamp, audit_id)

---

## 🔄 Architecture Flow

1. Incident is submitted via **AskOrchestrate**
2. InvestigatorAgent analyzes the incident and produces structured output
3. CoordinatorAgent evaluates confidence and policy rules
4. RemediationAgent executes the approved action (if allowed)
5. System returns a final, audited JSON response

---

## 🎬 Demo Modes

### ✅ Simulated Demo (Recommended)

No external services required.

Steps:
1. Open **IBM watsonx Orchestrate → Agent Chat**
2. Select **AskOrchestrate**
3. Paste an incident payload
4. Observe structured JSON output including:
   - Decision
   - Confidence
   - Remediation status
   - Audit record

---

### ⚙️ Live Demo (Optional)

Demonstrates real HTTP execution using a local remediation stub.

Run remediation stub:
```bash
cd remediation-stub
npm install
npm start
Expose the service:

ngrok http 3000
Configure RemediationAgent to POST to:

https://<ngrok-url>/remediate
🧪 Sample Incident Payload
{
  "alert_id": "alert-002",
  "service": "payments-api",
  "symptoms": "sudden spike of 5xx errors on /charge endpoint after deployment",
  "recent_deploy": "payments-api:v1.9.2 deployed 5 minutes ago",
  "logs": [
    "ERROR payments-db connection timeout",
    "ERROR failed to write transaction",
    "ERROR retry limit exceeded"
  ]
}
📤 Example Final Output
{
  "decision": "auto-remediate",
  "confidence": 0.9,
  "status": "success",
  "audit_id": "audit-001",
  "remediation_action": "restart payments-db",
  "remediation_status": "success"
}
🧭 Confidence-Based Outcomes (SRE-Aligned)
High confidence → Safe auto-remediation

Medium confidence → Suggested remediation ticket

Low confidence → Immediate human triage

This mirrors real enterprise SRE decision-making.

📸 Screenshots
The following screenshots validate system behavior and decision paths:

agents_list.png — Agents defined in IBM watsonx Orchestrate

scenario_high_confidence.png — Auto-remediation path

scenario_medium_confidence.png — Suggested remediation path

scenario_low_confidence.png — Human escalation path

🌟 Key Benefits
Reduced MTTR (minutes instead of hours)

Confidence-driven automation

Strong safety and governance controls

Full auditability of remediation actions

Enterprise-ready agentic AI design

🧰 Tech Stack
IBM watsonx Orchestrate

AskOrchestrate (agent interaction layer)

Agentic AI (multi-agent coordination)

Optional Node.js remediation stub

JSON-based structured decision flow

📜 License
MIT License
Cline retains intellectual property.
Hackathon participation follows IBM Dev Day rules.

👥 Team
Team Cline
Contact: bmkiran79@gmail.com
