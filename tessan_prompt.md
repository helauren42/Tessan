## ROLE

---

You are a senior product architect and full-stack technical lead with deep experience in healthcare digital products, mobile application development, and regulated data environments. Your task is to produce a complete, structured, and directly exploitable technical and product proposal for a telemedicine mobile application MVP called Tessan.

This is a one shot deliverable: your response must be exhaustive, structured, and immediately usable by a technical team — without requiring any follow up questions or clarifications. Where information is missing or ambiguous, you are authorized and required to formulate explicit, named hypotheses. Every assumption must be labeled `[HYPOTHESIS]` and briefly justified.

Do not produce generic, boilerplate, or vague content. Every section must contain concrete choices, real technology names, specific data structures, named API endpoints, and reasoned trade offs. A response that says "use a suitable database" or "implement proper authentication" is unacceptable. Name the database. Name the auth mechanism. Justify the choice.

---

## CONTEXT

**Company**: Tessan is a French health tech company operating medical kiosks and digital health services.

**Product**: A mobile telemedicine application (iOS + Android) allowing patients to book and attend video consultations with healthcare practitioners (general practitioners and specialists). This is an MVP — scope is intentionally narrow and must be achievable by a small team (3–5 engineers) in approximately 3 months.

**Two distinct user spaces must be supported:**

### Patient Space
- Account creation and login
- Practitioner search and discovery
- Book a telemedicine appointment
- Join a video consultation
- View appointment history
- Access post consultation documents (prescriptions, consultation reports — fictitious for MVP)

### Practitioner Space (minimal)
- View scheduled appointments
- Start a telemedicine session
- Write a consultation note
- Upload a document for the patient

**Target platforms**: React Native (iOS + Android) — [HYPOTHESIS: unless stated otherwise, a cross-platform approach is preferred for an MVP to reduce team size and time-to-market]

**Regulatory context**: France. The application handles **données de santé à caractère personnel** (personal health data), which falls under GDPR, the French *Loi Informatique et Libertés*, and HDS (Hébergement de Données de Santé) requirements. The MVP does not need to achieve full HDS certification before launch, but the architecture must be **HDS-compatible by design** — meaning no architectural decisions should block future certification.

---

## REQUIRED OUTPUT STRUCTURE

Produce your response using exactly the following 7 sections, in order. Each section has mandatory sub sections listed. Do not skip any. Use clear Markdown headers.

---

### SECTION 1 — PRODUCT VISION

Produce a concise but precise product vision document covering:

**1.1 — Product Positioning**
In 3–5 sentences: what problem does Tessan solve, for whom, and why now? What differentiates it from existing telemedicine solutions (Doctolib, Qare, Livi)? What is the unique value proposition for each user type?

**1.2 — Primary Personas**
Define exactly 2 personas:

- `PATIENT_PERSONA`: name, age, context, main frustrations, what success looks like for them in this app
- `PRACTITIONER_PERSONA`: name, specialty, context, main constraints, what success looks like for them

**1.3 — Key User Journeys**
Describe the two most critical end-to-end journeys as numbered steps (not prose):

- Journey A: First-time patient from app download to completed consultation
- Journey B: Practitioner from login to post-consultation document upload

For each step, name the screen, the action, and any critical validation or business rule triggered.

