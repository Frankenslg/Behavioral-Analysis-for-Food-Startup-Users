# 🚀 **Behavioral Analysis for Food Startup Users**

## 📝 **Introduction**

In this project, we explored user behavior within the application of a food startup. The primary objective was to analyze the sales funnel and evaluate the results of an A/A/B test to inform decisions regarding potential changes to the user interface.

### **Objectives**

1. **Sales Funnel Analysis:**
   - Understand how users progress through different stages of the sales funnel.
   - Identify drop-off points and determine what percentage of users complete the purchase process.

2. **A/A/B Test Analysis:**
   - Assess the impact of a font change in the application by comparing two control groups and one test group.
   - Ensure the control groups are consistent to validate the experiment's accuracy.

---

## 📚 **Libraries Used**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime
from scipy import stats
import plotly.graph_objects as go
import math as mth
```

---

## 📥 **Dataset Loading and Preparation**

### 1️⃣ **Dataset Loading**
```python
# Loading the dataset with tab delimiter
file_path = '/datasets/logs_exp_us.csv'
data = pd.read_csv(file_path, delimiter='\t')

# Displaying dataset structure
print(data.head())
print(data.info())
```

### 2️⃣ **Data Cleaning**
- Converted `event_timestamp` to `datetime` format.
- Created a separate `event_date` column.
- Removed duplicate entries.
- Verified and handled missing values.
```python
# Data cleaning and formatting
data['event_timestamp'] = pd.to_datetime(data['event_timestamp'], unit='s')
data['event_date'] = data['event_timestamp'].dt.date
data = data.drop_duplicates()
print(data.isnull().sum())
```

---

## 🔍 **Exploratory Data Analysis**

### 1️⃣ **Key Metrics**
```python
# Total events and users
num_events = data.shape[0]
num_users = data['device_id'].nunique()
avg_events_per_user = num_events / num_users

# Date range
min_date = data['event_timestamp'].min()
max_date = data['event_timestamp'].max()

print(f"Total events: {num_events}, Total users: {num_users}, Avg events/user: {avg_events_per_user:.2f}")
print(f"Date range: {min_date} to {max_date}")
```

### 2️⃣ **Event Timeline**
```python
# Histogram of events over time
plt.figure(figsize=(12, 6))
sns.histplot(data['event_timestamp'], bins=50)
plt.title('Event Timeline')
plt.xlabel('Date and Time')
plt.ylabel('Number of Events')
plt.show()
```

---

## 📊 **Sales Funnel Analysis**

### 1️⃣ **Event Funnel Setup**
```python
# Define event sequence
event_sequence = ['MainScreenAppear', 'OffersScreenAppear', 'CartScreenAppear', 'PaymentScreenSuccessful']
filtered_data = data[data['event_name'].isin(event_sequence)]
```

### 2️⃣ **User Drop-Off Analysis**
```python
# Funnel construction
funnel = pd.DataFrame(index=event_sequence, columns=['users'])
for i, event in enumerate(event_sequence):
    if i == 0:
        funnel.loc[event, 'users'] = filtered_data[filtered_data['event_name'] == event]['device_id'].nunique()
    else:
        prev_users = filtered_data[filtered_data['event_name'] == event_sequence[i - 1]]['device_id'].unique()
        funnel.loc[event, 'users'] = filtered_data[(filtered_data['event_name'] == event) & (filtered_data['device_id'].isin(prev_users))]['device_id'].nunique()

# Funnel visualization
fig = go.Figure(go.Funnel(y=funnel.index, x=funnel['users'], textinfo="value+percent initial"))
fig.update_layout(title='Sales Funnel')
fig.show()
```

---

## ⚖️ **A/A/B Test Analysis**

### 1️⃣ **Control and Test Groups**
```python
# Users per group
users_per_group = data.groupby('exp_id')['device_id'].nunique()
print(users_per_group)
```

### 2️⃣ **Proportion Tests**
```python
def z_test_proportions(df1, df2, event_name):
    ... # Function details omitted for brevity

# Run tests for event sequence
for event in event_sequence:
    z_test_proportions(df_control_1, df_test, event)
```

---

## 🏁 **Conclusions**

### 1️⃣ **Sales Funnel Insights:**
- Major drop-off occurs at **OffersScreenAppear**.
- About **47.63%** of users complete the funnel.

### 2️⃣ **A/A/B Test Findings:**
- No statistically significant differences were found between groups, indicating that the font change did not impact user behavior.

### 3️⃣ **Recommendations:**
- Focus on improving the **OffersScreenAppear** stage to reduce user drop-off.
- Conduct further analyses to identify other potential friction points.

---

📅 **Date:** January 6, 2025  
✍️ **Francisco SLG**

