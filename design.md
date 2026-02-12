# System Design Document
## AI-Based Reality-Driven Internship Matching and Skill Gap Analysis System

**Project Name:** AI Internship Finder  
**Team:** The_laxy-coderxx  
**Version:** 1.0  
**Last Updated:** February 12, 2026  
**Document Type:** Technical Architecture & Design Specification

---

## 1. System Overview

### 1.1 Purpose

The AI Internship Finder is a reality-driven matching platform that analyzes student resumes, calculates skill readiness scores, filters inflated job descriptions, and generates personalized learning roadmaps. Unlike traditional job boards that show all opportunities, this system applies AI-powered realism filters to display only internships where students have ≥60% probability of success.

### 1.2 High-Level Architecture

The system follows a microservices-oriented architecture with six core layers:

1. **Presentation Layer:** React-based SPA with responsive UI
2. **API Gateway:** FastAPI REST endpoints with request validation
3. **AI Processing Layer:** NLP-based resume parsing and skill extraction
4. **Matching Engine:** Vectorized similarity computation and scoring
5. **Data Layer:** PostgreSQL for structured data, Redis for caching
6. **External Services:** OpenAI API for LLM-based reasoning

### 1.3 Core Workflow

```
Student uploads resume → AI extracts skills → System normalizes skill taxonomy →
Fetches internship JDs → Detects inflated descriptions → Calculates readiness scores →
Filters matches (≥60%) → Identifies skill gaps → Generates 30-day roadmap →
Displays ranked results with explanations
```

### 1.4 Key Design Principles

- **Explainability First:** Every AI decision must be traceable and human-readable
- **Fail-Safe Defaults:** Rule-based fallbacks when AI models fail
- **Performance Targets:** <5s end-to-end latency for matching
- **Bias Mitigation:** College-agnostic, demographic-blind scoring
- **India-Optimized:** Regional language support, local resource recommendations

---

## 2. Architecture Diagram


```
┌─────────────────────────────────────────────────────────────────────────┐
│                            USER INTERFACE LAYER                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐ │
│  │   Upload     │  │  Dashboard   │  │  Internship  │  │   Roadmap   │ │
│  │   Resume     │  │   (Scores)   │  │   Browser    │  │   Viewer    │ │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬──────┘ │
└─────────┼──────────────────┼──────────────────┼──────────────────┼───────┘
          │                  │                  │                  │
          └──────────────────┴──────────────────┴──────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          API GATEWAY (FastAPI)                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐ │
│  │ POST /resume │  │ GET /matches │  │ GET /roadmap │  │ GET /skills │ │
│  │   /upload    │  │              │  │              │  │             │ │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬──────┘ │
└─────────┼──────────────────┼──────────────────┼──────────────────┼───────┘
          │                  │                  │                  │
          ▼                  ▼                  ▼                  ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         AI PROCESSING LAYER                              │
│  ┌──────────────────┐  ┌──────────────────┐  ┌────────────────────────┐│
│  │  Resume Parser   │  │  JD Analyzer     │  │  Roadmap Generator     ││
│  │  (NLP + LLM)     │  │  (Inflation Det.)│  │  (LLM + Templates)     ││
│  └────────┬─────────┘  └────────┬─────────┘  └──────────┬─────────────┘│
└───────────┼──────────────────────┼────────────────────────┼──────────────┘
            │                      │                        │
            ▼                      ▼                        │
┌─────────────────────────────────────────────────────────┐│
│           SKILL NORMALIZATION ENGINE                     ││
│  ┌────────────────────────────────────────────────────┐ ││
│  │  Skill Taxonomy Mapper (spaCy + Custom Dictionary) │ ││
│  │  Input: ["JS", "reactjs", "ML"]                    │ ││
│  │  Output: ["JavaScript", "React.js", "Machine       │ ││
│  │           Learning"]                                │ ││
│  └────────────────────┬───────────────────────────────┘ ││
└───────────────────────┼─────────────────────────────────┘│
                        │                                   │
                        ▼                                   │
┌─────────────────────────────────────────────────────────┐│
│              MATCHING ENGINE                             ││
│  ┌────────────────────────────────────────────────────┐ ││
│  │  Vectorization: Sentence-BERT Embeddings           │ ││
│  │  Similarity: Cosine + Weighted Jaccard             │ ││
│  │  Scoring: Multi-factor formula (0-100)             │ ││
│  │  Filtering: Readiness ≥60%, Realism ≥50%           │ ││
│  └────────────────────┬───────────────────────────────┘ ││
└───────────────────────┼─────────────────────────────────┘│
                        │                                   │
                        ▼                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          DATA PERSISTENCE LAYER                          │
│  ┌──────────────────┐  ┌──────────────────┐  ┌────────────────────────┐│
│  │   PostgreSQL     │  │   Redis Cache    │  │   S3 / File Storage    ││
│  │   (Structured)   │  │   (Sessions)     │  │   (Resume PDFs)        ││
│  │                  │  │                  │  │                        ││
│  │ • Resumes        │  │ • Skill vectors  │  │ • Encrypted uploads    ││
│  │ • Internships    │  │ • Match results  │  │ • Temp processing      ││
│  │ • Skills         │  │ • User sessions  │  │                        ││
│  │ • Roadmaps       │  │                  │  │                        ││
│  └──────────────────┘  └──────────────────┘  └────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        EXTERNAL SERVICES                                 │
│  ┌──────────────────┐  ┌──────────────────┐  ┌────────────────────────┐│
│  │   OpenAI API     │  │   Internship     │  │   Learning Resource    ││
│  │   (GPT-4)        │  │   Data Sources   │  │   APIs (YouTube, etc.) ││
│  └──────────────────┘  └──────────────────┘  └────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Component Design

### 3.1 Resume Parser Module

**Purpose:** Extract structured data from unstructured resume documents

**Input:**
- File: PDF/DOCX (max 5MB)
- Metadata: Upload timestamp, user ID

**Processing Pipeline:**

1. **Document Extraction**
   - Library: PyMuPDF (PDF), python-docx (DOCX)
   - Output: Raw text with layout preservation

2. **Section Segmentation**
   - Method: Regex patterns + heuristic rules
   - Sections: Personal Info, Education, Skills, Experience, Projects, Certifications
   - Fallback: LLM-based segmentation if rules fail

3. **Entity Extraction**
   - NLP Model: spaCy (en_core_web_lg)
   - Entities: Name (PERSON), Education (ORG + DATE), Skills (custom NER)
   - Custom Training: Fine-tuned on 500 Indian resumes

4. **Skill Identification**
   - Primary: Keyword matching against skill taxonomy (150 terms)
   - Secondary: LLM extraction for implicit skills in project descriptions
   - Prompt: "Extract technical skills from: {project_text}. Return JSON array."

**Output:**
```json
{
  "name": "Priya Sharma",
  "education": [{"degree": "B.Tech CSE", "year": 2025, "cgpa": 7.2}],
  "skills": ["Python", "HTML", "CSS", "C++"],
  "experience_months": 0,
  "projects": [{"title": "E-commerce Website", "tech": ["HTML", "CSS", "JavaScript"]}],
  "parsing_confidence": 0.92
}
```

**AI/ML Techniques:**
- Named Entity Recognition (spaCy)
- GPT-4 for ambiguous project descriptions
- Confidence scoring via ensemble agreement

**Error Handling:**
- Corrupted file → Return 400 with "File unreadable"
- Low confidence (<0.7) → Flag fields for manual review
- Timeout (>10s) → Abort and suggest re-upload

---

### 3.2 Skill Normalization Engine

**Purpose:** Map skill variants to canonical taxonomy

**Input:**
- Raw skills: ["JS", "reactjs", "ML", "machine learning"]

**Processing:**

1. **Lowercase & Trim:** "JS" → "js"

2. **Synonym Mapping:**
   - Dictionary: `{"js": "JavaScript", "reactjs": "React.js", "ml": "Machine Learning"}`
   - Source: Manually curated 300-entry mapping

3. **Fuzzy Matching:**
   - Library: RapidFuzz (Levenshtein distance)
   - Threshold: 85% similarity
   - Example: "Reactjs" → "React.js" (90% match)

4. **Category Assignment:**
   - Categories: Programming Language, Framework, Tool, Soft Skill, Domain
   - Method: Predefined taxonomy tree

5. **Proficiency Inference:**
   - Beginner: Skill mentioned, no project usage
   - Intermediate: Used in 1-2 projects
   - Advanced: Used in 3+ projects OR certification

**Output:**
```json
[
  {"skill": "JavaScript", "category": "Programming Language", "proficiency": "Intermediate"},
  {"skill": "React.js", "category": "Framework", "proficiency": "Beginner"},
  {"skill": "Machine Learning", "category": "Domain", "proficiency": "Beginner"}
]
```

**AI/ML Techniques:**
- Fuzzy string matching (Levenshtein)
- Rule-based proficiency heuristics

---

### 3.3 Internship JD Analyzer

**Purpose:** Parse job descriptions and extract requirements

**Input:**
- JD text (raw HTML or plain text)
- Metadata: Company, location, stipend, duration

**Processing:**

1. **Text Cleaning:**
   - Remove HTML tags, special characters
   - Normalize whitespace

2. **Section Extraction:**
   - Sections: Responsibilities, Requirements, Qualifications, Nice-to-Have
   - Method: Keyword anchors ("Required Skills:", "Qualifications:")

3. **Skill Extraction:**
   - Primary: Regex for common patterns ("Experience in X", "Proficiency in Y")
   - Secondary: NER for technical terms
   - LLM Fallback: "Extract required skills from: {jd_text}"

4. **Experience Parsing:**
   - Regex: "(\d+)\s*(year|month|yr|mo)" → Extract numeric value
   - Normalize to months

5. **Requirement Classification:**
   - Critical: "Must have", "Required", "Essential"
   - Preferred: "Nice to have", "Bonus", "Plus"

**Output:**
```json
{
  "required_skills": [
    {"skill": "Python", "proficiency": "Intermediate", "critical": true},
    {"skill": "Django", "proficiency": "Beginner", "critical": true},
    {"skill": "AWS", "proficiency": "Beginner", "critical": false}
  ],
  "experience_months": 6,
  "education": "B.Tech/B.E. in CS",
  "extracted_confidence": 0.88
}
```

**AI/ML Techniques:**
- NER (spaCy)
- GPT-4 for complex JDs
- Regex for structured patterns

---

### 3.4 Inflation Detection Model

**Purpose:** Identify unrealistic internship requirements

**Input:**
- Parsed JD requirements
- Role type: "Internship"

**Processing Logic:**


**Inflation Signals (Penalty Points):**

| Signal | Penalty | Logic |
|--------|---------|-------|
| Experience >12 months for "internship" | 30 | Internships should be entry-level |
| Skill count >12 | 20 | Unrealistic breadth for interns |
| Contradictory terms ("fresher" + "2 years exp") | 25 | Logical inconsistency |
| Multi-domain stack (Frontend + Backend + ML + DevOps) | 15 | Unrealistic for single role |
| Vague buzzwords ("rockstar", "ninja", "guru") | 10 | Unprofessional language |
| Proficiency mismatch (all skills "expert" level) | 20 | Unrealistic for interns |

**Scoring Formula:**
```
Realism Score = 100 - Total Penalty Points
Realism Score = max(0, Realism Score)

