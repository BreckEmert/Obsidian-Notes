**Simple plots of all methods using simple x vs. y**
```
# Load necessary libraries
library(ggplot2)
library(splines)
library(mgcv)

# Base plot for unemployment average by year
base_plot <- ggplot(unemployment.global.avg, aes(x = Year, y = Average_Unemployment)) +
  geom_point() + 
  theme_minimal() +
  labs(title = "Average Unemployment Over Years", x = "Year", y = "Average Unemployment Rate")

# Cubic splines
base_plot + 
  geom_smooth(method = "lm", formula = y ~ bs(x, degree = 3), se = FALSE, color = "blue") +
  labs(subtitle = "Cubic Splines")

# Natural splines
base_plot + 
  geom_smooth(method = "lm", formula = y ~ ns(x, df = 4), se = FALSE, color = "green") +
  labs(subtitle = "Natural Splines")

# Smoothing splines
spline_fit <- smooth.spline(unemployment.global.avg$Year, unemployment.global.avg$Average_Unemployment)

base_plot + 
  geom_line(aes(x = spline_fit$x, y = spline_fit$y), color = "purple") +
  labs(subtitle = "Smoothing Splines")

# LOESS
base_plot + 
  geom_smooth(method = "loess", se = FALSE, color = "orange", span = 0.75) +
  labs(subtitle = "Local Regression (LOESS)")

# GAM
base_plot + 
  geom_smooth(method = "gam", formula = y ~ s(x), se = FALSE, color = "red") +
  labs(subtitle = "Generalized Additive Model (GAM)")
```