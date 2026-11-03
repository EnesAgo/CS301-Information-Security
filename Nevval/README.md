# EPA-IS-1 Assess: individual artefact pack (Hafsa Nevval Yaman)

| | |
|---|---|
| **Author** | Hafsa Nevval Yaman ([@nevval2206](https://github.com/nevval2206)) |
| **System** | MUDT AI Learning Assistant Platform |
| **Course** | CS-301 Basics of Information Security |
| **Epic** | KAN-4 EPA-IS-1 Assess security risks and threats |
| **Review** | Sprint 1 review (Week 4) |
| **Version** | Artefacts v1.0 (2026-10-30), committed 2026-11-03 |
| **Status** | Ready for Sprint 1 review |

Each student prepares all three EPA-IS-1 artefacts individually. This folder holds my version.

## Artefacts

| Jira story | Story | Artefact | Editable source | Review copy |
|---|---|---|---|---|
| KAN-11 | US-1.1 Identify critical assets | Asset register | [`EPA1_US1.1_Asset_Register_v1.0.xlsx`](EPA1_US1.1_Asset_Register_v1.0.xlsx) | [`EPA1_US1.1_Asset_Register_v1.0.pdf`](EPA1_US1.1_Asset_Register_v1.0.pdf) |
| KAN-9 | US-1.2 Identify threats and attack paths | Basic Risk concept map (3 pages: concept chain, attack paths, system context) | [`EPA1_US1.2_Risk_Concept_Map_v1.0.drawio`](EPA1_US1.2_Risk_Concept_Map_v1.0.drawio) | [`EPA1_US1.2_Risk_Concept_Map_v1.0.pdf`](EPA1_US1.2_Risk_Concept_Map_v1.0.pdf) |
| KAN-10 | US-1.3 Prioritise risks | Basic Risk register | [`EPA1_US1.3_Risk_Register_v1.0.xlsx`](EPA1_US1.3_Risk_Register_v1.0.xlsx) | [`EPA1_US1.3_Risk_Register_v1.0.pdf`](EPA1_US1.3_Risk_Register_v1.0.pdf) |

## Scope

I selected five assets from the team's 75-asset register (`Enes/CS301_Asset_Register.xlsx`, final version):
- **E15** Grades and assessment results
- **E20** Student–AI conversations and prompts
- **S13** Identity Provider (SSO), directory and MFA
- **S28** Secure API Gateway and Metadata API
- **S30** Agent orchestrator and AI agents

The selection follows the Session 3 rule: two essential assets, plus supporting assets from three different zones (on-prem, bridge, cloud). Every selected asset sits on a trust boundary. Six attack paths (AP1–AP6) lead to six risks (R1–R6), scored on a 5×5 likelihood × impact scale:
- **Critical:** R2, prompt injection that abuses the gateway (the Session 1 incident pattern).
- **High:** R1, R3, R4, R6.
- **Medium:** R5.

## ID conventions

| Prefix | Meaning | Where defined |
|---|---|---|
| E / S | Essential / supporting asset | Team register IDs, used unchanged |
| TB | Trust boundary | Platform Map, translated to the team register's IDs |
| TA | Threat actor | Risk register tab 3 |
| EP | Entry point | Risk register tab 3 |
| AP | Attack path | Concept map page 2; risk register tab 4 |
| R | Risk | Risk register tab 1 |
| A / Q | Assumption / open question | Asset register tab 6 |
| UC | Use case | Team register |

Asset IDs follow the team register, so they can be checked directly against `Enes/CS301_Asset_Register.xlsx`. The Platform Map (`Enes/MUDT Platform Map.md`) uses older v1 asset IDs. For example, the map's S13 means API tokens, but in the register S13 is the Identity Provider. Asset register tab 7 translates the map's IDs to the register's.

## Acceptance criteria and evidence

| Story | Acceptance criterion | Evidence |
|---|---|---|
| US-1.1 | Architecture evidence is referenced | Concept map page 3; asset register columns "Trust boundaries" and "Evidence / source" |
| US-1.1 | Essential and supporting assets are distinguished | Asset register column C (2 essential, 3 supporting) |
| US-1.1 | Business value and CIA priorities are justified | Asset register: a separate C, I and A reason for every rating |
| US-1.1 | Asset owners, assumptions and dependencies are recorded | Asset register owner and dependency columns; tab 6 |
| US-1.2 | Threat actors and goals are stated | Risk register tab 3 (TA1–TA4) |
| US-1.2 | Entry points and trust boundaries are identified | EP1–EP7, TB1–TB6; concept map page 3 |
| US-1.2 | Attack paths connect actors to affected assets | Concept map page 2 (AP1–AP6); risk register tab 4 |
| US-1.2 | Assumptions are explicit; no unverified vulnerabilities are claimed | Every condition is written as "If …"; assumption A-12 |
| US-1.3 | At least three credible risk scenarios (KAN-14) | Six risks, R1–R6 |
| US-1.3 | Risk statements link asset, threat, vulnerability and impact | Risk register column D (Session 3 sentence template) |
| US-1.3 | Likelihood and impact have written rationale | Risk register columns P and R |
| US-1.3 | Priority is assigned consistently | Score and band are formulas (columns S and T); tie-break rule in tab 0 |
| US-1.3 | Assumptions are documented | Risk register tab 6 |
| US-1.3 | Initial treatment is recommended | Risk register columns U onwards (option, controls, control type, owner) |
| All | Assets, attack paths and risks are cross-referenced | Risk register tab 5 (traceability matrix) |

## Open items to confirm with the lecturer

- **Q-01:** Which case study Sprint 1 should use (the MUDT platform or the commercial application from the slides).
- **A-11:** The 5×5 risk scale.
- **A-03:** The pseudonymisation assumption, which changes R2 and R3 the most.

## AI use statement

I used Claude (Anthropic) as an assistant for this pack. Claude:
- drafted the artefacts from the course materials (Sessions 1–3, the delivery guideline and templates) and the team asset register;
- generated the Excel, diagrams.net and PDF files;
- prepared the Git commits.

My part:
- I chose the case and the scope.
- I reviewed the asset selection, every CIA rating, attack path, likelihood and impact score, assumption and treatment.
- I can explain and defend each of them.

Where the course material does not state something, the artefacts mark it as an assumption (A-xx) instead of a fact. Session 1 slide 17: "The answer may be AI-generated. The explanation must be owned."
