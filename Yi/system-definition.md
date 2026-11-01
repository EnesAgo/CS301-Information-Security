# MUDT AI Learning Assistant Platform – Business Value, System Parts and Common Asset Table

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
| Money | Students who enrol and pay | A01 |
| Students | A good evaluation of MUDT | A02 |
| Evaluation | Reputation, knowledge gained, employment prospects | A03 |
| Knowledge | Teaching method, professors, reputation | A04, A05, A25 (and A03) |
| Employment | Reputation, valid and trusted administration | A03, A07, A08 |
| Administration (legally proving that knowledge was gained) | Correct grades, records and official documents | A06, A07, A08, A09 |
| All of the above run on the platform | Systems, people and services that store, process and protect the essential assets | A11–A27 |

## System parts

| Part | From the course material (evidence-based) | Implementation assumptions to add (suggested, to be validated) |
|---|---|---|
| On-premises trust zone | Student systems, grades, research data, identity/authentication, audit logging, MCP service | Deployed in the MUDT data centre's internal network and not directly exposed to the Internet; the database accepts connections from internal services only; the identity system uses SSO or LDAP; audit logs are stored centrally |
| Cloud execution zone | AI agents, orchestration, LLM inference, vector services, reporting | Deployed in a public cloud; LLM inference is provided by the Anthropic Claude API (third-party hosted, accessed over HTTPS from the cloud zone). Prompts and retrieved context are sent to Anthropic for processing; the API key is held in the cloud zone's secret store and is not exposed to end users |
| Controlled bridge (Metadata API) | Transfers only approved reporting data and is the recurring security focus; the Session 1 example mentions OAuth2 authentication | The Metadata API is a REST API exposed over HTTPS (TLS) and is the only path between the on-premises zone and the cloud zone. Callers must authenticate with OAuth2 access tokens (as in the Session 1 example); tokens are short-lived and scoped to the minimum data needed. Only approved reporting data (aggregated or metadata fields, not raw student records) is returned; the list of approved fields is defined and owned by the coordinator. Requests and responses are logged to the on-premises audit log, and rate limits apply per client. Data flows in one direction by default (on-premises to cloud, on request); the cloud zone cannot query on-premises databases directly |

## Data-flow register

Format follows Session 1, slide 31. All flows, protocols and boundary rules are team assumptions derived from the system parts above.

### Trust boundaries

| ID | Boundary | Between |
|---|---|---|
| TB1 | Internet → on-premises | Users on the Internet and the user portal (A34) |
| TB2 | Cloud ↔ on-premises | The cloud execution zone and the on-premises trust zone |
| TB3 | MUDT → third party | The cloud execution zone and the Anthropic Claude API (A18) |
| TB4 | Management network → servers | Administrator workstations and on-premises servers (A29) |

### Flows

