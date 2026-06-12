---
name: health-logger
description: "Generic personal health logging system. Use when the user reports symptoms, talks about health/sleep/digestion, mentions doctor appointments. Creates structured SOAP entries + tracks problems, treatments, clinical events, and doctor questions in Notion databases."
version: 1.0.0
author: community
license: MIT
metadata:
  hermes:
    tags: [health, symptoms, soap, triage, notion, medical, pgHD, personal-record]
    related_skills: [notion]
---

# Health Logger — Personal Health Documentation System

Generic skill for tracking personal health with clinical-grade methodology. Designed for anyone who wants to keep a structured health log compatible with PGHD (Patient Generated Health Data) standards.

## How it works

This skill defines **7 Notion databases** that work together:

| Database | Purpose |
|----------|---------|
| 👤 Profile / Base data | Demographics, height, weight, allergies |
| 🏥 Health problems | Diagnoses, active/past conditions |
| 💊 Treatments | Medications, supplements, regimens |
| 📓 Daily log | PGHD core: symptoms, food, sleep, bowel movements |
| ❓ Questions & alerts | Actionable doubts for doctor visits |
| 📋 Clinical events | Tests, consults, procedures |
| 🔍 Hypotheses / patterns | Inferences, correlations, suspected triggers |

All databases share **dual-axis traceability**: Origin + Clinical status.

## Setup

### 1. Create the databases

Create 7 databases in a Notion page (e.g., "🩺 My Health"). Use the properties defined in the sections below.

Recommended parent: a page called "🩺 Health" under your main Notion workspace.

### 2. Configure the agent

The agent needs:
- Notion API integration token with access to the health databases
- The skill loaded in the agent's skills list

### 3. Start logging

Just talk about your health — the agent processes everything automatically.

## 🧾 Ingestion rule (mandatory)

Every time the user mentions health, follow this pipeline:

```
Original text (audio/text)
    │
    ▼
1️⃣ Clean form (fillers, repetitions) WITHOUT altering clinical meaning
    │
    ▼
2️⃣ Save to 📄 Clean source text field
    │
    ▼
3️⃣ Assign Origin:
    ── patient      → user said it directly
    ── medical document → from a report/prescription
    ── professional → from a doctor/nurse/specialist
    ── AI           → agent inference
    │
    ▼
4️⃣ Assign Clinical status:
    ── confirmed    → validated by professional or document
    ── observed     → reported by patient, unconfirmed
    ── inferred     → pattern-based deduction, no direct evidence
    ── uncertain    → contradictory or unreliable
    │
    ▼
5️⃣ Patient's literal facts  → Clinical status = observed
   Document/doctor facts     → Clinical status = confirmed
   Agent interpretations     → NEVER to clinical facts
   Agent interpretations     → to Hypotheses database or Questions
    │
    ▼
6️⃣ Write to correct database with all available fields
```

### ⚠️ Absolute prohibitions
- ❌ Never mix inference with fact in the same record
- ❌ Never write a hypothesis as if it were a confirmed fact
- ❌ Never skip the Clean source text field
- ❌ Never alter the patient's clinical meaning when cleaning text

## 📐 Database schemas

### 👤 Profile / Base data

| Property | Type | Options |
|----------|------|---------|
| Name | Title | |
| Date of birth | Date | |
| Height (cm) | Number | |
| Usual weight (kg) | Number | |
| Sex | Select | Male / Female / Other |
| Allergies / intolerances | Multi-Select | gluten, lactose, caffeine, histamine, fructose, etc. |
| Notes | Text | |
| Origin | Select | patient / medical document / professional / AI |
| Clinical status | Select | confirmed / observed / inferred / uncertain |
| 📄 Clean source text | Text | |

### 🏥 Health problems

