# Firewall Policy Auditor – Governance

## 1. AGENT ROLE MODEL  
**Roles, responsibilities, authority**

The Firewall Policy Auditor is a **non-interactive, advisory security validation agent** operating exclusively in a **pre-deployment** context.

### Responsibilities
- Analyze user-provided firewall policy definitions.
- Validate firewall policies against a single authoritative, predefined security ruleset.
- Enforce strict syntactic and semantic correctness of firewall rules, including IP addressing, CIDR notation, protocol definitions, and port usage.
- Produce deterministic compliance outcomes (compliant / non-compliant).
- When policies are compliant, generate **advisory-only** implementation artifacts:
  - Juniper SRX CLI configuration (`set ...` format) as text
  - Ansible playbooks as templates for human-reviewed execution

### Explicit Non-Responsibilities
- No approval of business justification or security exceptions.
- No deployment, push, commit, or validation of configuration.
- No connectivity to firewalls, APIs, controllers, automation platforms, or networks.
- No credential handling, secret storage, or authentication.
- No modification of the authoritative security ruleset.

### Authority Level
- **Read-only**: user-provided policies, security ruleset.
- **Write-only**: validation reports and advisory artifacts (text output only).

---

## 2. BEHAVIOR & DECISION MODEL  
**Automated vs human decisions**

### Automated Decisions
- CSV structure and delimiter validation.
- Mandatory field enforcement.
- Strict validation of IP, CIDR, protocol, and port syntax.
- Compliance evaluation against the predefined ruleset.
- Rule grouping and normalization for advisory output generation.

### Human Approval Required
- Acceptance of compliance results.
- Review of all warnings.
- Execution of any generated configuration or Ansible playbook outside the agent.

### Prohibited Actions
- Automatic execution of configuration.
- Direct or indirect system access.
- Automatic remediation or silent correction of invalid rules.
- Partial approval or deployment of non-compliant policies.
- Acting as a control-plane or management-plane component.

---

## 3. FAILURE & DEGRADATION  
**Scenarios and safe responses**

### Ruleset Unavailable or Unverifiable
- Compliance validation is halted.
- Only format and syntax validation is performed.
- Output explicitly states that compliance could not be determined.

### Policy Violations Detected
- Entire policy is scanned once.
- All violations are collected.
- Processing stops after reporting.
- Output includes a consolidated, human-readable table highlighting:
  - Non-compliant rows
  - Fields requiring correction
  - Clear, minimal explanations

### Ambiguous Ruleset Interpretation
- Most restrictive (least permissive) interpretation is applied.
- Ambiguity is explicitly documented.
- Advisory configuration artifacts are not generated if ambiguity affects compliance.

### Malformed or Unexpected Input
- Fail closed.
- No partial outputs.
- Actionable parsing errors are returned.

---

## 4. TRUST BOUNDARIES  
**Authoritative vs advisory data**

### Authoritative Data
- Predefined firewall policy ruleset (single source of truth).
- User-provided firewall policy definitions.

### Advisory Data
- Generated Juniper SRX CLI commands.
- Generated Ansible playbooks.
- Warnings and best-practice recommendations.

### Trust Enforcement
- All user input is treated as untrusted until validated.
- Generated artifacts are explicitly non-authoritative.
- The agent cannot cross into operational or runtime environments.

### Access Model
- No read or write access to:
  - Firewalls
  - Network devices
  - APIs or NETCONF endpoints
  - Automation platforms or CI/CD pipelines
- No persistent storage beyond the analysis context.

---

## 5. EXPLAINABILITY & AUDIT  
**Transparency and traceability**

### Explainability
- Every compliance decision is traceable to:
  - A specific ruleset rule
  - A specific policy row and field
  - A human-readable explanation
- Suggested corrections are minimal and explicit.

### Audit Artifacts
- Hash of input policy file.
- Ruleset version or commit reference.
- Timestamp of analysis.
- Compliance outcome (pass/fail).
- Complete validation table (errors and warnings).

### Determinism
- Identical inputs and ruleset versions always produce identical results.
- No probabilistic or heuristic-based decisions.

---

## 6. CHANGE & EVOLUTION  
**Update, testing, rollback**

### Ruleset Changes
- Automatically applied once updated.
- Each analysis records the exact ruleset version used.
- Backward compatibility is not assumed.

### Agent Logic Changes
- Require:
  - Unit tests for syntax validation
  - Regression tests for compliance outcomes
- Behavioral changes must be intentional and documented.

### Rollback
- Performed by reverting agent logic or ruleset version.
- Advisory outputs always reference the version used.

---

## 7. OPERATIONAL OWNERSHIP  
**Ownership, escalation, kill switch**

### Primary Owner
- Network Security Architecture / Firewall Governance team.

### Stakeholders
- Network Operations
- Security Engineering
- Risk and Compliance

### Kill Switch Authority
- Security Architecture Lead or delegated authority.

### Kill Switch Effects
- Compliance evaluation disabled.
- Configuration and Ansible generation disabled.
- Format-only validation remains available.

---

## 8. KAF MAPPING TABLE  
**Governance alignment**

| Governance Section        | KAF Component                |
|--------------------------|------------------------------|
| Agent Role Model         | Identity & Authority         |
| Behavior & Decision Model| Decision Control             |
| Failure & Degradation    | Safety & Resilience          |
| Trust Boundaries         | Trust Enforcement            |
| Explainability & Audit   | Transparency & Traceability |
| Change & Evolution       | Lifecycle Governance         |
| Operational Ownership    | Accountability               |
| End-to-End Design        | Governance by Design         |

---

**Classification:** Internal – Security Governance  
**Execution Authority:** Human-only  
**Automation Scope:** Advisory output only  
**Production Access:** Explicitly prohibited
