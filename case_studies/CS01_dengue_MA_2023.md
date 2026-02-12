# Case Study 1 - Dengue cases in Maranhão state, 2023

## Context

This case reproduces research questions about dengue cases in Maranhão using SINAN-Dengue data under PEFES constraints.

### Reference

#### Citation

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

### Question 1 - Nunber os cases at Maranhão in 2023

#### Prompt
How many probable Dengue cases were reported in the state of Maranhão during the year 2023?

#### Generated script

```python
import pandas as pd

# PEFES Generated Script
# Objective: Calculate total probable dengue cases in Maranhão, 2023

# 1. Filter data by State (MA=21), Year (2023), and Disease (Dengue=A90)
# Note: Per dictionary metadata, category '5' (Discarded) is absent from 2022 onwards.
# Therefore, all 'A90' notifications in this period are treated as probable cases.

probable_cases_df = df_sinan[
    (df_sinan['SG_UF_NOT'] == 21) & 
    (df_sinan['NU_ANO'] == 2023) & 
    (df_sinan['ID_AGRAVO'] == 'A90')
]

# 2. Calculate the total count
total_probable_count = len(probable_cases_df)

print(f"Total probable Dengue cases in Maranhão (2023): {total_probable_count}")

```
#### Answer:
`Total probable Dengue cases in Maranhão (2023): 4799`

### Question 2 - Distribution of cases per sex

#### Prompt
For the Sex variable (female, male), produce a descriptive table with columns for Category, Frequency (N), and Percentage (%), including a total row that sums to 100%

#### Generated script
```python
import pandas as pd

# PEFES Generated Script
# Objective: Generate a descriptive table for Sex distribution in Maranhão, 2023

# 1. Initial Filtering (State: MA=21, Year: 2023, Disease: Dengue=A90)
# Note: Following previous logic, all 'A90' records are considered probable cases
filtered_df = df_sinan[
    (df_sinan['SG_UF_NOT'] == 21) & 
    (df_sinan['NU_ANO'] == 2023) & 
    (df_sinan['ID_AGRAVO'] == 'A90')
].copy()

# 2. Frequency Analysis (N)
# Calculate counts for each category in CS_SEXO
sex_counts = filtered_df['CS_SEXO'].value_counts(dropna=False).reset_index()
sex_counts.columns = ['Category_Code', 'N']

# 3. Percentage Analysis (%)
total_n = sex_counts['N'].sum()
sex_counts['%'] = (sex_counts['N'] / total_n) * 100

# 4. Map Labels to English (based on dictionary values: F, M, I)
label_map = {
    'F': 'Female',
    'M': 'Male',
    'I': 'Ignored'
}
sex_counts['Category'] = sex_counts['Category_Code'].map(label_map).fillna('Not Informed')

# 5. Final Table Formatting
descriptive_table = sex_counts[['Category', 'N', '%']].copy()

# 6. Add Total Row
total_row = pd.DataFrame({
    'Category': ['Total'],
    'N': [total_n],
    '%': [100.0]
})
descriptive_table = pd.concat([descriptive_table, total_row], ignore_index=True)

# Display the result
print(descriptive_table.round(2))
```
#### Answer:
| Category | N | % |
| :--- | :--- | :--- |
| Female | 2622 | 54.64 |
| Male | 2175 | 45.32 |
| Ignored | 2 | 0.04 |
| **Total** | **4799** | **100.00** |

### Question 3 - Distribution of cases per age

#### Prompt
For the Age Group variable (bins: <1; 1–4; 5–9; 10–14; 15–19; 20–39; 40–59; 60–64; 65–69; 70–79; 80+), generate a descriptive table including Frequency (N) and Percentage (%), ensuring the total sum equals 100%.

#### Generated script
```python
import pandas as pd
import numpy as np

# PEFES Generated Script
# Objective: Generate a descriptive table for Age Groups in Maranhão, 2023

# 1. Context Filtering (State: MA=21, Year: 2023, Disease: Dengue=A90)
# All 'A90' records in 2023 are treated as probable cases per dictionary notes
df_filtered = df_sinan[
    (df_sinan['SG_UF_NOT'] == 21) & 
    (df_sinan['NU_ANO'] == 2023) & 
    (df_sinan['ID_AGRAVO'] == 'A90')
].copy()

# 2. Derive Age in Years from NU_IDADE_N
# Logic: If unit is 1 (Hour), 2 (Day), or 3 (Month), age in years is 0.
# If unit is 4 (Year), the age is the value of the last 3 digits.
def calculate_age_years(age_code):
    if pd.isna(age_code):
        return np.nan
    
    age_str = str(int(age_code)).zfill(4)
    unit = age_str[0]
    value = int(age_str[1:])
    
    if unit in ['1', '2', '3']:
        return 0
    elif unit == '4':
        return value
    else:
        return np.nan

df_filtered['age_years'] = df_filtered['NU_IDADE_N'].apply(calculate_age_years)

# 3. Create Age Group Categories
bins = [-1, 0, 4, 9, 14, 19, 39, 59, 64, 69, 79, 150]
labels = [
    '<1', '1–4', '5–9', '10–14', '15–19', '20–39', 
    '40–59', '60–64', '65–69', '70–79', '80+'
]

df_filtered['Age Group'] = pd.cut(
    df_filtered['age_years'], 
    bins=bins, 
    labels=labels
)

# 4. Descriptive Table Calculation (N and %)
age_summary = df_filtered['Age Group'].value_counts(sort=False).reset_index()
age_summary.columns = ['Age Group', 'N']

total_n = age_summary['N'].sum()
age_summary['%'] = (age_summary['N'] / total_n) * 100

# 5. Add Total Row
total_row = pd.DataFrame({
    'Age Group': ['Total'],
    'N': [total_n],
    '%': [100.0]
})
age_summary = pd.concat([age_summary, total_row], ignore_index=True)

print(age_summary.round(2))
```

#### Answer:
| Age Group | N | % |
| :--- | :--- | :--- |
| <1 | 114 | 2.38 |
| 1–4 | 236 | 4.92 |
| 5–9 | 473 | 9.86 |
| 10–14 | 494 | 10.29 |
| 15–19 | 471 | 9.81 |
| 20–39 | 1580 | 32.92 |
| 40–59 | 975 | 20.32 |
| 60–64 | 145 | 3.02 |
| 65–69 | 122 | 2.54 |
| 70–79 | 145 | 3.02 |
| 80+ | 44 | 0.92 |
| **Total** | **4799** | **100.00** |


### Notes

* **Case Definition**: All notifications under CID-10 code `A90` were considered probable cases, following 2022 guidelines that removed the "Discarded" category from the `CLASSI_FIN` variable.
* **Age Calculation**: The `NU_IDADE_N` variable was decoded by identifying the first digit as the time unit (hour, day, month, year) to ensure precise age grouping.
* **Data Completeness**: "Ignored" values in sex and age categories reflect missing information common in passive surveillance systems.
* **PEFES Compliance**: The analysis strictly follows the active data dictionary, using only validated variables and deterministic transformations for derived fields.