| Property | Type | Purpose |
|----------|------|---------|
| Name | Title | Condition name |
| Category | Select | Digestive / Neurological / Dermatological / Musculoskeletal / ENT / Dental / Other |
| Status | Select | Active / Partial remission / Complete remission / Resolved / Monitoring |
| Start date | Date | |
| Resolution date | Date | |
| Specialist | Text | Doctor's name |
| Intolerances | Multi-Select | Temporary (SSOT is Profile database) |
| Notes | Text | |
| Related profile | Relation | → Profile |
| Related events | Relation | → Clinical events |
| Related hypotheses | Relation | → Hypotheses |
| Origin | Select | |
| Clinical status | Select | |
| 📄 Clean source text | Text | |

### 💊 Treatments

| Property | Type | Purpose |
|----------|------|---------|
| Name | Title | |
| Type | Select | Medication / Supplement / Probiotic / Herbal / Diet / Other |
| Status | Select | Active / Paused / Suspended / Historical |
| Regimen | Select | Fixed (continuous) / On demand |
| Start date | Date | |
| End date | Date | |
| Dosage | Text | e.g. "1 capsule/12h with food" |
| Route | Select | Oral / Topical / Subcutaneous / Inhaled |
| Reason | Text | |
| Prescriber | Text | |
| Perceived effectiveness | Select | High / Moderate / Low / None / Not yet evaluated / Worsens |
| Side effects | Text | |
| Related problems | Relation (N:N) | → Health problems |
| Related daily logs | Relation (N:N) | → Daily log |
| Origin | Select | |
| Clinical status | Select | |
| 📄 Clean source text | Text | |

### 📓 Daily log (PGHD core)

| Property | Type | Purpose |
|----------|------|---------|
| Name | Title | Auto-generated |
| Date | Date (with time) | |
| Entry type | Select | Symptom / Food-intake / Bowel-movement / Sleep / Mood-stress / Activity / Medication-taken / General-state / Free-note |
| Subtype | Select | Depends on entry type |
| Start time | Date (time) | |
| Duration | Text | |
| Description | Text | Free narrative |
| Severity (1-10) | Number | |
| Pain (0-10) | Number | Optional |
| Taking time | Date (time) | For medication entries |
| Bowel - number | Number | |
| Bowel - Bristol | Select | Bristol 1-7 |
| Bowel - urgency | Select | Normal / Mild / Moderate / Urgent |
| Bowel - strain | Select | Normal / With strain / Incomplete evacuation / Painful |
| Blood with stool | Select | Yes / No / N/A |
| Pain with stool | Select | None / Mild / Moderate / Severe / N/A |
| Incontinence | Select | Yes / No / N/A |
| Triggers (normalized) | Multi-Select | caffeine, alcohol, gluten, dairy, histamine, eating-out, high-stress, poor-sleep, exercise, medication, other |
| Triggers (free text) | Text | |
| Time window | Select | Early morning / Morning / Midday / Afternoon / Night |
| Food detail | Text | |
| Food amount | Select | Little / Normal / Much |
| Sleep - hours | Number | |
| Sleep - quality | Select | 1-Poor to 5-Excellent |
| Hydration | Select | Normal / Low / Very low / N/A |
| Stress (1-10) | Number | |
| Weight (kg) | Number | Optional, for tracking |
| Tags | Multi-Select | Flexible categorization |
| Related problems | Relation (N:N) | → Health problems |
| Related treatments | Relation (N:N) | → Treatments taken |
| Origin | Select | |
| Clinical status | Select | |
| 📄 Clean source text | Text | |

### ❓ Questions & alerts

| Property | Type | Purpose |
|----------|------|---------|
| Title | Title | The question |
| Type | Select | Question for doctor / Clinical alert / Reminder / Important observation |
| Priority | Select | 🔴 High / 🟡 Medium / 🟢 Low |
| Status | Select | Pending / In consultation / Answered / Archived |
| Target specialist | Text | Doctor's name |
| Next appointment | Date | |
| Created date | Date | |
| Answer date | Date | |
| Answer | Text | |
| Context | Text | Brief reason |
| Related problem | Relation | → Health problems |
| Origin | Select | |
| Clinical status | Select | |
| 📄 Clean source text | Text | |

### 📋 Clinical events

