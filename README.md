# Team Cline — Agentic Incident Response (watsonx Orchestrate PoC)

This is a  proof-of-concept multi-agent incident response system built using **IBM watsonx Orchestrate**.  
It demonstrates how agentic AI can safely investigate incidents, make confidence-based decisions, execute approved remediations, and produce auditable results.

---

## Overview

Modern incident response is slow, manual, and risky. Engineers must analyze logs, assess deployments, decide on actions, and execute fixes under pressure.  
Cline addresses this by orchestrating multiple specialized AI agents that collaborate safely and efficiently.

The system is composed of three agents:

- **InvestigatorAgent**  
	Analyzes incident context (service, symptoms, logs, recent deployments) and returns:
	- Root cause hypothesis  
	- Confidence score (0–1)  
	- Ranked remediation options with idempotency metadata  

- **CoordinatorAgent**  
	Applies deterministic decision rules:
	- Confidence ≥ 0.8 → auto-remediate (if idempotent)
	- Confidence 0.5–0.79 → create remediation ticket
	- Confidence < 0.5 → escalate to human triage  

- **RemediationAgent**  
	Executes only whitelisted, idempotent actions and returns structured audit records.

---

## Architecture Flow

1. Incident is submitted through AskOrchestrate
2. InvestigatorAgent analyzes and produces structured output
3. CoordinatorAgent evaluates confidence and policy
4. RemediationAgent executes approved action (if allowed)
5. System returns final audited JSON response

---

## Demo Modes

### Simulated Demo (Recommended)

1. Open IBM watsonx Orchestrate → Agent Chat
2. Select **AskOrchestrate**
3. Paste an incident payload
4. Observe the final structured JSON output with:
	 - Decision
	 - Confidence
	 - Remediation action
	 - Audit ID

No external services required.

---

### Live Demo (Optional)

A local remediation stub can be used to demonstrate real HTTP execution.

#### Run remediation stub
```bash
cd remediation-stub
npm install
npm start

Expose with ngrok
ngrok http 3000


Configure the RemediationAgent to POST to:

https://<ngrok-url>/remediate

Sample Incident Payload
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

Example Final Output
{
	"decision": "auto-remediate",
	"confidence": 0.9,
	"status": "success",
	"audit_id": "audit-001",
	"remediation_action": "restart payments-db",
	"remediation_status": "success"
}

Key Benefits

Reduced MTTR (minutes instead of hours)

Confidence-based automation

Strict safety and governance controls

Fully auditable remediation actions

Enterprise-ready agentic design

Tech Stack

IBM watsonx Orchestrate

Agentic AI (multi-agent coordination)

Optional Node.js remediation stub

JSON-based structured decision flow

License

MIT License
Cline retains intellectual property. Hackathon participation follows IBM Dev Day rules.

Team

Cline
Contact: bmkiran79@gmail.com
