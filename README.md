# Electric-Vehicle-Population-Analysis-R-tidyverse-
This project explores relationships in the Washington State Electric Vehicle Population dataset. Using tidyverse, we analyze how electric vehicle range, type, and eligibility for clean fuel programs affect vehicle pricing and classification.

Dataset

The dataset used is:

```
MA337/Electric_Vehicle_Population_Data.csv
```
Preprocessing:
```
library(tidyverse)

df0 = read.csv("MA337/Electric_Vehicle_Population_Data.csv")
df0 = remove_missing(df0)
attach(df0)

# Filter out invalid MSRP values
df0 = df0 %>%
  filter(Base.MSRP > 0)
```
Research Questions

1. Does a vehicle's electric range affect its base MSRP?

  Investigated using multiple regression with interaction terms.

  H0: No interaction between range and vehicle type

  H1: There is an interaction

2. Does the vehicle type affect the electric range of a vehicle?

3. Does vehicle type affect CAFV (Clean Alternative Fuel Vehicle) eligibility?

Visualizations & Analysis
1. Electric Range vs Base MSRP
```
ggplot(data= df0, aes(x= Electric.Range, y=Base.MSRP, color= Electric.Vehicle.Type))+
  geom_point()+
  geom_smooth(method = "lm", se= FALSE)+
  scale_x_continuous(breaks = seq(0, 300, 50))+
  ylim(0, 900000)+
  xlab("Electric Range in Miles")+
  ylab("Base MSRP in USD")+
  ggtitle("Brand and Electric Range on MSRP Price")

fit = lm(Base.MSRP ~ Electric.Range * Electric.Vehicle.Type)
summary(fit)
```
Findings:

Electric range and vehicle type show a statistically significant interaction.

Other predictors were also significant.

However, R² ≈ 0.02 → only 2% of MSRP variance is explained. Many other factors likely influence MSRP.

2. Vehicle Type Effect on Range
```
ggplot(data= df0, aes(x= Electric.Vehicle.Type, y= Electric.Range))+
  geom_boxplot()+
  xlab("Electric Vehicle Type")+
  ylab("Electric Range in Miles")+
  ggtitle("Vehicle Type Effect on Electric Vehicle Range")

# Mean range by type
df1 = df0 %>%
  group_by(Electric.Vehicle.Type) %>%
  summarise(mean(Electric.Range))

df1

# Count by type
df0 %>%
  group_by(Electric.Vehicle.Type) %>%
  summarise(n())
```
Findings:

BEVs: Mean range ≈ 181 miles

PHEVs: Mean range ≈ 17 miles

Despite fewer PHEVs (n=1187 vs n=2054 BEVs), the difference is statistically meaningful.

PHEVs cluster tightly around their mean, while BEVs show more variability.

3. Vehicle Type vs CAFV Eligibility
```
ggplot(data= df0, aes(x= Electric.Vehicle.Type, fill = Clean.Alternative.Fuel.Vehicle..CAFV..Eligibility))+
  geom_bar()+
  labs(fill= "CAFV Eligibility")+
  xlab("Electric Vehicle Type")+
  ylab("Number of Vehicles")+
  ggtitle("Vehicle Type Effect on CAFV Eligibility")

# Create binary column
df0 = df0 %>%
  mutate(CAFV_Eligibility = ifelse(
    Clean.Alternative.Fuel.Vehicle..CAFV..Eligibility == "Clean Alternative Fuel Vehicle Eligible",
    1, 0
  ))

CAFV_Eligibility = as.factor(CAFV_Eligibility)
```
Findings:

CAFV eligibility is strongly associated with vehicle type.

Tools & Libraries

R

tidyverse (dplyr, ggplot2)

Conclusions

Range ↔ MSRP: Significant relationship, but weak explanatory power.

Vehicle Type ↔ Range: Strong and meaningful difference between BEV and PHEV.

Vehicle Type ↔ CAFV: Clear association between vehicle type and CAFV eligibility.
