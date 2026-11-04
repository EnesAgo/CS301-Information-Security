# MUDT AI Learning Assistant Platform – Business Value, System Parts and Common Asset Table

Version 1.1: asset IDs follow the team asset register (`Enes/CS301_Asset_Register.xlsx`, E/S IDs) and trust boundaries follow the Platform Map (TB1–TB6). BO1 (funding) and BO2 (teaching and learning service) are business outcomes with no team register ID. The v1.0 → v1.1 translation is in `EPA1_US1.1_Asset_Register_v1.1.xlsx`, sheet "ID translation".

Legend: "Course" = stated in the course material (Session 1 = S1, Session 2 = S2). "Team" = the team's own reasoning or assumption, to be validated.

## Business value chain (Team)

```
MUDT needs money (funding / tuition)
      ▲ comes from
Students (enrolment and retention)
      ▲ choose MUDT because of
Their evaluation of MUDT
      ▲ is based on their needs
 ┌────┴─────────────┐
Knowledge        Employment
 ▲ shaped by        ▲ needs
 teaching method,   reputation,
 professors,        administration (valid records
 reputation         and documents)
```

### Mapping from the chain to assets

| Link in the chain | What it depends on | Assets (see the common asset table) |
|---|---|---|
| Money | Students who enrol and pay | BO1 |
| Students | A good evaluation of MUDT | E01 |
| Evaluation | Reputation, knowledge gained, employment prospects | E26 |
| Knowledge | Teaching method, professors, reputation | BO2, E17, S01 (and E26) |
| Employment | Reputation, valid and trusted administration | E26, E15, E23 |
| Administration (legally proving that knowledge was gained) | Correct grades, records and official documents | E14, E15, E23, E22 |
| All of the above run on the platform | Systems, people and services that store, process and protect the essential assets | the supporting (S) assets and E25 |

## System parts

| Part | From the course material (evidence-based) | Implementation assumptions to add (suggested, to be validated) |
|---|---|---|
| On-premises trust zone | Student systems, grades, research data, identity/authentication, audit logging, MCP service | Deployed in the MUDT data centre's internal network and not directly exposed to the Internet; the database accepts connections from internal services only; the identity system uses SSO or LDAP; audit logs are stored centrally |
| Cloud execution zone | AI agents, orchestration, LLM inference, vector services, reporting | Deployed in a public cloud; LLM inference is provided by the Anthropic Claude API (third-party hosted, accessed over HTTPS from the cloud zone). Prompts and retrieved context are sent to Anthropic for processing; the API key is held in the cloud zone's secret store and is not exposed to end users |
| Controlled bridge (Metadata API) | Transfers only approved reporting data and is the recurring security focus; the Session 1 example mentions OAuth2 authentication | The Metadata API is a REST API exposed over HTTPS (TLS) and is the only path between the on-premises zone and the cloud zone. Callers must authenticate with OAuth2 access tokens (as in the Session 1 example); tokens are short-lived and scoped to the minimum data needed. Only approved reporting data (aggregated or metadata fields, not raw student records) is returned; the list of approved fields is defined and owned by the coordinator. Requests and responses are logged to the on-premises audit log, and rate limits apply per client. Data flows in one direction by default (on-premises to cloud, on request); the cloud zone cannot query on-premises databases directly |

## Data-flow register

Format follows Session 1, slide 31. All flows, protocols and boundary rules are team assumptions derived from the system parts above.

### Trust boundaries

| ID | Boundary (team Platform Map) | Between, in this flow register | v1.0 ID |
|---|---|---|---|
| TB1 | Internet → DMZ | Users on the Internet and the user portal (S11) | TB1 |
| TB2 | DMZ → internal zone | Not modelled separately: the portal is treated as on-premises (see note) | – |
| TB3 | Cloud → gateway → on-prem | The cloud execution zone and the on-premises trust zone | TB2 |
| TB4 | Users → cloud | Not modelled separately: prompts reach the cloud through the portal, DF06 (see note) | – |
| TB5 | Our tenant → LLM provider | The cloud execution zone and the Anthropic Claude API (S31) | TB3 |
| TB6 | Admin path | Administrator workstations and on-premises servers (S24) | TB4 |

