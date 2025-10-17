# Electric-vehicle-data-analysis-python-project
import numpy as np
import pandas as pd
df = pd.read_csv(r"C:\Users\Admin\Downloads\FEV-data-Excel.xlsx - Auta elektryczne.csv")

# Task 1: A customer has a budget of 350,000 PLN and wants an EV with a minimum range of 400 km.
a) Your task is to filter out EVs that meet these criteria.
b) Group them by the manufacturer (Make).
c) Calculate the average battery capacity for each manufacturer.

df.rename(columns = {"Minimal price (gross) [PLN]": "Minimal_price_(gross)_[PLN]","Range (WLTP) [km]": "Range_(WLTP)_[km]","Battery capacity [kWh]": "Battery_capacity_[kwh]", "mean - Energy consumption [kWh/100 km]": "mean_Energy_consumption_[kWh/100 km]","Engine power [KM]": "Engine_power_[KM]"}, inplace = True)

Budget = 350000
Min_range = 400

filtered_ev = df[(df["Minimal_price_(gross)_[PLN]"]<=Budget) & (df["Range_(WLTP)_[km]"]>=Min_range)]
filtered_ev

filtered_ev.groupby("Make").size()

avg_battery_capacity = df.groupby("Make")["Battery_capacity_[kwh]"].mean()
avg_battery_capacity

# Task 2: You suspect some EVs have unusually high or low energy consumption.
Find the outliers in the mean - Energy consumption [kWh/100 km] column.

from scipy.stats import zscore
df["zscore"] = zscore(df["mean_Energy_consumption_[kWh/100 km]"])
outliers = df[df["zscore"].abs() > 3]
print(outliers[["mean_Energy_consumption_[kWh/100 km]", "zscore"]])

import seaborn as sns
import matplotlib.pyplot as plt
sns.boxplot(x = df["mean_Energy_consumption_[kWh/100 km]"])
plt.title("boxplot of mean energy consumption")
plt.show()

# Task 3: Your manager wants to know if there's a strong relationship between battery capacity and range.
a) Create a suitable plot to visualize.
b) Highlight any insights.

corr = df[["Battery_capacity_[kwh]","Range_(WLTP)_[km]"]].corr()
corr

plt.figure(figsize = (4,3))
sns.scatterplot( data = df, x = "Battery_capacity_[kwh]", y = "Range_(WLTP)_[km]", palette = "tab10")
plt.title("battery capacity vs range")
plt.xlabel("Battery Capacity [kWh]")
plt.ylabel("Range (WLTP) [km]")
plt.legend()
plt.tight_layout()
plt.show()

# Task 4: Build an EV recommendation class. 
The class should allow users to input their
budget, desired range, and battery capacity. The class should then return the top three EVs
matching their criteria.

budget = int(input("enter your budget"))
desired_range = int(input("enter the range"))
battery_capacity = int(input("enter the battery capacity"))
ev_recommendation_class = df[(df["Minimal_price_(gross)_[PLN]"]<=budget) & (df["Range_(WLTP)_[km]"]>=desired_range) & (df["Battery_capacity_[kwh]"]>=battery_capacity)]
ev_recommendation_class.head(3)

# Inferential Statistics – Hypothesis Testing: 
Test whether there is a significant difference in the average Engine power [KM] of vehicles manufactured by two leading
manufacturers i.e. Tesla and Audi. What insights can you draw from the test results?
Recommendations and Conclusion: Provide actionable insights based on your analysis.
(Conduct a two sample t-test using ttest_ind from scipy.stats module)

# Null Hypothesis(H0): there is no significance difference between audi and tesa engine power
# Alternative hypothesis(H1): there is difference

from scipy.stats import ttest_ind
import numpy as np
audi_power = df[df["Make"] == "Audi"]["Engine_power_[KM]"]
tesla_power = df[df["Make"] == "Tesla"]["Engine_power_[KM]"]
# Two-sample t-test
t_stat, p_value = ttest_ind(tesla_power, audi_power, equal_var=False)
print(f"T-statistic: {t_stat:.4f}")
print(f"P-value: {p_value:.4f}")
# Since p-value (0.1068) > 0.05, we fail to reject the null hypothesis.
# There is no statistically significant difference in the average engine power between Tesla and Audi vehicles.



