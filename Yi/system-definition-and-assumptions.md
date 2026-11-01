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

## Common asset table

One table for all assets (34 in total); "Category" marks essential or supporting. Columns follow Session 2, slide 11. C / I / A = High (H), Medium (M), Low (L); every H carries a one-sentence rationale. Ratings and owner roles are team proposals.


| ID | Asset | Category | Type | Business value | Owner role | C / I / A and rationale | Dependencies | Assumptions |
|---|---|---|---|---|---|---|---|---|
| A01 | Funding and revenue (tuition) | Essential | Business outcome | MUDT can only operate if students pay | Management | C: L · I: M · **A: H** – interruption of services stops enrolment and fee income | A02 | Funded mainly by student fees |
| A02 | Student enrolment and retention | Essential | Business outcome | Student numbers drive funding | Management | C: L · I: M · A: M | A03, A04, A08 | Students choose MUDT based on evaluation |
| A03 | Reputation and public trust | Essential | Intangible | Drives student choice and employer recognition | Management | C: L · **I: H** – false results, leaked data or wrong AI answers damage trust · A: M | A04–A09 | – |
| A04 | Teaching and learning service (course delivery, AI tutoring, document search, learning support) | Essential | Service | Core value: students gain knowledge | Head of teaching | C: M · **I: H** – incorrect AI answers mislead students · **A: H** – students cannot learn when it is down | A05, A11–A20, A22, A23, A25, A34 | AI features are part of the teaching quality |
| A05 | Course materials and teaching content | Essential | Information | Quality of knowledge delivered | Lecturers | C: M · **I: H** – altered material teaches wrong content · A: M | A11, A12 | Stored in student systems |
| A06 | Student personal and learning data (records, submissions, analytics data) | Essential | Information | Legal duty to protect; basis of administration and learning insights | Data protection officer | **C: H** – disclosure causes GDPR consequences and loss of trust (S1 slide 21) · **I: H** – wrong records distort assessment and reporting · A: M | A11, A12, A13, A24 | Analytics data is derived from student records |
| A07 | Grades and assessment records | Essential | Information | Legal proof that knowledge was gained | Coordinator | **C: H** – student grades are private · **I: H** – altered grades invalidate qualifications · A: M | A11, A12, A13, A24 | Grades stay on-premises |
| A08 | Official academic documents and records | Essential | Information | Legally valid; needed for employment | Coordinator | C: M · **I: H** – forged or changed documents lose legal value · A: M | A11, A12, A13, A24, A26 | Printed on request by coordinators |
| A09 | Approved reporting data and reports | Essential | Information | Management and coordination reporting | Coordinator | **C: H** – only approved data may leave the on-premises zone · **I: H** – reports must not change without authorisation (S1 slide 23) · A: M | A15, A20, A11 | Approval rules owned by coordinator |
| A10 | Research data | Essential | Information | Valuable academic work (on-premises, S1 slide 5) | Research lead | **C: H** – unpublished research is sensitive · I: M · A: L | A11, A12 | Scope to be confirmed; not linked to any teaching or administration service |
| A11 | Student systems (records, grades, submissions) | Supporting | Software | Stores and processes A05–A10 | IT operations | **C: H** – holds private student data · **I: H** – source of grades and records · A: M | A12, A22, A28, A29, A30 | On-premises, internal access only |
| A12 | Identity and authentication system | Supporting | Software | Decides who may access what | IT security | **C: H** – holds credentials · **I: H** – a manipulated identity gives access to everything · **A: H** – no login means no service | A22, A29 | SSO or LDAP |
| A13 | Audit logging | Supporting | Software / data | Evidence for investigation | IT security | C: M · **I: H** – logs must be tamper-proof to serve as evidence · A: M | A11, A22, A31 | Stored centrally on-premises |
| A14 | MCP service | Supporting | Software | Gives AI agents controlled access to on-premises functions | IT operations | **C: H** – gateway to student data · **I: H** – wrong tool results mislead agents · A: M | A11, A12, A29 | Sits in the on-premises zone |
| A15 | Metadata API | Supporting | Service | Only path between zones; recurring security focus (S1 slide 5) | IT operations | **C: H** – exposure discloses learning data (S1 slide 21) · **I: H** – reports must stay unchanged · A: M | A12, A21, A22, A29 | REST over HTTPS, OAuth2 |
| A16 | AI agents | Supporting | Software | Carry out tutoring and reporting tasks | AI service owner | C: M · **I: H** – prompt injection can change agent behaviour (S1 slide 25) · A: M | A14, A15, A17, A18, A19, A23 | Run in the cloud zone |
| A17 | Orchestration | Supporting | Software | Coordinates agents and tools | AI service owner | C: M · I: H – wrong routing changes what agents do · A: M | A23 | – |
| A18 | LLM inference (Anthropic Claude API) | Supporting | External service | Generates answers | AI service owner | C: M – prompts leave MUDT · **I: H** – wrong output reaches students · A: M | A21, A23 | Third-party hosted, accessed over HTTPS |
| A19 | Vector services | Supporting | Software / data | Retrieval of documents for AI answers | AI service owner | C: M · I: M · A: M | A23, A32 | Store embeddings or text (to be decided) |
| A20 | Reporting service | Supporting | Software | Produces reports | IT operations | C: M · **I: H** – wrong reports mislead management · A: M | A15, A23 | Runs in the cloud zone |
| A21 | Credentials (OAuth2 tokens, API keys, service accounts) | Supporting | Information | Enable access between components | IT security | **C: H** – a stolen token enables API abuse (S1 slide 25) · I: M · A: M | A33 | Held in a secret store |
| A22 | On-premises network and perimeter firewall / gateway | Supporting | Infrastructure | Separates zones and publishes only the Metadata API | IT operations | C: M · **I: H** – wrong rules open the internal network · **A: H** – bridge and services depend on it | – | Not stated in the course |
| A23 | Public cloud environment | Supporting | Infrastructure | Hosts the cloud execution zone | IT operations | C: M · I: M · **A: H** – AI services run there | A21 | Third-party provider |
| A24 | Backups and recovery media | Supporting | Information | Allow recovery after incidents | IT operations | **C: H** – contain full copies of A06–A08 · **I: H** – corrupted backups fail recovery · A: M | A28, A30 | Not stated in the course |
| A25 | Lecturers (professors) | Supporting | People | Knowledge delivery depends on their expertise | Head of teaching | C: L · I: M · A: M | – | Teaching quality depends on them |
| A26 | Coordinators and administrators | Supporting | People | Operate and maintain systems and records; privileged access | Management | C: M · **I: H** – misuse or compromise changes records or configuration · A: M | A12 | – |
| A27 | Policies, compliance documentation and contracts (GDPR, cloud/API provider terms) | Supporting | Organisational | Define rules and legal duties | Management | C: L · I: M · A: L | – | – |
| A28 | Database server and DBMS | Supporting | Infrastructure | Stores structured records, grades and submissions for the student systems | IT operations | **C: H** – holds all student records and grades · **I: H** – changes alter grades and records · A: M | A22, A29 | Relational DBMS on-premises; only the student systems' service account may connect |
| A29 | On-premises servers and virtualisation | Supporting | Infrastructure | Hosts every on-premises service | IT operations | C: M · **I: H** – a compromised host controls everything it runs · **A: H** – all on-premises services stop without it | A22 | Virtual machines in the MUDT data centre |
| A30 | File and document storage | Supporting | Infrastructure | Stores submissions, course material files and official documents | IT operations | C: M · **I: H** – altered official documents lose legal value · A: M | A22, A29 | On-premises storage, accessed through the student systems only |
| A31 | Log storage and monitoring (SIEM) | Supporting | Infrastructure | Central store and alerting for audit logs | IT security | C: M · **I: H** – logs must be tamper-proof to serve as evidence · A: M | A22, A29 | Components write logs to it; they cannot read or delete |
| A32 | Vector database | Supporting | Infrastructure | Stores embeddings used for AI retrieval | AI service owner | C: M – embeddings can leak content · I: M · A: M | A23 | Managed vector database in the cloud zone |
| A33 | Cloud secret store (key vault) | Supporting | Infrastructure | Holds API keys, client secrets and signing keys | IT security | **C: H** – holds every credential of the cloud zone · **I: H** – a replaced secret redirects trust · A: M | A23 | Managed vault of the cloud provider |
| A34 | User portal (web interface) | Supporting | Software | Only way students, lecturers and coordinators reach the services | IT operations | C: M · **I: H** – altered pages mislead users or steal logins · **A: H** – no portal means no access to any service | A11, A12, A22, A29 | Web application hosted on-premises behind the perimeter (not stated in the course) |


## Asset definitions and implementation

"What it is" describes the asset. "How it is implemented" is the team's assumption, to be validated. Where the implementation needs something that is itself worth protecting (a database, servers, storage), that item is its own asset in the table above.

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
| A10 | Research data | Data from research activities held on-premises | Stored in the database (A28) or file storage (A30); scope to be confirmed |
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
| A24 | Backups and recovery media | Copies of data used to recover after an incident | Scheduled, encrypted backups of the database (A28) and file storage (A30) to separate storage; restores are tested |
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