Note: the team Platform Map places the portal in a DMZ (TB2 between DMZ and internal zone) and lets students reach the AI chat in the cloud directly (TB4). This register keeps the v1.0 architecture assumption; DF02, DF03 would cross TB2 and DF06 would become a TB4 flow under the team architecture. To be settled in KAN-23.

### Flows

| ID | Source → Destination | Data / purpose | Protocol | Boundary | Rule (assumed) |
|---|---|---|---|---|---|
| DF01 | User browser → gateway (S25) → user portal (S11) | Logins, requests, submissions, uploaded files, prompts | HTTPS 443 | TB1 | Only the portal is published to users |
| DF02 | User portal (S11) → identity (S13) | Authentication and role lookup | OIDC / SAML over HTTPS | Internal | SSO for all users; MFA for administrators |
| DF03 | User portal (S11) → student systems (S17) | Application requests | HTTPS | Internal | Portal uses the signed-in user's identity |
| DF04 | Student systems (S17) → database (S18) | Queries and results: records, grades | DB protocol over TLS | Internal | Only the student systems' service account may connect |
| DF05 | Student systems (S17) → file storage (S23) | Submissions, course files, official documents | SMB / object API over TLS | Internal | No direct user access to storage |
| DF06 | User portal (S11) → orchestration (S30) | Prompts and uploaded files for AI tutoring and document search | HTTPS | TB3 | User identity is passed along; content may contain personal data |
| DF07 | Orchestration (S30) → AI agents (S30) | Tasks and context | Internal API | Cloud internal | – |
| DF08 | AI agents (S30) → Claude API (S31) | Prompts and retrieved context | HTTPS | TB5 | API key from the secret store (S35) |
| DF09 | AI agents (S30) / vector services (S32) ↔ vector database (S32) | Embeddings and retrieved passages | Internal API | Cloud internal | – |
| DF10 | Reporting service (S44) / AI agents (S30) → gateway (S25) → Metadata API (S28) | Request with OAuth2 token; response with approved reporting data | HTTPS + OAuth2 | TB3 | Only approved, aggregated fields; rate limited; logged |
| DF11 | Metadata API (S28) → student systems (S17) | Read approved fields | Internal API | Internal | Read-only |
| DF12 | AI agents (S30) → MCP service (S21) | Tool calls for on-premises functions | HTTPS (MCP) | TB3 | **Open decision**, see below |
| DF13 | MCP service (S21) → student systems (S17) | Least-privilege calls on behalf of an agent | Internal API | Internal | Only approved tools |
| DF14 | Student systems (S17) → vector services (S32) | Course materials for embedding | HTTPS | TB3 | **Open decision**, see below |
| DF15 | Cloud services → secret store (S35) | Fetch API keys, client secrets | HTTPS | Cloud internal | Access by service identity only |
| DF16 | All components → log storage (S22) | Security and operational events | TLS / log agent | Internal and TB3 | Write-only |
| DF17 | Administrator workstation → servers (S24) | Maintenance and deployment | SSH / RDP over VPN | TB6 | VPN + MFA, separate management path |
| DF18 | Backup service → database (S18), file storage (S23) → backup storage (S26) | Encrypted backups and restores | Backup protocol | Internal | Dedicated service account |

### Finding: the Metadata API is not the only path between the zones

Drawing the flows shows four crossings of TB3 besides the Metadata API (DF10): user prompts to the cloud (DF06), agent tool calls to MCP (DF12), course materials to the vector store (DF14) and cloud logs to on-premises log storage (DF16). The bridge assumption "the only path between the on-premises zone and the cloud zone" therefore does not hold. The team must decide either to route these flows through the gateway with the same controls as the Metadata API, or to treat each as a separate entry point in the threat model.

## Business outcomes

Business outcomes are not assets with C / I / A properties. They are what is harmed when an asset is disclosed, changed or unavailable, so every C / I / A rating in the common asset table names the outcome it affects.

