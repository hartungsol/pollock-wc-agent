# Pollock Workers' Compensation Defeasibility Agent

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Google ADK](https://img.shields.io/badge/Google-ADK-4285F4.svg)](https://github.com/google/adk-python)

A **Pollock-style defeasible reasoning** system for workers' compensation claim analysis, built with **Google Agent Development Kit (ADK)**.

> 🏆 Developed as part of the **Kaggle Agents Intensive Capstone**

## Overview

This project implements a sophisticated multi-agent system for workers' compensation claim processing using John Pollock's epistemological framework. Claims are analyzed through a belief-rule-defeater architecture where conclusions are provisional and revisable as new evidence emerges.

## Key Features

| Feature | Description |
|---------|-------------|
| **Defeasible Reasoning** | Conclusions are provisional and revisable based on new evidence |
| **OSCAR-Style Architecture** | Prima facie vs conclusive reasons, argument graphs, global defeat |
| **Multi-Agent Orchestration** | Specialized agents for belief management, planning, analysis, and investigation |
| **Structured Defeaters** | Classifications (undercutting, rebutting, regulatory, evidential) with severity scores |
| **Forward Reasoning** | Automatically derives new beliefs from existing ones |
| **Backward Reasoning** | Decomposes goals into subgoals for gap analysis |
| **Human-in-the-Loop** | Interactive investigation with belief review and manual entry |

## Installation

### Prerequisites

- Python 3.10 or higher
- A Google API key with access to Gemini models

### Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/YOUR_USERNAME/pollock-wc-agent.git
   cd pollock-wc-agent
   ```

2. **Create and activate a virtual environment:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure your API key:**
   ```bash
   cp .env.example .env
   # Edit .env and add your GOOGLE_API_KEY
   ```

### Required Environment Variables

```bash
export GOOGLE_API_KEY="your-google-api-key"
```

Or create a `.env` file:
```
GOOGLE_API_KEY=your-google-api-key
```

## Notebook Structure

The main notebook `pollock_WC_Defeasibility_agent.ipynb` is organized as follows:

### Core Components

| Section | Description |
|---------|-------------|
| **1. Core Reasoning Models** | `Belief`, `Goal`, `PlanStep`, `Defeater` dataclasses |
| **2. SimpleContext Adapter** | Decouples reasoning from ADK for testing and demos |
| **3. OSCAR-Style Reasoning** | Reason objects, argument graphs, global defeat computation |
| **4. Forward & Backward Reasoning** | Automatic belief derivation and goal decomposition |
| **5. WC Domain Models** | Workers' compensation-specific extensions |

### Rule Engine

| Section | Description |
|---------|-------------|
| **6. Plan Initialization** | Generates standard WC plan steps |
| **7. Defeasibility Rules** | Employment eligibility, course of employment, late reporting, fraud detection |
| **8-12. Demos** | End-to-end scenarios for various claim types |

### Agent Pipeline

| Section | Description |
|---------|-------------|
| **13. Intake Agent** | Gemini-powered conversational claim intake |
| **14. Iterative Investigation** | Loop-style refinement with investigation agent |
| **15. Interactive Investigation** | Human-in-the-loop belief review and entry |
| **16. Final Recommendations** | Approval/denial recommendation generation |

## Key Concepts

### Defeater Types

| Type | Description |
|------|-------------|
| **Undercutting** | Attacks the inference connection, not the conclusion |
| **Rebutting** | Directly contradicts the conclusion with contrary evidence |
| **Regulatory** | Based on statutory/regulatory requirements |
| **Evidential** | Based on evidentiary gaps or conflicts |

### Plan Step Status

| Status | Condition | Meaning |
|--------|-----------|---------|
| `SUPPORTED` | No defeaters | Step proceeds normally |
| `QUESTIONED` | Severity < 0.8 | Step flagged for investigation |
| `DEFEATED` | Severity ≥ 0.8 | Step blocked; requires resolution |

### Workers' Compensation Rules

| Rule | Trigger | Effect |
|------|---------|--------|
| Employment Eligibility | Contractor/volunteer status | Defeats compensability |
| Course of Employment | Off-duty or outside scope | Defeats approval |
| Late Reporting | Beyond notice deadline | Flags for review |
| Fraud Red Flag | High fraud score | Requires SIU referral |
| Mandatory Review | Severe/catastrophic claim | Requires additional review |

## Usage

### Quick Start: Interactive Investigation

```python
# Run the interactive investigation loop
result = run_interactive_wc_investigation(
    intake_data={
        "jurisdiction": "Illinois",
        "employee_age": 35,
        "occupation": "Warehouse Worker",
        "date_of_injury": "2024-11-01",
        "injury_description": "Back strain from lifting boxes",
        "work_related": True,
        "date_reported": "2024-11-03",
        "prior_injuries": False,
        "current_work_status": "light duty",
    },
    verbose=True
)
```

### Programmatic Analysis

```python
# Seed state from intake data
state = seed_state_from_intake(intake_data)

# Run iterative analysis
result = run_iterative_wc_analysis(state, max_iterations=5, verbose=True)

# Generate final recommendation
recommendation = generate_final_recommendation(result)
print_final_recommendation(recommendation)
```

### Forward & Backward Reasoning Demo

```python
# Run forward reasoning to derive new beliefs
forward_count = run_forward_reasoning_step(state)

# Run backward analysis for key goals
run_backward_analysis_for_key_goals(state)

# Display backward analysis results
display_backward_analysis(state)
```

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     Claim Intake Agent                          │
│              (Gemini-powered conversational Q&A)                │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   State Initialization                          │
│         (seed_state_from_intake → beliefs, goals, plan)         │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   Forward Reasoning                             │
│         (Derive new beliefs from existing premises)             │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   Rule Evaluation                               │
│   (Employment, Course of Employment, Fraud, Late Reporting)    │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   Backward Reasoning                            │
│         (Analyze subgoals for key decisions)                    │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                Investigation Agent Loop                          │
│   (Suggests beliefs for QUESTIONED steps, human-in-the-loop)   │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│               Final Recommendation Generation                   │
│        (APPROVE / DENY / ESCALATE with justification)          │
└─────────────────────────────────────────────────────────────────┘
```

## Key Goals for Backward Analysis

| Goal | Description |
|------|-------------|
| `decision:approve_claim` | Approve the workers' comp claim |
| `decision:deny_claim` | Deny the workers' comp claim |
| `analysis:compensability_confirmed=true` | Confirm compensability |
| `decision:escalate_claim` | Escalate to special investigation |

## Demo Scenarios

The notebook includes several pre-built demo scenarios:

1. **Standard Compensable Claim** - Illinois back strain, clean case
2. **Contractor Exclusion** - Independent contractor, non-compensable
3. **Late Reporting** - Notice deadline violation
4. **Fraud Red Flags** - Elevated fraud indicators
5. **Interactive Investigation** - Human-in-the-loop resolution

## Dependencies

- `google-adk` - Google Agent Development Kit
- `google-genai` - Google Generative AI
- `pydantic` - Data validation
- `nest-asyncio` - Async support in notebooks
- `python-dotenv` - Environment variable management

## References

- [John Pollock's OSCAR Project](https://oscarhome.soc-sci.arizona.edu/ftp/OSCAR-web-page/OSCAR.html)
- [Defeasible Reasoning (SEP)](https://plato.stanford.edu/entries/reasoning-defeasible/)
- [Google ADK Documentation](https://github.com/google/adk-python)

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

<p align="center">
  <i>Built with ❤️ using Google Agent Development Kit</i>
</p>
