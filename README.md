```markdown
# Betting App Data Cleaning and Data Push

This project involves cleaning and merging various datasets related to a betting application and then pushing the cleaned data into a MySQL database.

## Datasets

The datasets provided can be found in this [Google Drive Link](https://drive.google.com/drive/folders/1p9zG9KN-cLSdHBNBsnA48JPE9-oPVm3X).

## Prerequisites

Ensure you have the necessary libraries installed:

```bash
pip install sqlalchemy mysqlclient openpyxl
```

## Importing Libraries

The following libraries are required for data cleaning and database operations:

```python
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import warnings
from sqlalchemy import create_engine
warnings.filterwarnings('ignore')
```

## Cleaning Data

### 1. Bonus Cost Data

- Load the data
- Set the first row as column names
- Drop columns with all NaN values
- Convert `ActivityMonth` to datetime
- Convert `BONUS_COST` to integer
- Save cleaned data to CSV

### 2. First Bet Data

- Load the data
- Set the first row as column names
- Drop columns with all NaN values
- Drop rows with any NaN values
- Convert `System_First_Bet_Datetime` to datetime
- Convert `System_First_BetSlip_Amt` to numeric
- Save cleaned data to CSV

### 3. First Deposit Data

- Load the data
- Set the first row as column names
- Drop columns with all NaN values
- Drop rows with any NaN values
- Convert `First_Deposit_Date` to datetime
- Convert `First_Deposit_Amount` to integer
- Save cleaned data to CSV

### 4. Player Details Data

- Load the data
- Set the first row as column names
- Drop columns with all NaN values
- Fill NaN values in specific columns with default values
- Convert `Signup_Date` and `Date_Of_Birth` to datetime
- Save cleaned data to CSV

### 5. Player Activity Data

- Load the data
- Set the first row as column names
- Drop columns with all NaN values
- Convert `ActivityMonth` to datetime
- Convert numeric columns to numeric datatype
- Save cleaned data to CSV

## Merging DataFrames

- Create copies of the cleaned DataFrames
- Rename the first column of each DataFrame to `SRC_PLAYER_ID`
- Merge the DataFrames using an inner join on `SRC_PLAYER_ID`
- Save the merged DataFrame to a CSV file

## Data Push

### Database Connection

Establish a connection to the MySQL database:

```python
engine = create_engine('mysql+mysqldb://root:123456789@127.0.0.1:3306/B37CW')
conn = engine.connect()
```

### Loading Data

Load cleaned data from CSV files into Pandas DataFrames:

```python
df = pd.read_csv('cleaned_player_activity_orig.csv')
df1 = pd.read_csv('cleaned_bonus_cost_data.csv')
df2 = pd.read_csv('cleaned_first_bet_orig.csv')
df3 = pd.read_csv('cleaned_first_deposit_orig.csv')
df4 = pd.read_csv('cleaned_player_details_orig.csv')
```

### Pushing Data to MySQL

Push each DataFrame to the MySQL database:

```python
df.to_sql('cleaned_player_activity_orig', engine, index=False, if_exists="replace")
df1.to_sql('cleaned_bonus_cost_data', engine, index=False, if_exists="replace")
df2.to_sql('cleaned_first_bet_orig', engine, index=False, if_exists="replace")
df3.to_sql('cleaned_first_deposit_orig', engine, index=False, if_exists="replace")
df4.to_sql('cleaned_player_details_orig', engine, index=False, if_exists="replace")
conn.close()
```

## Summary

This documentation covers the process of cleaning data from various CSV files and pushing the cleaned data into a MySQL database. By following the steps outlined, you can efficiently manage and store your cleaned data for further analysis or querying within a SQL environment.

![nexttt_page-0003](https://github.com/user-attachments/assets/700025d3-ec7e-4df5-868d-10e8aef3c622)



Conclusions Drawn:
1. Data Collection in the company has to be improved. It's harder to analyze data when we have many Unknown values, such as we would have seen in Acquisition channels. Thus, it would be wise to modify the links we are using on our websites to include trackers, such that we would be able to track numerous data points about the users who are using our service and thus create better services for them using that data and try to gain insights from that data.
2.

Can you hear me?
