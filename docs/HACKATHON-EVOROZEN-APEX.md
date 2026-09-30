# CampusNotice.AI @ Evorozen Apex

This document covers what's specific to CampusNotice.AI's submission to **Evorozen Apex: NextGen AI Buildathon**. For the project itself, see the [main README](../README.md).

## Why this project for Evorozen Apex

Evorozen Apex is focused on AI-driven products that can move beyond a prototype toward a scalable product, with particular emphasis on AI integration, business viability and Go-To-Market, UI/UX, technical scalability, and the quality of the final pitch. citeturn390502view1

CampusNotice.AI maps naturally to those requirements:

**Core Engine & AI Integration** — CampusNotice.AI uses AI at the core of the product rather than as a decorative chatbot. A Strands Agent interprets messy notices and extracts structured metadata, while semantic embeddings match notices against each student's natural-language interests. The final routing decision remains deterministic, separating AI interpretation from institutional eligibility logic.

**Business Viability & Go-To-Market** — The problem is concrete: students receive a large volume of campus announcements, but only a small subset is relevant to an individual student. The product can be positioned as a B2B2C campus communication platform: colleges and departments publish once, while students receive personalized, explainable feeds. A practical initial GTM path is to pilot with individual colleges/departments, prove engagement and acknowledgement rates, and then expand to more departments and campuses.

**UI/UX & Design Aesthetic** — The product has separate Admin and Student portals. The student experience is centered around a personalized feed, while administrators get a direct notice-upload workflow. The interface should keep the core interaction simple: upload → understand → route → discover.

**Technical Scalability & Code Quality** — The architecture separates document processing, AI interpretation, embeddings, eligibility rules, persistence, authentication, and frontend concerns. This makes individual components easier to test, replace, and scale independently as usage grows.

**Pitch & Video Demonstration** — The strongest three-minute demonstration is a complete story: an admin uploads a real notice, the AI extracts and structures it, the system checks eligibility and semantic relevance, and a student receives a personalized recommendation with an explanation.

### Relevant Evorozen category

**Primary fit: Next-Gen Consumer SaaS & Micro-Tools**

CampusNotice.AI is a digital service that helps students discover relevant campus opportunities and announcements while giving administrators a simpler distribution workflow. Evorozen lists this category for everyday consumer applications, productivity tools, and digital services. citeturn390502view1

The product also contains a strong personalization component, but it is not positioned as an adaptive learning engine, so the Consumer SaaS track is the more direct description of the current product.

## The product in one line

> **CampusNotice.AI turns overloaded campus announcements into personalized, explainable student feeds.**

## How the AI actually works

**Notice → Text Extraction / OCR → Strands Agent → Structured Metadata → Embedding → Eligibility Check → Relevance Score → Decision + Priority → Student Feed / Notification**

The core architectural principle is:

> **AI understands. Deterministic logic decides.**

The AI layer handles ambiguous, unstructured information:

- Understanding notices in PDF, image, TXT, or raw-text form
- OCR fallback for scanned notices and images
- Extracting title, category, summary, eligibility, deadline, required action, and importance
- Generating semantic representations for relevance matching

The deterministic layer handles decisions that should be predictable and testable:

- Academic eligibility
- Student preference matching thresholds
- Routing logic
- Priority handling
- Final recommendation decision

This separation makes the system easier to debug and gives students a reason for each recommendation instead of exposing an unexplained black-box score.

## Technical architecture

```text
                    ┌───────────────────┐
                    │   Admin Portal    │
                    │ React + Vite      │
                    └─────────┬─────────┘
                              │
                       Upload Notice
                              │
                              ▼
                 ┌────────────────────────┐
                 │ FastAPI Backend        │
                 └───────────┬────────────┘
                             │
               ┌─────────────┴─────────────┐
               │                           │
               ▼                           ▼
      ┌────────────────┐          ┌─────────────────┐
      │ PDF / Text      │          │ OCR Pipeline    │
      │ Extraction      │          │ Scans / Images  │
      └───────┬────────┘          └────────┬────────┘
              └──────────────┬──────────────┘
                             ▼
                   ┌──────────────────┐
                   │ Strands Agent    │
                   │ Notice Metadata  │
                   └────────┬─────────┘
                            ▼
                  ┌────────────────────┐
                  │ Embedding Layer    │
                  │ Semantic Relevance │
                  └─────────┬──────────┘
                            ▼
                 ┌─────────────────────┐
                 │ Eligibility +       │
                 │ Routing Engine      │
                 └──────────┬──────────┘
                            ▼
                  ┌────────────────────┐
                  │ PostgreSQL /       │
                  │ SQLAlchemy         │
                  └─────────┬──────────┘
                            ▼
                 ┌─────────────────────┐
                 │ Student Portal      │
                 │ Personalized Feed   │
                 └─────────────────────┘
```

