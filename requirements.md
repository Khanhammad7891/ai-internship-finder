# Requirements Document
## AI-Based Reality-Driven Internship Matching and Skill Gap Analysis System

**Project Name:** AI Internship Finder  
**Team:** The_laxy-coderxx  
**Team Lead:** Khan Hammad Raza  
**Version:** 1.0  
**Last Updated:** February 12, 2026

---

## 1. Problem Statement

### 1.1 Real-World Student Pain Points

Indian students face a broken internship discovery process characterized by:

- **High Rejection Rates:** 85%+ of applications result in rejection due to skill-experience mismatch
- **Wasted Effort:** Students apply to 50-100 internships with <5% response rate
- **Unrealistic Self-Assessment:** 70% of applicants overestimate their eligibility for posted roles
- **Opaque Feedback:** Zero actionable guidance on why applications fail or how to improve
- **Time Drain:** 40+ hours spent per month on unsuitable applications

### 1.2 Internship Ecosystem Challenges (India-Specific)

- **Inflated Job Descriptions:** Companies post "internship" roles requiring 2-3 years experience
- **Aspirational Listings:** Positions demand full-stack expertise for basic frontend tasks
- **Tier-2/Tier-3 Disadvantage:** Students lack mentorship to decode realistic vs. unrealistic requirements
- **Skill Vocabulary Gap:** Students use different terminology than industry (e.g., "made website" vs. "React.js")
- **No Reality Check Mechanism:** Existing platforms show all internships regardless of feasibility

### 1.3 Core Problem

Students cannot distinguish between internships they can realistically secure versus those beyond their current capability, leading to demotivation, wasted effort, and delayed career progression.

---

## 2. Objectives

### 2.1 Primary Goals

1. **Reality-Based Matching:** Show only internships where student has ≥60% skill readiness
2. **Skill Gap Transparency:** Quantify exact deficiencies preventing eligibility for desired roles
3. **Actionable Improvement:** Generate concrete 30-day learning plans to bridge gaps
4. **JD Realism Detection:** Flag inflated internship descriptions to protect student time

### 2.2 Secondary Goals

1. Enable students to track skill progression over time
2. Build confidence through achievable milestone targeting
3. Reduce average application-to-interview ratio from 50:1 to 10:1
4. Provide explainable AI outputs for trust and learning

### 2.3 Measurable Success Criteria

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| Resume parsing accuracy | ≥90% | Manual validation on 100 test resumes |
| Skill extraction precision | ≥85% | F1-score against labeled dataset |
| Matching relevance | ≥75% | User feedback on top 10 recommendations |
| JD inflation detection | ≥80% | Accuracy on manually labeled inflated JDs |
| Roadmap completion rate | ≥40% | 30-day follow-up survey |
| User satisfaction (NPS) | ≥50 | Post-demo survey |

---

## 3. Functional Requirements

### 3.1 Resume Upload & Parsing

**FR-1.1:** System shall accept PDF and DOCX resume formats (max 5MB)  
**FR-1.2:** Extract structured data: name, education, skills, projects, experience, certifications  
**FR-1.3:** Handle Indian resume formats (10th/12th marks, CGPA, regional language names)  
**FR-1.4:** Parse unstructured project descriptions and infer technical skills  
**FR-1.5:** Return parsing confidence score (0-100) with low-confidence field flagging

### 3.2 Skill Extraction & Normalization

**FR-2.1:** Identify technical skills from resume text using NLP  
**FR-2.2:** Normalize skill variants (e.g., "JS" → "JavaScript", "ML" → "Machine Learning")  
**FR-2.3:** Categorize skills into: Programming Languages, Frameworks, Tools, Soft Skills, Domain Knowledge  
**FR-2.4:** Assign proficiency levels: Beginner (mentioned), Intermediate (project use), Advanced (production/certification)  
**FR-2.5:** Detect skill inflation indicators (e.g., "expert in 15 technologies" with 6-month experience)

### 3.3 Skill Readiness Score Calculation

**FR-3.1:** Calculate score (0-100) for each internship based on:
- Required skills match (50% weight)
- Proficiency level alignment (30% weight)
- Experience duration match (10% weight)
- Project relevance (10% weight)