| ID | Source → Destination | Data / purpose | Protocol | Boundary | Rule (assumed) |
|---|---|---|---|---|---|
| DF01 | User browser → gateway (A22) → user portal (A34) | Logins, requests, submissions, uploaded files, prompts | HTTPS 443 | TB1 | Only the portal is published to users |
| DF02 | User portal (A34) → identity (A12) | Authentication and role lookup | OIDC / SAML over HTTPS | Internal | SSO for all users; MFA for administrators |
| DF03 | User portal (A34) → student systems (A11) | Application requests | HTTPS | Internal | Portal uses the signed-in user's identity |
| DF04 | Student systems (A11) → database (A28) | Queries and results: records, grades | DB protocol over TLS | Internal | Only the student systems' service account may connect |
| DF05 | Student systems (A11) → file storage (A30) | Submissions, course files, official documents | SMB / object API over TLS | Internal | No direct user access to storage |
| DF06 | User portal (A34) → orchestration (A17) | Prompts and uploaded files for AI tutoring and document search | HTTPS | TB2 | User identity is passed along; content may contain personal data |
| DF07 | Orchestration (A17) → AI agents (A16) | Tasks and context | Internal API | Cloud internal | – |
| DF08 | AI agents (A16) → Claude API (A18) | Prompts and retrieved context | HTTPS | TB3 | API key from the secret store (A33) |
| DF09 | AI agents (A16) / vector services (A19) ↔ vector database (A32) | Embeddings and retrieved passages | Internal API | Cloud internal | – |
| DF10 | Reporting service (A20) / AI agents (A16) → gateway (A22) → Metadata API (A15) | Request with OAuth2 token; response with approved reporting data | HTTPS + OAuth2 | TB2 | Only approved, aggregated fields; rate limited; logged |
| DF11 | Metadata API (A15) → student systems (A11) | Read approved fields | Internal API | Internal | Read-only |
| DF12 | AI agents (A16) → MCP service (A14) | Tool calls for on-premises functions | HTTPS (MCP) | TB2 | **Open decision**, see below |
| DF13 | MCP service (A14) → student systems (A11) | Least-privilege calls on behalf of an agent | Internal API | Internal | Only approved tools |
| DF14 | Student systems (A11) → vector services (A19) | Course materials for embedding | HTTPS | TB2 | **Open decision**, see below |
| DF15 | Cloud services → secret store (A33) | Fetch API keys, client secrets | HTTPS | Cloud internal | Access by service identity only |
| DF16 | All components → log storage (A31) | Security and operational events | TLS / log agent | Internal and TB2 | Write-only |
| DF17 | Administrator workstation → servers (A29) | Maintenance and deployment | SSH / RDP over VPN | TB4 | VPN + MFA, separate management path |
| DF18 | Backup service → database (A28), file storage (A30) → backup storage (A24) | Encrypted backups and restores | Backup protocol | Internal | Dedicated service account |

### Finding: the Metadata API is not the only path between the zones

Drawing the flows shows four crossings of TB2 besides the Metadata API (DF10): user prompts to the cloud (DF06), agent tool calls to MCP (DF12), course materials to the vector store (DF14) and cloud logs to on-premises log storage (DF16). The bridge assumption "the only path between the on-premises zone and the cloud zone" therefore does not hold. The team must decide either to route these flows through the gateway with the same controls as the Metadata API, or to treat each as a separate entry point in the threat model.

## Business outcomes

Business outcomes are not assets with C / I / A properties. They are what is harmed when an asset is disclosed, changed or unavailable, so every C / I / A rating in the common asset table names the outcome it affects.

| ID | Outcome | What it is | Depends on | Harmed when |
|---|---|---|---|---|
| A01 | Funding and revenue (tuition) | The income MUDT receives, mainly student fees | A02 | Enrolment falls or operations stop |
| A02 | Student enrolment and retention | The number of students who join and stay | A03, A04, A08 | MUDT's reputation drops, teaching is poor or its documents are not trusted |
| A03 | Reputation and public trust | How students, employers and the public judge MUDT | A04–A09 | Data leaks, grades or documents are altered, or AI answers are wrong |
| A04 | Teaching and learning service | The functions through which students gain knowledge: course delivery, AI tutoring, document search, learning support | A05, A11–A20, A22, A23, A25, A34 | The platform is unavailable or delivers wrong content |

## Common asset table

One table for the 30 assets (A05–A34); "Category" marks essential or supporting. Columns follow Session 2, slide 11. C / I / A = High (H), Medium (M), Low (L). Each rating states the consequence and the business outcome it harms (A01–A04). Ratings and owner roles are team proposals.


