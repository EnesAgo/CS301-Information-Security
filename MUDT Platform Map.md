CS-301 · Session 2 workshop · Team map

# MUDT AI Learning Assistant Platform

What the slide 5 picture actually describes, where each part lives, which parts are ours and which are someone else's, and how our 30 workshop assets sit on top of it.

On-prem · trustedBridge · controlledCloud · untrustedExternal · not oursPeople & usersSpans zonesOrganisation

0 · Start from the users

## No users, no platform

The platform exists for its users, and students are the customers. So the register starts with the people: who they are, what each one can do, and what each one may open. Every asset after that has to serve at least one of these use cases. That's also the Sprint 1 Definition of Done: every item is connected to at least one use case.

STU

### Students

The customers. No students means no tuition/funding, no university. Everything else exists to serve them.

- **Log in once with SSO (and MFA)**Own account only
- **Open their enrolled courses: pages, announcements, materials**Only courses they are enrolled in
- **Submit assignments and read feedback**Own submissions only
- **View own grades and download a transcript**Own grades only (never other students')
- **Ask the AI tutor and search course documents**Course content of enrolled courses
- **Request official documents (enrolment certificate, transcript)**Own requests
- **See timetable, deadlines, book a lab environment**Own schedule, own lab
- **Update own contact data, see what data is stored about them**Own profile (GDPR right of access)

LEC

### Lecturers

Deliver the teaching students pay for; they create materials and make grading decisions.

- **Open the classes they teach and see the enrolled students**Rosters of own courses only
- **Upload and send materials, post announcements to a class**Own course workspaces
- **Create assignments, set deadlines, download and grade submissions**Submissions in own courses
- **Enter grades and release them**Grades of own courses; changes after release need the office
- **Choose which materials the AI tutor may use for their course**AI configuration of own courses
- **See learning insights for their class**Aggregated / pseudonymised data of own class

COO

### Programme coordinators

Keep programmes running and accredited; first to spot struggling students.

- **Read programme dashboards and progress reports**Programme-level reports
- **Plan timetables, rooms and exam dates**Schedules of the programme
- **Handle grade disputes and approve grade corrections**Grades of the programme (read), approval workflow

OFF

### Office staff (Student Office / Examination Office secretaries)

Turn academic work into legally valid records: enrolment, exam registrations, transcripts.

- **Enrol students, update personal records, manage leave/exmatriculation**All student records (write)
- **Manage exam registrations and record final grades**Exam and grade records (write)
- **Issue and print official documents on request**Transcripts, certificates, document printer
- **Answer student requests (helpdesk)**Student records (read), support tickets

ADM

### System administrators

Keep the platform running; hold the most powerful accounts.

- **Create accounts, assign and remove roles**Directory and role model
- **Operate servers, cloud tenant, backups and labs**All infrastructure
- **Deploy updates and change configuration**CI/CD, gateway and agent configs

MGT

### University management (Dean, Board)

Decide on budget, staffing and strategy based on platform reports.

- **Read aggregated university reports**Aggregated reports only

RES

### Researchers

Bring funding, publications and reputation.

- **Store, version and share research data with partners**Own research projects

AGT

### AI agents (non-human users)

Deliver the AI value; they act on behalf of users, so they need their own identity and limits.

- **Retrieve course chunks from the vector DB**Course content allowed by the lecturer
- **Request approved metadata from on-prem through the gateway (MCP)**Approved MCP tools only

DPO

### Data Protection Officer / IT Security

Answer to regulators; handle data subject requests and incidents.

- **Review audit logs, handle access/deletion requests, run DPIAs**Audit logs, processing records

### Who can open what

W = create or change · R = read · Own = only their own records · Agg = aggregated only · Roster = names in their own course · Tech = technical access, should not read content · Audit = reads the access logs

| Information | STU | LEC | COO | OFF | ADM | MGT | RES | AGT | DPO |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Student personal records | Own | Roster | R | W | Tech | - | - | - | Audit |
| Grades | Own | W own course | R | W | Tech | Agg | - | - | Audit |
| Assignment submissions | Own | R own course | - | - | Tech | - | - | Feedback\* | - |
| Course materials | R enrolled | W own course | R | - | Tech | - | - | R allowed | - |
| Class rosters / enrolment lists | - | R own course | R | W | Tech | - | - | - | Audit |
| Exam questions before release | - | W own course | - | - | Tech | - | - | - | - |
| AI conversations | Own | - | - | - | Tech | - | - | W | Audit |
| Learning analytics | Own | Agg own class | Agg | - | Tech | Agg | - | Compute | Audit |
| Reporting dataset (metadata) | - | - | R | - | Tech | Agg | - | R via MCP | Audit |
| Official documents | Own request | - | - | W | - | - | - | - | - |
| Research data | - | - | - | - | Tech | - | W own | - | - |
| Audit logs | - | - | - | - | R | - | - | Write only | R |
| Role & permission model | - | - | - | - | W | - | - | - | R |

The matrix is the business version of the role and permission model (S10 in the v2 register). Every "Own" or "own course" cell is a promise the platform must keep. Breaking one is exactly the IDOR / broken access control you attack in CS-303.

1 · The big picture

## Three zones and one door

The university keeps everything sensitive at home, rents compute for AI in the cloud, and lets the two talk through one inspected gateway. The picture leaves out the internet services everything depends on, so they're added at the top. The DMZ and firewall are drawn in too: the image shows a shield on the data centre wall but no explicit public entry point.

encrypted gateway linkuser HTTPSadmin path (VPN + MFA)public DNS points only at the portal and the AI chatThick border = core asset holder

2 · Why things live where they do

## On-prem, cloud or bridge?

### Keep it on-prem when…

- it is the **system of record** (grades, enrolment, research)
- law or exam regulations demand control (GDPR, Prüfungsordnung)
- it is the **root of trust** (identity, audit evidence, keys)

### Put it in the cloud when…

- it needs **elastic GPU compute** (LLM inference)
- load spikes in exam weeks and calms down after
- it can work on **minimised, pseudonymised** data only

### Route it through the bridge when…

- anything in the cloud needs anything from on-prem
- the data must be checked, limited and logged on the way
- no other path may exist: no direct DB link, no shortcut VPN

| Component | Lives in | Run by | Why there |
| --- | --- | --- | --- |
| Student Information System, Grade DB, Research Repository | On-prem, internal zone | University IT | Systems of record for personal and legally binding data (GDPR, exam regulations). The university must stay in full control. |
| Identity Provider (SSO) and directory | On-prem, internal zone | University IT | Root of trust for every login. Cloud services trust it through federation (OIDC/SAML) instead of keeping their own user list. |
| MCP service | On-prem, internal zone | AI platform team | Sits next to the data so agents never touch databases directly. It only answers through the gateway. |
| Audit & logging (SIEM) | On-prem, internal zone | Security team | Evidence must live where the untrusted cloud cannot change or delete it. |
| Web portal, reverse proxy / WAF, admin VPN | On-prem, DMZ | University IT | Anything reachable from the internet sits in the DMZ, behind the firewall and away from the databases. Same idea as the DMZ in your CS-302 Proxmox lab. |
| Secure API Gateway + Metadata API | Between zones (usually on-prem edge) | Security + integration team | The single, inspected door between trusted and untrusted. It is not in public DNS and accepts only mTLS clients from the cloud tenant. |
| Agent orchestrator, AI agents, LLM inference, vector DB | Cloud tenant | AI platform team | Needs elastic GPU compute and scales with exam-week peaks. It only works with minimised data. |
| Cloud storage, reporting dashboard, monitoring, cloud IAM | Cloud tenant | Cloud platform team | Serve the cloud workloads. Cloud IAM is federated to the on-prem IdP. |
| LLM model provider (if the model is a vendor API) | Third party | Supplier | If inference is an external API, prompts leave our tenant. That makes it a separate trust boundary and a contract (DPA) question. |
| Email, Teams, SharePoint (Microsoft 365) | Third party SaaS | Microsoft, run by university IT | The course already uses Teams and SharePoint. Password-reset email is a hidden dependency of every account. |
| Git hosting, package registries (PyPI, npm, container images) | Third party | Dev team | Where code and dependencies come from. These are supply-chain assets. |

3 · The services nobody draws

## DNS is three things, not one

This is called **split-horizon DNS**: the outside world sees a few public names, and the internal names stay private. Each part lives in a different zone with a different owner, which is why each one is its own asset.

External · authoritative

### Public DNS

- portal.mudt.de → DMZ reverse proxy
- ai.mudt.de → cloud AI chat front end
- Hosted at a DNS provider, domain at a registrar
- Never lists the gateway, databases or MCP

On-prem · resolver

### Internal DNS

- sis.mudt.internal, gradedb.mudt.internal …
- Only answers inside the campus network
- Often part of the directory (e.g. AD DNS)
- Leaking it gives attackers a map

Cloud tenant · private zone

### Private cloud DNS

- vector-db, orchestrator, storage endpoints
- Visible only inside the tenant
- Gateway name resolved via private link
- Managed by the cloud platform team

The same "who owns which piece" question applies to the other shared services:

IdentityIdP + directory = source of truth·Cloud IAM federated to it·Agent workload identities, short-lived tokensTimePTB public time servers·Internal NTP servers·Cloud provider time serviceLoggingCloud monitoring (local view)·Gateway audit trail·Central SIEM keeps the evidenceBackupsOn-prem backup system·Off-site / offline copy·Cloud snapshots for cloud data onlySecrets & keysOn-prem key store / HSM·Gateway mTLS certificates·Cloud key vault for API tokens

4 · How data moves

## Four flows worth tracing

Session 2 slide 7 asks you to trace one customer order from browser to database and back. These are the same exercise for our platform. Each step is coloured by the zone it happens in, so every colour change is a boundary crossing.

### A · Student asks the AI tutor

The most common flow. Most of it stays in the cloud; it only crosses into the trusted zone through the gateway, and only for approved data.

1. UsersStudent opens ai.mudt portal
2. ExternalPublic DNS resolves the name
3. CloudAI chat front end
4. On-premRedirect to SSO login (IdP)
5. CloudOrchestrator picks tutor agent
6. CloudVector DB: course chunks
7. CloudLLM writes the answer
8. BridgeNeeds a deadline? Gateway call
9. On-premMCP returns approved fact
10. CloudAnswer shown, chat logged

### B · Reporting metadata goes to the cloud

The flow the whole module is about. Raw grades stay home; only a minimised, pseudonymised dataset leaves.

1. On-premSIS + Grade DB (raw data)
2. On-premMCP / export job aggregates
3. BridgeGateway: auth, schema check, rate limit
4. CloudCloud storage (reporting dataset)
5. CloudReporting dashboard
6. UsersCoordinator reads report

### C · Admin fixes a server

The privileged path. It skips most application controls, so it gets its own entry point and the strongest checks.

1. UsersSysadmin on managed laptop
2. On-premAdmin VPN in DMZ + MFA
3. On-premAdmin workstation
4. On-premServer / VM
5. On-premEvery action to SIEM

### D · Evidence trail

Needed in Sprint 3. Every zone writes logs, and they meet in one place the cloud cannot edit.

1. BridgeGateway request log
2. CloudAgent + monitoring logs
3. On-premSIEM (central, append-only)
4. ExternalNTP keeps all timestamps aligned

5 · Where the risk sits

## Trust boundaries

A trust boundary is any line where data moves between zones with different trust levels. These six are where next week's threats and attack paths will start.

| ID | Boundary | What crosses it | Assets exposed (v1 IDs) |
| --- | --- | --- | --- |
| TB1 | **Internet → DMZ** | Users and attackers reach the web portal and the admin VPN. | `S09, S04, portal` |
| TB2 | **DMZ → internal zone** | The portal must talk to the SIS without opening the databases. | `S01, S02, S09` |
| TB3 | **Cloud → gateway → on-prem** | The recurring focus: agents asking the trusted zone for data. | `S12, S13, S05, E06` |
| TB4 | **Users → cloud** | Students type into the AI chat; prompt injection enters here. | `E07, E09, S15` |
| TB5 | **Our tenant → LLM provider** | Only if inference is a vendor API. Prompts and context leave us. | `S16, S20, E07` |
| TB6 | **Admin path** | Privileged access that bypasses application controls. | `S19, S08, S10` |

6 · Connecting our assets

## How the register sits on the architecture

STEP 1

### Place it

Put every asset in a zone. Its zone tells you who owns it and which boundary protects it.

STEP 2

### Chain it

Link supporting → essential → business outcome. Every S-asset must point at an E-asset, or it isn't needed.

STEP 3

### Cross it

Mark which assets sit on a trust boundary. Those go first into next week's five selected assets.

### Step 1 · Our 30 workshop assets by zone

Solid chips are in the 30-asset first version, with v1 IDs. Dashed chips are what the final 80-asset version adds. The users-first v2 register (71 assets) has its own IDs and starts with the user groups from section 0.

### On-prem · trusted

**E01** Student records**E02** Grades**E03** Research data**E08** Audit logs**S01** SIS**S02** Grade DB**S03** Research repo**S04** IdP (SSO)**S05** MCP service**S06** Logging / SIEM**S07** Data storage**S08** On-prem LAN**S09** Firewall**S10** Data centre facility**S11** BackupsServers / VMsAdmin workstationsInternal DNSNTP serverMCP tool policiesOn-prem keysPower & UPSAdmin VPN pathWeb portal (DMZ)

### Bridge · controlled

**E06** Reporting dataset**S12** API Gateway**S13** API keys / tokensMetadata API specGateway configEncrypted linkmTLS certificates

### Cloud · untrusted

**E07** AI conversations**E09** AI tutoring service**S15** AI agents + orchestrator**S16** LLM inference**S17** Vector DB**S18** Reporting dashboardSystem promptsAI modelCloud storageCloud computeMonitoringCloud IAMKey vaultPrivate DNS

### Spans zones

**E04** Submissions**E05** Course materials**S14** Service accounts / agent identitiesUser accounts & MFASessionsAdmin accountsInternet uplinkPublic DNSNTP syncEmail

### Organisation · people & paper

**E10** Reputation & trust**S19** System admins**S20** Supplier contractsCompliance (GDPR, AI Act)Business processesSOC teamDevelopersLecturersDPOPoliciesDPIALicencesSource codeCI/CDLibrariesArchitecture docs

### Step 2 · Dependency chains

#### Grades

**S10** Facility→**S07** Data storage→**S02** Grade DB→**S01** SIS→**S04** IdP→**E02** Grades→valid degrees and academic credibility

#### AI tutoring

**S14** Agent identity→**S17** Vector DB→**E05** Course materials→**S16** LLM→**S15** AI agents→**E09** AI tutoring→personalised learning support

#### Reporting dataset

**E02** Grades→**S05** MCP→**S13** Tokens→**S12** Gateway→**E06** Reporting dataset→correct management decisions

Read each chain right to left and you get the Session 2 explanation test:

"The organisation must protect *Grades (E02)* because they enable **valid degrees and academic credibility**, and that depends on the Grade DB, the storage under it and the data centre around it."

### Step 3 · What this sets up for next week

Assets on TB3 (gateway, tokens, MCP, reporting dataset) and TB4 (AI chat, agents) are the strongest picks for the five assets and two attack paths on Session 2 slide 16. Example path: **external attacker → leaked API token (S13) → gateway trusts it (TB3) → bulk pull of the reporting dataset (E06) → GDPR breach and loss of trust (E10).**

Sources: CS-301 Session 1 slide 5 (architecture image) and summary document, Session 2 slides 3–16. Everything not drawn in the image (DMZ, firewall placement, DNS split, NTP, Microsoft 365, LLM provider) is a team assumption to confirm with the lecturer.