
```
summary_table <- 
  subset(glioma, select = -Age) %>%
  tbl_summary(by=Grade) %>%
  modify_header(label ~ "**Variable**") %>%
  modify_spanning_header(c("stat_1", "stat_2") ~ "**Grade**")
```
![[Pasted image 20240904182232.png]]
Save this table as a png with
`gt::gtsave(as_gt(summary_table), zoom=5, "summary table.png")`

## `KableExtra`
https://cran.r-project.org/web/packages/kableExtra/vignettes/awesome_table_in_html.html

```r
# Pivot to show experimental setup
block_table <- fresh %>%
  select(week, store, trtmt) %>%  # Remove outcome 'sales' as we're just looking at the experimental setup
  pivot_wider(names_from = store, values_from = trtmt) %>%  # Stores as columns, treatments as values
  arrange(week)  # Week as rows

# Format the table
design <- block_table %>%
  rename(" " = week) %>%  # Remove week column label
  mutate(
    ` ` = cell_spec(` `, "html", bold = TRUE),  # Bold week column
    across(-1, ~ cell_spec(., "html", color = "white", 
      background = spec_color(as.numeric(.), option = "C", end = 0.8)))  # Color palette
  ) %>%
  kbl(escape = FALSE, caption = "<b style='text-align: center; font-size: 1.7em;'
      >Weeks (rows) × <br>Stores (columns)<b>") %>%  # Title
  kable_styling(
    bootstrap_options = c("striped", "hover", "condensed"),
    full_width = FALSE,
    font_size = 30
  )

# install.packages("magick") relevant?
webshot2::save_kable(design, file="design.jpg")

# If save_kable doesn't work
webshot2::save_kable(design, file = "design.html")
browseURL("design.html")
```
![[RowColumnDesign_FancyKableTable.png]]