Classification:
- Realistic: 80-100
- Moderate: 50-79
- Inflated: 0-49
```

**Explainability:**
- Return list of triggered signals with explanations
- Example: "Experience requirement (18 months) exceeds typical internship duration"

**Output:**
```json
{
  "realism_score": 45,
  "classification": "Inflated",
  "signals": [
    {"type": "experience_mismatch", "penalty": 30, "detail": "Requires 18 months for internship"},
    {"type": "skill_overload", "penalty": 20, "detail": "Lists 15 required skills"},
    {"type": "vague_language", "penalty": 10, "detail": "Uses term 'rockstar developer'"}
  ]
}
```

**AI/ML Techniques:**
- Rule-based heuristics (primary)
- LLM validation for edge cases
- Threshold tuning via labeled dataset

---

### 3.5 Matching Algorithm Engine

**Purpose:** Calculate skill readiness score and rank internships

**Input:**
- Student profile: Normalized skills with proficiency
- Internship requirements: Required skills with proficiency
- Metadata: Experience, projects, education

**Processing Pipeline:**

**Step 1: Skill Vectorization**
- Model: Sentence-BERT (all-MiniLM-L6-v2)
- Embedding dimension: 384
- Input: Skill names + context (e.g., "Python for web development")
- Output: Dense vectors for similarity computation

**Step 2: Skill Match Ratio**
```python
matched_skills = set(student_skills) ∩ set(required_skills)
skill_match_ratio = len(matched_skills) / len(required_skills)
```

**Step 3: Proficiency Alignment**
```python
proficiency_map = {"Beginner": 1, "Intermediate": 2, "Advanced": 3}

alignment_scores = []
for skill in matched_skills:
    student_prof = proficiency_map[student.proficiency[skill]]
    required_prof = proficiency_map[jd.proficiency[skill]]
    alignment = min(student_prof / required_prof, 1.0)
    alignment_scores.append(alignment)

proficiency_alignment = mean(alignment_scores)
```

**Step 4: Experience Match**
```python
experience_match = min(student.experience_months / jd.experience_months, 1.0)
if jd.experience_months == 0:
    experience_match = 1.0  # No experience required
```

**Step 5: Project Relevance**
```python
# Jaccard similarity between project keywords and JD keywords
project_keywords = extract_keywords(student.projects)
jd_keywords = extract_keywords(jd.description)

project_relevance = len(project_keywords ∩ jd_keywords) / len(project_keywords ∪ jd_keywords)
```

**Step 6: Final Score Calculation**
```python
readiness_score = (
    0.50 * skill_match_ratio +
    0.30 * proficiency_alignment +
    0.10 * experience_match +
    0.10 * project_relevance
) * 100

# Apply realism penalty
if jd.realism_score < 50:
    readiness_score = 0  # Exclude inflated JDs

# Round to integer
readiness_score = round(readiness_score)
```

**Step 7: Ranking**
```python
# Multi-factor ranking
rank_score = (
    0.60 * readiness_score +
    0.20 * location_match_bonus +  # +10 if same city
    0.10 * stipend_normalized +     # 0-10 scale
    0.10 * company_rating           # 0-10 scale
)
```

**Output:**
```json
{
  "internship_id": "INT-12345",
  "readiness_score": 72,
  "rank_score": 68.5,
  "breakdown": {
    "skill_match_ratio": 0.83,
    "proficiency_alignment": 0.75,
    "experience_match": 1.0,
    "project_relevance": 0.40
  },
  "matched_skills": ["Python", "Django", "PostgreSQL"],
  "missing_skills": ["AWS", "Docker"],
  "explanation": "You match 5/6 required skills. Missing: AWS (critical), Docker (nice-to-have)."
}
```

**AI/ML Techniques:**
- Sentence-BERT embeddings for semantic similarity
- Weighted scoring formula (tuned on validation set)
- Cosine similarity for skill vectors

**Filtering Rules:**
- Exclude if readiness_score < 60
- Exclude if realism_score < 50
- Limit results to top 50 matches

---

### 3.6 Skill Gap Analyzer

**Purpose:** Identify missing skills and prioritize learning

**Input:**
- Student skills
- Target internship requirements
- Readiness score breakdown

**Processing:**

**Step 1: Gap Identification**
```python
missing_skills = set(jd.required_skills) - set(student.skills)
partial_skills = [s for s in matched_skills if student.proficiency[s] < jd.proficiency[s]]
```

**Step 2: Priority Ranking**
```python
# Priority score for each gap
for skill in missing_skills:
    priority = 0
    if skill.critical:
        priority += 50
    if skill.frequency_in_top_internships > 0.7:
        priority += 30
    if skill.learning_time_hours < 40:
        priority += 20
    
    skill.priority_score = priority