**FR-3.2:** Display score breakdown with per-skill contribution  
**FR-3.3:** Flag critical missing skills (deal-breakers) vs. nice-to-have gaps  
**FR-3.4:** Provide confidence interval (e.g., 65% ± 8%)

### 3.4 Internship JD Realism Detection

**FR-4.1:** Analyze job descriptions for inflation signals:
- Experience requirements >1 year for "internship"
- Skill count >12 for entry-level role
- Contradictory requirements (e.g., "fresher" + "3 years experience")
- Unrealistic tech stack breadth (full-stack + DevOps + ML)

**FR-4.2:** Assign realism score: Realistic (80-100), Moderate (50-79), Inflated (0-49)  
**FR-4.3:** Display warning badges on inflated listings  
**FR-4.4:** Provide explanation for inflation classification

### 3.5 Matching Algorithm

**FR-5.1:** Filter internships where student's Skill Readiness Score ≥60%  
**FR-5.2:** Rank results by: Readiness Score (60%) + Location Match (20%) + Stipend (10%) + Company Rating (10%)  
**FR-5.3:** Support filters: Location, Stipend Range, Duration, Domain, Work Mode  
**FR-5.4:** Show "Stretch Opportunities" (55-59% readiness) in separate section with gap analysis  
**FR-5.5:** Exclude internships with Realism Score <50

### 3.6 Skill Gap Visualization

**FR-6.1:** Display radar chart comparing student skills vs. target internship requirements  
**FR-6.2:** List missing skills with priority ranking (critical → nice-to-have)  
**FR-6.3:** Show proficiency gaps for partially matched skills  
**FR-6.4:** Estimate time-to-readiness for each gap (hours/days)  
**FR-6.5:** Compare student profile against "typical accepted candidate" benchmark

### 3.7 30-Day Learning Roadmap Generation

**FR-7.1:** Generate personalized plan addressing top 3-5 skill gaps  
**FR-7.2:** Structure roadmap: Week 1-4 milestones with daily tasks  
**FR-7.3:** Recommend free resources: YouTube tutorials, documentation, practice platforms  
**FR-7.4:** Include mini-projects to demonstrate skill acquisition  
**FR-7.5:** Provide progress tracking checklist  
**FR-7.6:** Estimate daily time commitment (1-3 hours)  
**FR-7.7:** Prioritize India-accessible resources (no paywalls, regional language options)

### 3.8 Dashboard & Reporting

**FR-8.1:** Student dashboard showing:
- Current skill inventory
- Readiness scores for saved internships
- Roadmap progress tracker
- Application success rate

**FR-8.2:** Internship detail page with:
- Readiness score + breakdown
- Gap analysis
- Similar internships (higher readiness)
- Company insights

**FR-8.3:** Export reports: Resume gap analysis (PDF), Roadmap (PDF), Skill inventory (CSV)  
**FR-8.4:** Email digest: Weekly matched internships + progress reminders

---

## 4. Non-Functional Requirements

### 4.1 Performance

**NFR-1.1:** Resume parsing: <5 seconds for 2-page resume  
**NFR-1.2:** Skill matching: <3 seconds for 100 internship comparisons  
**NFR-1.3:** Roadmap generation: <8 seconds  
**NFR-1.4:** Dashboard load time: <2 seconds  
**NFR-1.5:** Support 50 concurrent users (hackathon demo target)

### 4.2 Scalability

**NFR-2.1:** Architecture supports horizontal scaling to 10,000 users  
**NFR-2.2:** Database design handles 100,000 internship listings  
**NFR-2.3:** Caching strategy for repeated skill matching queries  
**NFR-2.4:** Async processing for batch resume analysis

### 4.3 Security & Data Privacy

**NFR-3.1:** Encrypt resume files at rest (AES-256)  
**NFR-3.2:** No storage of personally identifiable information without consent  
**NFR-3.3:** GDPR-compliant data deletion on user request  
**NFR-3.4:** Secure API endpoints (authentication required)  
**NFR-3.5:** No sharing of student data with third parties

### 4.4 Fairness & Bias Mitigation

**NFR-4.1:** Skill scoring independent of: College name, gender, location, age  
**NFR-4.2:** Validate algorithm against protected class bias (caste, religion, gender)  
**NFR-4.3:** Transparent scoring criteria (no black-box decisions)  
**NFR-4.4:** Regular bias audits on matching outcomes

