# Seaborn

## Importing
``` python
import seaborn as sns
import matplotlib.pyplot as plt
```

## Basic Plots
**Scatterplot**
``` python
# random lists
height = [45, 78, 23, 90, 55, 67]
weight = [25, 67, 86, 36, 90, 86]
# create the plot
sns.scatterplot(x=weight, y=height)
# show the plot
plt.show()
```
