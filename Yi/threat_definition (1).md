# Threat Definition – MUDT AI Learning Assistant Platform

Story: US-1.2 Identify threats and attack paths (EPIC-1 / EPA-IS-1)
Related files: `system-definition.md` (trust boundaries TB1–TB6, data flows DF01–DF18), `EPA1_US1.1_Asset_Register_v1.1.xlsx`, `EPA1_US1.2_Risk_Concept_Map_v1.1.drawio` / `.pdf`, `EPA1_US1.3_Risk_Register_v1.1.xlsx`

IDs (v1.1): assets use the team register (E/S), trust boundaries the Platform Map (TB1–TB6); BO1/BO2 are business outcomes without a team ID.

Legend: "Course" = stated in the course material (Session 1 = S1, Session 2 = S2). "Team" = the team's own reasoning or assumption, to be validated.

## Approach

- Threat actors are defined by motivation, capability and starting access.
- An actor's gain sits on the **essential assets** (E17, E14, E15, E23, E22, E24). Supporting assets (the supporting (S) assets and E25) are the paths the actor must pass through; they appear in the attack paths, not in the gain matrix.
- Every gain links back to a business outcome (BO1, E01, E26, BO2): what the actor gains is what MUDT loses.
- Threats are hypotheses. They do not claim that a vulnerability exists (S2 slide 16).

## Threat actors

| ID | Threat actor | Motivation | Capability | Starting access | Basis |
|---|---|---|---|---|---|
| T1 | Student (malicious user) | Better grades, obtaining documents | Low to medium | Normal student account | Team; derived from "malicious or compromised user" (S1 slide 33) and "student impersonation" (S1 slide 41) |
| T2 | Cybercriminal group | Money: selling data, ransom | High | None; starts from the Internet | Course: "external attacker" (S1 slides 21, 33) |
| T3 | Malicious or careless insider (lecturer, coordinator, administrator) | Revenge, personal gain, mistakes | Medium, but high privileges | Privileged account | Course: "compromised administrator" (S1 slide 33); careless insider is Team |
| T4 | Prompt injection attacker | Make the AI leak data or give wrong answers | Medium | Only needs to get content into the AI (uploaded files, prompts) | Course: prompt injection (S1 slides 25, 27); as a separate actor is Team |
| T5 | Compromised supplier or dependency (cloud provider, Claude API, software packages) | Indirect; an attacker enters through the supply chain | High | Inside the chain of trust | Course: "supplier or dependency compromise" (S1 slide 33, S2 slide 16) |
| T6 | Hacktivist or competitor | Damage reputation, create attention | Medium | None | Team |
| T7 | Academic or state-sponsored espionage | Obtain research data | High | None | Team; relevant only if research data (E24) is in scope |

T5 is not in the gain matrix: a compromised supplier is a means, not an actor with its own gain. It appears in the attack paths as an entry point.

## Gain matrix: threat actor × essential asset

| Asset | T1 Student | T2 Cybercriminal | T3 Insider | T4 Prompt injection | T6 Hacktivist | T7 Espionage |
|---|---|---|---|---|---|---|
| E17 Course materials | Get exam questions or answers in advance | Low | Leak or alter | Make the AI reveal unpublished material | Alter and publish to embarrass MUDT | – |
| E14 Student data | View other students' information | Sell data, ransom | Steal, misuse | Make the agent leak other students' data | Publish a leak | – |
| E15 Grades | **Change own grade** | Ransom | Change grades for money | Trick the agent into changing or leaking grades | Alter grades to create a scandal | – |
| E23 Official documents | Forge certificates or transcripts | Sell forged documents | Issue false documents | – | Forge to create a scandal | – |
| E22 Reporting data | Low | Ransom | Alter reports | Make reports include data that must not appear | Publish | – |
| E24 Research data | – | Sell to third parties | Take it away | – | – | **Steal** |

### Linking gain to business outcomes (example)

T1 changes own grade → integrity of E15 is broken → degrees lose credibility (E26) → enrolment suffers (E01) → funding falls (BO1).

## Entry points and trust boundaries

Entry points are where an actor first touches the platform. Trust boundaries (from the data-flow register in `system-definition.md`) are where data or control passes between zones of different trust.

| Entry point | Reached by | Boundary crossed | Data flows |
|---|---|---|---|
| User portal (S11) | T1, T2, T4, T6 | TB1 Internet → on-premises | DF01 |
| Metadata API (S28) via gateway (S25) | T2, T5 | TB3 Cloud ↔ on-premises | DF10, DF11 |
| AI agents (S30) via prompts and uploaded files | T4 | TB1, then TB3 | DF01, DF06, DF07 |
| MCP service (S21) | T4 (through an agent) | TB3 | DF12, DF13 |
| Credentials (S29) / secret store (S35) | T2, T5 | TB3 (T5 also TB5) | DF15 |
| Claude API (S31) | T5 | TB5 MUDT → third party | DF08 |
| Administrative path (S25, S07) | T3, T2 (after phishing) | TB6 Management network → servers | DF17 |

## Attack paths

Format (S2 slide 16): `Actor → entry point → boundary / data flows → enabling condition → asset → business impact`. The paths are drawn in the risk concept map and referenced in the risk register (column "Attack path").

| Path | Actor | Entry point | Boundary · flows | Enabling condition (assumed) | Asset | Business impact | Risk |
|---|---|---|---|---|---|---|---|
| AP1 Grade tampering | T1 | Portal S11 → student systems S17 | TB1 · DF01 → DF03 → DF04 | Broken server-side authorisation | E15 (I) | E26 → E01 | R03 |
| AP2 Unauthenticated Metadata API | T2 | Metadata API S28 via gateway S25 | TB1 → TB3 · DF10 → DF11 | Missing or misconfigured authentication | E22, E14 (C) | E26 | R05 |
| AP3 Prompt injection | T4 | AI agents S30 | TB1 → TB3 → TB3 · DF01 → DF06 → DF07 → DF12 → DF13 | Excessive agent capability, no output filtering | E14 (C) via S21 | E26, BO2 | R02 |
| AP4 Stolen token, API abuse | T2 or T5 | Credentials S29 / secret store S35 | TB3 (T5 also TB5) · DF15 → DF10 → DF11 | Token usable until expiry, loose rate limits | E14 (C) via S28 | E26 → E01 | R04 |
| AP5 Insider abuse | T3 | Administrative path S25, account S07 | TB6 · DF17 → DF04 | Excessive privileges, editable logs | E15, E23 (I) | E26 → E01 | R09 |
| AP6 Ransomware | T2 | Portal S11 or phishing of staff S07 | TB1 or TB6 · DF01 / DF17 → DF18 | Unpatched server, online backups | S18, S24, S23 (A) | BO2 → BO1 | R01 |

### Actor coverage

- T1–T5 each appear in at least one path. T5 is drawn as an entry route (AP4), not as an actor with its own gain.
- T6 Hacktivist has no separate path: it would use the AP2 or AP6 entry points with a publication goal instead of a financial one.
- T7 Espionage is not drawn: research data (E24) is rated low and its risk (R12) is accepted for now.

### Open decision affecting the paths

DF12 (AI agents → MCP service) crosses TB3 without the controls of the Metadata API. AP3 depends on this flow. Until the team decides to route it through the gateway or add agent authentication at the MCP service, AP3 is treated as having no existing control at DF12.