### 4.5 Explainability

**NFR-5.1:** Every score must display calculation logic  
**NFR-5.2:** Roadmap recommendations cite reasoning (e.g., "React.js needed for 80% of target roles")  
**NFR-5.3:** JD inflation flags show specific problematic phrases  
**NFR-5.4:** Matching algorithm uses interpretable features (no deep learning black boxes for core logic)

### 4.6 Usability

**NFR-6.1:** Mobile-responsive design (60% of users on mobile)  
**NFR-6.2:** Hindi language support for UI (optional for MVP)  
**NFR-6.3:** Accessible to screen readers (WCAG 2.1 Level A minimum)  
**NFR-6.4:** Onboarding flow <3 minutes

### 4.7 Reliability

**NFR-7.1:** 99% uptime during demo period  
**NFR-7.2:** Graceful degradation if AI service fails (fallback to keyword matching)  
**NFR-7.3:** Error messages in plain language with recovery steps

---

## 5. User Personas

### Persona 1: Tier-2/Tier-3 College Student

**Name:** Priya Sharma  
**Age:** 21  
**Education:** B.Tech CSE, 3rd year, Tier-3 college (CGPA 7.2)  
**Skills:** C++, Python basics, HTML/CSS, 2 college projects  
**Pain Points:**
- Applies to 60+ internships monthly, gets 2-3 responses
- Doesn't know if she's "good enough" for roles
- Overwhelmed by job descriptions asking for "full-stack + cloud + AI"
- No mentorship on skill prioritization

**Goals:**
- Find 5-10 realistic internships she can actually get
- Understand exactly what skills she's missing
- Get a clear plan to improve in 1 month

**Tech Access:** Android phone (primary), occasional laptop access, 4G internet

---

### Persona 2: Final-Year Diploma Student

**Name:** Arjun Patel  
**Age:** 20  
**Education:** Diploma in Computer Engineering (Polytechnic)  
**Skills:** Java, MySQL, Android basics, 1 capstone project  
**Pain Points:**
- Feels inferior to B.Tech students
- Unsure if diploma qualifies for "engineering internships"
- Rejected without feedback 40+ times
- Doesn't know how to compete with degree holders

**Goals:**
- Find internships that accept diploma students
- Prove skills through projects, not just degree
- Build confidence with achievable targets

**Tech Access:** Budget smartphone, limited laptop access, Hindi-preferred

---

### Persona 3: Early Career Job Seeker (Career Switcher)

**Name:** Rahul Verma  
**Age:** 24  
**Education:** B.Com graduate, self-learning programming  
**Skills:** JavaScript, React (online courses), 3 personal projects  
**Pain Points:**
- Non-CS background creates doubt
- Doesn't know if self-taught skills "count"
- Applies to both internships and junior roles, confused about fit
- Needs validation that he's job-ready

**Goals:**
- Objective assessment of skill level
- Internships that value self-taught developers
- Roadmap to fill formal education gaps

**Tech Access:** Laptop, good internet, English-fluent

---

## 6. System Constraints

### 6.1 Hackathon MVP Scope

**C-1.1:** Development timeline: 24-48 hours  
**C-1.2:** Team size: 3-5 members  
**C-1.3:** Focus on core flow: Upload → Match → Roadmap  
**C-1.4:** Defer features: User authentication, payment, mobile app, email notifications  
**C-1.5:** Demo dataset: 50 sample resumes, 200 internship JDs

### 6.2 API Usage Limits

**C-2.1:** OpenAI API: 10,000 tokens/day (free tier) → Optimize prompts  
**C-2.2:** Resume parsing API: 100 requests/day (if using third-party)  
**C-2.3:** Internship data: Scrape/mock 200 listings (no live API integration for MVP)

### 6.3 Dataset Limitations

**C-3.1:** Training data: Manually labeled 100 resumes + 100 JDs for validation  
**C-3.2:** Skill taxonomy: Curated list of 150 common tech skills (not exhaustive)  
**C-3.3:** No real-time internship feed (static dataset for demo)  
**C-3.4:** Roadmap resources: Pre-curated list of 50 learning resources

### 6.4 Technical Constraints

