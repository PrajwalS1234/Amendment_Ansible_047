Datasets provided to us can be found in this [Google Drive Link](https://drive.google.com/drive/folders/1p9zG9KN-cLSdHBNBsnA48JPE9-oPVm3X)
```markdown
# Betting App Data Cleaning

This project involves cleaning and merging various datasets related to a betting application. The datasets include bonus costs, first bets, first deposits, player activity, and player details. The goal is to clean these datasets and merge them into a single comprehensive dataset for analysis.

## Libraries and Warnings

```python
# Importing important libraries
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings('ignore')
import requests
import openpyxl
```

## Importing Data

Data is imported from Google Drive links using the `pd.read_excel()` function.

```python
bonus_cost_link = 'https://docs.google.com/uc?export=download&id=12xPdp6PEJEJP8qyVaaj7Qey6ZR0AkbVx'
first_bet_link = 'https://docs.google.com/uc?export=download&id=1_s8Kbv7KgdJlY1eJueW6hKiI2gfAF1PR'
first_deposit_link = 'https://docs.google.com/uc?export=download&id=18QgUwvY_yQml8bYa21chHS0AlaeAE45N'
player_activity_link = 'https://docs.google.com/uc?export=download&id=16037oorbl30sMudPRQKNGO5xXg27773a'
player_details_link = 'https://docs.google.com/uc?export=download&id=1WZ6PBVqSRj77feQHNojp9Q5a5ibCfVJz'
```

## Cleaning Data

### Bonus Cost Data

1. Load data.
2. Set the first row as the column names.
3. Drop columns with all NaN values.
4. Convert `ActivityMonth` to datetime.
5. Convert `BONUS_COST` to integer.
6. Save cleaned data to a CSV file.

```python
bonus_cost_data_orig = pd.read_excel(bonus_cost_link)
bonus_cost_data_orig.columns = bonus_cost_data_orig.iloc[0]
bonus_cost_data_orig = bonus_cost_data_orig[1:]
bonus_cost_data_orig = bonus_cost_data_orig.dropna(axis=1, how='all')
bonus_cost_data_orig['ActivityMonth'] = pd.to_datetime(bonus_cost_data_orig['ActivityMonth'])
bonus_cost_data_orig['BONUS_COST'] = bonus_cost_data_orig['BONUS_COST'].astype(int)
bonus_cost_data_orig.to_csv('cleaned_bonus_cost_data.csv', index=False)
```

### First Bet Data

1. Load data.
2. Set the first row as the column names.
3. Drop columns with all NaN values.
4. Drop rows with any NaN values.
5. Convert `System_First_Bet_Datetime` to datetime.
6. Convert `System_First_BetSlip_Amt` to numeric.
7. Save cleaned data to a CSV file.

```python
first_bet_orig = pd.read_excel(first_bet_link)
first_bet_orig.columns = first_bet_orig.iloc[0]
first_bet_orig = first_bet_orig[1:]
first_bet_orig = first_bet_orig.dropna(axis=1, how='all')
first_bet_orig = first_bet_orig.dropna()
first_bet_orig['System_First_Bet_Datetime'] = pd.to_datetime(first_bet_orig['System_First_Bet_Datetime'])
first_bet_orig['System_First_BetSlip_Amt'] = pd.to_numeric(first_bet_orig['System_First_BetSlip_Amt'], errors='coerce')
first_bet_orig.to_csv('cleaned_first_bet_orig.csv', index=False)
```

### First Deposit Data

1. Load data.
2. Set the first row as the column names.
3. Drop columns with all NaN values.
4. Drop rows with any NaN values.
5. Convert `First_Deposit_Date` to datetime.
6. Convert `First_Deposit_Amount` to integer.
7. Save cleaned data to a CSV file.

```python
first_deposit_orig = pd.read_excel(first_deposit_link)
first_deposit_orig.columns = first_deposit_orig.iloc[0]
first_deposit_orig = first_deposit_orig[1:]
first_deposit_orig = first_deposit_orig.dropna(axis=1, how='all')
first_deposit_orig = first_deposit_orig.dropna()
first_deposit_orig['First_Deposit_Date'] = pd.to_datetime(first_deposit_orig['First_Deposit_Date'])
first_deposit_orig['First_Deposit_Amount'] = first_deposit_orig['First_Deposit_Amount'].astype(int)
first_deposit_orig.to_csv('cleaned_first_deposit_orig.csv', index=False)
```

### Player Details Data

1. Load data.
2. Set the first row as the column names.
3. Drop columns with all NaN values.
4. Fill NaN values in specific columns with default values.
5. Convert `Signup_Date` and `Date_Of_Birth` to datetime.
6. Save cleaned data to a CSV file.

```python
player_details_orig = pd.read_excel(player_details_link)
player_details_orig.columns = player_details_orig.iloc[0]
player_details_orig = player_details_orig[1:]
player_details_orig = player_details_orig.dropna(axis=1, how='all')
player_details_orig['acquisition_channel'].fillna("unknown", inplace=True)
player_details_orig['src_advertiser_id'].fillna("000000", inplace=True)
player_details_orig['Gender'].fillna("O", inplace=True)
player_details_orig['Signup_Date'] = pd.to_datetime(player_details_orig['Signup_Date'])
player_details_orig['Date_Of_Birth'] = pd.to_datetime(player_details_orig['Date_Of_Birth'])
player_details_orig.to_csv('cleaned_player_details_orig.csv', index=False)
```

### Player Activity Data

1. Load data.
2. Set the first row as the column names.
3. Drop columns with all NaN values.
4. Convert `ActivityMonth` to datetime.
5. Convert numeric columns to numeric datatype.
6. Save cleaned data to a CSV file.

```python
player_activity_orig = pd.read_excel(player_activity_link, sheet_name="Backend Data", engine='openpyxl')
player_activity_orig.columns = player_activity_orig.iloc[0]
player_activity_orig = player_activity_orig[1:]
player_activity_orig = player_activity_orig.dropna(axis=1, how='all')
player_activity_orig['ActivityMonth'] = pd.to_datetime(player_activity_orig['ActivityMonth'])
numeric_columns = ['ActivePlayerDays', 'Bet_Amount', 'Win_Amount', 'Gross_Win', 'Net_Gross_Win']
player_activity_orig[numeric_columns] = player_activity_orig[numeric_columns].apply(pd.to_numeric)
player_activity_orig.to_csv('cleaned_player_activity_orig.csv', index=False)
```

## Merging DataFrames

1. Create copies of the cleaned DataFrames.
2. Rename the first column of each DataFrame to `SRC_PLAYER_ID`.
3. Merge the DataFrames using an inner join on `SRC_PLAYER_ID`.
4. Save the merged DataFrame to a CSV file.

```python
pa = player_activity_orig.copy()
bc = bonus_cost_data_orig.copy()
fb = first_bet_orig.copy()
fd = first_deposit_orig.copy()
pdd = player_details_orig.copy()

bc.rename(columns={bc.columns[0]: 'SRC_PLAYER_ID'}, inplace=True)
pa.rename(columns={pa.columns[0]: 'SRC_PLAYER_ID'}, inplace=True)
fb.rename(columns={fb.columns[0]: 'SRC_PLAYER_ID'}, inplace=True)
fd.rename(columns={fd.columns[0]: 'SRC_PLAYER_ID'}, inplace=True)
pdd.rename(columns={pdd.columns[0]: 'SRC_PLAYER_ID'}, inplace=True)

merged_df = bc.merge(pa, on='SRC_PLAYER_ID', how='inner') \
             .merge(fb, on='SRC_PLAYER_ID', how='inner') \
             .merge(fd, on='SRC_PLAYER_ID', how='inner') \
             .merge(pdd, on='SRC_PLAYER_ID', how='inner')
merged_df.to_csv('merged_df.csv', index=False)
```

## Exporting Data

Install `openpyxl` if not already installed. Export each DataFrame to CSV and Excel files.

```python
!pip install openpyxl

dataframes = {
    'player_activity_orig': pa,
    'bonus_cost_data_orig': bc,
    'first_bet_orig': fb,
    'first_deposit_orig': fd,
    'player_details_orig': pdd
}

for name, df in dataframes.items():
    df.to_csv(f'{name}.csv', index=False)
```

## Downloading the Merged DataFrame

```python
from google.colab import files
files.download('merged_df.csv')
```
```
