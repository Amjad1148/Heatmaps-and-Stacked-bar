# Heatmaps-and-Stacked-bar

```
# Important Libraries         
                  library(tidyverse)
                  library(UpSetR)
                  library(reshape2)
                  library(pheatmap)
```                  
# Load gene count matrix
```                  
                  df <- read.delim("C:/Users/lenovo/Desktop/WheatNLR/Data/1_thesis_data/1_yield/Orthofinder_results/Results_Apr24_5/Orthogroups/Orthogroups.GeneCount.tsv", row.names = 1, check.names=FALSE)
 ```                 
            
                
# Specify the species and groupings you are interested in
           ```      
            species <- c("CS_refseqv2", "Abo", "AMN", "ZM366", 
                               "Ae.speltoides", "Ae.tauschii", "T.turgidum", "T.urartu_2.1")
                  group <- c("Hexaploid", "Hexaploid", "Hexaploid", "Hexaploid",
                             "Wild", "Wild", "Tetraploid", "Wild")
                  ```
# Subset the dataframe for the species of interest
                ``` 
                  df_filtered <- df[, species]
                  ```
 # Create binary matrix for UpSet plot
 ```
                  binary_df <- df_filtered
                  binary_df[binary_df > 0] <- 1
```                  
 # UpSet plot
```                  
                  upset(binary_df, sets = colnames(binary_df), order.by = "freq")
```

![image](https://github.com/user-attachments/assets/67fdf7d4-f13d-42e2-907e-77570e786eb1)
        
# Heatmap of top 100 variable orthogroups
```
                  top_orthos <- df_filtered %>% mutate(var=rowSums(. > 0)) %>% 
                    arrange(desc(var)) %>% head(100)
                  pheatmap(as.matrix(top_orthos), scale="row")
```                  
![image](https://github.com/user-attachments/assets/b53c2b71-90c1-4824-949a-fd982cb13322)
                 
# Stacked Bar
```                  
                  
                   df_filtered$Orthogroup <- rownames(df_filtered)
```                  
# Melt the matrix into long format
```                  
                  df_long <- melt(df_filtered, id.vars = "Orthogroup", variable.name = "Species", value.name = "CopyNumber")
```                  
 # Add GeneType column
```                  
                  df_long <- df_long %>% mutate(GeneType = case_when(
                    CopyNumber == 0 ~ "Absent",
                    CopyNumber == 1 ~ "Single",
                    CopyNumber > 1 ~ "Multi"
                  ))
```                  
# Add Group column based on species
```
                  df_long$Group <- factor(rep(group, length.out = nrow(df_long)), levels = unique(group))
```                  
# Plot the stacked bar chart
```
                  
                  ggplot(df_long, aes(x = Species, fill = GeneType)) +
                    geom_bar(position = "fill") +
                    labs(y = "Proportion", title = "Gene Copy Type Distribution") +
                    facet_wrap(~ Group)
```
![image](https://github.com/user-attachments/assets/168dd11e-c2be-43f6-adcc-0a981e2f4342)


