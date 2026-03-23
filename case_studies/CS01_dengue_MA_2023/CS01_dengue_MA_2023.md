# Case Study 1 - Dengue cases in Maranhão state, 2023

## Context

This case reproduces research questions about dengue cases in Maranhão using SINAN-Dengue data under PEFES constraints.

#### Reference

da Conceição Cruz, L., de Almeida, V. M., Barroso, W. A., & de Morais, C. D. M. (2024). *Perfil epidemiológico da dengue no Maranhão de 2019 a 2023* . Revista Eletrônica Acervo Saúde, 24(12), 17390-17390.
https://acervomais.com.br/index.php/saude/article/view/17390

### Epidemiological Context
Maranhão is an endemic region for dengue in Brazil with significant seasonal variations. In 2023, the analysis focuses on demographic distribution and case severity based on SINAN data.

### Technical details
- **Database:** SINAN Dengue
- **Geographical Filter:** Maranhão State (IBGE Code: 21)
- **Period:** 2023
- **Preferred Language Chosen:** Python

##  Results

### Question 1 — Number of dengue cases in Maranhão, 2023

#### Prompt:
Follow the instructions and data dictionaries provided in the attachment. 

I want to know how many dengue cases were reported in Maranhão in 2023. 

Use the dataset 'df' and follow the provided data dictionary. I'm using Python.

### Answer from the LLM:
```markdown
**Question interpretation**
You want the number of dengue cases reported in Maranhão in 2023, using the SINAN dengue dataset (df). “Reported cases” corresponds to notifications (records) of dengue (CID-10 = A90) in the specified year and UF.

**Relevant variables**
From the SINAN dictionary :

ID_AGRAVO → identifies dengue (A90)
NU_ANO → year of notification
SG_UF_NOT → UF of notification (Maranhão = 21)
```

#### Generated script:

```python
import pandas as pd
# Ensure required columns exist
required_cols = ["ID_AGRAVO", "NU_ANO", "SG_UF_NOT"]
missing = [col for col in required_cols if col not in df.columns]
if missing:
    raise ValueError(f"Missing columns: {missing}")

# Filter: dengue, Maranhão, 2023
df_filtered = df[
    (df["ID_AGRAVO"] == "A90") &   # Dengue
    (df["NU_ANO"] == 2023) &       # Year
    (df["SG_UF_NOT"] == 21)        # Maranhão
]

# Count reported cases
n_cases = df_filtered.shape[0]

print(n_cases)
```
#### Result:
**4794 cases**

### Question 2 - Distribution of dengue cases by sex in Maranhão, 2023

#### Prompt
Follow the instructions and data dictionaries provided in the attachment. 

I want to see how dengue cases are distributed by sex in Maranhão in 2023. Make a table including percentages and a plot. 

Use the dataset 'df' and follow the provided data dictionary. I'm using Python.

### Answer from the LLM:
```markdown
**Question interpretation**
Analyze dengue notifications in Maranhão (UF = 21) for 2023 and show their distribution by sex, including absolute counts and percentages, with a table and a plot.

**Relevant variables**
From the SINAN dictionary :

ID_AGRAVO → dengue = "A90"
NU_ANO → year = 2023
SG_UF_NOT → Maranhão = 21
CS_SEXO → sex (M, F, I)
```

#### Generated script
```python
import pandas as pd
import matplotlib.pyplot as plt

# Validate required columns
required_cols = ["ID_AGRAVO", "NU_ANO", "SG_UF_NOT", "CS_SEXO"]
missing = [col for col in required_cols if col not in df.columns]
if missing:
    raise ValueError(f"Missing columns: {missing}")

# Filter dataset
df_filtered = df[
    (df["ID_AGRAVO"] == "A90") &   # Dengue
    (df["NU_ANO"] == 2023) &       # Year
    (df["SG_UF_NOT"] == 21)        # Maranhão
].copy()

# Map sex labels according to dictionary
sex_map = {
    "M": "Male",
    "F": "Female",
    "I": "Ignored"
}

df_filtered["sexo_label"] = df_filtered["CS_SEXO"].map(sex_map)

# Handle missing values explicitly
df_filtered["sexo_label"] = df_filtered["sexo_label"].fillna("Missing")

# Aggregate counts
table = (
    df_filtered
    .groupby("sexo_label", dropna=False)
    .size()
    .reset_index(name="cases")
    .sort_values("cases", ascending=False)
)

print(table)

# Plot
plt.figure()
plt.bar(table["sexo_label"], table["cases"])
plt.xlabel("Sex")
plt.ylabel("Number of cases")
plt.title("Dengue cases by sex - Maranhão (2023)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```
### Result