# Sort by priority descending
gaps_sorted = sorted(missing_skills, key=lambda x: x.priority_score, reverse=True)
```

**Step 3: Time Estimation**
```python
# Learning time lookup table (hours)
learning_time = {
    "React.js": 30,
    "Node.js": 25,
    "Docker": 15,
    "AWS": 40,
    "MongoDB": 20
}

for skill in gaps_sorted:
    skill.estimated_hours = learning_time.get(skill.name, 30)  # Default 30h
```

**Output:**
```json
{
  "critical_gaps": [
    {"skill": "React.js", "priority": 100, "estimated_hours": 30, "reason": "Required by 85% of target roles"}
  ],
  "nice_to_have_gaps": [
    {"skill": "Docker", "priority": 35, "estimated_hours": 15, "reason": "Bonus skill for DevOps roles"}
  ],
  "proficiency_gaps": [
    {"skill": "Python", "current": "Beginner", "required": "Intermediate", "estimated_hours": 20}
  ],
  "total_learning_hours": 65
}
```

**Visualization Data:**
```json
{
  "radar_chart": {
    "labels": ["Frontend", "Backend", "Database", "DevOps", "Soft Skills"],
    "student_scores": [60, 40, 70, 20, 80],
    "required_scores": [80, 70, 80, 60, 70]
  }
}
```

---

### 3.7 Roadmap Generator

**Purpose:** Create personalized 30-day learning plan

**Input:**
- Prioritized skill gaps
- Student's current proficiency
- Available time per day (default: 2 hours)

**Processing:**

**Step 1: Skill Selection**
```python
# Select top 3-5 gaps that fit in 30 days
selected_gaps = []
total_hours = 0
for gap in gaps_sorted:
    if total_hours + gap.estimated_hours <= 60:  # 30 days × 2h/day
        selected_gaps.append(gap)
        total_hours += gap.estimated_hours
```

**Step 2: Weekly Breakdown**
```python
# Allocate skills to weeks
week_1 = selected_gaps[0]  # Highest priority
week_2 = selected_gaps[1]
week_3 = selected_gaps[2]
week_4 = "Project integration"  # Apply learned skills
```

**Step 3: Resource Recommendation**
```python
# Resource database (curated)
resources = {
    "React.js": [
        {"type": "video", "title": "React in 30 Days", "url": "youtube.com/...", "duration": "10h", "free": true},
        {"type": "docs", "title": "Official React Docs", "url": "react.dev", "free": true},
        {"type": "practice", "title": "React Challenges", "url": "frontendmentor.io", "free": true}
    ]
}

for skill in selected_gaps:
    skill.resources = resources.get(skill.name, [])
```

**Step 4: LLM-Based Plan Generation**
```python
prompt = f"""
Generate a 30-day learning roadmap for:
Skills to learn: {selected_gaps}
Student level: {student.proficiency}
Daily time: 2 hours

Format:
Week 1: [Skill] - [Daily tasks]
Week 2: [Skill] - [Daily tasks]
Week 3: [Skill] - [Daily tasks]
Week 4: [Integration project]

Include:
- Specific daily goals
- Mini-milestones
- Practice exercises
- One capstone project
"""

roadmap_text = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[{"role": "user", "content": prompt}],
    temperature=0.7
)
```

**Output:**
```json
{
  "roadmap_id": "RM-67890",
  "duration_days": 30,
  "daily_hours": 2,
  "skills_covered": ["React.js", "Node.js", "MongoDB"],
  "weekly_plan": [
    {
      "week": 1,
      "skill": "React.js",
      "goals": ["Understand components", "Build 3 mini-apps", "Learn hooks"],
      "resources": [...],
      "daily_tasks": {
        "day_1": "Watch React basics (2h)",
        "day_2": "Build counter app (2h)",
        ...
      }
    }
  ],
  "capstone_project": {
    "title": "Build a Task Manager App",
    "description": "Full-stack app using React + Node + MongoDB",
    "estimated_hours": 15
  },
  "progress_checklist": [
    {"task": "Complete React tutorial", "completed": false},
    {"task": "Build 3 React components", "completed": false}
  ]
}
```

**AI/ML Techniques:**
- GPT-4 for natural language roadmap generation
- Template-based fallback if API fails
- Resource matching via keyword similarity

---

### 3.8 Dashboard Module

**Purpose:** Display results with visualizations

**Components:**

**3.8.1 Skill Inventory Card**
- Display: Student's skills grouped by category
- Visualization: Tag cloud with proficiency color coding
- Interaction: Click skill → Show related internships

**3.8.2 Readiness Score Display**
- Primary metric: Large circular progress bar (0-100)
- Breakdown: Horizontal bar chart for 4 factors
- Tooltip: Hover for detailed explanation

**3.8.3 Internship List**
- Sorting: Readiness score (default), Stipend, Location
- Filters: Domain, Work mode, Duration
- Card layout: Company logo, title, score badge, "View Details" button

**3.8.4 Gap Analysis View**
- Radar chart: Student vs. Required skills
- Priority list: Critical gaps highlighted in red
- Time estimate: "65 hours to bridge gaps"

**3.8.5 Roadmap Timeline**
- Gantt-style weekly view
- Progress tracker: Checkboxes for daily tasks
- Resource links: Direct access to learning materials

**Data Refresh:**
- Real-time: Readiness scores (on filter change)
- Cached: Internship list (5-minute TTL)
- On-demand: Roadmap regeneration

---

## 4. Data Flow

### 4.1 End-to-End Pipeline

**Phase 1: Resume Upload (0-5 seconds)**

```
1. User uploads resume.pdf
   ↓
2. Frontend validates file (size, type)
   ↓
3. POST /api/resume/upload
   ↓
4. Backend saves to temp storage (S3)
   ↓
5. Resume Parser extracts text
   ↓
6. NLP model extracts entities
   ↓
7. LLM fills gaps (low-confidence fields)
   ↓
8. Return parsed JSON to frontend
```

**Phase 2: Skill Normalization (1-2 seconds)**

```
9. Receive raw skills: ["JS", "reactjs", "ML"]
   ↓
10. Skill Normalization Engine processes
   ↓
11. Map to canonical taxonomy
   ↓
12. Assign proficiency levels
   ↓
13. Store in PostgreSQL (resumes table)
   ↓
14. Cache skill vector in Redis
```

**Phase 3: Internship Matching (2-4 seconds)**

```
15. Fetch internship JDs from database (200 records)
   ↓
16. For each JD:
    a. Parse requirements (if not cached)
    b. Detect inflation
    c. Calculate readiness score
   ↓
17. Filter: readiness ≥60%, realism ≥50%
   ↓
18. Rank by composite score
   ↓
19. Return top 50 matches
```

**Phase 4: Gap Analysis (1 second)**

```
20. For selected internship:
    a. Identify missing skills
    b. Calculate priority scores
    c. Estimate learning time
   ↓
21. Generate visualization data
   ↓
22. Return gap analysis JSON
```

**Phase 5: Roadmap Generation (3-8 seconds)**

```
23. Select top 3-5 gaps
   ↓
24. Fetch learning resources from database
   ↓
25. Call GPT-4 API for roadmap text
   ↓
26. Parse and structure response
   ↓
27. Store roadmap in database
   ↓
28. Return roadmap JSON
```

### 4.2 Data Flow Diagram

```
┌─────────┐
│  User   │
└────┬────┘
     │ 1. Upload resume.pdf
     ▼
