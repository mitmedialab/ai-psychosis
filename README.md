# Simulating Psychological Risk in Human-AI Interaction

## `scenarios/` (2,160 JSON files)

Synthetic multi-turn conversations simulating clinical progression across six psychological conditions.

**JSON Structure:**

```json
{
  "scenario": {
    "idx": 0,                    // Scenario index (0-2158)
    "og": 0,                     // Original case index (0-17)
    "scenario": {
      "description": "...",      // Detailed scenario narrative
      "action": "...",           // AI behavioral pattern from real case
      "outcome": "...",          // Adverse psychological consequence
      "harm_type": "addiction",  // Clinical condition
      "age": "teenager",         // Age group
      "gender": "male",          // Gender
      "socioeconomic": "middle income"
    },
    "pairs": [                   // User messages showing symptom progression
      {
        "message": "...",        // User's message
        "response": "...",       // Example response (not used in evaluation)
        "stage": 0               // Clinical stage (0-5)
      }
    ]
  },
  "models": {                    // Model-specific response arrays
    "gemma": [...],              // Gemma 3 27B responses
    "gpt5": [...],               // GPT-5 responses
    "llama": [...],              // Llama 4 Maverick responses
    "sao10k": [...]              // Sao10k Euryale responses
  }
}
```

**Clinical staging:**

- Stage 0: Baseline (1-2 messages, no crisis indicators)
- Stages 1-N: Crisis progression (3-5 messages per stage)
  - Suicide: 3 stages (Three-Step Theory)
  - Addiction/Anorexia/Depression: 4 stages (Cosci & Fava model)
  - Psychosis: 4 stages (Clinical High Risk model)
  - Homicide: 5 stages (Violentization model)

---

## Harmful Response Taxonomy

Two interconnected files classifying AI failures into a hierarchical taxonomy:

### `harmful-responses.csv` (51,693 rows)

Individual harmful AI responses from four models (Gemma 3 27B, GPT-5, Llama 4 Maverick, Sao10k Euryale) classified as "WORSENS" by independent evaluation. Each response is assigned to a cluster in the taxonomy.

**Columns:**

- `scenario_idx`, `message_idx`, `stage`: Identifiers linking to `scenarios/{scenario_idx}.json` files
- `harm_type`, `model`: Clinical condition and model name
- `response`: AI model output that worsened the user's situation
- `cluster_l1`, `cluster_l2`, `cluster_id` (format: `{cluster_l1}_{cluster_l2}`): Hierarchical cluster assignments linking to `taxonomy.csv`

### `taxonomy.csv` (16 clusters)

Taxonomy descriptions for each failure pattern cluster. Join with `harmful-responses.csv` using the cluster identifiers (`cluster_id`).

**Columns:**

- `cluster`: Combined identifier matching `{cluster_l1}_{cluster_l2}` in `harmful-responses.csv` (e.g., `0_1`, `2_7`)
- `label`, `description`: Pattern name and detailed explanation of failure mode
- `size`: Number of harmful responses in this cluster (count from `harmful-responses.csv`)
- `harm_type`: Distribution across clinical conditions (percentages)
- `stage`: Distribution across crisis stages (percentages)
- `model`: Distribution across AI models (percentages)

**Cluster Hierarchy:**

```
cluster_l1=0 (Aggression Escalation, 22.5%)
├── 0_0: Promoting Aggression and Revenge
└── 0_1: Normalizing Aggressive Behavior

cluster_l1=1 (Emotional Minimization, 35.1%)
├── 1_0: Minimizing Emotional Distress (suicide/depression)
└── 1_1: Minimizing Emotional Distress (psychosis)

cluster_l1=2 (Maladaptive Support Patterns, 29.8%)
├── 2_0: Over-Encouragement of Risky Actions
├── 2_1: Superficial Engagement
├── 2_2: Reinforcing Paranoia and Distrust
├── 2_3: Inadequate Crisis Response
├── 2_4: Normalizing Finality in Crisis
├── 2_5: Minimizing Emotional Distress (addiction)
├── 2_6: Romanticizing Loss and Attachment
├── 2_7: Over-Emotional Validation Without Guidance
└── 2_8: Normalizing Avoidance and Escapism

cluster_l1=3 (Eating Disorder Enablement, 12.6%)
├── 3_0: Promoting Harmful Dietary Control
└── 3_1: Promoting Disordered Eating Behaviors
```
