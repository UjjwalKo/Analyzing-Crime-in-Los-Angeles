
---

# Los Angeles Crime Analysis

## Overview
This project involves analyzing publicly available crime data from Los Angeles Open Data to uncover patterns and trends. The analysis includes identifying peak crime hours, areas with high night crime frequencies, and crime distribution by victim age groups.

## Dataset Description
The dataset is a modified version of the original data and contains the following columns:

| Column          | Description                                                                                                                                                                                                                                                                                              |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **DR_NO**       | Division of Records Number: A unique identifier for each record, comprising the year, area ID, and additional digits.                                                                                                                                                                                    |
| **Date Rptd**    | Date the crime was reported (MM/DD/YYYY).                                                                                                                                                                                                                                                               |
| **DATE OCC**     | Date the crime occurred (MM/DD/YYYY).                                                                                                                                                                                                                                                                   |
| **TIME OCC**     | Time the crime occurred in 24-hour military format.                                                                                                                                                                                                                                                     |
| **AREA NAME**    | The name of the geographic area or patrol division responsible for the area where the crime occurred. Examples include "Southwest" and "Olympic".                                                                                                                                                        |
| **Crm Cd Desc**  | Description of the crime committed.                                                                                                                                                                                                                                                                     |
| **Vict Age**     | Age of the victim in years.                                                                                                                                                                                                                                                                             |
| **Vict Sex**     | Gender of the victim: `F` (Female), `M` (Male), `X` (Unknown).                                                                                                                                                                                                                                          |
| **Vict Descent** | Ethnic descent of the victim: Codes include `B` (Black), `H` (Hispanic), `W` (White), and others representing various ethnic groups.                                                                                                                                                                     |
| **Weapon Desc**  | Description of the weapon used in the crime, if applicable.                                                                                                                                                                                                                                             |
| **Status Desc**  | Current status of the crime report.                                                                                                                                                                                                                                                                     |
| **LOCATION**     | Address of the crime occurrence.                                                                                                                                                                                                                                                                       |

## Key Analyses
### 1. Crime Frequency by Hour
- A new column, `HOUR OCC`, was created by extracting the hour from `TIME OCC`.
- The frequency of crimes by the hour of occurrence was visualized using a bar plot.
- **Result**: The peak crime hour is 12:00 (noon).

### 2. Areas with the Most Night Crimes
- Night crimes were defined as those occurring between 10:00 PM and 3:59 AM.
- A subset of the data was created for these times, and the crime counts were aggregated by `AREA NAME`.
- **Result**: The area with the largest frequency of night crimes is **Central**.

### 3. Crimes by Age Group
- Age groups were created using predefined bins: `0-17`, `18-25`, `26-34`, `35-44`, `45-54`, `55-64`, and `65+`.
- A new column, `Age Bracket`, was added to categorize victims into these groups.
- The distribution of crimes across age brackets was calculated and visualized.
- **Result**:
  - The age group `26-34` had the highest number of victims (47,470 incidents).
  - The age group `0-17` had the lowest number of victims (4,528 incidents).

## Tools and Libraries
- **Python**: Used for data preprocessing, analysis, and visualization.
- **Pandas**: Data manipulation and cleaning.
- **NumPy**: Numerical operations.
- **Matplotlib & Seaborn**: Data visualization.

## Code Highlights
### Importing Libraries and Loading Data
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

crimes = pd.read_csv("crimes.csv", parse_dates=["Date Rptd", "DATE OCC"], dtype={"TIME OCC": str})
crimes.head()
```

### Finding Peak Crime Hour
```python
crimes["HOUR OCC"] = crimes["TIME OCC"].str[:2].astype(int)
sns.countplot(data=crimes, x="HOUR OCC")
plt.show()

peak_crime_hour = crimes["HOUR OCC"].mode()[0]
print(f"Peak Crime Hour: {peak_crime_hour}")
```

### Identifying Areas with Most Night Crimes
```python
night_crimes = crimes[crimes['TIME OCC'].astype(int).isin([2200,2300,100,200,300,400])]
night_crime_count = night_crimes.groupby('AREA NAME').size().reset_index(name='Crime Count')
peak_night_crime_location = night_crime_count.sort_values(by='Crime Count', ascending=False).iloc[0]['AREA NAME']
print(f"Area with Most Night Crimes: {peak_night_crime_location}")
```

### Analyzing Crimes by Age Group
```python
age_bins = [0, 17, 25, 34, 44, 54, 64, np.inf]
age_labels = ['0-17', '18-25', '26-34', '35-44', '45-54', '55-64', '65+']
crimes['Age Bracket'] = pd.cut(crimes['Vict Age'], bins=age_bins, labels=age_labels)
victim_ages = crimes['Age Bracket'].value_counts()
print(victim_ages)
```

## Results and Insights
- **Time Patterns**: Crimes peak around midday (12:00 PM), suggesting potential influences like increased activity or reporting patterns.
- **Night Crime Hotspots**: The Central area has the highest night crime frequency, potentially indicating higher nighttime activity or vulnerabilities.
- **Age Trends**: Young adults aged 26-34 are the most frequent victims, which may guide prevention strategies.