| Sex     | Cases | Percentage (%) |
|---------|------:|---------------:|
| Female  | 2622  | 54.64          |
| Male    | 2175  | 45.32          |
| Ignored | 2     | 0.04           |

![Distribution of dengue cases by sex](/case_studies/CS01_dengue_MA_2023/question2/q2_output.png)

### Question 3 - Distribution of dengue cases by age group in Maranhão, 2023

#### Prompt
Follow the instructions and data dictionaries provided in the attachment. 

I want to see how dengue cases are distributed by age groups in Maranhão in 2023. Make a table including percentages and a plot. 

The age groups are: < 1; 1–4; 5–9; 10–14; 15–19; 20–39; 40–59; 60–64; 65–69; 70–79; 80+

Use the dataset 'df' and follow the provided data dictionary. I'm using Python.

### Answer from the LLM:
```markdown
**Question interpretation**
Analyze dengue notifications in Maranhão (UF = 21) for 2023 and describe their distribution by predefined age groups, including counts and percentages, with a table and a plot.

**Relevant variables**
From the SINAN dictionary :

ID_AGRAVO → dengue = "A90"
NU_ANO → year = 2023
SG_UF_NOT → Maranhão = 21
NU_IDADE_N → age coded (requires transformation)

NU_IDADE_N encodes both unit and value (e.g., 4018 = 18 years). A deterministic transformation is required to extract age in years, as allowed by the rules.
```

#### Generated script
```python
import pandas as pd
import matplotlib.pyplot as plt

# Validate required columns
required_cols = ["ID_AGRAVO", "NU_ANO", "SG_UF_NOT", "NU_IDADE_N"]
missing = [col for col in required_cols if col not in df.columns]
if missing:
    raise ValueError(f"Missing columns: {missing}")

# Filter dataset
df_filtered = df[
    (df["ID_AGRAVO"] == "A90") &   # Dengue
    (df["NU_ANO"] == 2023) &       # Year
    (df["SG_UF_NOT"] == 21)        # Maranhão
].copy()

# --- Derive age in years ---
idade_str = df_filtered["NU_IDADE_N"].astype(str).str.zfill(4)

unit = idade_str.str[0]
value = idade_str.str[1:].astype(float)

df_filtered["age_years"] = None

df_filtered.loc[unit == "4", "age_years"] = value[unit == "4"]                # years
df_filtered.loc[unit == "3", "age_years"] = value[unit == "3"] / 12           # months → years
df_filtered.loc[unit == "2", "age_years"] = value[unit == "2"] / 365          # days → years
df_filtered.loc[unit == "1", "age_years"] = value[unit == "1"] / (365*24)     # hours → years

# Keep valid ages
df_filtered = df_filtered[df_filtered["age_years"].notna()]

# --- Define age groups (as requested) ---
bins = [-float("inf"), 0.999, 4, 9, 14, 19, 39, 59, 64, 69, 79, float("inf")]
labels = [
    "<1", "1–4", "5–9", "10–14", "15–19",
    "20–39", "40–59", "60–64", "65–69", "70–79", "80+"
]

df_filtered["age_group"] = pd.cut(
    df_filtered["age_years"],
    bins=bins,
    labels=labels,
    right=True
)

# Aggregate counts
table = (
    df_filtered
    .groupby("age_group", dropna=False)
    .size()
    .reset_index(name="cases")
)

# Calculate percentages
total = table["cases"].sum()
table["percentage"] = (table["cases"] / total * 100).round(2)

# Order categories
table["age_group"] = pd.Categorical(table["age_group"], categories=labels, ordered=True)
table = table.sort_values("age_group")

print(table)

# Plot
plt.figure()
plt.bar(table["age_group"].astype(str), table["percentage"])
plt.xlabel("Age group")
plt.ylabel("Percentage (%)")
plt.title("Dengue cases by age group (%) - Maranhão (2023)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

#### Answer:
| Age Group | Cases | Percentage (%) |
|-----------|------:|---------------:|
| <1        | 114   | 2.38           |
| 1–4       | 236   | 4.92           |
| 5–9       | 473   | 9.86           |
| 10–14     | 494   | 10.29          |
| 15–19     | 471   | 9.81           |
| 20–39     | 1580  | 32.92          |
| 40–59     | 975   | 20.32          |
| 60–64     | 145   | 3.02           |
| 65–69     | 122   | 2.54           |
| 70–79     | 145   | 3.02           |
| 80+       | 44    | 0.92           |

![Distribution of dengue cases by age group](/case_studies/CS01_dengue_MA_2023/question3/q3_output.png)

### Question 4 - Distribution of dengue cases by race/color in Maranhão, 2023

#### Prompt
Follow the instructions and data dictionaries provided in the attachment. 

I want to see how dengue cases are distributed by race/color in Maranhão in 2023. Make a table including percentages and a plot. 

Use the dataset 'df' and follow the provided data dictionary. I'm using Python.

### Answer from the LLM:
```markdown
**Question interpretation**
Analyze dengue notifications in Maranhão (UF = 21) for 2023 and describe their distribution by race/color, including counts and percentages, with a table and a plot.