| ID | Outcome | What it is | Depends on | Harmed when |
|---|---|---|---|---|
| BO1 | Funding and revenue (tuition) | The income MUDT receives, mainly student fees | E01 | Enrolment falls or operations stop |
| E01 | Students and the student relationship (enrolled student body) | The number of students who join and stay | E26, BO2, E23 | MUDT's reputation drops, teaching is poor or its documents are not trusted |
| E26 | Reputation and trust (students, applicants, partners, regulators) | How students, employers and the public judge MUDT | BO2, E17, E14, E15, E23, E22 | Data leaks, grades or documents are altered, or AI answers are wrong |
| BO2 | Teaching and learning service | The functions through which students gain knowledge: course delivery, AI tutoring, document search, learning support | E17, S17, S13, E25, S21, S28, S30, S31, S32, S44, S25, S35, S01, S11 | The platform is unavailable or delivers wrong content |

## Common asset table

One table for the 27 assets (team register E and S IDs); "Category" marks essential or supporting. Columns follow Session 2, slide 11. C / I / A = High (H), Medium (M), Low (L). Each rating states the consequence and the business outcome it harms (BO1, E01, E26, BO2). Ratings and owner roles are team proposals.


| ID | Asset | Category | Type | Business value | Owner role | C / I / A and impact on outcomes | Dependencies | Assumptions |
|---|---|---|---|---|---|---|---|---|
| E14 | Student personal records | Essential | Information | Legal duty to protect; basis of administration and learning insights | Data protection officer | **C: H** – disclosure is a GDPR breach and loses trust (E26 → E01) · **I: H** – wrong records distort support and reporting (BO2, E26) · A: M – administration is delayed | S17, S13, E25, S26 | Analytics data is derived from student records |
| E15 | Grades and assessment results | Essential | Information | Legal proof that knowledge was gained | Coordinator | **C: H** – leaked grades breach privacy and trust (E26) · **I: H** – altered grades invalidate qualifications, so degrees lose value for employment (E26 → E01) · A: M – results are delayed | S17, S13, E25, S26 | Grades stay on-premises |
| E17 | Course materials (slides, exercises, readings) | Essential | Information | Quality of knowledge delivered | Lecturers | C: M – leaked material undermines fair assessment (E26) · **I: H** – altered content teaches wrong knowledge (BO2, E26) · A: M – teaching continues with delays (BO2) | S17, S13 | Stored in student systems |
| E22 | Approved reporting dataset (metadata over the Metadata API) | Essential | Information | Management and coordination reporting | Coordinator | **C: H** – sensitive data in reports is a GDPR breach (E26) · **I: H** – wrong reports lead to wrong decisions (E26, BO1) · A: M – reporting is delayed | S28, S44, S17 | Approval rules owned by coordinator |
| E23 | Official documents issued (transcripts, certificates) | Essential | Information | Legally valid; needed for employment | Coordinator | C: M – limited personal data (E26) · **I: H** – forged or changed documents lose legal value and employers stop trusting MUDT degrees (E26 → E01) · A: M – documents are issued late | S17, S13, E25, S26, S07 | Printed on request by coordinators |
| E24 | Research data and unpublished outputs | Essential | Information | Academic research work (on-premises, S1 slide 5); little exists today | Research lead | C: L – MUDT holds little research data, so a leak has little effect (E26) · I: M – altered data weakens research results (E26) · A: L – short outages have little effect | S17, S13 | MUDT is a new school with little research output (context assumption); re-assess when research grows |
| E25 | Audit logs and activity evidence | Supporting | Software / data | Evidence for investigation | IT security | C: M – logs contain user activity (E26) · **I: H** – tampered logs hide attacks and remove proof (E26) · A: M – investigation is delayed | S17, S25, S22 | Stored centrally on-premises |
| S01 | Lecturers (teaching staff) | Supporting | People | Knowledge delivery depends on their expertise | Head of teaching | C: L · I: M – errors in teaching lower quality (BO2) · A: M – absence lowers teaching quality (BO2) | – | Teaching quality depends on them |
| S07 | System administrators | Supporting | People | Operate and maintain systems and records; privileged access | Management | C: M – know sensitive records (E26) · **I: H** – misuse or compromise changes records or configuration (E26) · A: M – administration is delayed | S13 | – |
| S11 | Web portal and course platform front end (DMZ, reverse proxy + WAF) | Supporting | Software | Only way students, lecturers and coordinators reach the services | IT operations | C: M – shows user data (E26) · **I: H** – altered pages mislead users or steal logins (E26) · **A: H** – no portal means no access to any service (BO2) | S17, S13, S25, S24 | Web application hosted on-premises behind the perimeter (not stated in the course) |
| S13 | Identity Provider (SSO), directory and MFA | Supporting | Software | Decides who may access what | IT security | **C: H** – stolen identities open every service (E26) · **I: H** – manipulated roles allow grade changes (E26) · **A: H** – nobody can log in, so teaching stops (BO2) | S25, S24 | SSO or LDAP |
| S17 | Student Information System (SIS) | Supporting | Software | Stores and processes E17, E14, E15, E23, E22, E24 | IT operations | **C: H** – exposes student data and grades (E26) · **I: H** – corrupts grades and records (E26 → E01) · A: M – administration and teaching are interrupted (BO2) | S13, S25, S18, S24, S23 | On-premises, internal access only |
| S18 | Grade database | Supporting | Infrastructure | Stores structured records, grades and submissions for the student systems | IT operations | **C: H** – holds all student records and grades (E26) · **I: H** – changes alter grades and records (E26 → E01) · A: M – student systems stop (BO2) | S25, S24 | Relational DBMS on-premises; only the student systems' service account may connect |
| S21 | MCP service and its tool permissions | Supporting | Software | Gives AI agents controlled access to on-premises functions | IT operations | **C: H** – agents could pull student data through it (E26) · **I: H** – wrong tool results mislead students (BO2) · A: M – AI features degrade (BO2) | S17, S13, S24 | Sits in the on-premises zone |
| S22 | Audit and logging service (SIEM) | Supporting | Infrastructure | Central store and alerting for audit logs | IT security | C: M – logs contain user activity (E26) · **I: H** – logs must be tamper-proof to serve as evidence (E26) · A: M – alerts are missed | S25, S24 | Components write logs to it; they cannot read or delete |
| S23 | Sensitive data storage | Supporting | Infrastructure | Stores submissions, course material files and official documents | IT operations | C: M – holds submissions and documents (E26) · **I: H** – altered official documents lose legal value (E26 → E01) · A: M – documents and materials are delayed (BO2) | S25, S24 | On-premises storage, accessed through the student systems only |
| S24 | On-prem servers, hypervisors and VMs | Supporting | Infrastructure | Hosts every on-premises service | IT operations | C: M – access to hosted data (E26) · **I: H** – a compromised host controls everything it runs (E26, BO2) · **A: H** – all on-premises services stop (BO2) | S25 | Virtual machines in the MUDT data centre |
| S25 | On-prem network, firewall and DMZ | Supporting | Infrastructure | Separates zones and publishes only the Metadata API | IT operations | C: M – traffic metadata is visible · **I: H** – wrong rules expose internal systems (E26) · **A: H** – all services go offline (BO2) | – | Not stated in the course |
| S26 | Backups (on-prem and off-site copy) | Supporting | Information | Allow recovery after incidents | IT operations | **C: H** – contain full copies of student data (E26) · **I: H** – corrupted backups prevent recovery (BO2, E26) · A: M – recovery is slower | S18, S23 | Not stated in the course |
| S28 | Secure API Gateway and Metadata API (incl. its configuration) | Supporting | Service | Only path between zones; recurring security focus (S1 slide 5) | IT operations | **C: H** – exposure discloses learning data (E26; S1 slide 21) · **I: H** – altered reports (E26) · A: M – AI features and reports degrade (BO2) | S13, S29, S25, S24 | REST over HTTPS, OAuth2 |
| S29 | API tokens, mTLS certificates and keys | Supporting | Information | Enable access between components | IT security | **C: H** – a stolen token enables API abuse and data theft (E26; S1 slide 25) · I: M – replaced credentials break access (BO2) · A: M – services fail to authenticate (BO2) | S35 | Held in a secret store |
| S30 | Agent orchestrator and AI agents (incl. system prompts) | Supporting | Software | Carry out tutoring and reporting tasks + Coordinates agents and tools | AI service owner | C: M – answers may expose data (E26) · **I: H** – prompt-injected agents give wrong or harmful answers (BO2, E26; S1 slide 25) · A: M – AI tutoring is unavailable (BO2) + C: M – sees request content (E26) · **I: H** – misrouting changes what agents do (BO2) · A: M – AI features stop (BO2) | S21, S28, S30, S31, S32, S35 + S35 | Run in the cloud zone |
| S31 | LLM inference service / model provider | Supporting | External service | Generates answers | AI service owner | C: M – prompts leave MUDT (E26) · **I: H** – wrong answers reach students (BO2) · A: M – AI features stop (BO2) | S29, S35 | Third-party hosted, accessed over HTTPS |
| S32 | Vector database (embeddings of course materials) | Supporting | Software / data + Infrastructure | Retrieval of documents for AI answers + Stores embeddings used for AI retrieval | AI service owner | C: M – retrieved content may leak (E26) · I: M – poisoned retrieval lowers answer quality (BO2) · A: M – search degrades (BO2) + C: M – embeddings can leak content (E26) · I: M – altered embeddings lower answer quality (BO2) · A: M – search degrades (BO2) | S35, S32 + S35 | Store embeddings or text (to be decided) + Managed vector database in the cloud zone |
| S35 | Cloud tenant: compute, IAM and key vault | Supporting | Infrastructure | Hosts the cloud execution zone + Holds API keys, client secrets and signing keys | IT operations + IT security | C: M – tenant data could be exposed (E26) · I: M – misconfiguration changes AI services (BO2) · **A: H** – AI services stop (BO2) + **C: H** – holds every credential of the cloud zone (E26) · **I: H** – a replaced secret redirects trust (E26, BO2) · A: M – services cannot fetch secrets (BO2) | – + S35 | Third-party provider + Managed vault of the cloud provider |
| S42 | Policies and GDPR documentation (access policy, AI use policy, DPIA, retention) | Supporting | Organisational | Define rules and legal duties | Management | C: L · I: M – outdated rules weaken compliance (E26) · A: L | – | – |
| S44 | Reporting dashboard application | Supporting | Software | Produces reports | IT operations | C: M – handles approved data only (E26) · **I: H** – wrong reports mislead management (E26, BO1) · A: M – reporting is delayed | S28, S35 | Runs in the cloud zone |