**C-4.1:** Deployment: Single cloud instance (AWS/GCP free tier)  
**C-4.2:** Database: PostgreSQL or MongoDB (free tier)  
**C-4.3:** Frontend: React.js (fast prototyping)  
**C-4.4:** Backend: Python Flask/FastAPI (AI integration ease)  
**C-4.5:** AI Model: GPT-3.5/4 or open-source LLM (Llama 2)

---

## 7. Acceptance Criteria

### 7.1 Resume Parsing Accuracy

**AC-1.1:** System correctly extracts name, education, skills from 90/100 test resumes  
**AC-1.2:** Skill extraction F1-score ≥0.85 on labeled dataset  
**AC-1.3:** Handles resumes with typos, formatting variations, regional languages (names)  
**AC-1.4:** Parsing fails gracefully with error message for corrupted files

### 7.2 Matching Precision

**AC-2.1:** Top 10 recommendations have ≥75% user-rated relevance (demo survey)  
**AC-2.2:** Zero internships shown with <60% readiness score (hard filter)  
**AC-2.3:** Readiness score variance <±10% on re-upload of same resume  
**AC-2.4:** Matching completes in <3 seconds for 200 internship dataset

### 7.3 JD Inflation Detection Accuracy

**AC-3.1:** Correctly flags 80/100 manually labeled inflated JDs  
**AC-3.2:** False positive rate <15% (realistic JDs wrongly flagged)  
**AC-3.3:** Explanation provided for every inflation flag  
**AC-3.4:** Realism score consistent across similar JDs (±5% variance)

### 7.4 Roadmap Relevance

**AC-4.1:** Roadmap addresses ≥80% of critical skill gaps identified  
**AC-4.2:** Recommended resources are accessible (free, India-available)  
**AC-4.3:** Time estimates within ±20% of actual learning time (user feedback)  
**AC-4.4:** 70% of demo users rate roadmap as "actionable" or "very actionable"

### 7.5 Explainability

**AC-5.1:** Every readiness score displays breakdown (skill-by-skill contribution)  
**AC-5.2:** Gap analysis lists specific missing skills with examples  
**AC-5.3:** JD inflation shows exact problematic phrases  
**AC-5.4:** 80% of users understand why they got their score (post-demo survey)

### 7.6 Performance

**AC-6.1:** 95% of operations complete within specified time limits (NFR 4.1)  
**AC-6.2:** System handles 20 concurrent demo users without crashes  
**AC-6.3:** Dashboard loads in <2 seconds on 4G mobile connection

---

## 8. Risks & Mitigation

### 8.1 AI Hallucination

**Risk:** LLM generates fake skills, incorrect gap analysis, or nonsensical roadmaps  
**Impact:** High - Destroys user trust, provides harmful guidance  
**Mitigation:**
- Use structured prompts with output format constraints
- Validate AI outputs against skill taxonomy whitelist
- Implement confidence thresholds (reject low-confidence outputs)
- Human-in-loop review for roadmap templates
- Fallback to rule-based matching if AI fails

### 8.2 Biased Scoring

**Risk:** Algorithm favors Tier-1 colleges, male candidates, or specific regions  
**Impact:** High - Ethical failure, perpetuates inequality  
**Mitigation:**
- Remove college name from scoring features
- Blind testing on diverse resume set (gender, location, college tier)
- Bias audit: Compare score distributions across demographics
- Transparent feature weights (no hidden bias proxies)
- User feedback mechanism to report unfair scores

### 8.3 Poor JD Quality

**Risk:** Scraped/mock internship data is outdated, fake, or low-quality  
**Impact:** Medium - Reduces matching relevance, wastes student time  
**Mitigation:**
- Manual curation of 200 demo JDs from reputable sources (Internshala, LinkedIn)
- Timestamp validation (only JDs <6 months old)
- Remove duplicate/spam listings
- Realism filter removes worst offenders
- Disclaimer: "Demo data - verify on company website"

### 8.4 Overfitting to Dataset

**Risk:** Model works on test data but fails on real-world resumes  
**Impact:** High - System unusable post-demo  
**Mitigation:**
- Diverse training set (Tier-1/2/3 colleges, CS/non-CS, diploma/degree)
- Cross-validation on held-out test set
- Collect real user resumes during demo for validation
- Design rules-based fallbacks (keyword matching)
- Plan post-hackathon retraining with production data

