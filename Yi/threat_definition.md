# Threat Definition – MUDT AI Learning Assistant Platform

Story: US-1.2 Identify threats and attack paths (EPIC-1 / EPA-IS-1)
Related files: `system-definition.md`, `EPA1_US1.1_Asset_Register_v1.0.xlsx`

Legend: "Course" = stated in the course material (Session 1 = S1, Session 2 = S2). "Team" = the team's own reasoning or assumption, to be validated.

## Approach

- Threat actors are defined by motivation, capability and starting access.
- An actor's gain sits on the **essential assets** (A05–A10). Supporting assets (A11–A34) are the paths the actor must pass through; they appear in the attack paths, not in the gain matrix.
- Every gain links back to a business outcome (A01–A04): what the actor gains is what MUDT loses.
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
| T7 | Academic or state-sponsored espionage | Obtain research data | High | None | Team; relevant only if research data (A10) is in scope |

T5 is not in the gain matrix: a compromised supplier is a means, not an actor with its own gain. It appears in the attack paths as an entry point.

## Gain matrix: threat actor × essential asset

| Asset | T1 Student | T2 Cybercriminal | T3 Insider | T4 Prompt injection | T6 Hacktivist | T7 Espionage |
|---|---|---|---|---|---|---|
| A05 Course materials | Get exam questions or answers in advance | Low | Leak or alter | Make the AI reveal unpublished material | Alter and publish to embarrass MUDT | – |
| A06 Student data | View other students' information | Sell data, ransom | Steal, misuse | Make the agent leak other students' data | Publish a leak | – |
| A07 Grades | **Change own grade** | Ransom | Change grades for money | Trick the agent into changing or leaking grades | Alter grades to create a scandal | – |
| A08 Official documents | Forge certificates or transcripts | Sell forged documents | Issue false documents | – | Forge to create a scandal | – |
| A09 Reporting data | Low | Ransom | Alter reports | Make reports include data that must not appear | Publish | – |
| A10 Research data | – | Sell to third parties | Take it away | – | – | **Steal** |

### Linking gain to business outcomes (example)

T1 changes own grade → integrity of A07 is broken → degrees lose credibility (A03) → enrolment suffers (A02) → funding falls (A01).

## Next steps

1. Confirm the actor list (remove or add actors).
2. Add capability and opportunity to each cell: can the actor reach the asset, and from which entry point?
3. Select five assets (at least two essential, varied supporting assets) and draft attack paths (S2 slide 16):
   `Actor → entry point → enabling condition → asset → business impact`
