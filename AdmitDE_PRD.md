# AdmitDE — Product Requirements Document
**Version:** 1.0  
**Date:** August 2026  
**Status:** Ready for Development  
**Target Completion:** 1 Sprint (1–2 days)

---

## 1. Overview

**AdmitDE** is a single-page web application that acts as an AI-powered admission consultant for students who want to pursue **BS or MS degrees in Computer Science, Artificial Intelligence, or Data Science at public universities in Germany.**

The user inputs their academic profile once. The app cross-references it against a curated database of real German university requirements, runs an AI analysis via Groq, and returns a ranked list of universities showing where the user qualifies, where they fall short, and exactly what they need to improve or prepare.

**One-liner:** *"Tell us your grades and scores. We'll tell you exactly where you stand in Germany."*

---

## 2. Problem Statement

Students from South Asia, the Middle East, and other regions want to study in Germany but face a fragmented, confusing research process — scattered across DAAD, uni-assist, individual university websites, and outdated forums. They don't know:

- Which universities they actually qualify for right now
- What documents they're missing (especially Germany-specific ones like APS)
- Whether to apply for BS or MS based on their profile
- Which programs are taught in English vs German

AdmitDE solves this in under 3 minutes.

---

## 3. Goals

| Goal | Metric |
|------|--------|
| Fast profile-to-results flow | User gets results in < 60 seconds |
| Accurate requirement matching | All data sourced from official sources; no dummy data |
| Actionable gap analysis | Every "not eligible" result explains the specific gap |
| Country-specific compliance | APS, DSH, TestDaF, blocked account all checked |
| Clean, premium UI | No template/bootstrap feel; editorial design |

---

## 4. Target Users

**Primary:** Undergraduate and recent bachelor's graduates from Pakistan, India, Bangladesh, or similar countries considering Germany for their next degree.

**Secondary:** Students already in Germany on a student visa exploring grad school options.

**Key trait:** They are self-researching, not using a paid consultancy. They want honest, direct answers — not vague encouragement.

---

## 5. User Flow

```
[Landing / Hero Screen]
        ↓
[Step 1: Academic Profile Form]
  - Nationality
  - Degree level applying for (BS / MS)
  - Current/completed degree
  - GPA input (format selector: percentage, 4.0 scale, or German 1–5)
  - IELTS score (or TOEFL / exempt)
  - German language level (None / A1 / A2 / B1 / B2 / C1 / C2 / DSH-1 / DSH-2 / TestDaF)
  - Target field (CS / AI / Data Science / Software Engineering)
        ↓
[Step 2: Preferences]
  - Preferred city (optional multi-select or "No preference")
  - Preferred semester intake (Winter / Summer / Both)
  - Language of instruction (German / English / Both)
        ↓
[Step 3: AI Analysis Loading Screen]
  - Profile sent to Groq API
  - Animated consultation state (brief, tasteful)
        ↓
[Step 4: Results Dashboard]
  - AI consultation summary (2–3 sentences, written by Groq)
  - University cards sorted by match tier:
      TIER 1: Strong Match (meet all requirements)
      TIER 2: Borderline (meet most, 1–2 gaps)
      TIER 3: Not Eligible Yet (clear gaps shown)
  - Each card shows:
      → University name + city + QS ranking (where applicable)
      → Program name + language of instruction
      → Match badge (Strong / Borderline / Not Eligible)
      → Requirements checklist (✓ met / ✗ missing / ⚠ borderline)
      → Application portal (direct link label)
      → Deadline info
      → Required documents list
  - Sidebar or bottom panel: "What you need to apply to Germany" (universal checklist)
  - CTA: Start Over / Export PDF (optional v2 feature)
```

---

## 6. Feature Requirements

### P0 — Must Have (MVP)