### 8.5 Skill Taxonomy Incompleteness

**Risk:** System misses emerging skills (e.g., new frameworks) or regional variations  
**Impact:** Medium - Undercounts student capabilities  
**Mitigation:**
- Include "Other Skills" free-text field
- Fuzzy matching for skill variants
- Regular taxonomy updates (monthly)
- User feedback: "Missing skill" report button
- LLM fallback for unknown skill classification

### 8.6 Roadmap Non-Completion

**Risk:** Students don't follow 30-day plan, rendering feature useless  
**Impact:** Medium - Reduces product value, but doesn't break core matching  
**Mitigation:**
- Keep roadmap realistic (1-2 hours/day max)
- Gamification: Progress badges, streak tracking
- Weekly email reminders (post-MVP)
- Break into micro-milestones (daily wins)
- Offer "Quick Win" 7-day alternative roadmap

### 8.7 Technical Failures During Demo

**Risk:** API limits hit, server crashes, parsing errors on judge's resume  
**Impact:** Critical - Demo failure = disqualification  
**Mitigation:**
- Pre-cache all demo data (no live API calls)
- Offline mode with mock responses
- Test with 20 diverse resumes before demo
- Backup deployment on second cloud provider
- Graceful error handling with friendly messages
- Have video recording of working system as backup

### 8.8 Scope Creep

**Risk:** Team adds features (chat, mobile app, payments) and misses deadline  
**Impact:** Critical - Incomplete MVP  
**Mitigation:**
- Strict feature freeze after hour 12
- MVP checklist: Upload → Parse → Match → Roadmap (nothing else)
- Defer all "nice-to-haves" to post-hackathon backlog
- Time-boxed development: 40% backend, 30% AI, 30% frontend

---

## 9. Success Metrics (Post-Demo)

| Metric | Target | Timeline |
|--------|--------|----------|
| Demo user signups | 100+ | During hackathon |
| Resume uploads | 50+ | Demo day |
| Average readiness score | 55-70% | Baseline |
| User satisfaction (NPS) | ≥50 | Post-demo survey |
| Roadmap download rate | ≥60% | Demo day |
| Judge feedback score | ≥8/10 | Hackathon results |

---

## 10. Out of Scope (Post-MVP Features)

- User authentication & profiles
- Real-time internship scraping
- Application tracking
- Company reviews & ratings
- Peer comparison / leaderboards
- Resume builder / editor
- Interview preparation
- Salary negotiation guidance
- Mobile native app
- Payment / premium features
- Recruiter dashboard
- API for third-party integration

---

## Appendix A: Technical Stack (Recommended)

**Frontend:** React.js + Tailwind CSS  
**Backend:** Python FastAPI  
**Database:** PostgreSQL  
**AI/ML:** OpenAI GPT-4 API / Llama 2  
**Resume Parsing:** PyMuPDF + spaCy NLP  
**Deployment:** Vercel (frontend) + Railway (backend)  
**Version Control:** GitHub  

---

## Appendix B: Key Algorithms

### Skill Readiness Score Formula

```
Readiness Score = (
  0.50 × Skill Match Ratio +
  0.30 × Proficiency Alignment +
  0.10 × Experience Match +
  0.10 × Project Relevance
) × 100

Where:
- Skill Match Ratio = (Matched Skills / Required Skills)
- Proficiency Alignment = Avg(Student Proficiency / Required Proficiency) per skill
- Experience Match = min(Student Months / Required Months, 1.0)
- Project Relevance = Jaccard Similarity(Student Projects, JD Keywords)
```

### JD Inflation Detection Signals

- Experience >12 months for "internship" → +30 inflation points
- Skill count >12 → +20 inflation points
- Contradictory terms ("fresher" + "experienced") → +25 points
- Unrealistic stack (>4 domains) → +15 points
- Vague requirements ("rockstar", "ninja") → +10 points

**Inflation Score = 100 - Total Penalty Points**

---

**Document Status:** Draft v1.0  
**Next Review:** Post-hackathon (for production roadmap)  
**Approval Required:** Team Lead, Technical Mentor

---

*End of Requirements Document*