┌─────────────────┐
│   Frontend      │
│   (React)       │
└────┬────────────┘
     │ 2. POST /resume/upload
     ▼
┌─────────────────────────────────────┐
│   API Gateway (FastAPI)             │
│   - Validate request                │
│   - Route to Resume Parser          │
└────┬────────────────────────────────┘
     │ 3. Extract text
     ▼
┌─────────────────────────────────────┐
│   Resume Parser                     │
│   - PyMuPDF extracts text           │
│   - spaCy NER extracts entities     │
│   - GPT-4 fills gaps                │
└────┬────────────────────────────────┘
     │ 4. Raw skills
     ▼
┌─────────────────────────────────────┐
│   Skill Normalization Engine        │
│   - Map variants to taxonomy        │
│   - Assign proficiency              │
└────┬────────────────────────────────┘
     │ 5. Normalized skills
     ▼
┌─────────────────────────────────────┐
│   PostgreSQL                        │
│   - Store resume data               │
│   - Store skill vectors             │
└────┬────────────────────────────────┘
     │ 6. Fetch internships
     ▼
┌─────────────────────────────────────┐
│   Matching Engine                   │
│   - Calculate readiness scores      │
│   - Filter & rank                   │
└────┬────────────────────────────────┘
     │ 7. Top matches
     ▼
┌─────────────────────────────────────┐
│   Gap Analyzer                      │
│   - Identify missing skills         │
│   - Prioritize gaps                 │
└────┬────────────────────────────────┘
     │ 8. Gap data
     ▼
┌─────────────────────────────────────┐
│   Roadmap Generator                 │
│   - Select skills                   │
│   - GPT-4 generates plan            │
└────┬────────────────────────────────┘
     │ 9. Roadmap JSON
     ▼
┌─────────────────┐
│   Frontend      │
│   - Display     │
│   - Visualize   │
└─────────────────┘
```

---

## 5. AI/ML Design

### 5.1 NLP Models

**5.1.1 Resume Parsing**


**Model:** spaCy `en_core_web_lg` (685MB)
- **Task:** Named Entity Recognition (NER)
- **Entities:** PERSON, ORG, DATE, GPE (location)
- **Custom Training:** Fine-tuned on 500 Indian resumes for:
  - Indian college names (e.g., "IIT Bombay", "NIT Trichy")
  - Degree formats (e.g., "B.Tech CSE", "Diploma in IT")
  - Regional name patterns

**Training Process:**
```python
import spacy
from spacy.training import Example

nlp = spacy.load("en_core_web_lg")
ner = nlp.get_pipe("ner")

# Add custom labels
ner.add_label("SKILL")
ner.add_label("DEGREE")

# Training data format
TRAIN_DATA = [
    ("Completed B.Tech in Computer Science from IIT Delhi", 
     {"entities": [(10, 16, "DEGREE"), (17, 37, "DEGREE"), (43, 52, "ORG")]}),
    ...
]

# Train for 30 iterations
for i in range(30):
    for text, annotations in TRAIN_DATA:
        example = Example.from_dict(nlp.make_doc(text), annotations)
        nlp.update([example])
```

**5.1.2 Skill Extraction**

**Primary Method:** Keyword matching
- Dictionary: 150 curated technical skills
- Regex patterns: `r'\b(Python|Java|React\.js|Machine Learning)\b'`
- Case-insensitive matching

**Secondary Method:** LLM extraction
- Model: GPT-4 (gpt-4-turbo-preview)
- Prompt:
```
Extract technical skills from the following project description.
Return only a JSON array of skill names.

Project: "Built a web scraper using BeautifulSoup and Selenium to collect data from e-commerce sites. Stored data in MongoDB and visualized using Plotly."

Output format: ["Python", "BeautifulSoup", "Selenium", "MongoDB", "Plotly"]
```

**Confidence Scoring:**
```python
confidence = (
    0.6 * keyword_match_score +  # 1.0 if found in dictionary
    0.4 * llm_confidence          # GPT-4 logprobs
)
```

**5.1.3 Skill Similarity (Semantic Matching)**

**Model:** Sentence-BERT `all-MiniLM-L6-v2`
- **Embedding Dimension:** 384
- **Speed:** 2000 sentences/second on CPU
- **Use Case:** Match student skills to JD requirements semantically

**Example:**
```python
from sentence_transformers import SentenceTransformer, util

model = SentenceTransformer('all-MiniLM-L6-v2')

student_skills = ["JavaScript", "React.js", "Node.js"]
jd_requirements = ["Frontend development with React", "Backend API development"]

# Generate embeddings
student_embeddings = model.encode(student_skills)
jd_embeddings = model.encode(jd_requirements)

# Compute cosine similarity
similarity_matrix = util.cos_sim(student_embeddings, jd_embeddings)

# Threshold: 0.7 for match
matches = similarity_matrix > 0.7
```

### 5.2 Scoring Algorithms

**5.2.1 Skill Readiness Score**

**Formula:**
```
Readiness Score = (
    W1 × Skill_Match_Ratio +
    W2 × Proficiency_Alignment +
    W3 × Experience_Match +
    W4 × Project_Relevance
) × 100

Where:
W1 = 0.50  (Skill match is most important)
W2 = 0.30  (Proficiency matters)
W3 = 0.10  (Experience is secondary for internships)
W4 = 0.10  (Projects show practical application)
```

**Component Calculations:**

**Skill Match Ratio:**
```python
# Exact match
exact_matches = len(set(student_skills) & set(required_skills))
skill_match_ratio = exact_matches / len(required_skills)

# Semantic match (fallback)
if skill_match_ratio < 0.5:
    semantic_matches = count_semantic_matches(student_skills, required_skills, threshold=0.75)
    skill_match_ratio = max(skill_match_ratio, semantic_matches / len(required_skills))
```

**Proficiency Alignment:**
```python
proficiency_scores = {
    "Beginner": 1,
    "Intermediate": 2,
    "Advanced": 3
}

alignment_values = []
for skill in matched_skills:
    student_level = proficiency_scores[student.proficiency[skill]]
    required_level = proficiency_scores[jd.proficiency[skill]]
    
    # Ratio capped at 1.0 (no bonus for over-qualification)
    alignment = min(student_level / required_level, 1.0)
    
    # Weight by skill criticality
    weight = 1.5 if jd.critical[skill] else 1.0
    alignment_values.append(alignment * weight)

proficiency_alignment = sum(alignment_values) / len(alignment_values)
```

**Experience Match:**
```python
if jd.experience_months == 0:
    experience_match = 1.0  # No experience required
else:
    experience_match = min(student.experience_months / jd.experience_months, 1.0)
    
    # Bonus for exceeding requirement (capped at 1.2)
    if student.experience_months > jd.experience_months:
        experience_match = min(1.2, experience_match)
```

**Project Relevance:**
```python
# Extract keywords from projects and JD
project_keywords = set(extract_keywords(student.projects))
jd_keywords = set(extract_keywords(jd.description))

# Jaccard similarity
intersection = len(project_keywords & jd_keywords)
union = len(project_keywords | jd_keywords)

project_relevance = intersection / union if union > 0 else 0

# Boost if project directly uses required skills
skill_usage_bonus = 0
for skill in required_skills:
    if any(skill.lower() in project.tech_stack.lower() for project in student.projects):
        skill_usage_bonus += 0.1

project_relevance = min(project_relevance + skill_usage_bonus, 1.0)
```

**Final Score:**
```python
readiness_score = (
    0.50 * skill_match_ratio +
    0.30 * proficiency_alignment +
    0.10 * experience_match +
    0.10 * project_relevance
) * 100

# Round to integer
readiness_score = round(readiness_score)

# Apply realism filter
if jd.realism_score < 50:
    readiness_score = 0  # Exclude inflated JDs
