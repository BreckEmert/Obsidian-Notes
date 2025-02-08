
```
import matplotlib.patches as mpatches
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns
from matplotlib.gridspec import GridSpec

# 10 examples of chi-square(2) data
all_data = np.random.chisquare(2, (10, 10))
primary_data = all_data[0]
all_data = all_data.flatten()
print(primary_data)
print(len(all_data))

# Create a GridSpec and specify colors
fig = plt.figure(figsize=(15, 6))
gs = GridSpec(1, 4, width_ratios=[1, 1, 1, 0.4])

palette = sns.color_palette("Set2")
color1 = palette[1]
color2 = palette[2]

# Boxplot
boxplot_data_1 = pd.DataFrame({'Value': all_data}).assign(Group='Sample Size = 100')
boxplot_data_2 = pd.DataFrame({'Value': primary_data}).assign(Group='Sample Size = 10')
boxplot_data = pd.concat([boxplot_data_2, boxplot_data_1])

ax1 = fig.add_subplot(gs[0, 0])
sns.boxplot(x='Group', y='Value', data=boxplot_data, palette={'Sample Size = 10': color1, 'Sample Size = 100': color2}, ax=ax1)
ax1.set_xlabel('')
ax1.set_title('Box Plot by Sample Size')

# Histogram
ax2 = fig.add_subplot(gs[0, 1])
sns.histplot(all_data, color=color2, edgecolor='none', ax=ax2)
sns.histplot(primary_data, color=color1, ax=ax2)
ax2.set_title('Histogram by Sample Size')

# Bar plot
ax3 = fig.add_subplot(gs[0, 2])
sns.barplot(x=np.arange(len(all_data)), y=all_data, color=color2, ax=ax3)
sns.barplot(x=np.arange(len(primary_data)), y=primary_data, color=color1, ax=ax3)
ax3.set_xticks([])
ax3.set_title('Bar Plot by Sample Size')

# Custom Legend
sample_10 = mpatches.Patch(color=color1, label='Sample Size = 10')
sample_100 = mpatches.Patch(color=color2, label='Sample Size = 100')
fig.legend(handles=[sample_10, sample_100], loc='center right', bbox_to_anchor=(.99, 0.5))

plt.tight_layout()
plt.show()


# Statistics
mean_primary = np.mean(primary_data)
median_primary = np.median(primary_data)
mode_primary = pd.Series(primary_data).mode()[0]
std_primary = np.std(primary_data)

mean_all = np.mean(all_data)
median_all = np.median(all_data)
mode_all = pd.Series(all_data).mode()[0]
std_all = np.std(all_data)

stats_primary = pd.DataFrame({
    'Statistic': ['Mean', 'Median', 'Mode', 'Std Dev'],
    'Value': [mean_primary, median_primary, mode_primary, std_primary]
})
stats_all = pd.DataFrame({
    'Statistic': ['Mean', 'Median', 'Mode', 'Std Dev'],
    'Value': [mean_all, median_all, mode_all, std_all]
})

# Plot statistics
plt.figure(figsize=(10, 6))

max_value = -(-max(stats_primary['Value'].max(), stats_all['Value'].max()) * 1.1 // 1)
plt.subplot(1, 2, 1)
plt.ylim(0, max_value)
plt.subplot(1, 2, 2)
plt.ylim(0, max_value)

# Sample Size = 10
plt.subplot(1, 2, 1)
sns.barplot(x='Statistic', y='Value', data=stats_primary, palette=[color1]*4)
plt.title('Statistics for Sample Size = 10')

# Sample Size = 100
plt.subplot(1, 2, 2)
sns.barplot(x='Statistic', y='Value', data=stats_all, palette=[color2]*4)
plt.title('Statistics for Sample Size = 100')

plt.tight_layout()
plt.show()

```
![[Plots.png]]
![[Statistics.png]]

