https://patchwork.data-imaginist.com/

```
p3 <- ggplot(mtcars) + geom_smooth(aes(disp, qsec))
p4 <- ggplot(mtcars) + geom_bar(aes(carb))

(p1 | p2 | p3) /
      p4
```
![[Pasted image 20240901181121.png]]

For up to 3 plots `|` will behave just as `+` but using `|` will communicate the intended layout better.  `/` will be evaluated before `+`, so use braces `()`.

This remains a ggplot2 object and can be added to as normal, `plotly_object + geom_point()`.

**Adding non-ggplot2 content**:
`p1 + grid::textGrob('Some really important text')`
![[Pasted image 20240901181229.png]]

`p1 + gridExtra::tableGrob(mtcars[1:10, c('mpg', 'disp')])`
![[Pasted image 20240901181241.png]]

Aligning no longer works correctly, but you can mess around with `par` to get something workable.
```
old_par <- par(mar = c(0, 2, 0, 0), bg = NA)
p1 + wrap_elements(panel = ~plot(mtcars$mpg, mtcars$disp), clip = FALSE)
par(old_par)
```
![[Pasted image 20240901181332.png]]

You can try to add labels and styles to wrapped elements but try to use ggplot2 if possible.
```
old_par <- par(mar = c(0, 0, 0, 0), mgp = c(1, 0.25, 0), 
               bg = NA, cex.axis = 0.75, las = 1, tcl = -0.25)
p1 + 
  wrap_elements(panel = ~plot(mtcars$mpg, mtcars$disp), clip = FALSE) + 
  ggtitle('Plot 2') + 
  theme(plot.margin = margin(5.5, 5.5, 5.5, 35))
par(old_par)
```

Adding text on the left side:
```
# This won't do anything
grid::textGrob('Text on left side') + p1
```

```
# This will work
wrap_elements(grid::textGrob('Text on left side')) + p1
```
#### Controlling the grid further
**Add an empty, blank area with `plot_spacer()`.**

Plotly defaults to making the grid as square as possible, erring to the side of a horizontal grid if a square is not possible.  Use `plot_layout(widths = c(), heights = c(), ncol = 0`).

```
p1 + p2 + p3 + p4 + 
  plot_layout(widths = c(2, 1), heights = unit(c(5, 1), c('cm', 'null')))
```
![[Pasted image 20240901182347.png]]

You can actually define the grid with text beforehand, which is very dynamic:
```
layout <- "
##BBBB
AACCDD
##CCDD
"
p1 + p2 + p3 + p4 + 
  plot_layout(design = layout)
```
![[Pasted image 20240901182436.png]]

Make fixed aspects on the plots like this:
```
p_fixed <- ggplot(mtcars) + 
  geom_point(aes(hp, disp)) + 
  ggtitle('Plot F') + 
  coord_fixed()
p_fixed + p1 + p2 + p3
```
![[Pasted image 20240901182605.png]]

`free()` avoids alignment if you have long labels and want the overall alignment to be priority.
`free(p1) | p2mod`
![[Pasted image 20240901182722.png]]

#### Inset plots
`p1 + inset_element(p2, left = 0.6, bottom = 0.6, right = 1, top = 1)`
![[Pasted image 20240901182813.png]]

A more programmatic way is with the `area()` constructor.
```
layout <- c(
  area(t = 2, l = 1, b = 5, r = 4),
  area(t = 1, l = 3, b = 3, r = 5)
)
p1 + p2 + 
  plot_layout(design = layout)
```
![[Pasted image 20240901192940.png]]
#### Controlling legends and guides
You can have global legends using `collect`.  This removes duplicate guides:
`((p2 / p3 + plot_layout(guides = 'auto')) | p1) + plot_layout(guides = 'collect')`
![[Pasted image 20240901182919.png]]

`(p1a | (p2 / p3)) + plot_layout(guides = 'collect')`
![[Pasted image 20240901183048.png]]

The axes can also be collected to avoid redundancy:
`p1 + p2 + plot_layout(axes = "collect")`
![[Pasted image 20240901193158.png]]

