# R-Programming-with-ggplot2-and-dplry
### Overview of R
R is a programming language and free software environment widely used for statistical computing, data analysis, and graphical representation. Personally, Python is easy to use interms of code and has high community for lessorn, learning and tutorials so its a popular language and powerful. R is renowned for its capability to produce high-quality plots and graphics, which can be customized to a GREAT EXTENT, and this is what will take advantage. R has default IDE which is R Studio which is very much user friendly and interactive. With a backgroud in statistics, when it comes to statistical analysis and visual analytics I choose R among data languages. I will do other projects with R for statistical tests but at the moment I will do some basic cleaning using dplyr and ggplot2 visualization. 
Use cases;
- Academic Research: Widely used in academia for statistical research and teaching.
- Data Science and Analytics: Employed by data scientists for exploratory data analysis, modeling, and visualization.
- Biostatistics and Bioinformatics: Utilized in the analysis of biological data, such as genomics and clinical trials.
- Finance: Used for financial modeling, risk assessment, and quantitative analysis.
- Marketing: Helps in customer segmentation, sentiment analysis, and market researc

![Banner-Image-2](https://github.com/Ackson507/R-Programming-with-ggplot2-and-dplry/assets/84422970/a9bcc4ba-38f8-4768-928d-1b9c46187ed1)


### Problem Statement
The project aims to analyze the mortality data from different countries to understand the impact of various health diseases on death rates from 1990 to 2020.

### Objective
- Trends in mortality rates over the specified period.
- Compare the impact of different health diseases on mortality across selected diseases in Zambia.
- Compare the motarity rate of different diseases in Zambia.

### Tidyverse
After dowloading and istalling R and RStudio on the local machine, before anything we have to intall packages in our working directory. Unlike Python, in R we have a collection of package for data science compiled together in  a library know as tidycerse. Tidyverse is a collection of R packages designed for data science. It is an ecosystem that promotes tidy data principles, making data manipulation, exploration, and visualization easier and more intuitive. Open the terminal and run the called library(tidyverse), below are the packages that come with it.
![Screenshot (632)](https://github.com/Ackson507/R-Programming-with-ggplot2-and-dplry/assets/84422970/6a05eed3-b3e3-4cd5-90f5-70f4a5702de8)

### Data Collection and Loading
We collect data from either coming from databases, APIs, spreadsheets, or other data sources. For this practice it is just a csv file sourced from Kaggle
```R
#Libraries
library(ggplot2) # Visualization
library(dplyr)   # Data manipulation
library(fs)

 
# Loading the data
df <- read.csv("data/Death_Cause_disease_20.csv")

```

### Data Cleaning and Preprocessing using dplyr

Cleaning: Handle missing values, remove duplicates, and correct errors in the dataset.
Preprocessing: Format the data appropriately, convert data types if necessary, and create new variables or features that will help in the analysis.

```r
#1. Lets start with counting null values using (sapply) 
null_rows <- sapply(df, function(x) sum(is.na(x)))
print(null_rows)

#2 Remove rows with any NA values using (na.omit()).
clean_df <- na.omit(df)

#3 Lets check the datatype of the columns. Check the class of each column first
col_dtypes <- sapply(clean_df, class)
print(col_dtypes)

#4 Renaming a column
clean_df <- clean_df %>%
  rename(Diseases = Causes_allsexage)

#5 Summary Stats for numerical columns such as min, max, mean

summary_stats <- clean_df %>%
  summarise_if(is.numeric, list(
    mean = ~mean(.),
    min = ~min(.),
    max = ~max(.)
  ))
print(summary_stats)

```

### Exploratory Data Analysis (EDA) using ggplot2
Use summary statistics and simple visualizations (like histograms, box plots, and scatter plots) to get a feel for the data. We will just filter the dataflame into 2 datasets to only consist of 5 diseases across all countries and another filter to include five disease across country to zambia only.

```r
#1. Lets start with filtering column for disease so that we study just five condition in our df_clean
filtered_df <- clean_df %>%
  filter(Causes_allsexage %in% c("Malaria","Digestive diseases","Tuberculosis","HIV/AIDS","Diabetes mellitus"))

#2 We narrow it to zambia only
zam  <- filtered_df %>%
  filter(Entity %in% c("Zambia"))
```

PLOT 1: Bar plot  for the number of death associated to five seclected diseases which are Malaria, Digestive diseases, Tuberculosis, HIV/AIDS", and Diabetes mellitus across all countries in the dataset
```r
ggplot(filtered_df, 
       aes(x = Diseases, y = Death_Numbers)) +
  geom_bar(stat = "identity", fill = "darkgreen") +
  labs(title = "Number of death per condition from 2017-2020", x = "Conditions", y = "Number of deaths") +
  scale_y_continuous(labels = scales::comma) +  # Use commas to format y-axis labels
  theme(panel.grid.major.y = element_line(color = "black", linetype = "dotted"),  # Customize horizontal grid lines
        panel.grid.major.x = element_blank(),  # Remove vertical grid lines
        panel.grid.minor = element_blank())   # Remove minor grid lines

```
![all_countries](https://github.com/Ackson507/R-Programming-with-ggplot2-and-dplry/assets/84422970/a36aa770-94a3-40d0-9843-30cfb8b42db0)

PLOT 2: Bar plot for the number of death associated to five seclected diseases which are Malaria, Digestive diseases, Tuberculosis, HIV/AIDS", and Diabetes mellitus
```r
ggplot(zam, 
     aes(x = Diseases, y = Death_Numbers)) +
     geom_bar(stat = "identity", fill = "darkgreen") +
     labs(title = "Number of death per condition from 2017-2020", x = "Conditions", y = "Number of deaths") +
     scale_y_continuous(labels = scales::comma) +  # Use commas to format y-axis labels
     theme(panel.grid.major.y = element_line(color = "black", linetype = "dotted"),  # Customize horizontal grid lines
           panel.grid.major.x = element_blank(),  # Remove vertical grid lines
           panel.grid.minor = element_blank())   # Remove minor grid lines
```
![zambia_5](https://github.com/Ackson507/R-Programming-with-ggplot2-and-dplry/assets/84422970/6cc754bb-c3c8-4700-a3e9-2b15fbeac1d1)


PLOT 3: Line plot for the number of death associated to five seclected diseases which are Malaria, Digestive diseases, Tuberculosis, HIV/AIDS", and Diabetes mellitus. this plot is useful to check the trend of each disease in this period
```r
#2 Create the line plot.ZAMBIA
lines <- ggplot(data = zam, aes(x = Year, y = Death_Numbers, color = Diseases)) +
  geom_line() +
  geom_point() +
  labs(title = "Trend of Diseases by Deaths from 2007 to 2020",
       x = "Year",
       y = "Number of Deaths") +
  scale_y_continuous(labels = scales::comma) +  # Use commas to format y-axis labels
  theme_minimal() +
  theme(legend.title = element_text(face = "bold")) +
  theme(panel.grid.major.y = element_line(color = "black", linetype = "dotted"),  # Customize horizontal grid lines
        panel.grid.major.x = element_blank(),  # Remove vertical grid lines
        panel.grid.minor = element_blank())    # Remove minor grid lines
# Display the plot
print(lines)
```
![zam_line_5](https://github.com/Ackson507/R-Programming-with-ggplot2-and-dplry/assets/84422970/f45e7aae-69e7-4420-acd0-b1a1b49a202e)

Below is the chart after excluding HIV/AIDS so that we properly view trend of the disease at proper scale.
![zam_4_disea](https://github.com/Ackson507/R-Programming-with-ggplot2-and-dplry/assets/84422970/0e424578-95e6-489b-897c-bb45bbb61255)

# Insights from the plots
-  The graph can easily tell an easy story that Digestive diseases is a leading cause of death among selected diseases across the countries followed by Tuberculosis. Given the insight that digestive diseases and tuberculosis are leading causes of death, there are several additional layers of analysis and storytelling we can explore to provide a comprehensive understanding of the data. The next step is to Post-hoc test or anlysis so that we break down and understand the root cause of the happen. this can be done by exploring more in terms of demographic breakdown such as gender and age, Comparative Analysis by Country, Public Health Implications and Explore the relationship between digestive diseases, tuberculosis, and other health conditions. For example, is there a high incidence of digestive diseases among tuberculosis patients?
-  Filtering more to select a particluar. As ealier mentioned plot 2 we filter the dataset to explore various diseases particularly in country Zambia which shows that most deaths came from HIV and on the third line plot it shows the trend of each disease.
-  Trend line plot shown for HIV/AIDS in the period 2000 to 2008 started trending down and this is non other than the intervention and provision of free anti-retroviral therapy in Zambia which started the year in Jun 2002.













