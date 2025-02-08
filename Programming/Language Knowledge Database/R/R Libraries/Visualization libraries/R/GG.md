

![[ggplot2 cheat sheet.pdf]]

## GGally
#### `ggpairs`
Core tips:
- To remove `binwidth` warnings in `stat_bin`, 
- To remove progress bar, enable `progress=FALSE`

Dealing with binary variables:
```r
ggpairs(
  glioma_num[, c("Grade", "Gender", "Age")],
  aes(alpha = 0.5),
  lower = list(
    continuous = wrap(
      "points", 
      shape = 16, 
      position = position_jitter(width = 0.1, height = 0.1)
    )
  ),
  progress = FALSE
)
```
![[Pasted image 20240904182108.png]]

Adding jitter on the categorical variables:
```r
ggpairs(
  q21,
  lower = list(continuous = wrap(
    "points", 
    position = position_jitter(height = 0.2, width = 0.2),
    alpha = 0.5)
  )
)
```
![[GGally-ggpairs-jitter.png]]

## Basic plot code-dump
Boxplot setup:
```r
ggplot(data, aes(x=factor(trt), y=y, fill=factor(trt))) + 
  stat_boxplot(geom='errorbar', width=0.3) + 
  geom_boxplot(width=0.5, fatten=3, coef=T, outlier.shape=NA) + 
  scale_fill_manual(values = c("gray", "lightblue")) + 
  labs(x="Treatment groups", 
       title="Distribution of treatment before adjustment") + 
  theme_classic() + 
  theme(plot.title=element_text(hjust=0.5), 
        legend.position = "none", 
        aspect.ratio = 5/6)
```
![[Pasted image 20241111182446.png]]

Repeated measures scatterplot setup (the `geom_text` is awful but):
```r
ggplot(data, aes(x=x, y=y, color=factor(trt))) + 
  geom_text(aes(color=factor(trt), label=trt), size = 5) +
  scale_color_manual(values = c("black", "red")) + 
  labs(title="Scatterplot before adjusting for X") + 
  theme_classic() + 
  theme(plot.title = element_text(hjust=0.5), 
        legend.position="none", 
        aspect.ratio = 5/6)
```
![[GGPlot2_RepeatedScatter.png]]

Blocked design scatter
```r
metabolic.scatter <- ggplot(data=metabolic, aes(x=subject, y=rate, color=protocol)) +
  geom_point(aes(shape=protocol), size=3) + 
  geom_line(aes(linetype=protocol)) + 
  scale_color_manual(values = c("#1B9E77", "#D95F02", "#7570B3")) + 
  labs(x="Subject", y="Resting metabolic rate") + 
  theme(legend.position="top") + 
  theme_classic() + 
  theme(panel.grid.major = element_line(color = "grey75", size=0.2), 
        panel.grid.minor = element_line(color = "grey80", size=0.2))

ggsave("rate_scatter.png", plot = last_plot(), width = 7, height = 5, dpi = 300)
```
![[GGPlot2_BlockedScatter.png]]

