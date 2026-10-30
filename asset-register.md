# Asset register v2: users first

MUDT AI Learning Assistant Platform · CS-301 Session 2 workshop. Same content as [asset-register-v2-users-first.xlsx](asset-register-v2-users-first.xlsx) and the [v2 deck](CS301_Session_2_Asset_Register_MUDT_v2_users_first.pptx). Visual map: https://claude.ai/artifact/8Q5V5SqCHheSzDEY29aCZK

How to read it: start with the users (step 1), see what each one can do (step 2) and open (step 3). Every asset in step 4 serves at least one of those use cases.

## Step 1 · Who uses the platform

| Code | User group | Who they are | Why they matter |
|---|---|---|---|
| STU | Students | Enrolled bachelor/master students using the platform daily. | The customers. No students means no tuition/funding, no university. Everything else exists to serve them. |
| LEC | Lecturers | Teaching staff who run one or more courses (classes). | Deliver the teaching students pay for; they create materials and make grading decisions. |
| COO | Programme coordinators | Run a study programme: schedules, progress, quality. | Keep programmes running and accredited; first to spot struggling students. |
| OFF | Office staff (Student Office / Examination Office secretaries) | Administrative staff who maintain records, exams and official documents. | Turn academic work into legally valid records: enrolment, exam registrations, transcripts. |
| ADM | System administrators | On-prem and cloud IT operations staff. | Keep the platform running; hold the most powerful accounts. |
| MGT | University management (Dean, Board) | Decision makers who read aggregated reports. | Decide on budget, staffing and strategy based on platform reports. |
| RES | Researchers | Academic staff and PhD students storing research data. | Bring funding, publications and reputation. |
| AGT | AI agents (non-human users) | Tutor, search, reporting and scheduling agents in the cloud. | Deliver the AI value; they act on behalf of users, so they need their own identity and limits. |
| DPO | Data Protection Officer / IT Security | Oversight roles for privacy and security. | Answer to regulators; handle data subject requests and incidents. |

## Step 2 · What each user can do