## What makes this more than an LLM wrapper

The main product value is not simply asking an LLM to summarize a notice.

CampusNotice.AI combines:

**Unstructured document understanding**

A single ingestion layer needs to handle clean PDFs, scanned PDFs, images, TXT files, and pasted text.

**Semantic personalization**

Student interests are expressed in natural language, so matching is based on meaning rather than exact keyword overlap.

**Deterministic institutional rules**

Eligibility remains explicit and testable. A student can be interested in an event without being academically eligible for it.

**Explainable recommendations**

The system can surface why a notice was recommended, making personalization understandable to the student.

**Full-stack delivery**

The product connects document ingestion, AI processing, a relational database, authentication, APIs, and separate admin/student interfaces into one workflow.

## Challenges I ran into

### 1. Notices are messy

A notice may be a text PDF, scanned PDF, photograph, or pasted text. I implemented separate extraction paths so the system does not depend on one perfectly clean input format.

### 2. Keyword matching was not enough

Students describe interests differently from the language used in institutional notices. Embedding-based semantic matching was introduced to capture relationships such as *"technical events"* and *"engineering workshop"* even when the wording differs.

### 3. Relevance and eligibility are different problems

A student may be highly interested in robotics but not satisfy the academic requirements of a robotics competition. Keeping semantic relevance and deterministic eligibility separate prevents the recommendation engine from confusing interest with permission.

### 4. Full-stack integration was the difficult part

The system required OCR handling, AI integration, database constraints, authentication, API contracts, and frontend/backend synchronization. The project reinforced that production-oriented AI engineering is a systems problem, not just an API integration problem.

## What I built

- End-to-end admin-to-student notice personalization pipeline
- Multi-format notice ingestion
- OCR fallback for scanned documents and images
- Strands Agent workflow for notice understanding
- Structured notice metadata extraction
- Semantic embedding-based relevance matching
- Deterministic academic eligibility engine
- Personalized student feed
- Recommendation explanations
- Separate Admin and Student portals
- PostgreSQL-backed application architecture
- FastAPI backend and React + Vite frontend

## Go-To-Market strategy

Evorozen Apex specifically asks participants to explain how they will acquire users, and live deployment with verifiable user analytics is described as an additional advantage. citeturn390502view1

### Initial customer

Start with colleges and individual departments that already distribute large volumes of announcements through PDFs, WhatsApp groups, email, notice boards, and student portals.

### Wedge

Begin with a single department or batch rather than attempting an entire institution at once.

### User acquisition

- Partner with student clubs and department coordinators
- Run a pilot with one class or department
- Demonstrate reduced information overload and higher notice discovery
- Use student referrals to expand adoption across batches
- Publish the product journey publicly on LinkedIn / X as part of building in public

### Business model direction

A potential model is **institution-paid SaaS** with department or campus-level plans, keeping the student-facing experience free to maximize adoption.

### Metrics to track

The product should measure:

- Registered students
- Weekly active students
- Notices published
- Notices opened
- Recommendation click-through rate
- Notice acknowledgement rate
- Notification-to-action conversion
- Student preference completion rate
- Department / campus retention

These metrics can provide the evidence needed for a more credible GTM story and future product decisions.

## Evorozen Neural Pulse integration

Evorozen describes the **Neural Pulse API** as a virtual database / intelligence layer and states that integrating it into the core architecture can receive bonus consideration. The current Devpost overview presents this as a bonus rather than a baseline requirement. citeturn390502view1turn390502view0

If Neural Pulse is integrated, it should have a meaningful architectural role rather than being added only for judging optics. A sensible integration would be around persistent intelligent application data, memory, retrieval, or routing where it genuinely reduces complexity or improves the product.

If the submitted version does **not** use Neural Pulse, the README and pitch should clearly explain the existing AI architecture and why the selected AI components are appropriate for their specific responsibilities.

