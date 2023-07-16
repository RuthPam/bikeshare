---
title: "DIVVY CASE STUDY 202205 -202305"
author: "Ruthcell Pamintuan"
date: "2023-07-16"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
# Introduction

## About the Company

Cyclistic is a bike-share company based in Chicago that launched its successful bike-sharing offering in 2016. With a fleet of 5,824 bicycles and a network of 692 stations, Cyclistic provides convenient transportation options for riders across the city. The bike-share program allows riders to unlock bikes from one station and return them to any other station in the system at any time.

Cyclistic serves two main types of riders: annual members and casual riders. Annual members have a subscription plan, while casual riders purchase single-ride or full-day passes. The company aims to maximize the number of annual memberships as they are more profitable and provide a stable revenue stream.

## Project Objective

In this analysis, we will explore how annual members and casual riders use Cyclistic bikes differently. By understanding their usage patterns and preferences, we can identify opportunities to convert casual riders into annual members. This analysis will provide insights into the factors that drive casual riders to purchase annual memberships and how digital media can be leveraged to influence their decision-making.

## Data Source

For this analysis, we will be using the public dataset provided by Motivate International Inc., the company behind Cyclistic. The dataset contains bike trip data for the year 2022, including information on trip duration, start and end stations, rider type, and more. The dataset is available in CSV format and consists of 13 columns.
<https://divvy-tripdata.s3.amazonaws.com/index.html>
## Analysis Approach

To address the project objectives, we will follow a structured data analysis process. This process includes the following steps:

1. Data Preparation: We will clean and preprocess the data, handling missing values, data formatting, and any necessary transformations.
2. Exploratory Data Analysis: We will explore the data to gain insights into riders' behavior, usage patterns, and trends. This analysis will involve visualizations and descriptive statistics.
3. Comparative Analysis: We will compare the usage patterns of annual members and casual riders, examining factors such as trip duration, popular stations, and riding preferences.
4. Customer Segmentation: We will segment the riders based on their characteristics and behavior to identify distinct groups and tailor marketing strategies accordingly.
5. Marketing Recommendations: Based on the analysis findings, we will provide recommendations on how Cyclistic can attract and convert casual riders into annual members using digital media and targeted marketing campaigns.

The analysis will be conducted using R programming language and various data analysis and visualization packages.

Now, let's dive into the data preparation step and start exploring the Cyclistic bike trip data!

```{r}
install.packages('tidyverse')
install.packages('lubridate')
install.packages('ggplot2')
install.packages('dplyr')
library(tidyverse)  #helps wrangle data
library(lubridate)  #helps wrangle date attributes
library(ggplot2)#helps visualize data
library(dplyr)
getwd() #displays your working directory
setwd("C:/Users/ruthp/OneDrive/Desktop/bikeshare")

```
# Upload Divvy datasets (csv files)
```{r}
may2022 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202205-divvy-tripdata.csv")
june2022 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202206-divvy-tripdata.csv")
july2022 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202207-divvy-tripdata.csv")
august2022 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202208-divvy-tripdata.csv")
september2022 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202209-divvy-publictripdata.csv")
october2022 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202210-divvy-tripdata.csv")
november2022 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202211-divvy-tripdata.csv")
december2022 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202212-divvy-tripdata.csv")
january2023 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202301-divvy-tripdata.csv")
february2023 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202302-divvy-tripdata.csv")
march2023 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202303-divvy-tripdata.csv")
april2023 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202304-divvy-tripdata.csv")
may2023 <- read_csv("C:/Users/ruthp/OneDrive/Desktop/bikeshare/202305-divvy-tripdata.csv")

```
# Compare column names each of the files
# While the names don't have to be in the same order, they DO need to match perfectly before we can use a command to join them into one file
```{r}
colnames(may2022)
colnames(june2022)
colnames(july2022)
colnames(august2022)
colnames(september2022)
colnames(october2022)
colnames(november2022)
colnames(december2022)
colnames(january2023)
colnames(february2023)
colnames(march2023)
colnames(april2023)
colnames(may2023)

```
# Inspect the dataframes and look for incongruencies
```{r}
str(may2022)
str(june2022)
str(july2022)
str(august2022)
str(september2022)
str(october2022)
str(november2022)
str(december2022)
str(january2023)
str(february2023)
str(march2023)
str(april2023)
str(may2023)

```