| ID | Asset | Category | Type | Business value | Owner role | C / I / A and impact on outcomes | Dependencies | Assumptions |
|---|---|---|---|---|---|---|---|---|
| A05 | Course materials and teaching content | Essential | Information | Quality of knowledge delivered | Lecturers | C: M – leaked material undermines fair assessment (A03) · **I: H** – altered content teaches wrong knowledge (A04, A03) · A: M – teaching continues with delays (A04) | A11, A12 | Stored in student systems |
| A06 | Student personal and learning data (records, submissions, analytics data) | Essential | Information | Legal duty to protect; basis of administration and learning insights | Data protection officer | **C: H** – disclosure is a GDPR breach and loses trust (A03 → A02) · **I: H** – wrong records distort support and reporting (A04, A03) · A: M – administration is delayed | A11, A12, A13, A24 | Analytics data is derived from student records |
| A07 | Grades and assessment records | Essential | Information | Legal proof that knowledge was gained | Coordinator | **C: H** – leaked grades breach privacy and trust (A03) · **I: H** – altered grades invalidate qualifications, so degrees lose value for employment (A03 → A02) · A: M – results are delayed | A11, A12, A13, A24 | Grades stay on-premises |
| A08 | Official academic documents and records | Essential | Information | Legally valid; needed for employment | Coordinator | C: M – limited personal data (A03) · **I: H** – forged or changed documents lose legal value and employers stop trusting MUDT degrees (A03 → A02) · A: M – documents are issued late | A11, A12, A13, A24, A26 | Printed on request by coordinators |
| A09 | Approved reporting data and reports | Essential | Information | Management and coordination reporting | Coordinator | **C: H** – sensitive data in reports is a GDPR breach (A03) · **I: H** – wrong reports lead to wrong decisions (A03, A01) · A: M – reporting is delayed | A15, A20, A11 | Approval rules owned by coordinator |
| A10 | Research data | Essential | Information | Academic research work (on-premises, S1 slide 5); little exists today | Research lead | C: L – MUDT holds little research data, so a leak has little effect (A03) · I: M – altered data weakens research results (A03) · A: L – short outages have little effect | A11, A12 | MUDT is a new school with little research output (context assumption); re-assess when research grows |
| A11 | Student systems (records, grades, submissions) | Supporting | Software | Stores and processes A05–A10 | IT operations | **C: H** – exposes student data and grades (A03) · **I: H** – corrupts grades and records (A03 → A02) · A: M – administration and teaching are interrupted (A04) | A12, A22, A28, A29, A30 | On-premises, internal access only |
| A12 | Identity and authentication system | Supporting | Software | Decides who may access what | IT security | **C: H** – stolen identities open every service (A03) · **I: H** – manipulated roles allow grade changes (A03) · **A: H** – nobody can log in, so teaching stops (A04) | A22, A29 | SSO or LDAP |
| A13 | Audit logging | Supporting | Software / data | Evidence for investigation | IT security | C: M – logs contain user activity (A03) · **I: H** – tampered logs hide attacks and remove proof (A03) · A: M – investigation is delayed | A11, A22, A31 | Stored centrally on-premises |
| A14 | MCP service | Supporting | Software | Gives AI agents controlled access to on-premises functions | IT operations | **C: H** – agents could pull student data through it (A03) · **I: H** – wrong tool results mislead students (A04) · A: M – AI features degrade (A04) | A11, A12, A29 | Sits in the on-premises zone |
| A15 | Metadata API | Supporting | Service | Only path between zones; recurring security focus (S1 slide 5) | IT operations | **C: H** – exposure discloses learning data (A03; S1 slide 21) · **I: H** – altered reports (A03) · A: M – AI features and reports degrade (A04) | A12, A21, A22, A29 | REST over HTTPS, OAuth2 |
| A16 | AI agents | Supporting | Software | Carry out tutoring and reporting tasks | AI service owner | C: M – answers may expose data (A03) · **I: H** – prompt-injected agents give wrong or harmful answers (A04, A03; S1 slide 25) · A: M – AI tutoring is unavailable (A04) | A14, A15, A17, A18, A19, A23 | Run in the cloud zone |
| A17 | Orchestration | Supporting | Software | Coordinates agents and tools | AI service owner | C: M – sees request content (A03) · **I: H** – misrouting changes what agents do (A04) · A: M – AI features stop (A04) | A23 | – |
| A18 | LLM inference (Anthropic Claude API) | Supporting | External service | Generates answers | AI service owner | C: M – prompts leave MUDT (A03) · **I: H** – wrong answers reach students (A04) · A: M – AI features stop (A04) | A21, A23 | Third-party hosted, accessed over HTTPS |
| A19 | Vector services | Supporting | Software / data | Retrieval of documents for AI answers | AI service owner | C: M – retrieved content may leak (A03) · I: M – poisoned retrieval lowers answer quality (A04) · A: M – search degrades (A04) | A23, A32 | Store embeddings or text (to be decided) |
| A20 | Reporting service | Supporting | Software | Produces reports | IT operations | C: M – handles approved data only (A03) · **I: H** – wrong reports mislead management (A03, A01) · A: M – reporting is delayed | A15, A23 | Runs in the cloud zone |
| A21 | Credentials (OAuth2 tokens, API keys, service accounts) | Supporting | Information | Enable access between components | IT security | **C: H** – a stolen token enables API abuse and data theft (A03; S1 slide 25) · I: M – replaced credentials break access (A04) · A: M – services fail to authenticate (A04) | A33 | Held in a secret store |
| A22 | On-premises network and perimeter firewall / gateway | Supporting | Infrastructure | Separates zones and publishes only the Metadata API | IT operations | C: M – traffic metadata is visible · **I: H** – wrong rules expose internal systems (A03) · **A: H** – all services go offline (A04) | – | Not stated in the course |
| A23 | Public cloud environment | Supporting | Infrastructure | Hosts the cloud execution zone | IT operations | C: M – tenant data could be exposed (A03) · I: M – misconfiguration changes AI services (A04) · **A: H** – AI services stop (A04) | – | Third-party provider |
| A24 | Backups and recovery media | Supporting | Information | Allow recovery after incidents | IT operations | **C: H** – contain full copies of student data (A03) · **I: H** – corrupted backups prevent recovery (A04, A03) · A: M – recovery is slower | A28, A30 | Not stated in the course |
| A25 | Lecturers (professors) | Supporting | People | Knowledge delivery depends on their expertise | Head of teaching | C: L · I: M – errors in teaching lower quality (A04) · A: M – absence lowers teaching quality (A04) | – | Teaching quality depends on them |
| A26 | Coordinators and administrators | Supporting | People | Operate and maintain systems and records; privileged access | Management | C: M – know sensitive records (A03) · **I: H** – misuse or compromise changes records or configuration (A03) · A: M – administration is delayed | A12 | – |
| A27 | Policies, compliance documentation and contracts (GDPR, cloud/API provider terms) | Supporting | Organisational | Define rules and legal duties | Management | C: L · I: M – outdated rules weaken compliance (A03) · A: L | – | – |
| A28 | Database server and DBMS | Supporting | Infrastructure | Stores structured records, grades and submissions for the student systems | IT operations | **C: H** – holds all student records and grades (A03) · **I: H** – changes alter grades and records (A03 → A02) · A: M – student systems stop (A04) | A22, A29 | Relational DBMS on-premises; only the student systems' service account may connect |
| A29 | On-premises servers and virtualisation | Supporting | Infrastructure | Hosts every on-premises service | IT operations | C: M – access to hosted data (A03) · **I: H** – a compromised host controls everything it runs (A03, A04) · **A: H** – all on-premises services stop (A04) | A22 | Virtual machines in the MUDT data centre |
| A30 | File and document storage | Supporting | Infrastructure | Stores submissions, course material files and official documents | IT operations | C: M – holds submissions and documents (A03) · **I: H** – altered official documents lose legal value (A03 → A02) · A: M – documents and materials are delayed (A04) | A22, A29 | On-premises storage, accessed through the student systems only |
| A31 | Log storage and monitoring (SIEM) | Supporting | Infrastructure | Central store and alerting for audit logs | IT security | C: M – logs contain user activity (A03) · **I: H** – logs must be tamper-proof to serve as evidence (A03) · A: M – alerts are missed | A22, A29 | Components write logs to it; they cannot read or delete |
| A32 | Vector database | Supporting | Infrastructure | Stores embeddings used for AI retrieval | AI service owner | C: M – embeddings can leak content (A03) · I: M – altered embeddings lower answer quality (A04) · A: M – search degrades (A04) | A23 | Managed vector database in the cloud zone |
| A33 | Cloud secret store (key vault) | Supporting | Infrastructure | Holds API keys, client secrets and signing keys | IT security | **C: H** – holds every credential of the cloud zone (A03) · **I: H** – a replaced secret redirects trust (A03, A04) · A: M – services cannot fetch secrets (A04) | A23 | Managed vault of the cloud provider |
| A34 | User portal (web interface) | Supporting | Software | Only way students, lecturers and coordinators reach the services | IT operations | C: M – shows user data (A03) · **I: H** – altered pages mislead users or steal logins (A03) · **A: H** – no portal means no access to any service (A04) | A11, A12, A22, A29 | Web application hosted on-premises behind the perimeter (not stated in the course) |