```

**5.2.2 JD Inflation Detection**

**Rule-Based Heuristic:**

```python
def detect_inflation(jd):
    penalty = 0
    signals = []
    
    # Signal 1: Experience requirement
    if jd.role_type == "Internship" and jd.experience_months > 12:
        penalty += 30
        signals.append({
            "type": "experience_mismatch",
            "detail": f"Requires {jd.experience_months} months for internship"
        })
    
    # Signal 2: Skill overload
    if len(jd.required_skills) > 12:
        penalty += 20
        signals.append({
            "type": "skill_overload",
            "detail": f"Lists {len(jd.required_skills)} required skills"
        })
    
    # Signal 3: Contradictory terms
    contradictions = [
        ("fresher", "experience"),
        ("entry-level", "senior"),
        ("internship", "lead")
    ]
    for term1, term2 in contradictions:
        if term1 in jd.text.lower() and term2 in jd.text.lower():
            penalty += 25
            signals.append({
                "type": "contradiction",
                "detail": f"Contains both '{term1}' and '{term2}'"
            })
    
    # Signal 4: Multi-domain stack
    domains = ["Frontend", "Backend", "ML", "DevOps", "Mobile"]
    domain_count = sum(1 for d in domains if any(skill.category == d for skill in jd.required_skills))
    if domain_count > 3:
        penalty += 15
        signals.append({
            "type": "multi_domain",
            "detail": f"Requires skills across {domain_count} domains"
        })
    
    # Signal 5: Vague buzzwords
    buzzwords = ["rockstar", "ninja", "guru", "wizard", "unicorn"]
    for word in buzzwords:
        if word in jd.text.lower():
            penalty += 10
            signals.append({
                "type": "vague_language",
                "detail": f"Uses unprofessional term '{word}'"
            })
            break
    
    # Signal 6: All skills at expert level
    if all(skill.proficiency == "Advanced" for skill in jd.required_skills):
        penalty += 20
        signals.append({
            "type": "proficiency_mismatch",
            "detail": "All skills require 'Advanced' proficiency"
        })
    
    realism_score = max(0, 100 - penalty)
    
    return {
        "realism_score": realism_score,
        "classification": classify_realism(realism_score),
        "signals": signals
    }

def classify_realism(score):
    if score >= 80:
        return "Realistic"
    elif score >= 50:
        return "Moderate"
    else:
        return "Inflated"
```

**LLM Validation (Optional):**
```python
# For edge cases, use GPT-4 to validate
prompt = f"""
Analyze this internship job description for realism:

{jd.text}

Is this realistic for an internship? Consider:
1. Experience requirements
2. Skill breadth
3. Proficiency expectations

Answer: Realistic / Moderate / Inflated
Reason: [One sentence]
"""

response = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[{"role": "user", "content": prompt}],
    temperature=0.3
)

llm_classification = parse_response(response)

# Combine with rule-based score (70% rules, 30% LLM)
final_score = 0.7 * rule_based_score + 0.3 * llm_score
```

### 5.3 Explainability Approach

**Principle:** Every AI decision must be traceable to human-understandable logic

**5.3.1 Score Breakdown**

Display contribution of each factor:
```json
{
  "readiness_score": 72,
  "breakdown": {
    "skill_match": {"value": 0.83, "contribution": 41.5, "weight": 0.50},
    "proficiency": {"value": 0.75, "contribution": 22.5, "weight": 0.30},
    "experience": {"value": 1.0, "contribution": 10.0, "weight": 0.10},
    "projects": {"value": 0.40, "contribution": 4.0, "weight": 0.10}
  }
}
```

**5.3.2 Natural Language Explanations**

Generate human-readable text:
```python
def generate_explanation(score_data):
    explanation = f"Your readiness score is {score_data['readiness_score']}/100. "
    
    # Highlight strengths
    strengths = [k for k, v in score_data['breakdown'].items() if v['value'] > 0.8]
    if strengths:
        explanation += f"Strong areas: {', '.join(strengths)}. "
    
    # Highlight gaps
    gaps = [k for k, v in score_data['breakdown'].items() if v['value'] < 0.6]
    if gaps:
        explanation += f"Areas to improve: {', '.join(gaps)}. "
    
    # Specific skill gaps
    missing = score_data['missing_skills']
    if missing:
        critical = [s for s in missing if s['critical']]
        if critical:
            explanation += f"Critical missing skills: {', '.join([s['name'] for s in critical])}. "
    
    return explanation
```

**5.3.3 Visual Explanations**

- **Radar Chart:** Compare student vs. required skills across categories
- **Bar Chart:** Show contribution of each factor to final score
- **Heatmap:** Display skill match matrix (student skills × JD requirements)

**5.3.4 SHAP-Like Attribution (Future Enhancement)**

For ML models (if used):
```python
import shap

# Train a simple model for score prediction
model = train_readiness_model(training_data)

# Explain predictions
explainer = shap.Explainer(model)
shap_values = explainer(student_features)

# Visualize
shap.waterfall_plot(shap_values[0])
```

---

## 6. Tech Stack

### 6.1 Frontend

**Framework:** React 18.2
- **Rationale:** Fast prototyping, rich ecosystem, component reusability
- **State Management:** React Context API (lightweight for MVP)
- **Routing:** React Router v6

**UI Library:** Tailwind CSS 3.3
- **Rationale:** Rapid styling, responsive design, small bundle size
- **Component Library:** Headless UI (accessible components)

**Visualization:** Recharts 2.5
- **Charts:** Radar, Bar, Line, Pie
- **Rationale:** React-native, declarative API

**HTTP Client:** Axios 1.4
- **Features:** Interceptors for auth, error handling

**Build Tool:** Vite 4.3
- **Rationale:** Fast HMR, optimized production builds

### 6.2 Backend

**Framework:** FastAPI 0.104
- **Rationale:** Async support, auto-generated docs, type safety
- **Python Version:** 3.11

**API Documentation:** OpenAPI (auto-generated by FastAPI)
- **Endpoint:** `/docs` (Swagger UI)

**Validation:** Pydantic 2.4
- **Use:** Request/response schema validation

**Task Queue:** Celery 5.3 (for async processing)
- **Broker:** Redis
- **Use Case:** Background resume parsing, roadmap generation

### 6.3 Database

**Primary Database:** PostgreSQL 15
- **Schema:**
  - `resumes`: Student profiles, skills, experience
  - `internships`: JD data, requirements, metadata
  - `skills`: Canonical skill taxonomy
  - `roadmaps`: Generated learning plans
  - `matches`: Cached readiness scores

**Caching Layer:** Redis 7.2
- **Use Cases:**
  - Session management
  - Skill vector caching (TTL: 1 hour)
  - Match result caching (TTL: 5 minutes)
  - Rate limiting

**File Storage:** AWS S3 / Local (for MVP)
- **Use:** Resume PDF storage (encrypted)
- **Retention:** 30 days, then auto-delete

### 6.4 AI/ML Framework

**NLP Library:** spaCy 3.7
- **Model:** `en_core_web_lg`
- **Custom Training:** Yes (Indian resume corpus)

**Embeddings:** Sentence-Transformers 2.2
- **Model:** `all-MiniLM-L6-v2`
- **Hosting:** Local (model loaded in memory)

**LLM API:** OpenAI GPT-4
- **Model:** `gpt-4-turbo-preview`
- **Use Cases:** Resume gap filling, roadmap generation
- **Fallback:** GPT-3.5-turbo (cost optimization)

**String Matching:** RapidFuzz 3.5
- **Algorithm:** Levenshtein distance
- **Use:** Skill normalization

### 6.5 Hosting & Deployment

**Frontend Hosting:** Vercel
- **Features:** Auto-deploy from Git, CDN, serverless functions
- **Plan:** Free tier (sufficient for demo)

**Backend Hosting:** Railway / Render
- **Features:** Docker support, auto-scaling, PostgreSQL addon
- **Plan:** Free tier (500 hours/month)

**Database:** Railway PostgreSQL
- **Storage:** 1GB (free tier)

**Redis:** Upstash (serverless Redis)
- **Plan:** Free tier (10K requests/day)

**File Storage:** AWS S3 (or Railway volumes for MVP)

### 6.6 Scalability Strategy

**Phase 1: MVP (50 users)**
- Single backend instance
- Shared PostgreSQL
- In-memory caching

**Phase 2: Growth (1K users)**
- Horizontal scaling: 3 backend instances (load balanced)
- Redis cluster (3 nodes)
- Database connection pooling (PgBouncer)
- CDN for static assets

**Phase 3: Scale (10K+ users)**
- Microservices architecture:
  - Resume Parser Service
  - Matching Engine Service
  - Roadmap Generator Service
- Message queue (RabbitMQ) for inter-service communication
- Database sharding (by user ID)
- Elasticsearch for internship search
- Kubernetes orchestration

**Caching Strategy:**
```python
# Cache layers
L1: In-memory (LRU cache, 1000 entries)
L2: Redis (5-minute TTL for match results)
L3: PostgreSQL (persistent storage)