# Combine all data frames using rbind
```{r}
all_trips <- rbind(may2022, june2022, july2022, august2022, september2022, october2022, november2022, december2022, january2023, february2023, march2023)
```
# Inspect the new table that has been created
```{r}
colnames(all_trips)  #List of column names
nrow(all_trips)  #How many rows are in data frame?   5432471
dim(all_trips)  #Dimensions of the data frame?   5432471   13
head(all_trips)  #See the first 6 rows of data frame.  Also tail(all_trips)
str(all_trips)  #See list of columns and data types (numeric, character, etc)
summary(all_trips)  #Statistical summary of data. Mainly for numerics

```
# Convert started_at column to date format
```{r}
all_trips$date <- as.Date(all_trips$started_at) # Convert started_at column to date format
all_trips$month <- format(all_trips$date, "%m") # Extract month from the date
all_trips$day <- format(all_trips$date, "%d") # Extract day from the date
all_trips$year <- format(all_trips$date, "%Y") # Extract year from the date
all_trips$day_of_week <- format(all_trips$date, "%A") # Extract day of the week from the date

```
# Add a "ride_length" calculation to all_trips(in seconds)
# Calculate ride_length in seconds
```{r}
all_trips <- transform(all_trips, ride_length = as.numeric(difftime(ended_at, started_at, units = "secs")))
```
# Inspect the structure of the columns
```{r}
str(all_trips)
```
# Convert "ride_length"  to numeric so we can run calculations on the data
```{r}
is.numeric(all_trips$ride_length)

all_trips$ride_length <- as.numeric(as.character(all_trips$ride_length))
is.numeric(all_trips$ride_length)

```
# We will create a new version of the dataframe (v2) since data is being removed
```{r}
all_trips_v2 <- subset(all_trips_v2, !(start_station_name == "HQ QR" | ride_length < 0))
```
#Conductive Analysis
#straight average (total ride length / rides)
```{r}
mean(all_trips$ride_length)
```
#midpoint number in the ascending array of ride lengths
```{r}
median(all_trips_v2$ride_length)
```
#longest ride
```{r}
max(all_trips_v2$ride_length)
```
#shortest ride
```{r}
min(all_trips_v2$ride_length)
```
## Compare members and casual users
```{r}
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = mean)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = median)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = max)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = min)

```
# Notice that the days of the week are out of order. Let's fix that
```{r}
all_trips_v2$day_of_week <- factor(all_trips_v2$day_of_week, levels = c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
```
# Now, let's run the average ride time by each day for members vs casual users
```{r}
ride_time_avg <- aggregate(ride_length ~ day_of_week + member_casual, data = all_trips_v2, FUN = mean)
```
# analyze ridership data by type and weekday
```{r}
all_trips_v2 %>%
  mutate(weekday = lubridate::wday(started_at, label = TRUE)) %>%
  group_by(member_casual, weekday) %>%
  summarise(number_of_rides = n(),
            average_duration = mean(ride_length)) %>%
  arrange(member_casual, weekday)

```
# Let's visualize the number of rides by rider type
```{r}
all_trips_v2 %>%
  mutate(weekday = lubridate::wday(started_at, label = TRUE)) %>%
  group_by(member_casual, weekday) %>%
  summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>%
  arrange(member_casual, weekday) %>%
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")

```
# Let's create a visualization for average duration
```{r}
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(x = "Weekday", y = "Average Duration", fill = "Member Type") +
  ggtitle("Average Duration of Rides by Member Type and Weekday") +
  theme_minimal()

```
##EXPORT SUMMARY FILE FOR FURTHER ANALYSIS
```{r}
counts <- aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
```

```{r}
write.csv(counts, file = "avg_ride_length.csv")
```

```{r}
write.csv(all_trips, file = "all_trips.csv")
```

#EXPLORING MORE WITH DIVVY
# Define the month names
```{r}
month_names <- c("January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December")
```
# Count the number of rides by rider type and month
```{r}
ride_counts <- all_trips_v2 %>%
  count(member_casual, month)

```
# Find the busiest month year-round
```{r}
busiest_month <- all_trips_v2 %>%
  group_by(month, year) %>%
  summarise(total_rides = n()) %>%
  arrange(desc(total_rides)) %>%
  slice(1)

```
# Plot the number of rides by rider type for all months
```{r}
plot_data <- all_trips_v2 %>%
  group_by(month, member_casual) %>%
  summarise(total_rides = n())

```
# Calculate percentage of total rides for each rider type
```{r}
plot_data <- plot_data %>%
  group_by(month) %>%
  mutate(percentage = total_rides / sum(total_rides) * 100)

ggplot(data = plot_data, aes(x = month, y = total_rides, fill = member_casual)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = total_rides), vjust = -0.3, color = "black", size = 3) +
  labs(x = "Month", y = "Number of Rides", fill = "Member Type") +
  ggtitle("Number of Rides by Rider Type for All Months") +
  scale_x_discrete(labels = month_names) +
  scale_fill_manual(values = c("member" = "steelblue", "casual" = "orange")) +
  theme_minimal()

```
#Based on this information, we can provide the following recommendations:

#Febuary and November have lower ride counts for both casual and member riders compared to the other months. Consider analyzing the reasons behind this trend and implementing targeted marketing or promotions during these months to boost ridership.
#May and June have higher ride counts for casual  riders. Explore the factors contributing to this increase, such as favorable weather conditions or special events, and leverage them in future marketing strategies.
#Evaluate the performance of marketing campaigns or promotions targeting casual riders, as they generally have lower ride counts compared to member riders in most months. Identify opportunities to attract more casual riders and convert them into members.
#Consider conducting surveys or collecting additional data to understand the preferences and behaviors of casual and member riders in order to tailor services and offerings accordingly.