## Definitions and implementation

"What it is" describes the asset. "How it is implemented" is the team's assumption, to be validated. Where the implementation needs something that is itself worth protecting (a database, servers, storage), that item is its own asset in the common asset table.

| ID | Asset | What it is | How it is implemented (assumed) |
|---|---|---|---|
| A01 | Funding and revenue (tuition) | The income MUDT receives, mainly student fees, which pays for running the university | Business outcome, no technical implementation; depends on enrolment (A02) |
| A02 | Student enrolment and retention | The number of students who join and stay at MUDT | Business outcome; enrolment records are kept in the student systems (A11) |
| A03 | Reputation and public trust | How students, employers and the public judge MUDT | Intangible; built from teaching results, correct records and protected data |
| A04 | Teaching and learning service | The functions through which students learn: course delivery, AI tutoring, document search, learning support | Users reach it through the portal (A34); AI features run as agents (A16) in the cloud and reach on-premises data through the MCP service (A14) and the Metadata API (A15) |
| A05 | Course materials and teaching content | Slides, notes, assignments and reading material | Files in file storage (A30) managed by the student systems (A11); copies may be embedded in the vector database (A32) for AI retrieval |
| A06 | Student personal and learning data | Identity and contact data, submissions, progress and the analytics derived from them | Records in the database (A28), submitted files in file storage (A30); analytics leave the on-premises zone only as aggregated data through the Metadata API |
| A07 | Grades and assessment records | Marks and assessment outcomes of each student | Rows in the database (A28) written by the student systems; every change is recorded by audit logging (A13) |
| A08 | Official academic documents and records | Certificates, transcripts and enrolment confirmations | Uploaded or generated by coordinators in the student systems, stored in file storage (A30) with metadata in the database (A28); printed on request |
| A09 | Approved reporting data and reports | Aggregated data approved for transfer, and the reports produced from it | Delivered by the Metadata API (A15) to the reporting service (A20); the approved field list is owned by the coordinator |
| A10 | Research data | Data from research activities held on-premises | Stored in the database (A28) or file storage (A30); little research data exists today |
| A11 | Student systems | The application software that manages records, grades, submissions and course materials | Business application on on-premises servers (A29); stores data in the database (A28) and file storage (A30); users authenticate through identity (A12) |
| A12 | Identity and authentication system | The system that verifies users and decides what they may do | Identity provider with SSO and an LDAP directory on on-premises servers (A29); roles for student, lecturer and coordinator; MFA for administrators |
| A13 | Audit logging | The record of who accessed or changed what, including API calls | Log agents on each component send events to log storage (A31) |
| A14 | MCP service | A server that offers a limited set of approved on-premises functions to AI agents as tools (Model Context Protocol) | Service on on-premises servers (A29); authenticates each agent and calls the student systems with least privilege |
| A15 | Metadata API | The controlled interface between the on-premises and cloud zones that returns only approved reporting data | REST API over HTTPS on an on-premises server (A29), published through the gateway (A22); callers use OAuth2 tokens (A21) |
| A16 | AI agents | Software that plans and carries out tasks using the LLM and tools | Agent processes in the cloud environment (A23); call the LLM (A18), retrieve through vector services (A19), reach on-premises data only through A14 and A15 |
| A17 | Orchestration | The component that routes requests to agents and tools and keeps workflow state | Workflow service running in the cloud environment (A23) |
| A18 | LLM inference (Anthropic Claude API) | The language model that generates answers | Called over HTTPS with an API key (A21); MUDT does not host a model |
| A19 | Vector services | The retrieval layer: turns documents into embeddings and finds similar content | Embedding pipeline plus the vector database (A32) in the cloud environment (A23) |
| A20 | Reporting service | The service that builds reports from approved data | Service in the cloud environment (A23) that pulls data through the Metadata API (A15) |
| A21 | Credentials (OAuth2 tokens, API keys, service accounts) | The secrets that let components prove their identity | Stored in the cloud secret store (A33); OAuth2 tokens are short-lived |
| A22 | On-premises network and perimeter firewall / gateway | Network segmentation and the perimeter that separates MUDT's network from the Internet | Firewalls and VLANs; an API gateway publishes only the Metadata API (and the portal, if used from outside); administrators use a separate management path |
| A23 | Public cloud environment | The third-party cloud platform that hosts the cloud zone | A tenant at a public cloud provider: compute, networking and access management hosting A16, A17, A19, A20, A32 and A33 |
| A24 | Backups and recovery media | Copies of data used to recover after an incident | Scheduled, encrypted backups of the database (A28) and file storage (A30) to separate storage on the same network (not offline); restore tests are not scheduled yet |
| A25 | Lecturers (professors) | The people who teach, write materials and grade | Accounts in identity (A12) with a lecturer role; no technical implementation |
| A26 | Coordinators and administrators | The people who run administration and operate the systems | Accounts in identity (A12) with elevated roles; administrators work through the management path |
| A27 | Policies, compliance documentation and contracts | Written rules and agreements: data protection procedures and contracts with the cloud and API providers | Documents held by management; no technical implementation |
| A28 | Database server and DBMS | The database system that stores structured records | Relational DBMS on on-premises servers (A29); only the student systems' service account may connect; TLS in transit, encryption at rest |
| A29 | On-premises servers and virtualisation | The machines in the MUDT data centre that run the on-premises services | Virtualised hosts running A11, A12, A14, A15, A28, A30, A31 and A34 |
| A30 | File and document storage | Storage for submitted work, course files and official documents | On-premises file or object storage; accessed through the student systems only |
| A31 | Log storage and monitoring (SIEM) | The central store for logs, with search and alerts | On-premises collector and storage; components may write but not read or delete; alerts go to IT security |
| A32 | Vector database | The database that stores embeddings for similarity search | Managed vector database in the cloud environment (A23) |
| A33 | Cloud secret store (key vault) | The managed vault that holds secrets | The cloud provider's key vault, with access limited by service identity |
| A34 | User portal (web interface) | The web interface through which students, lecturers and coordinators use the platform | Web application hosted on-premises behind the perimeter (A22); signs users in through identity (A12) and fronts the student systems and AI features |

Sources: asset table columns from S2 slide 11; zone components from S1 slide 5; use cases from S1 slide 8; risk examples from S1 slides 21 and 25; design objectives from S1 slide 23. The value chain, asset selection, ratings and owner roles are the team's own reasoning.