**Relevant variables**
From the SINAN dictionary :

ID_AGRAVO → dengue = "A90"
NU_ANO → year = 2023
SG_UF_NOT → Maranhão = 21
CS_RACA → race/color (coded values)

Valid categories for CS_RACA:
1 = White, 2 = Black, 3 = Asian, 4 = Brown (Parda), 5 = Indigenous, 9 = Ignored
```

#### Generated script
```python
import pandas as pd
import matplotlib.pyplot as plt

# Validate required columns
required_cols = ["ID_AGRAVO", "NU_ANO", "SG_UF_NOT", "CS_RACA"]
missing = [col for col in required_cols if col not in df.columns]
if missing:
    raise ValueError(f"Missing columns: {missing}")

# Filter dataset
df_filtered = df[
    (df["ID_AGRAVO"] == "A90") &   # Dengue
    (df["NU_ANO"] == 2023) &       # Year
    (df["SG_UF_NOT"] == 21)        # Maranhão
].copy()

# Map race/color labels according to dictionary
race_map = {
    1: "White",
    2: "Black",
    3: "Asian",
    4: "Brown",
    5: "Indigenous",
    9: "Ignored"
}

df_filtered["race_label"] = df_filtered["CS_RACA"].map(race_map)

# Handle missing/unmapped values
df_filtered["race_label"] = df_filtered["race_label"].fillna("Missing")

# Aggregate counts
table = (
    df_filtered
    .groupby("race_label", dropna=False)
    .size()
    .reset_index(name="cases")
)

# Calculate percentages
total = table["cases"].sum()
table["percentage"] = (table["cases"] / total * 100).round(2)

# Sort for readability
table = table.sort_values("cases", ascending=False)

print(table)