## Evorozen Apex submission checklist

- [ ] Project is AI-driven
- [ ] Repository initialization / first project work is on or after **July 17, 2026**
- [ ] Live application is deployed
- [ ] GTM / user acquisition strategy is documented
- [ ] Real user or analytics evidence collected, where available
- [ ] Clean and modern UI/UX
- [ ] Detailed GitHub README
- [ ] AI architecture documented
- [ ] Setup instructions documented
- [ ] 3-minute maximum video prepared
- [ ] Video demonstrates the live product and AI integration
- [ ] Video explains the core problem and GTM strategy
- [ ] Optional public proof-of-work posts completed
- [ ] Evorozen Neural Pulse integration evaluated for genuine product value
- [ ] AI assistance disclosure included, where applicable

Evorozen's current Devpost page lists the fresh-codebase, GTM, UI/UX, documentation, and maximum-three-minute video requirements; the rules also specify that the project must begin on or after July 17, 2026. citeturn390502view0turn390502view1

## 3-minute demo structure

### 0:00–0:25 — Problem

Show the problem immediately:

> Colleges publish dozens of announcements, but relevance is different for every student. CampusNotice.AI turns that information overload into a personalized feed.

### 0:25–1:10 — Admin workflow

- Log in as admin
- Upload an actual notice
- Show extraction / OCR
- Show structured metadata produced by the agent

### 1:10–2:00 — Personalization

- Switch to a student account
- Show student interests
- Show the personalized notice
- Show why it was recommended
- Show how eligibility affects routing

### 2:00–2:30 — Architecture

Explain the core principle:

> AI interprets the notice. Deterministic logic decides eligibility and routing.

Briefly show the FastAPI backend, agent workflow, embeddings, PostgreSQL, and frontend.

### 2:30–3:00 — Business + GTM

Explain the initial college/department pilot, user acquisition path, and the metrics being tracked. Finish with the product vision:

> **AI understands. Students discover. Nothing gets missed.**

## Devpost project description draft

> **CampusNotice.AI** solves the problem of campus notice overload: students receive a constant stream of announcements, but only a fraction are relevant to them. Important opportunities such as workshops, hackathons, placement drives, competitions, and academic events can easily get buried.
>
> CampusNotice.AI turns generic announcements into personalized student feeds. An administrator can upload a PDF, image, TXT file, or raw text notice. The system extracts the content, uses OCR when necessary, and sends the unstructured notice through a Strands Agent that converts it into structured metadata such as title, category, summary, eligibility, deadline, required action, and importance.
>
> Students describe their interests in natural language instead of selecting a long list of predefined tags. Semantic embeddings are then used to match notices with student interests. However, the AI does not make the final eligibility decision. Academic eligibility and routing are handled by deterministic business logic, keeping critical institutional decisions predictable, testable, and explainable.
>
> The result is a full-stack AI product that combines document processing, OCR, agent-based understanding, semantic matching, rule-based eligibility, PostgreSQL, FastAPI, and React into one end-to-end workflow.
>
> **Our core principle: AI understands. Deterministic logic decides.**
>
> The initial Go-To-Market strategy is to pilot the platform with individual college departments, measure engagement and acknowledgement rates, and expand to additional departments and campuses based on demonstrated usage.

## What I learned

Building CampusNotice.AI made the distinction between **an AI feature** and **an AI product** much clearer.

The AI model is only one component. The actual engineering work is in deciding where AI belongs, building reliable data flows around it, enforcing deterministic rules where required, exposing the system through APIs, and creating a product that real users can understand and use.

For Evorozen Apex specifically, the next step is not simply adding more AI. It is making the system more measurable, deployable, scalable, and useful to a real campus audience.

## What's next for CampusNotice.AI

- Cloud deployment and production-grade authentication
- Real user pilots with colleges / departments
- Email and push notifications
- Admin analytics and notice acknowledgement tracking
- Department-level administration
- Multi-campus architecture
- Production-ready vector storage architecture
- Stronger observability and usage analytics
- Evaluation datasets and automated tests for notice extraction and relevance
- Potential Neural Pulse integration where it provides genuine architectural value

**AI understands. Students discover. Nothing gets missed.**

## Participation

Submitted to **Evorozen Apex: NextGen AI Buildathon** as an **individual project** by the project author.