| ID | User group | What they can do | What they can open / change |
|---|---|---|---|
| UC-S1 | Students | Log in once with SSO (and MFA) | Own account only |
| UC-S2 | Students | Open their enrolled courses: pages, announcements, materials | Only courses they are enrolled in |
| UC-S3 | Students | Submit assignments and read feedback | Own submissions only |
| UC-S4 | Students | View own grades and download a transcript | Own grades only (never other students') |
| UC-S5 | Students | Ask the AI tutor and search course documents | Course content of enrolled courses |
| UC-S6 | Students | Request official documents (enrolment certificate, transcript) | Own requests |
| UC-S7 | Students | See timetable, deadlines, book a lab environment | Own schedule, own lab |
| UC-S8 | Students | Update own contact data, see what data is stored about them | Own profile (GDPR right of access) |
| UC-L1 | Lecturers | Open the classes they teach and see the enrolled students | Rosters of own courses only |
| UC-L2 | Lecturers | Upload and send materials, post announcements to a class | Own course workspaces |
| UC-L3 | Lecturers | Create assignments, set deadlines, download and grade submissions | Submissions in own courses |
| UC-L4 | Lecturers | Enter grades and release them | Grades of own courses; changes after release need the office |
| UC-L5 | Lecturers | Choose which materials the AI tutor may use for their course | AI configuration of own courses |
| UC-L6 | Lecturers | See learning insights for their class | Aggregated / pseudonymised data of own class |
| UC-C1 | Programme coordinators | Read programme dashboards and progress reports | Programme-level reports |
| UC-C2 | Programme coordinators | Plan timetables, rooms and exam dates | Schedules of the programme |
| UC-C3 | Programme coordinators | Handle grade disputes and approve grade corrections | Grades of the programme (read), approval workflow |
| UC-O1 | Office staff | Enrol students, update personal records, manage leave/exmatriculation | All student records (write) |
| UC-O2 | Office staff | Manage exam registrations and record final grades | Exam and grade records (write) |
| UC-O3 | Office staff | Issue and print official documents on request | Transcripts, certificates, document printer |
| UC-O4 | Office staff | Answer student requests (helpdesk) | Student records (read), support tickets |
| UC-A1 | System administrators | Create accounts, assign and remove roles | Directory and role model |
| UC-A2 | System administrators | Operate servers, cloud tenant, backups and labs | All infrastructure |
| UC-A3 | System administrators | Deploy updates and change configuration | CI/CD, gateway and agent configs |
| UC-M1 | University management | Read aggregated university reports | Aggregated reports only |
| UC-R1 | Researchers | Store, version and share research data with partners | Own research projects |
| UC-G1 | AI agents | Retrieve course chunks from the vector DB | Course content allowed by the lecturer |
| UC-G2 | AI agents | Request approved metadata from on-prem through the gateway (MCP) | Approved MCP tools only |
| UC-D1 | Data Protection Officer / IT Security | Review audit logs, handle access/deletion requests, run DPIAs | Audit logs, processing records |

## Step 3 · Who can open what

W = create/change · R = read · Own = own records only · Agg = aggregated only · Roster = names in own course · Tech = technical access only · Audit = reads access logs

| Information | STU | LEC | COO | OFF | ADM | MGT | RES | AGT | DPO |
|---|---|---|---|---|---|---|---|---|---|
| **Student personal records** | Own | Roster | R | W | Tech | - | - | - | Audit |
| **Grades** | Own | W own course | R | W | Tech | Agg | - | - | Audit |
| **Assignment submissions** | Own | R own course | - | - | Tech | - | - | Feedback* | - |
| **Course materials** | R enrolled | W own course | R | - | Tech | - | - | R allowed | - |
| **Class rosters / enrolment lists** | - | R own course | R | W | Tech | - | - | - | Audit |
| **Exam questions before release** | - | W own course | - | - | Tech | - | - | - | - |
| **AI conversations** | Own | - | - | - | Tech | - | - | W | Audit |
| **Learning analytics** | Own | Agg own class | Agg | - | Tech | Agg | - | Compute | Audit |
| **Reporting dataset (metadata)** | - | - | R | - | Tech | Agg | - | R via MCP | Audit |
| **Official documents** | Own request | - | - | W | - | - | - | - | - |
| **Research data** | - | - | - | - | Tech | - | W own | - | - |
| **Audit logs** | - | - | - | - | R | - | - | Write only | R |
| **Role & permission model** | - | - | - | - | W | - | - | - | R |

## Step 4a · Essential assets (28)

| ID | Asset | Type · zone | Users / use cases | Business value | Owner | C/I/A | Dependencies | Assumptions |
|---|---|---|---|---|---|---|---|---|
| **E01** | Students and the student relationship (enrolled student body) | People / Organisational · Users | STU · all UC-S | The customers: tuition/funding, the university's mission and its future depend on students enrolling and staying. | University Management | -/-/- | E03-E13, E26 | Platform quality influences student satisfaction and retention. |
| **E02** | Student accounts and student role (one per student) | Information (identity) · Users / On-prem | STU · UC-S1..S8 | Gives each student access to exactly their own courses, grades and documents. | IT Security / Identity team | H/H/M | S13, S14, S10 | SSO with MFA for students (to confirm). |
| **E03** | Course workspaces (class pages, materials, announcements) | Service · On-prem | STU UC-S2 · LEC UC-L1, UC-L2 | Where teaching happens: lecturers open their classes and send materials, students follow the course. | Lecturer (per course) / Digital Learning | M/H/H | S19, S11, S13, S10, E19 | An LMS-like course platform runs on-prem (not in the image). |
| **E04** | Assignment submission and feedback service | Service · On-prem | STU UC-S3 · LEC UC-L3 | Students hand in work; lecturers collect, grade and return feedback. | Digital Learning | M/H/H | S19, S23, S11, S37 | Deadlines enforced by server time. |
| **E05** | Grade entry, release and viewing service | Service · On-prem | LEC UC-L4 · OFF UC-O2 · COO UC-C3 · STU UC-S4 | Turns assessment into results students can see and the office can certify. | Examination Office | H/H/M | S17, S18, S10, S13, E15 | Grade changes after release need office approval (four-eyes). |
| **E06** | AI tutor and document search | Service · Cloud | STU UC-S5 · LEC UC-L5 · AGT UC-G1 | The platform's new value: help for students at any hour. | Digital Learning / AI Platform Lead | M/H/M | S30, S31, S32, S12, E17 | The tutor only uses materials the lecturer allowed. |
| **E07** | Student administration service (enrolment, records, exam registration) | Service · On-prem | OFF UC-O1, UC-O2, UC-O4 · STU UC-S8 | The office runs the student lifecycle from enrolment to graduation. | Registrar / Student Office | H/H/M | S17, S04, S15, S13 | Used from office workstations on campus. |
| **E08** | Official documents service (request, issue, print) | Service · On-prem | STU UC-S6 · OFF UC-O3 | Students get legally valid certificates and transcripts for jobs, visas and further study. | Examination Office | H/H/M | S17, S18, S16, E23 | Printed and signed/sealed by the office on request. |
| **E09** | Reporting and analytics dashboards | Service · Cloud | COO UC-C1 · MGT UC-M1 · LEC UC-L6 | Coordinators and management decide on programmes, staffing and student support. | Programme Coordinator | M/H/M | S28, S33, E22, E21 | Users see only aggregated data for their scope. |
| **E10** | Timetable, scheduling and room booking | Service · On-prem / Cloud | STU UC-S7 · COO UC-C2 · AGT | Keeps the semester running: who teaches what, where and when. | Programme Coordinator | L/M/H | S17, S11, S30 | A scheduling agent can suggest changes but not publish them. |
| **E11** | Lab environment service | Service · On-prem / Cloud | STU UC-S7 · ADM UC-A2 | Practical labs for courses (like our CS-302 Proxmox lab). | System Administration | L/M/H | S24, S35, S13 | In scope per Session 1 use cases; not in the image. |
| **E12** | Notifications and announcements (email / Teams) | Service · External | LEC UC-L2 · STU · OFF | Deadlines, exam dates and grade releases reach users on time. | IT Operations | M/H/M | S38, S36 | Delivered through Microsoft 365. |
| **E13** | Student support / helpdesk requests | Service · On-prem | STU · OFF UC-O4 | Students get problems solved; unsolved problems cost satisfaction. | Student Office | M/M/M | S17, S38, S03 | Ticketing tool assumed; requests may contain personal data. |
| **E14** | Student personal records | Information · On-prem | OFF write · STU own · LEC roster | Legally required records to admit, enrol and serve students. | Registrar / Student Office | H/H/M | S17, S23, S13, S26 | Stays on-prem; cloud gets at most pseudonymised IDs. |
| **E15** | Grades and assessment results | Information · On-prem | LEC write · OFF write · STU own · COO read | Basis for progression and degrees; the university's academic credibility. | Examination Office | H/H/M | S18, S17, S23, S22, S26 | Raw grades never leave on-prem. |
| **E16** | Assignment submissions and feedback | Information · On-prem / Cloud | STU write own · LEC read own course | Evidence of student work for grading, disputes and plagiarism checks. | Lecturer (per course) | M/H/H | S19, S23, S26, S33 | AI feedback may put copies in cloud storage. |
| **E17** | Course materials (slides, exercises, readings) | Information · On-prem / Cloud | LEC write · STU read · AGT read | The teaching content; also the knowledge source of the AI tutor. | Lecturer (per course) | L/H/M | S19, S32, S33 | Internal, not secret. |
| **E18** | Exam questions, solutions and rubrics before release | Information · On-prem | LEC only | Fair exams need secret questions until the exam starts. | Lecturer / Examination Office | H/H/M | S19, S23, S13 | Kept out of the AI tutor's index. |
| **E19** | Class rosters and course enrolment lists | Information · On-prem | LEC read own course · OFF write · COO read | Decide who may open which class; drive access for students and lecturers. | Student Office | M/H/M | S17, S19, S10 | Synced from SIS to the course platform. |
| **E20** | Student–AI conversations and prompts | Information · Cloud | STU own · AGT write | Record of tutoring; students often paste personal details into it. | Digital Learning / AI Platform Lead | H/M/L | S30, S33, S31 | Limited retention; provider retention unknown. |
| **E21** | Learning analytics and progress insights | Information · Cloud | COO agg · LEC agg own class · STU own | Spot struggling students early and improve courses. | Programme Coordinator | H/M/L | E22, S30, S31 | Computed on pseudonymised data. |
| **E22** | Approved reporting dataset (metadata over the Metadata API) | Information · Bridge | AGT via MCP · COO · MGT | The only data the cloud may see; feeds reports and agents. | Programme Coordinator + DPO | M/H/M | S28, S21, S29, E14, E15 | Minimised and pseudonymised by design (main assumption). |
| **E23** | Official documents issued (transcripts, certificates) | Information · On-prem | OFF write · STU own | Legal documents issued in the university's name. | Examination Office | H/H/M | E15, S17, S16 | Each issued document is logged. |
| **E24** | Research data and unpublished outputs | Information · On-prem | RES UC-R1 | IP, funding, ethics approvals and publication priority. | Research Office | H/H/M | S20, S23, S26 | Some datasets contain study participants' personal data. |
| **E25** | Audit logs and activity evidence | Information · On-prem | ADM · DPO UC-D1 · all roles write | Proves who opened or changed what; needed for disputes, investigations and GDPR. | IT Security Officer | M/H/M | S22, S23, S37 | Every role's actions are logged, including agents. |
| **E26** | Reputation and trust (students, applicants, partners, regulators) | Organisational (intangible) · Organisation | Everyone | Drives future enrolments, partnerships and funding. | University Management | -/-/- | All essential assets | Rated through the impact of other assets. |
| **E27** | Core academic processes (enrolment, exams, grading, graduation) | Organisational (process) · Organisation | OFF · LEC · COO | The university's core operations; must be correct and on time. | Examination Office / Registrar | M/H/H | E05, E07, E08, S03 | AI assists, humans decide. |
| **E28** | Regulatory compliance (GDPR, exam regulations, accreditation, EU AI Act) | Organisational (intangible) · Organisation | DPO · MGT | Avoids fines, lawsuits and loss of accreditation. | DPO / Legal | -/-/- | S42, S41, E25 | AI in education may count as high-risk under the AI Act (to check). |

## Step 4b · Supporting assets (43)

| ID | Asset | Type · zone | Users / use cases | Business value | Owner | C/I/A | Dependencies | Assumptions |
|---|---|---|---|---|---|---|---|---|
| **S01** | Lecturers (teaching staff) | People · Users | LEC | Create content and make grading decisions; their knowledge and time are the teaching capacity. | Dean | -/-/M | S02 | Phishing target because their accounts can change grades. |
| **S02** | Lecturer accounts and course-ownership rights | Information (identity) · Users / On-prem | LEC UC-L1..L6 | Let each lecturer open only the classes they teach and grade only those. | IT Security / Identity team | H/H/M | S13, S14, S10, E19 | MFA required for staff. |
| **S03** | Office staff (Student Office / Examination Office secretaries) | People · Users | OFF | Run enrolment, exams and documents; know the processes. | Head of Student Office | -/-/H | S04 | Small team, key-person risk in exam periods. |
| **S04** | Office staff accounts (records and exam admin roles) | Information (identity) · Users / On-prem | OFF UC-O1..O4 | Highest business privilege: can change any student record and final grade. | IT Security / Identity team | H/H/M | S13, S14, S10 | Office accounts limited to campus network. |
| **S05** | Programme coordinators | People · Users | COO | Plan programmes and react to reports. | Dean | -/-/M | S06 |  |
| **S06** | Coordinator accounts (reporting and scheduling roles) | Information (identity) · Users | COO UC-C1..C3 | Access to programme-wide reports and dispute approvals. | IT Security / Identity team | H/H/M | S13, S14, S10 |  |
| **S07** | System administrators | People · Organisation | ADM | Operate, patch and recover everything. | Head of IT Operations | -/-/H | S08 | Small team. |
| **S08** | Administrator and privileged accounts (on-prem, DB, cloud) | Information (identity) · All | ADM UC-A1..A3 | Maintenance access; can bypass application controls. | CISO | H/H/M | S13, S35, S25 | Separate admin accounts, MFA, every action logged. |
| **S09** | AI agent identities (service accounts, workload tokens) | Information (identity) · Cloud / Bridge | AGT UC-G1, UC-G2 | Each agent acts with its own limited rights instead of a shared superuser. | IT Security / AI Platform Lead | H/H/M | S13, S29, S21 | One identity per agent, short-lived tokens. |
| **S10** | Role and permission model (RBAC: who can open what) | Information (configuration) · On-prem | ADM writes · all roles depend on it | Encodes the access matrix: students see own data, lecturers own classes, office all records. | CISO + data owners | M/H/M | S13, E19 | Roles derived from SIS (staff/student status) and rosters. |
| **S11** | Web portal and course platform front end (DMZ, reverse proxy + WAF) | Software · On-prem (DMZ) | STU · LEC · OFF · COO | The front door for every human user. | Digital Learning / IT Operations | M/H/H | S13, S25, S36, S29 | Reachable from the internet through the reverse proxy. |
| **S12** | AI chat front end | Software · Cloud | STU · LEC | Where students talk to the AI tutor. | AI Platform Lead | M/H/M | S13, S35, S36 | Hosted in the cloud tenant. |
| **S13** | Identity Provider (SSO), directory and MFA | Software · On-prem | All roles UC-S1 | One login for every user and service; enforces MFA. | IT Security / Identity team | H/H/H | S24, S25, S37, S26 | Cloud tenant federated to it. |
| **S14** | User sessions and session tokens | Information (credential) · Users | All roles | Keep users logged in after SSO. | IT Security / Identity team | H/H/L | S13, S11 | Short lifetime, secure cookies. |
| **S15** | End-user devices (student laptops, lecturer devices, office workstations) | Infrastructure · Users | All roles | How users reach the platform; office PCs show full student records. | Users (office PCs: IT Operations) | M/M/M | S39 | Student devices unmanaged and untrusted; office PCs managed. |
| **S16** | Official document printers, seal and security paper | Infrastructure (physical) · On-prem | OFF UC-O3 | Produce physically valid transcripts and certificates. | Examination Office | M/H/M | S25, S27 | Located in the office, locked. |
| **S17** | Student Information System (SIS) | Software · On-prem | OFF · STU · LEC · COO | System of record for students, enrolment, exams. | IT Operations (owner: Registrar) | H/H/M | S23, S24, S25, S13 | Vendor product. |
| **S18** | Grade database | Software · On-prem | LEC · OFF · STU (via service) | Stores all grades. | IT Operations (owner: Examination Office) | H/H/M | S23, S24, S25, S26 | Only the application service account connects. |
| **S19** | Course platform / LMS backend (workspaces, submissions, materials) | Software · On-prem | LEC · STU | Runs course workspaces and submissions. | Digital Learning | M/H/H | S23, S24, S13, E19 | LMS-like system assumed (e.g. Moodle); not drawn in the image. |
| **S20** | Research repository | Software · On-prem | RES | Stores and shares research data. | Research Office / IT | H/H/M | S23, S24, S13 | External partners may have accounts. |
| **S21** | MCP service and its tool permissions | Software · On-prem | AGT UC-G2 | Lets agents use approved capabilities without database access. | AI Platform Lead + CISO | H/H/M | S28, S09, S24 | Agents only reach it through the gateway. |
| **S22** | Audit and logging service (SIEM) | Software · On-prem | ADM · DPO | Collects evidence from all zones. | IT Security Officer | M/H/M | S23, S24, S37 |  |
| **S23** | Sensitive data storage | Infrastructure · On-prem | (all on-prem data) | Stores records, grades, submissions, research at rest. | IT Operations | H/H/H | S24, S27 | Encrypted at rest. |
| **S24** | On-prem servers, hypervisors and VMs | Infrastructure · On-prem | ADM | Run all on-prem services. | IT Operations | M/H/H | S27, S25 |  |
| **S25** | On-prem network, firewall and DMZ | Infrastructure · On-prem | ADM | Connects and separates zones; DMZ isolates the portal. | Network team | M/H/H | S27, S39 | Segmented by VLAN. |
| **S26** | Backups (on-prem and off-site copy) | Infrastructure / Information · On-prem | ADM | Restore after ransomware or error. | IT Operations | H/H/H | S23, S27 | Encrypted, one offline copy (assumed). |
| **S27** | Data centre facility, power and cooling | Infrastructure (physical) · On-prem | ADM | Physical home of all on-prem hardware. | Facilities Manager | M/M/H | - | UPS and badge access. |
| **S28** | Secure API Gateway and Metadata API (incl. its configuration) | Software / Infrastructure · Bridge | AGT · COO (indirect) | The single controlled bridge between on-prem and cloud. | CISO / Integration team | M/H/H | S29, S22, S25 | Only path between zones; not in public DNS. |
| **S29** | API tokens, mTLS certificates and keys | Information (credential) · Bridge / All | AGT · services | Authenticate services and encrypt traffic. | IT Security / PKI | H/H/M | S35, S08 | Stored in key vaults, short-lived. |
| **S30** | Agent orchestrator and AI agents (incl. system prompts) | Software · Cloud | AGT · STU (indirect) | Do the tutoring, search, reporting and scheduling work. | AI Platform Lead | M/H/M | S31, S32, S09, S35 | Narrow role per agent. |
| **S31** | LLM inference service / model provider | Service (external) · Cloud / External | AGT | Generates all AI answers. | AI Platform Lead (supplier) | H/M/H | S39, S41 | No training on university data (contract). |
| **S32** | Vector database (embeddings of course materials) | Software / Information · Cloud | AGT UC-G1 | Lets the tutor find relevant course content. | AI Platform Lead | M/H/M | S35, E17 | Course material only, no personal data. |
| **S33** | Cloud storage (reports, exports, AI feedback files) | Infrastructure · Cloud | AGT · COO | Stores files of the cloud zone. | Cloud Platform team | H/M/M | S35 | Private, encrypted. |
| **S34** | Cloud monitoring and alerting | Software · Cloud | ADM | Detects abnormal agent behaviour (e.g. too many record requests). | Cloud Platform team | M/H/M | S35, S22 |  |
| **S35** | Cloud tenant: compute, IAM and key vault | Infrastructure · Cloud | ADM | Runs and controls everything in the cloud zone. | Cloud Platform team + CISO | H/H/H | S13, S08, S39 | Federated to on-prem IdP, MFA. |
| **S36** | DNS (public, internal and private cloud zones) | Infrastructure (external service) · All | All roles | Users find the portal and AI chat; services find each other. | Network team | L/H/H | S39 | Split-horizon; gateway not public. |
| **S37** | Time synchronisation (NTP) | Infrastructure · All | (all) | Correct times for deadlines, tokens and logs. | IT Operations | L/H/M | S39 | Synced to PTB time servers. |
| **S38** | Email and Teams (Microsoft 365) | Service (external) · External | All roles | Notifications, password resets, communication. | IT Operations (supplier: Microsoft) | M/H/M | S39, S41 |  |
| **S39** | Internet connectivity (ISP uplinks) | Infrastructure (external service) · All | All roles | Connects users and both zones. | Network team | L/M/H | S41 | Redundant uplink assumed. |
| **S40** | Source code, CI/CD pipeline and third-party libraries | Software · Organisation | ADM · developers | Build and deploy all components and configurations. | Development team lead | M/H/M | S08, S29 | Git repository + automated deploys. |
| **S41** | Supplier contracts, DPAs, SLAs and licences | Organisational · Organisation | MGT · DPO | Define what cloud/LLM providers may do with our users' data. | Procurement / Legal | M/H/M | S42 | EU data location. |
| **S42** | Policies and GDPR documentation (access policy, AI use policy, DPIA, retention) | Organisational · Organisation | DPO · MGT · all users | Rules every user and control follows; legal basis for processing. | DPO / CISO | L/H/M | S43 | Approved by management. |
| **S43** | DPO, security team and developers (oversight and build roles) | People · Organisation | DPO · ADM | Handle privacy requests, incidents and changes. | CISO / DPO | -/-/M | S42 |  |

## CIA rationale for High ratings

- **E01 Students and the student relationship (enrolled student body):** Not rated with CIA; protected through every asset students rely on. Losing their trust loses enrolments.
- **E02 Student accounts and student role (one per student):** C/I: a taken-over student account exposes grades and personal data and allows fake submissions.
- **E03 Course workspaces (class pages, materials, announcements):** I: only the course's lecturer may post. A: needed every teaching day.
- **E04 Assignment submission and feedback service:** I: submissions and timestamps must be unchanged. A: an outage at a deadline hits a whole cohort.
- **E05 Grade entry, release and viewing service:** C: a student must never see another student's grades (IDOR). I: only the right lecturer/office may change a grade.
- **E06 AI tutor and document search:** I: prompt injection or poisoned materials make the tutor give wrong or leaking answers.
- **E07 Student administration service (enrolment, records, exam registration):** C: office screens show full personal records. I: a wrong record changes a student's legal status.
- **E08 Official documents service (request, issue, print):** I: a forged or wrong transcript is fraud. C: documents contain grades and personal data.
- **E09 Reporting and analytics dashboards:** I: wrong numbers lead to wrong management decisions.
- **E10 Timetable, scheduling and room booking:** A: no timetable at semester start means chaos for every user group.
- **E11 Lab environment service:** A: labs down means practical sessions are cancelled.
- **E12 Notifications and announcements (email / Teams):** I: fake announcements (phishing in the university's name) mislead students.
- **E14 Student personal records:** C: GDPR personal data. I: wrong enrolment status blocks exams, fees, visas.
- **E15 Grades and assessment results:** C: personal data. I: an altered grade is an invalid academic decision.
- **E16 Assignment submissions and feedback:** I: a swapped file means a wrong grade. A: must be there at grading time.
- **E17 Course materials (slides, exercises, readings):** I: altered material makes the AI tutor teach wrong content.
- **E18 Exam questions, solutions and rubrics before release:** C: an early leak voids the exam for everyone.
- **E19 Class rosters and course enrolment lists:** I: a wrong roster gives a student or lecturer access to the wrong class (broken access control).
- **E20 Student–AI conversations and prompts:** C: chats may contain health issues, grades, personal stories.
- **E21 Learning analytics and progress insights:** C: profiling individual students is sensitive under GDPR.
- **E22 Approved reporting dataset (metadata over the Metadata API):** I: tampered metadata gives wrong reports and wrong AI decisions.
- **E23 Official documents issued (transcripts, certificates):** I: a forged document is fraud and damages reputation.
- **E24 Research data and unpublished outputs:** C: leaks lose IP and break partner NDAs. I: manipulated data invalidates research.
- **E25 Audit logs and activity evidence:** I: editable logs mean no evidence (Sprint 3 depends on it).
- **E26 Reputation and trust (students, applicants, partners, regulators):** Harmed by any C/I/A failure of the assets above.
- **E27 Core academic processes (enrolment, exams, grading, graduation):** I: process errors produce invalid degrees. A: missed legal deadlines.
- **E28 Regulatory compliance (GDPR, exam regulations, accreditation, EU AI Act):** Affected by breaches of E14-E25.
- **S02 Lecturer accounts and course-ownership rights:** A stolen lecturer account can change grades and read rosters.
- **S03 Office staff (Student Office / Examination Office secretaries):** A: without them no enrolment, no transcripts; also social-engineering targets (fake student calls).
- **S04 Office staff accounts (records and exam admin roles):** Compromise allows mass changes to records and grades.
- **S06 Coordinator accounts (reporting and scheduling roles):** Programme-wide read access to sensitive progress data.
- **S07 System administrators:** A: nobody else can restore the platform after an incident.
- **S08 Administrator and privileged accounts (on-prem, DB, cloud):** Admin accounts are the master keys of the platform.
- **S09 AI agent identities (service accounts, workload tokens):** An over-privileged agent identity is a direct path from the cloud to student data.
- **S10 Role and permission model (RBAC: who can open what):** I: one wrong role mapping gives users access to data that is not theirs.
- **S11 Web portal and course platform front end (DMZ, reverse proxy + WAF):** Internet-facing: A for daily use, I so pages cannot be tampered (XSS).
- **S12 AI chat front end:** I: injected scripts or prompts here reach every chat user.
- **S13 Identity Provider (SSO), directory and MFA:** Every access decision depends on it; down = nobody works; altered = anyone becomes admin.
- **S14 User sessions and session tokens:** A hijacked session equals the user's full access.
- **S16 Official document printers, seal and security paper:** I: stolen security paper or seals enable forged certificates.
- **S17 Student Information System (SIS):** Holds E14 and drives rosters and roles.
- **S18 Grade database:** Holds E15 directly.
- **S19 Course platform / LMS backend (workspaces, submissions, materials):** Holds submissions, materials and exam drafts.
- **S20 Research repository:** Holds E24.
- **S21 MCP service and its tool permissions:** The door from untrusted agents into trusted data.
- **S22 Audit and logging service (SIEM):** Without it, E25 does not exist.
- **S23 Sensitive data storage:** Ransomware here stops every user group.
- **S24 On-prem servers, hypervisors and VMs:** A compromised hypervisor controls every VM.
- **S25 On-prem network, firewall and DMZ:** A flat network lets an attacker move from the portal to the databases.
- **S26 Backups (on-prem and off-site copy):** Contain all data; must be untampered and restorable.
- **S27 Data centre facility, power and cooling:** A: fire, flood or power loss stops on-prem services.
- **S28 Secure API Gateway and Metadata API (incl. its configuration):** Every cross-zone request passes here; a bypass opens the trusted zone.
- **S29 API tokens, mTLS certificates and keys:** A leaked token or key = trusted access to the Metadata API.
- **S30 Agent orchestrator and AI agents (incl. system prompts):** I: prompt-injected agents give wrong answers or over-request data.
- **S31 LLM inference service / model provider:** C: every prompt passes through it. A: provider down = no tutor.
- **S32 Vector database (embeddings of course materials):** I: poisoned chunks change answers. C: chunks are readable copies.
- **S33 Cloud storage (reports, exports, AI feedback files):** C: a public bucket is a classic breach.
- **S34 Cloud monitoring and alerting:** I: disabled alerts hide an attack.
- **S35 Cloud tenant: compute, IAM and key vault:** The cloud admin can delete or expose the whole cloud zone.
- **S36 DNS (public, internal and private cloud zones):** I: hijacked public DNS sends users to a phishing copy.
- **S37 Time synchronisation (NTP):** I: wrong time breaks deadlines, token validity and the incident timeline.
- **S38 Email and Teams (Microsoft 365):** I: control of reset email = account takeover.
- **S39 Internet connectivity (ISP uplinks):** A: no uplink, no cloud features and no remote users.
- **S40 Source code, CI/CD pipeline and third-party libraries:** I: a compromised pipeline or package deploys backdoors everywhere.
- **S41 Supplier contracts, DPAs, SLAs and licences:** I: contract terms decide whether a provider may store or train on student data.
- **S42 Policies and GDPR documentation (access policy, AI use policy, DPIA, retention):** I: missing or wrong DPIA makes the cloud processing unlawful.