| ID | Feature | Description |
|----|---------|-------------|
| F01 | Multi-step form | 2-step form with field validation, no page reload |
| F02 | GPA conversion engine | Auto-converts percentage / 4.0 / German scale to German grade using official formula |
| F03 | University matching engine | Client-side logic that checks each uni's requirements against user profile before sending to AI |
| F04 | Groq AI consultation | Sends structured profile + matching pre-analysis to Groq; returns narrative + any corrections |
| F05 | Tiered results | Universities sorted into 3 tiers with clear visual distinction |
| F06 | Gap analysis per card | Each card shows specifically which requirement is not met |
| F07 | APS flag | If nationality is from APS-required list (Pakistan, India, China, Vietnam, Mongolia), flag this requirement prominently |
| F08 | German language gate | If applying to German-taught program with < B2, auto-flag as ineligible with explanation |
| F09 | BS language reality check | Warn BS applicants that virtually all German public uni BS programs are German-medium |
| F10 | Real university data | Minimum 12 universities, real requirements, no placeholder data |

### P1 — Should Have

| ID | Feature | Description |
|----|---------|-------------|
| F11 | Blocked account note | Show €11,208/year blocked account requirement on all results (visa requirement) |
| F12 | Semester validity check | If applying for Summer intake but university only offers Winter, mark accordingly |
| F13 | Application portal links | Label (not just raw URL) for each university: "Apply via uni-assist" or "Apply directly on [University] portal" |
| F14 | Responsive layout | Fully usable on mobile (Android Chrome priority) |
| F15 | Start over flow | Clean state reset without page reload |

### P2 — Nice to Have (post-v1)

| ID | Feature | Description |
|----|---------|-------------|
| F16 | PDF export | Export results as a downloadable PDF |
| F17 | Save profile | LocalStorage-based profile memory |
| F18 | DAAD scholarship flag | Note if a program has DAAD scholarship relevance |
| F19 | Studienkolleg flag | For BS applicants whose high school isn't recognized, flag Studienkolleg pathway |

---

## 7. Technical Architecture

### Stack
- **Framework:** React (single .jsx or .html file — no build step required for Antigravity IDE)
- **Styling:** Tailwind CSS (CDN) or custom CSS variables
- **AI:** Groq API — `llama-3.3-70b-versatile` (latest, free tier)
- **Data:** Embedded JSON constant in the file (no external DB)
- **No backend required** for MVP