# Cache invalidation
- Resume update → Invalidate user's match cache
- New internship → Invalidate all match caches (batch job)
```

### 6.7 Monitoring & Logging

**Application Monitoring:** Sentry
- **Metrics:** Error rate, response time, user sessions

**Logging:** Python `logging` module
- **Format:** JSON (structured logs)
- **Levels:** DEBUG (dev), INFO (prod)

**Performance Monitoring:** FastAPI middleware
```python
@app.middleware("http")
async def log_requests(request, call_next):
    start_time = time.time()
    response = await call_next(request)
    duration = time.time() - start_time
    logger.info(f"{request.method} {request.url.path} - {duration:.2f}s")
    return response
```

---

## 7. Algorithm Details

### 7.1 Skill Readiness Score Formula

**Mathematical Definition:**


```
R = (W₁ × SMR + W₂ × PA + W₃ × EM + W₄ × PR) × 100

Where:
R   = Readiness Score (0-100)
SMR = Skill Match Ratio
PA  = Proficiency Alignment
EM  = Experience Match
PR  = Project Relevance

Weights:
W₁ = 0.50  (Skill coverage is primary)
W₂ = 0.30  (Proficiency depth matters)
W₃ = 0.10  (Experience is secondary for internships)
W₄ = 0.10  (Projects demonstrate application)
```

**Component Formulas:**

**Skill Match Ratio (SMR):**
```
SMR = |S ∩ R| / |R|

Where:
S = Set of student skills
R = Set of required skills
∩ = Intersection (matched skills)
| | = Cardinality (count)

Example:
S = {Python, HTML, CSS, JavaScript}
R = {Python, JavaScript, React, Node.js}
|S ∩ R| = |{Python, JavaScript}| = 2
|R| = 4
SMR = 2/4 = 0.50
```

**Proficiency Alignment (PA):**
```
PA = (1/|M|) × Σ min(Pₛ(sᵢ) / Pᵣ(sᵢ), 1.0) × Wc(sᵢ)

Where:
M = Set of matched skills
Pₛ(sᵢ) = Student proficiency for skill i (1=Beginner, 2=Intermediate, 3=Advanced)
Pᵣ(sᵢ) = Required proficiency for skill i
Wc(sᵢ) = Criticality weight (1.5 if critical, 1.0 otherwise)

Example:
Matched skills: Python, JavaScript
Student: Python=Intermediate(2), JavaScript=Beginner(1)
Required: Python=Intermediate(2), JavaScript=Intermediate(2)
Critical: Python=Yes, JavaScript=No

PA = (1/2) × [(min(2/2, 1.0) × 1.5) + (min(1/2, 1.0) × 1.0)]
   = (1/2) × [(1.0 × 1.5) + (0.5 × 1.0)]
   = (1/2) × [1.5 + 0.5]
   = 1.0
```

**Experience Match (EM):**
```
EM = min(Eₛ / Eᵣ, 1.0)  if Eᵣ > 0
EM = 1.0                 if Eᵣ = 0

Where:
Eₛ = Student experience (months)
Eᵣ = Required experience (months)

Example 1: Student=6 months, Required=12 months
EM = min(6/12, 1.0) = 0.5

Example 2: Student=18 months, Required=12 months
EM = min(18/12, 1.0) = 1.0

Example 3: Student=0 months, Required=0 months (fresher role)
EM = 1.0
```

**Project Relevance (PR):**
```
PR = J(Kₚ, Kⱼ) + B

Where:
J(Kₚ, Kⱼ) = Jaccard similarity between project keywords and JD keywords
Kₚ = Set of keywords from student projects
Kⱼ = Set of keywords from JD description
B = Skill usage bonus (0.1 per required skill used in projects, max 0.3)

Jaccard Similarity:
J(A, B) = |A ∩ B| / |A ∪ B|

Example:
Kₚ = {web, scraping, python, beautifulsoup, mongodb}
Kⱼ = {web, development, python, django, postgresql}
|Kₚ ∩ Kⱼ| = |{web, python}| = 2
|Kₚ ∪ Kⱼ| = 8
J = 2/8 = 0.25

Required skills used in projects: Python (yes), Django (no)
B = 0.1 × 1 = 0.1

PR = 0.25 + 0.1 = 0.35
```

**Final Calculation Example:**

```
Student Profile:
- Skills: Python (Intermediate), JavaScript (Beginner), HTML (Advanced), CSS (Advanced)
- Experience: 0 months
- Projects: E-commerce website (HTML, CSS, JavaScript)

Internship Requirements:
- Skills: Python (Intermediate, critical), JavaScript (Intermediate), React (Beginner)
- Experience: 0 months
- Keywords: web, frontend, react, javascript

Step 1: SMR
Matched: Python, JavaScript
SMR = 2/3 = 0.667

Step 2: PA
Python: min(2/2, 1.0) × 1.5 = 1.5
JavaScript: min(1/2, 1.0) × 1.0 = 0.5
PA = (1.5 + 0.5) / 2 = 1.0

Step 3: EM
EM = 1.0 (both require 0 months)

Step 4: PR
Project keywords: {ecommerce, website, html, css, javascript}
JD keywords: {web, frontend, react, javascript}
Intersection: {javascript}
Union: {ecommerce, website, html, css, javascript, web, frontend, react}
J = 1/8 = 0.125
Skill usage: JavaScript (yes) → B = 0.1
PR = 0.125 + 0.1 = 0.225

Step 5: Final Score
R = (0.50 × 0.667 + 0.30 × 1.0 + 0.10 × 1.0 + 0.10 × 0.225) × 100
R = (0.3335 + 0.30 + 0.10 + 0.0225) × 100
R = 0.756 × 100
R = 75.6 ≈ 76
```

### 7.2 Matching Threshold Logic

**Hard Filters:**

1. **Minimum Readiness:** Score ≥ 60
   - Rationale: <60% indicates significant skill gaps
   - Exception: "Stretch Opportunities" section shows 55-59% matches

2. **Realism Filter:** JD realism score ≥ 50
   - Rationale: Protect students from inflated listings
   - Inflated JDs (score <50) are hidden by default

3. **Critical Skill Filter:** Student must have ≥50% of critical skills
   - Rationale: Missing critical skills = likely rejection
   - Example: If JD marks Python as critical, student must have Python

**Ranking Logic:**

```python
def calculate_rank_score(readiness_score, internship, student):
    # Base score (60% weight)
    rank_score = 0.60 * readiness_score
    
    # Location match bonus (20% weight)
    location_bonus = 0
    if student.location == internship.location:
        location_bonus = 10  # Same city
    elif student.state == internship.state:
        location_bonus = 5   # Same state
    rank_score += 0.20 * location_bonus
    
    # Stipend normalization (10% weight)
    # Normalize to 0-10 scale (assuming max stipend = 50K/month)
    stipend_normalized = min(internship.stipend / 5000, 10)
    rank_score += 0.10 * stipend_normalized
    
    # Company rating (10% weight)
    # Assume rating is 0-5, scale to 0-10
    company_rating = internship.company_rating * 2
    rank_score += 0.10 * company_rating
    
    return rank_score