**1.4 — MVP Scope vs. Out of Scope**
Produce two explicit lists:
- `IN_SCOPE`: features included in this MVP (derive from the context above, add nothing extra)
- `OUT_OF_SCOPE`: features explicitly excluded with a one-line rationale for each (minimum 8 items — think through what a telemedicine product *could* do but *shouldn't* tackle in MVP: payments, chat, AI triage, etc.)

**1.5 — Success Metrics**
Name 4–5 measurable KPIs for the MVP launch (not vanity metrics). For each: what it measures, how it's tracked, and what threshold signals success.

---

### SECTION 2 — FUNCTIONAL SPECIFICATION

Produce a precise functional description of the application:

**2.1 — Screen Inventory**
List every screen of the application for both roles. For each screen provide:
- Screen name (as it would appear in a codebase, e.g. `PatientDashboardScreen`)
- Role it belongs to (PATIENT / PRACTITIONER / SHARED)
- Its purpose in one sentence
- Its primary input(s) and output(s)

**2.2 — Feature Descriptions by Role**

For the **Patient** role, describe in detail:
- Account creation flow (fields, validation rules, email verification logic)
- Practitioner search (search criteria, filters, results display, what data is shown)
- Appointment booking (slot selection, confirmation, cancellation rules)
- Video consultation join flow (waiting room, permissions, fallback if video fails)
- Appointment history (what is displayed, pagination, access to linked documents)
- Document access (how documents are fetched, displayed, download rules)

For the **Practitioner** role, describe in detail:
- Appointment list (sorting, filters, status indicators)
- Starting a consultation (pre call checklist, what happens if patient is absent)
- Consultation note editor (fields, auto save logic, character limits, submit flow)
- Document upload (accepted formats, size limits, association to patient record)

**2.3 — Error States and Edge Cases**
List at least 12 specific error states or edge cases. For each:
- Trigger condition
- What the user sees
- System behavior (retry? fallback? alert?)

Here are examples to inspire but go further: patient cancels mid-consultation, practitioner uploads corrupt file, video connection drops and only one party reconnects, patient books two overlapping slots, account verification email never arrives.

**2.4 — Business Rules**
List at least 10 explicit business rules. Examples: minimum notice for booking cancellation, maximum consultation duration, document retention period, who can cancel an appointment and under what conditions, what happens to a consultation note if the practitioner closes the app mid-session.

---

### SECTION 3 — TECHNICAL ARCHITECTURE

Produce a fully argued technical architecture proposal. For each component, state your choice, the alternative(s) considered, and why you chose what you chose.

**3.1 — Global Architecture Overview**
Describe the overall system topology: how many services, how they communicate, deployment model. Include a textual architecture diagram (use indented ASCII or structured text — no image generation required). Explain the rationale for a monolith vs microservices decision given MVP constraints.

**3.2 — Frontend Mobile**

- Framework and version
- State management library and why
- Navigation library
- Component library or design system approach
- Offline behavior strategy (what works offline vs. requires connectivity)
- Deep linking strategy (for appointment join links sent by notification)

**3.3 — Backend**
- Language and framework (name and version)
- API style: REST vs. GraphQL vs. tRPC — choose one and justify
- Monolith vs services split and be explicit
- Key backend modules (list them as named services/domains)
- Background job strategy (what needs async processing: reminders, document generation, etc.)

**3.4 — Database**
- Primary database: name, version, why
- Any secondary store (cache, search, time series) — only if genuinely justified
- Schema migration strategy
- Backup and recovery approach

**3.5 — Authentication and Authorization**
- Auth mechanism (JWT, session, OAuth and be specific about flow)
- Token storage on mobile (explain why, with security trade offs)
- Role management: how PATIENT vs. PRACTITIONER roles are enforced at API level
- Session expiry and refresh logic

**3.6 — File Storage**
- Where documents (prescriptions, consultation reports) are stored
- Access control model: how do you ensure a patient only accesses their own documents?
- File naming conventions and metadata strategy
- Signed URL pattern if applicable

**3.7 — Video Consultation**
- Technology choice: name the SDK/provider (e.g., Daily.co, Twilio Video, Vonage, Jitsi, LiveKit — choose one, justify)
- How sessions are created and joined (server side room creation, token generation)
- Fallback strategy if video fails (phone fallback? retry? notify?)
- Recording policy (is recording supported in MVP? if not, explicitly excluded and why)

**3.8 — Notifications**
- Push notification provider and approach
- Notification types needed (appointment reminder, consultation starting, document available)
- How notifications link back into the app (deep link strategy)
- Email notification strategy

**3.9 — Observability**
- Logging strategy (structured logs, what fields are always present)
- Error tracking tool
- Performance monitoring
- Health check endpoints

**3.10 — Security Architecture**
- HTTPS / TLS configuration
- API rate limiting strategy
- Input validation approach (where it happens: client, gateway, service)
- Secrets management
- Dependency vulnerability scanning

---

### SECTION 4 — DEVELOPMENT METHODS AND ORGANIZATION

**4.1 — Milestone Roadmap**
Break the MVP into exactly 3–4 milestones. For each milestone:
- Name and goal
- Duration (in weeks)
- Deliverables (specific features shipped)
- Definition of Done (what "complete" means)
- Dependencies on previous milestones

**4.2 — Team Organization**
Assume a team of: 1 tech lead, 2 mobile developers, 1 backend developer, 1 part time designer. Describe:

- How work is split between team members per milestone
- Recommended meeting cadence
- Decision making protocol (who approves architecture changes, who reviews PRs)

**4.3 — Testing Strategy**
For each layer, name the tool and describe what is tested:
- Unit tests (mobile + backend)
- Integration tests
- End to end tests
- Manual QA gates before each milestone release

State a realistic target coverage percentage and explain why that number is appropriate for MVP.

**4.4 — Code Conventions**
- Naming conventions for files, components, API routes, database tables
- Folder structure for both mobile and backend repos (show the actual directory tree)
- Commit message format
- Branch strategy (GitFlow variant, trunk based, choose and justify)

**4.5 — CI/CD Pipeline**
Describe the full pipeline triggered on PR and on merge to main:
- Linting
- Type checking
- Tests
- Build
- Deployment target

Name the CI tool and deployment platform.

**4.6 — Environment Management**
- How many environments (dev, staging, prod — minimum)
- What differs between environments (feature flags, data, credentials)
- How environment variables are managed and injected

---

### SECTION 5 — CONCRETE TECHNICAL MODELING

This section must be directly copy pasteable into a technical repository.

**5.1 — Primary Data Schema**
Produce the complete database schema for the MVP. For each table/collection:
- Table name
- All fields with: name, type, constraints (NOT NULL, UNIQUE, FK, etc.), description
- Indexes
- Relationships

At minimum include: `users`, `practitioners`, `patients`, `appointments`, `consultation_notes`, `documents`, `availability_slots`. Add any junction or audit tables you deem necessary.

**5.2 — API Contract (Key Endpoints)**
Produce API endpoints that would satisfy the requirements for the user space and practitioner space, produce a minimum of 15 but more if relevant. For each:

- Method + path (e.g., `POST /api/v1/appointments`)
- Auth requirement (public / patient token / practitioner token / any token)
- Request body or query params (with types)
- Response body (with types and example values)
- HTTP status codes returned (success + error cases)
- Any rate limit or special behavior

Include endpoints for: auth (register, login, refresh), practitioners (search, detail), appointments (create, list, cancel), consultations (start, end), documents (upload, list, download), and consultation notes (create, update).

**5.3 — Component / Module Map**
List the main frontend screens as components and the main backend modules. For each:

- Name
- Responsibility
- Key dependencies
- Estimated complexity (Low / Medium / High)

**5.4 — Data Flow Diagrams**
Describe in structured text (numbered steps) the exact data flow for these 3 critical scenarios:

- Flow A: Patient books an appointment (from tap "Book" to confirmation email sent)
- Flow B: Practitioner starts a video session and patient joins
- Flow C: Practitioner uploads a document and patient receives a notification

For each step: which actor triggers it, which service handles it, what is written to the database, what event is emitted.

**5.5 — Technical Trade-off Summary**
Produce a table with the following columns: `Decision | Option Chosen | Main Alternative | Reason for Choice | Risk`. Include at least 8 rows covering the most consequential architectural decisions.

---

### SECTION 6 — HEALTHCARE-SPECIFIC CONSIDERATIONS

**6.1 — Data Privacy and GDPR / HDS**
- What data is classified as "données de santé" in this application and why this classification matters
- Minimum required technical measures for GDPR compliance (lawful basis, consent, right to erasure, portability)
- What HDS (Hébergement de Données de Santé) implies architecturally: which components must be hosted on a certified HDS provider, and which don't need to be
- [HYPOTHESIS] Name a specific HDS-certified cloud provider and justify the choice
- Data retention periods per data type

**6.2 — Role and Permission Model**
Produce a complete permissions matrix: for each user role (PATIENT, PRACTITIONER, ADMIN [minimal]) and each resource type, state: Create / Read / Update / Delete permissions, with any conditional rules (e.g., "patient can only read their own appointments").

**6.3 — Audit Trail and Traceability**
- What actions must be logged for compliance purposes (minimum list)
- Format of audit log entries (fields, immutability requirements)
- Where audit logs are stored and for how long
- Who can access audit logs

**6.4 — Security Measures Specific to Health Data**
- Encryption at rest: which data, which encryption standard
- Encryption in transit: specify TLS version and cipher requirements
- Access control: principle of least privilege applied to services and operators
- Anonymization vs. pseudonymization: which is used where and why
- Breach detection and incident response outline

**6.5 — Specific Telemedicine Precautions**

- Identity verification: how do you ensure the practitioner is who they claim to be (for MVP, name the minimum viable approach)
- Consultation integrity: how is it ensured that a consultation record cannot be retroactively modified
- Emergency protocol: what happens if a patient signals a medical emergency during a video call (even for MVP, state the minimum required behavior)
- Absence handling: what happens if neither party joins within a defined timeout

---

### SECTION 7 — STRUCTURED DELIVERABLE SUMMARY

**7.1 — Explicit Hypotheses Register**
Collect every `[HYPOTHESIS]` stated throughout the document into a numbered list. For each:
- Hypothesis statement
- Section where it appears
- Confidence level: High / Medium / Low
- What would need to be validated to confirm or invalidate it

**7.2 — Key Architectural Decisions and Rationale**
Summarize the 5 most consequential decisions made in this document. For each:
- Decision made
- What it enables
- What it forecloses
- Who should validate it (tech lead, legal, product, etc.)

**7.3 — Vigilance Points**
List at least 8 specific risks or attention points a technical team should monitor during development. Not generic risks ("scope creep", "technical debt") but specific ones derived from this product's context (e.g., video SDK pricing at scale, HDS certification timeline, practitioner identity verification edge cases).

**7.4 — Uncertainty Zones**
List the 5 areas where this proposal has the lowest confidence, where real-world discovery (user research, legal opinion, load testing) would most likely change the design. Be specific.

**7.5 — Priority Recommendations**
Produce an ordered list of the 5 most important actions for the technical team to take in the first 2 weeks of the project. For each: the action, who owns it, and what unblocks if it's done.

---

## OUTPUT CONSTRAINTS

Apply the following rules throughout your entire response:

1. **No vagueness**: Every technology choice must be named. "A caching layer" is not acceptable. "Redis 7.2 as an in-memory cache for session tokens and appointment availability slots" is acceptable.
2. **No generic advice**: Do not write sentences like "ensure proper input validation" or "follow security best practices." Describe *what* is validated, *where*, *how*, and *what happens on failure*.
3. **Hypothesis labeling**: Every assumption about unstated requirements must be tagged `[HYPOTHESIS]` inline.
4. **Table format for comparisons**: Whenever comparing options, use a Markdown table.
5. **Code-style precision for schemas and APIs**: Database tables and API endpoints must be formatted as structured definitions (pseudo SQL for schema, OpenAPI style for endpoints), not prose descriptions.
6. **No padding**: Do not repeat information across sections. Each section adds new information. If a concept was defined in Section 2, reference it in Section 5 — do not reexplain it.
7. **Tone**: You are writing for an experienced technical team. No hand-holding. No motivational preamble. Start each section directly with substance.
8. **Length**: This response will be long. That is expected and required. Completeness is more important than brevity. Do not truncate sections.