### API: Groq
- **Base URL:** `https://api.groq.com/openai/v1/chat/completions`
- **Auth:** `Authorization: Bearer <GROQ_API_KEY>` (user provides key or it's hardcoded for demo)
- **Model:** `llama-3.3-70b-versatile`
- **Max tokens:** 1000
- **The app pre-runs matching logic client-side first**, then sends a structured summary to Groq for the narrative consultation — this reduces hallucination risk and API latency

### Groq Prompt Architecture
```
SYSTEM:
You are a Germany university admissions consultant. You receive a structured JSON 
of a student's profile and a pre-computed match analysis. Your job is to write a 
2–3 sentence honest consultation summary — what is their strongest asset, their 
biggest blocker, and one specific actionable recommendation. Be direct. Do not use 
filler phrases. Do not say "It seems like" or "Based on the information provided".

USER:
{
  "profile": {
    "nationality": "Pakistani",
    "degree_target": "MS",
    "field": "Artificial Intelligence",
    "gpa_german_equivalent": 2.4,
    "ielts": 6.5,
    "german_level": "B1",
    "aps_certified": false
  },
  "match_summary": {
    "strong_match_count": 2,
    "borderline_count": 5,
    "not_eligible_count": 5,
    "top_missing_requirements": ["APS Certificate", "German B2 for mixed programs"],
    "top_strength": "GPA meets most university thresholds"
  }
}
```

---

## 8. GPA Conversion Logic

All input GPAs must be converted to the **German grade scale (1.0–5.0)** where 1.0 is best.

### Formula (for percentage-based systems like Pakistan/India)
```
german_grade = 1 + 3 × (max_grade - student_grade) / (max_grade - min_passing_grade)

Where:
  max_grade = 100
  min_passing_grade = 50 (Pakistan standard)

Examples:
  90% → 1 + 3 × (10/50) = 1.60  ✓ Excellent
  80% → 1 + 3 × (20/50) = 2.20  ✓ Good
  70% → 1 + 3 × (30/50) = 2.80  ✓ Borderline
  65% → 1 + 3 × (35/50) = 3.10  ✗ Below most thresholds
  60% → 1 + 3 × (40/50) = 3.40  ✗ Not eligible for most programs
```

### For US 4.0 GPA
```
german_grade = 1 + 3 × (4.0 - student_gpa) / (4.0 - 1.0)

Examples:
  3.7 → 1.30  ✓
  3.0 → 2.00  ✓
  2.7 → 2.30  ✓
  2.3 → 2.70  ✓ Borderline
  2.0 → 3.00  ✗
```

### Admission threshold
Most German public universities require a German grade of **≤ 2.5** for direct admission. Some (Saarland, Freiburg, Dresden) accept up to **2.8** for borderline review. TUM and RWTH are generally more competitive and expect **≤ 2.3** in practice.

---

## 9. University Data (Real Requirements)

All data is sourced from DAAD, uni-assist, and official university admission pages. Include the following as a JSON array in the codebase.

### JSON Schema
```json
{
  "id": "string",
  "name": "string",
  "city": "string",
  "state": "string",
  "qs_ranking_cs": "number or null",
  "public": true,
  "programs": [
    {
      "degree": "BS | MS",
      "name": "string",
      "field_tags": ["CS", "AI", "Data Science", "SE"],
      "language": "German | English | Bilingual",
      "duration_semesters": 6,
      "min_german_grade": 2.5,
      "english_requirement": {
        "ielts_min": 6.5,
        "toefl_min": 90,
        "exempt_if": ["German-taught program", "Prior English-medium degree"]
      },
      "german_requirement": {
        "min_level": "B2 | DSH-2 | TestDaF-4x4 | None",
        "note": "string"
      },
      "aps_required_nationalities": ["Pakistan", "India", "China", "Vietnam", "Mongolia"],
      "intake": ["Winter", "Summer"],
      "application_portal": "uni-assist | direct",
      "application_portal_url_label": "string",
      "deadlines": {
        "winter": "string",
        "summer": "string"
      },
      "tuition_eur_per_semester": 0,
      "semester_contribution_eur": 150,
      "notes": "string"
    }
  ]
}
```

### Universities to Include

#### MS Programs (English-taught — PRIMARY)

**1. Technical University of Munich (TUM)**
- City: Munich, Bavaria
- QS CS Ranking: ~50 globally
- MS Informatics
  - Language: English
  - Min German grade: 2.5 (competitive; effectively ~2.2 in practice)
  - IELTS: 7.0 (C1 accepted)
  - TOEFL: 95
  - Intake: Winter only
  - Deadline: January 15
  - Portal: Direct (TUMonline)
  - APS: Required (Pakistan, India, China)
  - Notes: Highly competitive. Motivation letter and CV carry significant weight.

**2. RWTH Aachen University**
- City: Aachen, NRW
- QS CS Ranking: ~100
- MS Computer Science
  - Language: English (some German modules)
  - Min German grade: 2.5
  - IELTS: 6.5 or B2 German
  - Intake: Winter + Summer
  - Deadline: Winter → March 1, Summer → September 1
  - Portal: Direct (RWTH Campus)
  - APS: Required
  - Notes: Strong industry connections; automotive and embedded systems focus.

**3. TU Berlin**
- City: Berlin
- QS CS Ranking: ~150
- MS Computer Science
  - Language: English
  - Min German grade: 2.5
  - IELTS: 7.0 (TOEFL 100)
  - Intake: Winter + Summer
  - Deadline: Winter → May 31, Summer → November 30
  - Portal: Direct (TU Berlin portal)
  - APS: Required
  - Notes: Very international cohort. Berlin has no tuition at all.

**4. Karlsruhe Institute of Technology (KIT)**
- City: Karlsruhe, Baden-Württemberg
- QS CS Ranking: ~130
- MS Computer Science
  - Language: English
  - Min German grade: 2.5
  - IELTS: 6.5 (TOEFL 90)
  - Intake: Winter + Summer
  - Deadline: Winter → May 15, Summer → November 15
  - Portal: Direct (KIT Campus Management)
  - APS: Required
  - Notes: Top research output; strong in theoretical CS and AI.

**5. FAU Erlangen-Nürnberg**
- City: Erlangen/Nuremberg, Bavaria
- MS Artificial Intelligence (dedicated AI program — rare at this level)
  - Language: English
  - Min German grade: 2.5
  - IELTS: 7.0 (TOEFL 95)
  - Intake: Winter only
  - Deadline: May 15
  - Portal: Direct (campo)
  - APS: Required
  - Notes: One of the few dedicated MS AI programs at a public German university.

**6. LMU Munich**
- City: Munich, Bavaria
- MS Data Science
  - Language: English
  - Min German grade: 2.5
  - IELTS: 7.0 (C1 accepted)
  - Intake: Winter only
  - Deadline: January 15
  - Portal: Direct (LMU Portal)
  - APS: Required
  - Notes: Joint program with TUM in some modules. Very data-engineering focused.

**7. University of Stuttgart**
- City: Stuttgart, Baden-Württemberg
- MS Computer Science
  - Language: English
  - Min German grade: 2.5
  - IELTS: 6.5
  - Intake: Winter + Summer
  - Deadline: Winter → May 15
  - Portal: Direct
  - APS: Required
  - Notes: Strong in robotics, autonomous systems, and HCI.

**8. Saarland University**
- City: Saarbrücken, Saarland
- MS Computer Science
  - Language: English
  - Min German grade: 2.8 (more accessible threshold)
  - IELTS: 6.0
  - Intake: Winter + Summer
  - Deadline: Winter → May 31
  - Portal: Direct
  - APS: Required
  - Notes: Home to Max Planck Institute for CS and CISPA. Excellent for research track.

**9. TU Dresden**
- City: Dresden, Saxony
- MS Computer Science
  - Language: English
  - Min German grade: 2.5
  - IELTS: 7.0
  - Intake: Winter only
  - Deadline: May 31
  - Portal: uni-assist
  - APS: Required
  - Notes: Part of the German Excellence Initiative universities.

**10. University of Bonn**
- City: Bonn, NRW
- MS Computer Science
  - Language: English
  - Min German grade: 2.5
  - IELTS: 6.5
  - Intake: Winter + Summer
  - Deadline: May 31 for Winter
  - Portal: Direct
  - APS: Required
  - Notes: Close ties to Fraunhofer IAIS (AI research institute).

**11. University of Freiburg**
- City: Freiburg, Baden-Württemberg
- MS Computer Science
  - Language: English (partial German)
  - Min German grade: 2.8
  - IELTS: 6.5
  - Intake: Winter only
  - Deadline: May 15
  - Portal: uni-assist
  - APS: Required
  - Notes: Known for bioinformatics and machine learning research groups.

**12. University of Hamburg**
- City: Hamburg
- MS Computer Science
  - Language: English + German (bilingual)
  - Min German grade: 2.5
  - German: B2 required for bilingual modules
  - IELTS: 6.5
  - Intake: Winter only
  - Deadline: June 1
  - Portal: Direct
  - APS: Required
  - Notes: Largest city campus in Germany; strong industry access.

#### BS Programs (IMPORTANT NOTE)

> ⚠️ **Critical context for developers:** Virtually all BS Computer Science/AI programs at German public universities are taught **in German**. The BS section of the app must clearly inform users of this upfront. The pathway to an English-taught degree in Germany is almost exclusively at the MS level or through private universities (which have tuition fees).

**13. TU Munich — BS Informatics**
- Language: German
- Requirement: DSH-2 or TestDaF (4x4) — equivalent to C1
- Min German grade: ~2.5 (high competition)
- Intake: Winter only
- Deadline: January 15
- APS: Required
- Note: Hochschulzugangsberechtigung (HZB) equivalency check via anabin required

**14. RWTH Aachen — BS Computer Science**
- Language: German
- Requirement: DSH-2 or TestDaF (4x4)
- No minimum GPA stated (NC/numerus clausus applies based on applicant pool)
- Intake: Winter only
- Deadline: July 15
- APS: Required

**15. TU Berlin — BS Computer Science**
- Language: German
- Requirement: DSH-2 or TestDaF (4x4)
- Intake: Winter + Summer
- NC applies
- APS: Required

**16. KIT — BS Computer Science**
- Language: German
- Requirement: DSH-2 or TestDaF (4x4)
- Intake: Winter only
- APS: Required
- Note: Admission partially lottery-based for NC-restricted programs

**17. University of Bonn — BS Computer Science**
- Language: German
- Requirement: DSH-2 or TestDaF
- Intake: Winter only
- Deadline: July 15
- APS: Required

---

## 10. APS Certificate — Critical Requirement

**APS (Academic Evaluation Centre)** is a mandatory credential check for students from specific countries applying to German universities. This is NOT optional and delays of 6–8 weeks are common.

**Countries requiring APS:**
Pakistan, India, China, Vietnam, Mongolia (partial list — verify current list at aps-pakistanembassy.de or the respective APS authority)

**The app must:**
1. Detect if the user's nationality is from an APS-required country
2. Display a prominent warning banner in the results view
3. Show APS as a ✗ missing requirement on every university card if `aps_certified = false`
4. Include the APS processing timeline note: *"APS certificates take 6–8 weeks. Apply early."*

**APS Pakistan contact:** German Embassy Islamabad — aps-islamabad.diplo.de

---

## 11. Universal Germany Checklist (Show in Results Sidebar)

These apply to ALL applicants regardless of university:

- [ ] APS Certificate *(if applicable by nationality)*
- [ ] Valid Passport (min 6 months beyond study end)
- [ ] Blocked account — €11,208/year (required for student visa)
- [ ] Health Insurance (German statutory or recognized private)
- [ ] Degree certificates + transcripts (officially translated to German or English)
- [ ] Motivation Letter (1–2 pages, program-specific)
- [ ] CV / Résumé (academic format)
- [ ] Language certificate (IELTS / TOEFL for English programs; DSH / TestDaF for German)
- [ ] 2× Recommendation Letters *(most MS programs)*
- [ ] HZB Equivalency *(Bachelor applicants — verify degree recognized via anabin.kmk.org)*
- [ ] Student visa (applied at German embassy in home country after acceptance)

---

## 12. Matching Logic (Client-Side, Pre-Groq)

Run this before the Groq call. Store results in a `matchResults` object per university per program.

```js
function matchUniversity(profile, program) {
  const issues = [];
  const passes = [];

  // 1. GPA
  if (profile.german_grade > program.min_german_grade) {
    issues.push({ field: 'GPA', detail: `Minimum ${program.min_german_grade} required, you have ${profile.german_grade.toFixed(2)}` });
  } else {
    passes.push({ field: 'GPA', detail: 'Meets minimum GPA requirement' });
  }

  // 2. English requirement (for English-taught programs)
  if (program.language === 'English' || program.language === 'Bilingual') {
    if (profile.ielts !== null && profile.ielts < program.english_requirement.ielts_min) {
      issues.push({ field: 'IELTS', detail: `Minimum ${program.english_requirement.ielts_min} required, you have ${profile.ielts}` });
    } else if (profile.ielts !== null) {
      passes.push({ field: 'IELTS', detail: 'English score meets requirement' });
    }
  }

  // 3. German language (for German/Bilingual programs)
  if (program.language === 'German' || program.language === 'Bilingual') {
    const required = program.german_requirement.min_level;
    const userLevel = profile.german_level;
    if (!meetsGermanRequirement(userLevel, required)) {
      issues.push({ field: 'German', detail: `${required} required, you have ${userLevel || 'None'}` });
    } else {
      passes.push({ field: 'German', detail: 'German language level meets requirement' });
    }
  }

  // 4. APS
  const apsRequired = program.aps_required_nationalities.includes(profile.nationality);
  if (apsRequired && !profile.aps_certified) {
    issues.push({ field: 'APS Certificate', detail: 'APS Certificate required for your nationality. 6–8 weeks processing time.' });
  } else if (apsRequired) {
    passes.push({ field: 'APS Certificate', detail: 'APS certified ✓' });
  }

  // 5. Degree level
  if (program.degree === 'MS' && !profile.has_bachelor) {
    issues.push({ field: 'Bachelor Degree', detail: 'MS programs require a completed bachelor\'s degree' });
  }

  // 6. Intake availability
  if (profile.preferred_intake !== 'Both' && !program.intake.includes(profile.preferred_intake)) {
    issues.push({ field: 'Intake', detail: `This program only offers ${program.intake.join('/')} intake` });
  }

  // Determine tier
  let tier;
  if (issues.length === 0) tier = 'strong';
  else if (issues.length <= 2) tier = 'borderline';
  else tier = 'not_eligible';

  return { tier, issues, passes };
}

// German level ordering for comparison
function meetsGermanRequirement(userLevel, required) {
  const order = ['None','A1','A2','B1','B2','C1','C2','DSH-1','DSH-2','TestDaF-4x4'];
  // DSH-2 and TestDaF-4x4 are equivalent to C1
  const normalize = (l) => {
    if (l === 'DSH-2' || l === 'TestDaF-4x4') return 8; // maps to C1 level
    if (l === 'DSH-1') return 7;
    return order.indexOf(l);
  };
  return normalize(userLevel) >= normalize(required);
}
```

---

## 13. UI / Design Specification

### Design Direction
**Concept:** Premium admissions consultancy — not a student forum, not a government portal. Think Palantir's data dashboard aesthetic crossed with a high-end editorial magazine layout. Structured, authoritative, clean. Every element earns its place.

**Signature element:** The results tier system — a subtle left-border color rule on each card (green / amber / muted red) paired with a crisp monospaced match score, not a percentage ring or progress bar.

### Color Palette
```
--bg-primary:     #0D0F12    /* near-black canvas */
--bg-surface:     #161A20    /* card / panel background */
--bg-elevated:    #1E2229    /* hover state / secondary surface */
--border:         #2A2F38    /* subtle borders */
--text-primary:   #F0F2F5    /* headings, labels */
--text-secondary: #8A9099    /* subtext, meta */
--text-muted:     #4A5260    /* placeholder, disabled */
--accent-green:   #22C55E    /* strong match / pass */
--accent-amber:   #F59E0B    /* borderline / warning */
--accent-red:     #EF4444    /* not eligible / missing */
--accent-blue:    #3B82F6    /* interactive / links */
--accent-blue-dim:#1D4ED8    /* button background */
```

### Typography
```
Display / Hero:   'Inter' 700, letter-spacing -0.03em, size 2.5–4rem
Headings:         'Inter' 600, -0.02em
Body:             'Inter' 400, 0.02em, line-height 1.6
Labels / Tags:    'JetBrains Mono' 500, uppercase, 0.08em tracking
Data / Numbers:   'JetBrains Mono' 400
```
Both fonts available via Google Fonts CDN.

### Layout
- Max content width: 1100px, centered
- Form: single-column, 560px max-width, vertically stacked
- Results: left sidebar (280px universal checklist) + right main (university cards)  
  On mobile: sidebar collapses to a collapsible accordion at the bottom
- Progress indicator: 2-dot stepper at top of form (Step 1 / Step 2)
- No hero image. Hero is the headline only.

### Component Inventory

**1. `<StepIndicator />`** — minimal dots or dashes showing current step

**2. `<ProfileForm />`** — Step 1 form
  - Field: Nationality (dropdown, searchable)
  - Field: Applying for (BS / MS toggle)
  - Field: GPA format (Percentage / 4.0 Scale / German Scale — radio pills)
  - Field: GPA value (number input, validates range based on format)
  - Field: IELTS score (number input 0–9, 0.5 steps) or TOEFL or "Exempt / Not taken"
  - Field: German language level (dropdown: None through C2 / DSH / TestDaF)
  - Field: APS Certificate (Yes / No / Not Applicable radio)
  - Field: Target field (pill multi-select: CS, AI, Data Science, Software Engineering)

**3. `<PreferencesForm />`** — Step 2
  - Field: City preference (multi-select chips: Munich, Berlin, Hamburg, Aachen, Karlsruhe, Dresden, Bonn, Saarbrücken, Stuttgart, Freiburg, Erlangen, No Preference)
  - Field: Intake semester (Winter / Summer / Both)
  - Field: Language of instruction (German / English / Both)

**4. `<AnalysisLoader />`** — full-screen or card-sized loading state
  - Subtle animated state transitions
  - Copy: "Checking 12 universities...", "Running gap analysis...", "Writing your consultation..."

**5. `<ConsultationSummary />`** — Groq output
  - Rendered at top of results
  - Styled as a pull-quote or advisory note
  - Avatar/icon: small consultant icon, not a photo

**6. `<UniversityCard />`**
```
┌─────────────────────────────────────────────────────────┐
│ ║ TU Munich                              STRONG MATCH   │  ← left border color = tier color
│   Munich, Bavaria · QS #50 CS                           │
│   MS Informatics · English · Winter only                │
│                                                         │
│   ✓ GPA meets threshold (2.20 / required ≤ 2.50)       │
│   ✓ IELTS 7.0 meets requirement (min 7.0)               │
│   ✗ APS Certificate — not yet obtained                  │
│                                                         │
│   Deadline: Jan 15  ·  Apply via: TUMonline (direct)   │
│   Tuition: Free · Semester fee: ~€150                   │
└─────────────────────────────────────────────────────────┘
```

**7. `<UniversalChecklist />`** — sidebar panel showing the Germany-wide requirements all applicants need, independent of university.

**8. `<BSWarningBanner />`** — shown at top of BS results:
> *"Almost all BS programs at German public universities are taught in German. If you're targeting an English-language bachelor's, you'll need to look at private institutions (fees apply) or plan to reach DSH-2 / TestDaF level first."*

### Interaction Notes
- Form fields use smooth focus rings (accent-blue, 2px offset)
- Pill/toggle selectors use animated background slide, not checkbox default
- University cards have hover lift (subtle translateY + shadow)
- No modals. No tooltips (use inline contextual copy instead)
- No skeleton loaders — use the animated `<AnalysisLoader />` component instead

---

## 14. API Key Handling

For demo / hackathon use:
- Accept GROQ_API_KEY as a constant at the top of the file
- Consider a minimal "Enter your Groq API key" gate at the top of Step 1 if distributing publicly

Groq free tier limits:
- 14,400 requests/day
- 6,000 tokens/minute
- More than sufficient for this app

---

## 15. Error States

| Scenario | Handling |
|----------|----------|
| Groq API fails / timeout | Show pre-computed match results without narrative. Display: *"AI summary unavailable. Showing raw match analysis."* |
| No universities match (all tier 3) | Show all tier-3 cards with full gap analysis + encouraging note on what to improve |
| User submits BS + English-only preference | Show BS warning banner + suggest pivoting to MS or German language prep |
| Invalid GPA input (e.g. percentage > 100) | Inline field error, red border, no form submit |

---

## 16. Out of Scope (v1)

- Private universities (Constructor University Bremen, etc.)
- Countries other than Germany
- Fields outside CS / AI / Data Science / Software Engineering
- Scholarship matching
- Application tracking / saved state
- User accounts / authentication
- Backend / database
- PDF export (P2)
- Live deadlines (data is manually curated, not scraped)

---

## 17. File Structure

Since this is a single-file app for Antigravity IDE:

```
AdmitDE.jsx  (or AdmitDE.html if no JSX transpiler)
  ├── UNIVERSITY_DATA (const JSON array)
  ├── GPA conversion utilities
  ├── Matching engine functions
  ├── Groq API call function
  ├── React components (all inline)
  └── CSS (inline <style> tag or Tailwind classes)
```

---

## 18. Acceptance Criteria

The app is considered complete when:

- [ ] A user can complete both form steps without errors
- [ ] GPA in percentage (Pakistani format) is correctly converted to German scale
- [ ] APS warning appears for Pakistani nationality
- [ ] At least 12 universities render with real requirements
- [ ] Groq API returns a consultation summary that references the user's actual data
- [ ] University cards correctly show ✓ / ✗ per requirement
- [ ] BS applicants see the German-language warning prominently
- [ ] Results are sorted: Strong Match → Borderline → Not Eligible
- [ ] Layout works on mobile (375px width minimum)
- [ ] "Start Over" resets the form cleanly
- [ ] App works with a valid Groq API key with no other credentials needed

---

## 19. References / Data Sources

- DAAD (German Academic Exchange Service): daad.de/en/
- uni-assist: uni-assist.de
- anabin (degree recognition database): anabin.kmk.org
- APS Pakistan: aps-islamabad.diplo.de
- TU Munich admissions: portal.mytum.de
- RWTH Aachen: rwth-aachen.de/go/id/jlgb
- Groq API docs: console.groq.com/docs
- German Student Visa & Blocked Account: germany-visa.org/student-visa/

---

*Document prepared for developer handoff. All university requirements sourced from official admissions pages as of mid-2026. Deadlines subject to annual change — verify before launch.*
