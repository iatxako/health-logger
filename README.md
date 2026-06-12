# 🩺 health-logger

**Generic personal health documentation system for [Hermes Agent](https://hermes-agent.nousresearch.com)**

Track symptoms, treatments, appointments, and clinical questions using clinical-grade methodology — all from natural conversation. Built on SOAP + OLDCHARTS + PGHD standards.

> ⚠️ **This is a generic skill.** It contains zero personal data. Fork it, adapt it, use it for your own health tracking.

---

## ✨ What it does

| You say... | The agent does |
|-----------|---------------|
| *"I've had a headache for 3 days"* | Creates a structured SOAP log entry with severity, timing, triggers |
| *"I need to ask the doctor about this"* | Saves a question with context, ready for your next appointment |
| *"The medication isn't helping"* | Updates treatment effectiveness tracking |
| *"Good night, slept 7h"* | Logs sleep quality and duration |

## 🏗️ Architecture

**7 interconnected Notion databases** that form a complete personal health record:

```
👤 Profile          → Demographics, allergies, base data
🏥 Health problems  → Diagnoses, active/past conditions
💊 Treatments       → Medications, supplements, regimens
📓 Daily log        → Symptoms, food, sleep, bowel movements (PGHD core)
❓ Questions        → Doubts for the doctor, clinical alerts
📋 Clinical events  → Tests, consults, procedures
🔍 Hypotheses      → Inferences, patterns, suspected triggers
```

All entries carry **dual-axis traceability**: *Origin* (who said it) + *Clinical status* (how reliable).

## 🧠 Methodology

| Standard | What it tracks |
|----------|---------------|
| **SOAP** | Subjective — Objective — Assessment — Plan |
| **OLDCHARTS** | Onset, Location, Duration, Character, Alleviating/Aggravating, Radiation/Associated, Temporal, Severity |
| **PGHD** | Patient-Generated Health Data best practices |
| **Bristol Stool Scale** | Standardized stool classification (types 1–7) |
| **Traffic-light triage** | 🔴 Urgent / 🟡 Consult / 🟢 Normal |

## 🚀 Quick start

### 1. Prerequisites

- [Hermes Agent](https://hermes-agent.nousresearch.com) installed
- A [Notion](https://notion.so) workspace
- Notion API integration token with write access

### 2. Install the skill

```bash
# Clone the repo into your Hermes skills directory
cp SKILL.md ~/.hermes/skills/health-logger/

# Or if using a profile:
cp SKILL.md ~/.hermes/profiles/<profile>/skills/health-logger/
```

### 3. Create the databases

Create 7 databases under a health page in Notion with the properties defined in [`SKILL.md`](SKILL.md). The minimum viable setup is:

1. **Daily log** — just need Date + Entry type + Description + Severity
2. **Health problems** — Title + Status
3. **Questions** — Title + Status

(Add the rest as you grow.)

### 4. Grant access

Make sure your Notion integration has access to the health page and all 7 databases.

### 5. Start talking

Just tell your agent about your health. It handles the rest automatically.

## 📋 Database schemas (summary)

Full schemas with all properties and relation links are in [`SKILL.md`](SKILL.md). Key highlights:

### 📓 Daily log (most-used database)

| Property | Type | Example |
|----------|------|---------|
| Date | Date (with time) | 2026-06-12T14:30 |
| Entry type | Select | Symptom / Food-intake / Bowel-movement / Sleep / Mood-stress |
| Description | Text | "Sharp pain in lower right abdomen after lunch" |
| Severity (1-10) | Number | 6 |
| Triggers | Multi-Select | caffeine, gluten, stress |
| Bristol score | Select | Type 4 |
| Sleep - quality | Select (1-5) | 3 |

### ❓ Questions & alerts

| Property | Type | Options |
|----------|------|---------|
| Type | Select | Question / Alert / Reminder |
| Priority | Select | 🔴 High / 🟡 Medium / 🟢 Low |
| Status | Select | Pending / Answered / Archived |
| Target specialist | Text | "Dr. García, Digestive" |

### 💊 Treatments

| Property | Type | Options |
|----------|------|---------|
| Type | Select | Medication / Supplement / Probiotic / Diet |
| Status | Select | Active / Paused / Suspended / Historical |
| Perceived effectiveness | Select | High / Moderate / Low / None / Worsens |

## ⚠️ Safety rules built into the skill

- ❌ **Never suggest treatment changes** — only log what the user says
- ❌ **Never diagnose** — observations only
- ❌ **Never write to Questions without explicit approval** — suggest as text first
- ✅ Observations like *"this matches the pattern of..."* are OK
- ✅ **Dual traceability** — facts from patient vs facts from doctor are always distinguished

## 📚 References

- **SOAP notes**: Medical documentation standard
- **OLDCHARTS**: Symptom characterization mnemonic
- **POMR**: Problem-Oriented Medical Record (Lawrence Weed, 1968)
- **PGHD**: Patient-Generated Health Data framework (NIH / FDA)
- **Bristol Stool Scale**: Heaton & Lewis, 1997

## 📄 License

MIT — Free to use, modify, and share. Methodology based on open clinical documentation standards.

---

*Made for Hermes Agent. Not a substitute for professional medical advice.*