# Plot
plt.figure()
plt.bar(table["race_label"], table["percentage"])
plt.xlabel("Race/Color")
plt.ylabel("Percentage (%)")
plt.title("Dengue cases by race/color (%) - Maranhão (2023)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

#### Answer:
| Race/Color | Cases | Percentage (%) |
|------------|------:|---------------:|
| Brown      | 3842  | 80.06          |
| White      | 589   | 12.27          |
| Black      | 225   | 4.69           |
| Ignored    | 87    | 1.81           |
| Asian      | 44    | 0.92           |
| Indigenous | 12    | 0.25           |

![Distribution of dengue cases by race/color](/case_studies/CS01_dengue_MA_2023/question4/q4_output.png)

### Question 5 - Distribution of dengue cases by confirmation criteria in Maranhão, 2023

#### Prompt
Follow the instructions and data dictionaries provided in the attachment. 

I want to see how dengue cases are distributed by confirmation criteria in Maranhão in 2023. Make a table including percentages and a plot. 

Use the dataset 'df' and follow the provided data dictionary. I'm using Python.

### Answer from the LLM:
```markdown
**Question interpretation**
Analyze dengue notifications in Maranhão (UF = 21) for 2023 and describe their distribution by confirmation criteria, including counts and percentages, with a table and a plot.

**Relevant variables**
From the SINAN dictionary :

ID_AGRAVO → dengue = "A90"
NU_ANO → year = 2023
SG_UF_NOT → Maranhão = 21
CRITERIO → confirmation criterion

Typical valid categories for CRITERIO (must follow dataset coding):

1 = Laboratory
2 = Clinical-epidemiological
3 = Clinical
4 = Ignored/blank
```

#### Generated script
```python
import pandas as pd
import matplotlib.pyplot as plt

# Validate required columns
required_cols = ["ID_AGRAVO", "NU_ANO", "SG_UF_NOT", "CRITERIO"]
missing = [col for col in required_cols if col not in df.columns]
if missing:
    raise ValueError(f"Missing columns: {missing}")

# Filter dataset
df_filtered = df[
    (df["ID_AGRAVO"] == "A90") &   # Dengue
    (df["NU_ANO"] == 2023) &       # Year
    (df["SG_UF_NOT"] == 21)        # Maranhão
].copy()

# Map confirmation criteria (ensure codes match your dataset)
criterio_map = {
    1: "Laboratory",
    2: "Clinical-epidemiological",
    3: "Clinical",
    4: "Ignored"
}

df_filtered["criterio_label"] = df_filtered["CRITERIO"].map(criterio_map)

# Handle missing/unmapped values
df_filtered["criterio_label"] = df_filtered["criterio_label"].fillna("Missing")

# Aggregate counts
table = (
    df_filtered
    .groupby("criterio_label", dropna=False)
    .size()
    .reset_index(name="cases")
)

# Calculate percentages
total = table["cases"].sum()
table["percentage"] = (table["cases"] / total * 100).round(2)

# Sort for readability
table = table.sort_values("cases", ascending=False)

print(table)

# Plot
plt.figure()
plt.bar(table["criterio_label"], table["percentage"])
plt.xlabel("Confirmation criterion")
plt.ylabel("Percentage (%)")
plt.title("Dengue cases by confirmation criterion (%) - Maranhão (2023)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

#### Answer:

| Confirmation Criterion     | Cases | Percentage (%) |
|--------------------------|------:|---------------:|
| Laboratory               | 2428  | 50.59          |
| Clinical-epidemiological | 1614  | 33.63          |
| Missing                  | 609   | 12.69          |
| Clinical                 | 148   | 3.08           |

![Distribution of dengue cases by confirmation criteria](/case_studies/CS01_dengue_MA_2023/question5/q5_output.png)

### Question 6 - Distribution of dengue cases by final classification in Maranhão, 2023

#### Prompt
Follow the instructions and data dictionaries provided in the attachment. 

I want to see how dengue cases are distributed by final classification in Maranhão in 2023. Make a table including percentages and a plot. 

Use the dataset 'df' and follow the provided data dictionary. I'm using Python.

### Answer from the LLM:
```markdown
**Question interpretation**
Analyze dengue notifications in Maranhão (UF = 21) for 2023 and describe their distribution by final classification, including counts and percentages, with a table and a plot.

**Relevant variables**
From the SINAN dictionary :

ID_AGRAVO → dengue = "A90"
NU_ANO → year = 2023
SG_UF_NOT → Maranhão = 21
CLASSI_FIN → final classification

Valid categories:

5 = Discarded
8 = Inconclusive
10 = Dengue
11 = Dengue with warning signs
12 = Severe dengue
```

#### Generated script
```python
import pandas as pd
import matplotlib.pyplot as plt

# Validate required columns
required_cols = ["ID_AGRAVO", "NU_ANO", "SG_UF_NOT", "CLASSI_FIN"]
missing = [col for col in required_cols if col not in df.columns]
if missing:
    raise ValueError(f"Missing columns: {missing}")

# Filter dataset
df_filtered = df[
    (df["ID_AGRAVO"] == "A90") &   # Dengue
    (df["NU_ANO"] == 2023) &       # Year
    (df["SG_UF_NOT"] == 21)        # Maranhão
].copy()

# Map final classification labels
class_map = {
    5: "Discarded",
    8: "Inconclusive",
    10: "Dengue",
    11: "Dengue with warning signs",
    12: "Severe dengue"
}

df_filtered["class_label"] = df_filtered["CLASSI_FIN"].map(class_map)

# Handle missing/unmapped values
df_filtered["class_label"] = df_filtered["class_label"].fillna("Missing")

# Aggregate counts
table = (
    df_filtered
    .groupby("class_label", dropna=False)
    .size()
    .reset_index(name="cases")
)

# Calculate percentages
total = table["cases"].sum()
table["percentage"] = (table["cases"] / total * 100).round(2)

# Sort for readability
table = table.sort_values("cases", ascending=False)

print(table)

# Plot
plt.figure()
plt.bar(table["class_label"], table["percentage"])
plt.xlabel("Final classification")
plt.ylabel("Percentage (%)")
plt.title("Dengue cases by final classification (%) - Maranhão (2023)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

#### Answer:

| Final Classification           | Cases | Percentage (%) |
|--------------------------------|------:|---------------:|
| Dengue                         | 4009  | 83.54          |
| Inconclusive                   | 614   | 12.79          |
| Dengue with warning signs      | 152   | 3.17           |
| Severe dengue                  | 19    | 0.40           |
| Missing                        | 5     | 0.10           |

![Distribution of dengue cases by final classification](/case_studies/CS01_dengue_MA_2023/question6/q6_output.png)

### Question 7 - Dengue cases by epidemiological week in Maranhão, 2023

#### Prompt
Follow the instructions and data dictionaries provided in the attachment. 

I want to see how dengue cases evolved over time in Maranhão in 2023. Make a line plot showing the number of cases by epidemiological week. 

Use the dataset 'df' and follow the provided data dictionary. I'm using Python.

### Answer from the LLM:
```markdown
**Question interpretation**
Analyze dengue notifications in Maranhão (UF = 21) for 2023 and describe how cases evolved over time, using epidemiological weeks, with a line plot.

**Relevant variables**
From the SINAN dictionary :

ID_AGRAVO → dengue = "A90"
NU_ANO → year = 2023
SG_UF_NOT → Maranhão = 21
SEM_NOT → epidemiological week of notification (format: YYYYWW)
```

#### Generated script
```python
import pandas as pd
import matplotlib.pyplot as plt

# Validate required columns
required_cols = ["ID_AGRAVO", "NU_ANO", "SG_UF_NOT", "SEM_NOT"]
missing = [col for col in required_cols if col not in df.columns]
if missing:
    raise ValueError(f"Missing columns: {missing}")

# Filter dataset
df_filtered = df[
    (df["ID_AGRAVO"] == "A90") &   # Dengue
    (df["NU_ANO"] == 2023) &       # Year
    (df["SG_UF_NOT"] == 21)        # Maranhão
].copy()

# Extract epidemiological week (last two digits of SEM_NOT)
df_filtered["epi_week"] = df_filtered["SEM_NOT"].astype(str).str[-2:].astype(int)

# Aggregate cases by week
weekly = (
    df_filtered
    .groupby("epi_week")
    .size()
    .reset_index(name="cases")
    .sort_values("epi_week")
)

# Plot
plt.figure()
plt.plot(weekly["epi_week"], weekly["cases"], marker="o")
plt.xlabel("Epidemiological week")
plt.ylabel("Number of cases")
plt.title("Dengue cases by epidemiological week - Maranhão (2023)")
plt.tight_layout()
plt.show()

# First rows
print(weekly.head())

# Last rows
print(weekly.tail())
```

#### Answer:

| Epidemiological Week | Cases |
|---------------------|------:|
| 1                   | 42    |
| 2                   | 60    |
| 3                   | 88    |
| 4                   | 85    |
| 5                   | 118   |
| ...                 | ...   |
| 48                  | 18    |
| 49                  | 28    |
| 50                  | 15    |
| 51                  | 11    |
| 52                  | 12    |

![Distribution of dengue cases by epidemiological week](/case_studies/CS01_dengue_MA_2023/question7/q7_output.png)