## Definitions and implementation

"What it is" describes the asset. "How it is implemented" is the team's assumption, to be validated. Where the implementation needs something that is itself worth protecting (a database, servers, storage), that item is its own asset in the common asset table.

| ID | Asset | What it is | How it is implemented (assumed) |
|---|---|---|---|
| BO1 | Funding and revenue (tuition) | The income MUDT receives, mainly student fees, which pays for running the university | Business outcome, no technical implementation; depends on enrolment (E01) |
| E01 | Students and the student relationship (enrolled student body) | The number of students who join and stay at MUDT | Business outcome; enrolment records are kept in the student systems (S17) |
| E26 | Reputation and trust (students, applicants, partners, regulators) | How students, employers and the public judge MUDT | Intangible; built from teaching results, correct records and protected data |
| BO2 | Teaching and learning service | The functions through which students learn: course delivery, AI tutoring, document search, learning support | Users reach it through the portal (S11); AI features run as agents (S30) in the cloud and reach on-premises data through the MCP service (S21) and the Metadata API (S28) |
| E14 | Student personal records | Identity and contact data, submissions, progress and the analytics derived from them | Records in the database (S18), submitted files in file storage (S23); analytics leave the on-premises zone only as aggregated data through the Metadata API |
| E15 | Grades and assessment results | Marks and assessment outcomes of each student | Rows in the database (S18) written by the student systems; every change is recorded by audit logging (E25) |
| E17 | Course materials (slides, exercises, readings) | Slides, notes, assignments and reading material | Files in file storage (S23) managed by the student systems (S17); copies may be embedded in the vector database (S32) for AI retrieval |
| E22 | Approved reporting dataset (metadata over the Metadata API) | Aggregated data approved for transfer, and the reports produced from it | Delivered by the Metadata API (S28) to the reporting service (S44); the approved field list is owned by the coordinator |
| E23 | Official documents issued (transcripts, certificates) | Certificates, transcripts and enrolment confirmations | Uploaded or generated by coordinators in the student systems, stored in file storage (S23) with metadata in the database (S18); printed on request |
| E24 | Research data and unpublished outputs | Data from research activities held on-premises | Stored in the database (S18) or file storage (S23); little research data exists today |
| E25 | Audit logs and activity evidence | The record of who accessed or changed what, including API calls | Log agents on each component send events to log storage (S22) |
| S01 | Lecturers (teaching staff) | The people who teach, write materials and grade | Accounts in identity (S13) with a lecturer role; no technical implementation |
| S07 | System administrators | The people who run administration and operate the systems | Accounts in identity (S13) with elevated roles; administrators work through the management path |
| S11 | Web portal and course platform front end (DMZ, reverse proxy + WAF) | The web interface through which students, lecturers and coordinators use the platform | Web application hosted on-premises behind the perimeter (S25); signs users in through identity (S13) and fronts the student systems and AI features |
| S13 | Identity Provider (SSO), directory and MFA | The system that verifies users and decides what they may do | Identity provider with SSO and an LDAP directory on on-premises servers (S24); roles for student, lecturer and coordinator; MFA for administrators |
| S17 | Student Information System (SIS) | The application software that manages records, grades, submissions and course materials | Business application on on-premises servers (S24); stores data in the database (S18) and file storage (S23); users authenticate through identity (S13) |
| S18 | Grade database | The database system that stores structured records | Relational DBMS on on-premises servers (S24); only the student systems' service account may connect; TLS in transit, encryption at rest |
| S21 | MCP service and its tool permissions | A server that offers a limited set of approved on-premises functions to AI agents as tools (Model Context Protocol) | Service on on-premises servers (S24); authenticates each agent and calls the student systems with least privilege |
| S22 | Audit and logging service (SIEM) | The central store for logs, with search and alerts | On-premises collector and storage; components may write but not read or delete; alerts go to IT security |
| S23 | Sensitive data storage | Storage for submitted work, course files and official documents | On-premises file or object storage; accessed through the student systems only |
| S24 | On-prem servers, hypervisors and VMs | The machines in the MUDT data centre that run the on-premises services | Virtualised hosts running S17, S13, S21, S28, S18, S23, S22 and S11 |
| S25 | On-prem network, firewall and DMZ | Network segmentation and the perimeter that separates MUDT's network from the Internet | Firewalls and VLANs; an API gateway publishes only the Metadata API (and the portal, if used from outside); administrators use a separate management path |
| S26 | Backups (on-prem and off-site copy) | Copies of data used to recover after an incident | Scheduled, encrypted backups of the database (S18) and file storage (S23) to separate storage on the same network (not offline); restore tests are not scheduled yet |
| S28 | Secure API Gateway and Metadata API (incl. its configuration) | The controlled interface between the on-premises and cloud zones that returns only approved reporting data | REST API over HTTPS on an on-premises server (S24), published through the gateway (S25); callers use OAuth2 tokens (S29) |
| S29 | API tokens, mTLS certificates and keys | The secrets that let components prove their identity | Stored in the cloud secret store (S35); OAuth2 tokens are short-lived |
| S30 | Agent orchestrator and AI agents (incl. system prompts) | Software that plans and carries out tasks using the LLM and tools + The component that routes requests to agents and tools and keeps workflow state | Agent processes in the cloud environment (S35); call the LLM (S31), retrieve through vector services (S32), reach on-premises data only through S21 and S28 + Workflow service running in the cloud environment (S35) |
| S31 | LLM inference service / model provider | The language model that generates answers | Called over HTTPS with an API key (S29); MUDT does not host a model |
| S32 | Vector database (embeddings of course materials) | The retrieval layer: turns documents into embeddings and finds similar content + The database that stores embeddings for similarity search | Embedding pipeline plus the vector database (S32) in the cloud environment (S35) + Managed vector database in the cloud environment (S35) |
| S35 | Cloud tenant: compute, IAM and key vault | The third-party cloud platform that hosts the cloud zone + The managed vault that holds secrets | A tenant at a public cloud provider: compute, networking and access management hosting S30, S32 and S44, plus the key vault + The cloud provider's key vault, with access limited by service identity |
| S42 | Policies and GDPR documentation (access policy, AI use policy, DPIA, retention) | Written rules and agreements: data protection procedures and contracts with the cloud and API providers | Documents held by management; no technical implementation |
| S44 | Reporting dashboard application | The service that builds reports from approved data | Service in the cloud environment (S35) that pulls data through the Metadata API (S28) |

Sources: asset table columns from S2 slide 11; zone components from S1 slide 5; use cases from S1 slide 8; risk examples from S1 slides 21 and 25; design objectives from S1 slide 23. The value chain, asset selection, ratings and owner roles are the team's own reasoning.
