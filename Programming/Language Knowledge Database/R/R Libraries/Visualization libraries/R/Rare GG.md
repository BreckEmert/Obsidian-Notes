```
# Exclude genes
genes_data <- glioma_num[, -(which(names(glioma_num) %in% c("Grade", "Gender", "Age")))]

# Melt and factor
data_melted <- melt(genes_data, id.vars = NULL)
data_melted$value <- factor(data_melted$value, levels = c(0, 1), labels = c("No Mutation", "Mutation"))

# Plot
plot <- ggplot(data_melted, aes(x = variable, fill = value)) +
  geom_bar(position = "stack") +
  labs(x = "Gene", y = "Count", title = "Ratios of Mutations", fill = "Legend") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))  # Rotate x-axis labels for better readability
print(plot)
```
![[fancy r ratio graph.png]]