# Sort internships by rank_score descending
ranked_internships = sorted(matches, key=lambda x: x.rank_score, reverse=True)
```

**Pagination:**
- Display top 20 results per page
- Lazy load next 20 on scroll

### 7.3 Inflation Detection Heuristic

**Penalty System:**

| Heuristic | Condition | Penalty | Rationale |
|-----------|-----------|---------|-----------|
| Experience Mismatch | `experience_months > 12` for internship | 30 | Internships are entry-level |
| Skill Overload | `len(required_skills) > 12` | 20 | Unrealistic breadth |
| Contradiction | "fresher" AND "experience" in text | 25 | Logical inconsistency |
| Multi-Domain | Skills span >3 domains | 15 | Unrealistic for single role |
| Vague Buzzwords | Contains "rockstar", "ninja", etc. | 10 | Unprofessional language |
| Proficiency Mismatch | All skills require "Advanced" | 20 | Unrealistic for interns |
| Unrealistic Stack | >5 technologies in single category | 15 | Example: React + Vue + Angular + Svelte |

**Scoring:**
```python
realism_score = max(0, 100 - total_penalty)

if realism_score >= 80:
    classification = "Realistic"
elif realism_score >= 50:
    classification = "Moderate"
else:
    classification = "Inflated"
```

**Edge Case Handling:**

1. **Ambiguous JDs:** Use LLM validation
2. **Domain-Specific Roles:** Adjust thresholds (e.g., DevOps may need more tools)
3. **Startup JDs:** Often list "nice-to-haves" as "required" → Moderate penalty

---

## 8. Scalability & Deployment Plan

### 8.1 Containerization

**Docker Setup:**

```dockerfile
# Backend Dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Download spaCy model
RUN python -m spacy download en_core_web_lg

# Copy application code
COPY . .

# Expose port
EXPOSE 8000

# Run FastAPI with Uvicorn
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```dockerfile
# Frontend Dockerfile
FROM node:18-alpine

WORKDIR /app

# Install dependencies
COPY package*.json .
RUN npm ci

# Copy source code
COPY . .

# Build for production
RUN npm run build

# Serve with nginx
FROM nginx:alpine
COPY --from=0 /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**Docker Compose (Local Development):**

```yaml
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/internship_db
      - REDIS_URL=redis://redis:6379
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    depends_on:
      - db
      - redis
    volumes:
      - ./backend:/app

  frontend:
    build: ./frontend
    ports:
      - "3000:80"
    depends_on:
      - backend

  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=internship_db
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

### 8.2 Cloud Hosting Architecture

**MVP Deployment (Railway):**

```
┌─────────────────────────────────────────────────┐
│              Vercel (Frontend)                  │
│  - React SPA                                    │
│  - CDN distribution                             │
│  - Auto-deploy from GitHub                     │
└────────────────┬────────────────────────────────┘
                 │ HTTPS
                 ▼
┌─────────────────────────────────────────────────┐
│         Railway (Backend + Database)            │
│  ┌──────────────────────────────────────────┐  │
│  │  FastAPI Service                         │  │
│  │  - 1 instance (512MB RAM)                │  │
│  │  - Auto-scaling (up to 3 instances)      │  │
│  └──────────────┬───────────────────────────┘  │
│                 │                               │
│  ┌──────────────▼───────────────────────────┐  │
│  │  PostgreSQL                              │  │
│  │  - 1GB storage                           │  │
│  │  - Automated backups                     │  │
│  └──────────────────────────────────────────┘  │
│                                                 │
│  ┌──────────────────────────────────────────┐  │
│  │  Redis (Upstash)                         │  │
│  │  - Serverless                            │  │
│  │  - 10K requests/day                      │  │
│  └──────────────────────────────────────────┘  │
└─────────────────────────────────────────────────┘
```

### 8.3 API-Based Microservices (Future)

**Service Decomposition:**

```
┌─────────────────────────────────────────────────┐
│              API Gateway (Kong)                  │
│  - Authentication                               │
│  - Rate limiting                                │
│  - Request routing                              │
└────┬────────┬────────┬────────┬─────────────────┘
     │        │        │        │
     ▼        ▼        ▼        ▼
┌─────────┐ ┌──────┐ ┌──────┐ ┌──────────┐
│ Resume  │ │Match │ │ JD   │ │ Roadmap  │
│ Parser  │ │Engine│ │Analyzer│ │Generator │
│ Service │ │Service│ │Service│ │ Service  │
└────┬────┘ └──┬───┘ └──┬───┘ └────┬─────┘
     │         │        │          │
     └─────────┴────────┴──────────┘
                    │
                    ▼
          ┌──────────────────┐
          │  Message Queue   │
          │  (RabbitMQ)      │
          └──────────────────┘
```

**Service Communication:**

- **Synchronous:** REST APIs (for real-time requests)
- **Asynchronous:** Message queue (for background jobs)

**Example: Resume Upload Flow**

```
1. User uploads resume → API Gateway
2. Gateway routes to Resume Parser Service
3. Parser extracts data → Publishes "resume.parsed" event to queue
4. Matching Engine subscribes to event → Calculates scores
5. Matching Engine publishes "matches.calculated" event
6. Frontend polls API Gateway for results
```

### 8.4 Future Integration APIs

**Internship Data Providers:**

1. **Internshala API** (if available)
   - Endpoint: `GET /api/internships`
   - Sync frequency: Daily
   - Data: JD, company, location, stipend

2. **LinkedIn Jobs API**
   - OAuth integration
   - Filter: `experience_level=internship`

3. **Custom Web Scraper**
   - Target: Company career pages
   - Frequency: Weekly
   - Storage: PostgreSQL

**Learning Resource APIs:**

1. **YouTube Data API**
   - Search tutorials by skill name
   - Filter: Duration <2 hours, high ratings

2. **Udemy API** (affiliate)
   - Recommend free courses
   - Track completion (future)

3. **GitHub API**
   - Suggest open-source projects for practice
   - Filter by skill tags

**College Integration APIs:**

1. **Student Profile Import**
   - Endpoint: `POST /api/import/student`
   - Data: Name, degree, CGPA, skills (from college LMS)

2. **Placement Cell Dashboard**
   - Endpoint: `GET /api/analytics/college/{id}`
   - Metrics: Average readiness score, top skill gaps

---

## 9. Security Design

### 9.1 Resume Encryption

**At Rest:**
- Algorithm: AES-256-GCM
- Key Management: AWS KMS / Environment variables (MVP)
- Storage: S3 with server-side encryption

```python
from cryptography.fernet import Fernet

# Generate key (store securely)
key = Fernet.generate_key()
cipher = Fernet(key)

# Encrypt resume
with open("resume.pdf", "rb") as f:
    resume_data = f.read()
encrypted_data = cipher.encrypt(resume_data)

# Store encrypted data
s3.put_object(Bucket="resumes", Key=f"{user_id}.enc", Body=encrypted_data)
```

**In Transit:**
- HTTPS/TLS 1.3 for all API calls
- Certificate: Let's Encrypt (auto-renewed)

### 9.2 Role-Based Access Control (RBAC)

**Roles:**

1. **Student** (default)
   - Upload resume
   - View own matches
   - Generate roadmap

2. **Admin** (future)
   - View all resumes (anonymized)
   - Manage internship listings
   - Access analytics dashboard