| Property | Type | Purpose |
|----------|------|---------|
| Name | Title | Event description |
| Type | Select | Diagnostic test / MRI / CT / Ultrasound / Blood test / Biopsy / Consultation / Procedure / Emergency / Other |
| Specialty | Select | Traumatology / Digestive / Radiology / Dermatology / Neurology / Rehabilitation / Dental / ENT / Other |
| Date | Date | |
| Center | Text | |
| Professional | Text | |
| Reason | Text | |
| Findings | Text | Results |
| Conclusion | Text | |
| Related problems | Relation | → Health problems |
| Origin | Select | |
| Clinical status | Select | |
| 📄 Clean source text | Text | |

### 🔍 Hypotheses / patterns

| Property | Type | Purpose |
|----------|------|---------|
| Name | Title | |
| Category | Select | Pathophysiology / Trigger / Clinical correlation / Temporal pattern / Food / Other |
| Description | Text | |
| Proposed mechanism | Text | |
| Evidence | Text | |
| Confidence level | Select | High - multiple evidence / Medium - some evidence / Low - initial suspicion / Pending |
| Related problems | Relation | → Health problems |
| Tags | Multi-Select | vagal, MMC, histamine, gut-brain, SIBO, H.pylori, etc. |
| Origin | Select | |
| Clinical status | Select | |
| 📄 Clean source text | Text | |

## 🔄 Workflow

### Phase 0: Check Notion access
Verify the agent's integration has access to the health databases.

### Phase 1: Read context
Before responding, query:
- **Daily log** — last 5 entries for recent context
- **Treatments** — only active ones
- **Questions** — pending, filter by Question type
- **Health problems** — first time in session only

### Phase 2: Create SOAP entry using OLDCHARTS
For each symptom, document all axes:

| Axis | Guide question |
|------|---------------|
| **O**nset | When did it start? Sudden or gradual? |
| **L**ocation | Where exactly? |
| **D**uration | How long per episode? |
| **C**haracter | How would you describe it? |
| **A**lleviating/Aggravating | What makes it better/worse? |
| **R**adiation / Associated | Does it spread? Other symptoms? |
| **T**emporal pattern | Time of day? Relation to meals/sleep? |
| **S**everity | 1-10 scale. Compare to previous episodes. |

### Phase 3: Triage traffic light

| Color | Criteria | Example |
|-------|----------|---------|
| 🔴 **Red** | May need medical attention. Severe pain, bleeding, fever, loss of consciousness | "Can't move from pain" |
| 🟡 **Yellow** | Consult if persists. Bothersome but not urgent. First time or worse than usual | New symptom, persistent issue |
| 🟢 **Green** | Normal/mild, known, no concern | Occasional mild discomfort |

### Phase 4: Clinical signals checklist

- ⚠️ Worsening vs last time?
- ⚠️ Sleep disturbance?
- ⚠️ Functional impairment?
- ⚠️ Delayed care (3+ days)?
- ⚠️ Contradiction with previous entries?

### Phase 5: Questions & alerts
Auto-create entries when clinical doubts arise (only if user authorizes).

### Phase 6: Response rules

1. ❌ **Never** suggest treatment changes or dosages
2. ❌ **Never** diagnose — not even "this looks like..."
3. ✅ **Only** observations like "this matches the pattern of..."
4. ✅ **Potential questions for the doctor** — as text suggestion ONLY, never write to database without explicit approval

**Default response:**
- Confirm what was registered: "Logged ✅"
- If relevant: "This could be a good question for your doctor."
- Wait for "yes, add it" before creating anything in Questions.

## 📚 Sources / methodology

- **SOAP notes** — medical documentation standard (Subjective-Objective-Assessment-Plan)
- **OLDCHARTS** — mnemonic for symptom characterization
- **POMR** — Problem-Oriented Medical Record (Lawrence Weed)
- **PGHD** — Patient Generated Health Data framework
- **Bristol Stool Scale** — standardized stool classification

## License

MIT — free to use, modify, and share. The methodology is based on open clinical documentation standards.