3. **Recruiter** (future)
   - Search candidates by skills
   - View anonymized profiles
   - Request contact (with student consent)

**Implementation:**

```python
from enum import Enum

class Role(Enum):
    STUDENT = "student"
    ADMIN = "admin"
    RECRUITER = "recruiter"

def require_role(role: Role):
    def decorator(func):
        async def wrapper(request: Request, *args, **kwargs):
            user = get_current_user(request)
            if user.role != role:
                raise HTTPException(status_code=403, detail="Forbidden")
            return await func(request, *args, **kwargs)
        return wrapper
    return decorator

@app.get("/admin/analytics")
@require_role(Role.ADMIN)
async def get_analytics():
    return {"data": "..."}
```

### 9.3 Data Retention Policy

**Resume Files:**
- Retention: 30 days after upload
- Auto-deletion: Cron job runs daily
- User control: "Delete my data" button

**Parsed Data:**
- Retention: 90 days (for analytics)
- Anonymization: Remove PII after 30 days
- Aggregation: Convert to anonymous statistics

**Logs:**
- Retention: 7 days (application logs)
- Retention: 30 days (security logs)

### 9.4 API Security

**Rate Limiting:**
```python
from slowapi import Limiter
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)

@app.post("/api/resume/upload")
@limiter.limit("5/minute")  # Max 5 uploads per minute
async def upload_resume():
    pass
```

**Input Validation:**
- File type whitelist: PDF, DOCX only
- File size limit: 5MB
- Malware scanning: ClamAV (future)

**SQL Injection Prevention:**
- Use parameterized queries (SQLAlchemy ORM)
- Never concatenate user input into SQL

**XSS Prevention:**
- Sanitize all user inputs
- Content Security Policy headers

---

## 10. Future Enhancements

### 10.1 Multilingual Support (India)

**Phase 1: UI Translation**
- Languages: Hindi, Tamil, Telugu, Bengali
- Library: react-i18next
- Translation files: JSON (crowdsourced)

**Phase 2: Resume Parsing**
- Support resumes in regional languages
- NLP models: Multilingual BERT (mBERT)
- Transliteration: Indic-to-English for skill extraction

**Phase 3: Roadmap Localization**
- Recommend Hindi/regional language tutorials
- Partner with local content creators

### 10.2 Regional Internship Data Pipelines

**Tier-2/Tier-3 City Focus:**
- Scrape local job boards (e.g., Naukri, Shine)
- Partner with regional startups
- College placement cell integrations

**Data Enrichment:**
- Add company reviews (Glassdoor API)
- Salary benchmarks (AmbitionBox)
- Commute time (Google Maps API)

### 10.3 College API Integrations

**Student Profile Import:**
- Integrate with college LMS (Moodle, Canvas)
- Auto-populate skills from coursework
- Import project descriptions

**Placement Cell Dashboard:**
- Analytics: Average readiness by department
- Skill gap heatmap for entire batch
- Internship conversion tracking

**Bulk Operations:**
- Upload 100+ resumes at once
- Generate batch reports
- Export to Excel

### 10.4 Advanced Features

**AI Interview Prep:**
- Generate mock interview questions based on skill gaps
- Video practice with feedback (future)

**Peer Comparison:**
- Anonymous benchmarking: "You're in top 30% for React.js"
- Leaderboards (opt-in)

**Application Tracking:**
- Track applied internships
- Reminder: Follow up after 7 days
- Success rate analytics

**Resume Builder:**
- AI-powered resume optimization
- ATS-friendly templates
- Skill highlighting based on target role

**Salary Negotiation:**
- Stipend benchmarks by skill level
- Negotiation tips based on readiness score

---

## Appendix A: Database Schema

```sql
-- Resumes table
CREATE TABLE resumes (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL,
    name VARCHAR(255),
    email VARCHAR(255),
    phone VARCHAR(20),
    education JSONB,  -- [{degree, year, cgpa, college}]
    experience_months INT DEFAULT 0,
    projects JSONB,  -- [{title, description, tech_stack, url}]
    certifications JSONB,  -- [{name, issuer, date}]
    parsing_confidence FLOAT,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Skills table (normalized)
CREATE TABLE skills (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) UNIQUE NOT NULL,
    category VARCHAR(50),  -- Programming Language, Framework, Tool, etc.
    synonyms TEXT[],  -- ['JS', 'javascript', 'ECMAScript']
    learning_time_hours INT,
    popularity_score FLOAT  -- 0-1, based on job market demand
);

-- Resume skills (many-to-many)
CREATE TABLE resume_skills (
    resume_id UUID REFERENCES resumes(id) ON DELETE CASCADE,
    skill_id INT REFERENCES skills(id),
    proficiency VARCHAR(20),  -- Beginner, Intermediate, Advanced
    source VARCHAR(50),  -- 'mentioned', 'project', 'certification'
    PRIMARY KEY (resume_id, skill_id)
);

-- Internships table
CREATE TABLE internships (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title VARCHAR(255) NOT NULL,
    company VARCHAR(255),
    location VARCHAR(100),
    work_mode VARCHAR(20),  -- Remote, Hybrid, On-site
    stipend INT,  -- Monthly in INR
    duration_months INT,
    description TEXT,
    requirements JSONB,  -- [{skill, proficiency, critical}]
    experience_months INT,
    realism_score INT,  -- 0-100
    realism_signals JSONB,  -- [{type, penalty, detail}]
    posted_date DATE,
    application_url VARCHAR(500),
    created_at TIMESTAMP DEFAULT NOW()
);

-- Matches table (cached scores)
CREATE TABLE matches (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    resume_id UUID REFERENCES resumes(id) ON DELETE CASCADE,
    internship_id UUID REFERENCES internships(id) ON DELETE CASCADE,
    readiness_score INT,  -- 0-100
    rank_score FLOAT,
    breakdown JSONB,  -- {skill_match, proficiency, experience, projects}
    matched_skills TEXT[],
    missing_skills JSONB,  -- [{skill, priority, estimated_hours}]
    calculated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(resume_id, internship_id)
);

-- Roadmaps table
CREATE TABLE roadmaps (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    resume_id UUID REFERENCES resumes(id) ON DELETE CASCADE,
    target_internship_id UUID REFERENCES internships(id),
    duration_days INT DEFAULT 30,
    skills_covered TEXT[],
    weekly_plan JSONB,  -- [{week, skill, goals, resources, daily_tasks}]
    capstone_project JSONB,
    progress_checklist JSONB,  -- [{task, completed}]
    created_at TIMESTAMP DEFAULT NOW()
);

-- Indexes for performance
CREATE INDEX idx_resume_skills_resume ON resume_skills(resume_id);
CREATE INDEX idx_matches_resume ON matches(resume_id);
CREATE INDEX idx_matches_score ON matches(readiness_score DESC);
CREATE INDEX idx_internships_realism ON internships(realism_score);
```

---

## Appendix B: API Endpoints

```
POST   /api/resume/upload          Upload resume file
GET    /api/resume/{id}             Get parsed resume data
PUT    /api/resume/{id}             Update resume manually
DELETE /api/resume/{id}             Delete resume

GET    /api/skills                  List all skills in taxonomy
GET    /api/skills/search?q={term}  Search skills (autocomplete)

GET    /api/internships             List internships (paginated)
GET    /api/internships/{id}        Get internship details
POST   /api/internships             Create internship (admin)

GET    /api/matches?resume_id={id}  Get matches for resume
GET    /api/matches/{id}            Get specific match details

POST   /api/roadmap/generate        Generate learning roadmap
GET    /api/roadmap/{id}            Get roadmap details
PUT    /api/roadmap/{id}/progress   Update progress

GET    /api/analytics/dashboard     Get user dashboard data
```

---

**Document Status:** Final v1.0  
**Review Date:** February 12, 2026  
**Approved By:** Khan Hammad Raza (Team Lead)

---

*End of Design Document*
