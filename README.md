# Energy Tariff Optimisation Analysis


## Are UK Customers on the Right Tariff for Their Consumption Level, and How Much Could They Save by Switching?


**Tool:** Python (Pandas, NumPy, Matplotlib, Seaborn)

-----


## Project Overview

NovaPower UK is a mid-sized energy management and analytics company operating across **12 UK regions** from London and the South East to Scotland, Wales, and Northern Ireland. The company partners with **1,010 residential customers**, collecting and analysing their energy consumption data to ensure they are on the most cost-effective tariff available for their household profile.

NovaPower does not generate or supply electricity directly. Instead, it sits between the customer and the major UK energy suppliers British Gas, EDF Energy, E.ON, Octopus Energy, OVO Energy, and Scottish Power using data analytics to monitor customer consumption, flag tariff misalignment, and recommend switches that reduce household energy bills.

The customers in NovaPower’s portfolio are currently spread across five Ofgem-regulated tariff types Standard Variable Tariff, Fixed Rate Tariff, Economy 7, Green / Renewable Tariff, and Prepayment Meter Tariff with annual household electricity consumption ranging from under 2,000 kWh for small flats to over 5,000 kWh for large detached properties. Unit rates across these tariffs vary from **19.5p/kWh on Economy 7** to **30.5p/kWh on the Prepayment Meter Tariff, a gap** of over 11 pence per unit that can translate to hundreds of pounds difference in annual bills for the exact same volume of energy consumed.

Despite having access to detailed consumption data across its entire customer base, an internal review revealed that a large proportion of NovaPower’s customers had never been assessed against a cheaper tariff alternative. Many remained on the Standard Variable or Prepayment Meter Tariff consistently the most expensive options per unit simply because no structured review had ever been conducted. Leadership identified this as both a customer welfare concern and a significant missed opportunity, particularly against the backdrop of the UK cost-of-living crisis where energy bills represent one of the largest household expenses.

This analysis was commissioned to deliver a data-driven answer to the core question: **are NovaPower’s customers on the right tariff for their consumption level, and how much could they collectively save by switching to a better-suited deal?**

-----


## Business Problem

The leadership team identified five core questions to guide this analysis:

- Are customers currently matched to the most cost-effective tariff for their annual consumption level?
- Which tariff types are most associated with customer overpayment, and what is the average excess cost per customer?
- How does energy consumption vary across property types, regions, and heating systems and what does this mean for tariff suitability?
- What is the total potential saving available across the customer base, and which customer segments stand to benefit most from switching?
- Is the evidence strong enough to justify a structured national tariff switching and customer outreach programme in 2024?

-----

## About the Data

The dataset contains **1,010 residential customer records** generated to reflect realistic UK energy market conditions, referencing Ofgem tariff structures and typical household consumption benchmarks. It covers a single cross-sectional snapshot of NovaPower’s customer base with the following columns:

|**Column**                 |**Description**                                                |
|---------------------------|---------------------------------------------------------------|
|Customer_ID                |Unique Customer reference code                                 |
|Region                     |Uk Region Where the customer lives                             |
|Property_Type              |Type of home; Flat, Terraced, Semi-Detached, Detached, Bungalow|
|Num_Bedrooms               |Number of bedrooms (1–5)                                       |
|EPC_Rating                 |Energy Performance Certificate rating (A-G)                    |
|Heating_Type               |Type of heating system installed                               |
|Current_Supplier           |Energy supplier serving the customer                           |
|Current_Tariff             |The tariff the customer is currently on                        |
|Annual_Consumption_kWh     |Total electricity used in one year                             |
|Current_Annual_Cost_GBP    |Total annual energy bill in pounds                             |
|Contract_Start_Date        |Date the current contract began                                |
|Contract_Length_Months     |Contract duration in months                                    |
|Payment_Method             |How the customer pays their bill                               |
|Has_Smart_Meter            |Whether the customer has a smart meter                         |
|Num_Occupants              |Number of people in the household                              |
|Customer_Satisfaction_Score|Customer satisfaction rating (1–5)                             |

-----



## Methodology

The analysis was conducted in a single phase using Python inside a Jupyter Notebook, covering data loading, cleaning, feature engineering, exploratory data analysis, and visualisation.



**Tools Used:** Python · Pandas · NumPy · Matplotlib · Seaborn



**Phase 1: Data Inspection**

The raw dataset was loaded using pd.read_csv() and inspected using .shape, .dtypes, .head(), and .isnull().sum().

Pre-cleaning visualisations, histograms, box plots, and bar charts were produced to confirm quality issues before any cleaning was performed.



**Phase 2: Data Cleaning**

Eight quality issues were identified and resolved systematically using Pandas:

|**Issue**                                                         |**Action Taken**                                                            |
|------------------------------------------------------------------|----------------------------------------------------------------------------|
|10 duplicate rows                                                 |Removed with drop_duplicates()                                              |
|String errors in Annual_Consumption_kWh (N/A, unknown, error)     |Converted using pd.to_numeric(errors=‘coerce’)                              |
|Outlier consumption values (e.g. 42,000 kWh)                      |Filtered to Ofgem realistic range: 1,000-15,000 kWh                         |
|Missing values in 3 columns                                       |Filled with column median chosen over mean due to right-skewed distributions|
|Negative cost values                                              |Removed energy cost cannot be negative and true values could not be verified|
|Inconsistent casing in Has_Smart_Meter                            |Standardised with .str.strip().str.title()                                  |
|Whitespace and casing issues in Region                            |Standardised with .str.strip().str.title()                                  |
|Wrong data types in Annual_Consumption_kWh and Contract_Start_Date|Corrected using pd.to_numeric() and pd.to_datetime()                        |



**Phase 3: Feature Engineering**

Seven new columns were calculated entirely within the notebook.

|**New Column**             |**How it was Calculated**                                                   |
|---------------------------|----------------------------------------------------------------------------|
|Cost_Per_kWh_Pence         |(Annual Cost ÷ Consumption) × 100                                           |
|Recommended_Tariff         |Derived using consumption thresholds, heating type, and smart meter status  |
|On_Right_Tariff            |Boolean: Current_Tariff == Recommended_Tariff                               |
|Potential_Annual_Saving_GBP|(Current rate − Recommended rate) ÷ 100 × Consumption using Ofgem unit rates|
|Contract_End_Date          |Contract_Start_Date + Contract_Length_Months                                |
|Consumption_Band           |Binned into Low / Medium / High / Very High                                 |
|Saving_Category            |Binned into No Saving / Low / Medium / High                                 |



**Phase 4: Exploratory Data Analysis and Visualisation**

Nine EDA outputs were produced covering tariff distribution, consumption patterns, saving potential, regional breakdowns, smart meter analysis, correlation analysis, and customer segmentation.

Four key insight charts were produced to directly answer the business questions.

-----



## Analysis Results and Key Insights



### Business Question 1: Are customers on the right tariff?


![Q1](Q1.Q1.JPG)

The On_Right_Tariff flag and Chart 3 directly answer this question.

Approximately **80.6% of NovaPower’s customers are not on their recommended tariff.**

Tariff misalignment is not an edge case, it is the norm across every one of the 12 UK regions in the dataset.

The ratio of misaligned to correctly-placed customers is broadly consistent across all regions, confirming this is a nationwide structural problem rather than a localised one.

### Business Question 2: Which tariffs are associated with overpayment?

The Cost_Per_kWh_Pence column and Chart 1 answer this question.

The **Prepayment Meter Tariff** (31.47p/kWh) and **Standard Variable Tariff** (29.72p/kWh) are the most expensive tariffs per unit of energy consumed.

Critically, Prepayment Meter customers do not consume significantly more energy than customers on other tariffs; their higher bills are driven entirely by the tariff structure, not by usage behaviour.

They pay over 11 pence more per kWh than Economy 7 customers for the same volume of energy, making them the most overcharged group in the entire portfolio.

### Business Question 3: How does consumption vary across property types, regions, and heating systems?

Chart 2 and the regional saving analysis answer this question.

Consumption rises clearly with property size. **Detached Houses and Bungalows** record the highest median consumption while **Flats** record the lowest.

**Detached Houses** also show the widest interquartile range, meaning consumption varies significantly even within the same property type driven by household size, heating system, and occupancy levels.

Region has very little impact on savings potential, with the spread across all 12 UK regions remaining broadly even.

**Heating type is a stronger predictor than region** customers with Heat Pumps or Electric Heating consume significantly more and are best suited to Economy 7, which offers the cheapest overnight rate.

### Business Question 4: What is the total saving potential, and who benefits most?

The Potential_Annual_Saving_GBP column, Saving Category chart, and Top 10 chart answer this question.

**378 customers, the** largest single group, fall into the **No Saving / Costs More category** and would gain nothing from switching.

However **355 customers could save £300+ per year** and a further **224 could save between £100 and £299**, giving NovaPower a combined priority pool of **579 customers** where targeted outreach would deliver genuine, measurable financial benefit.

The **highest-saving customers are predominantly on the Prepayment Meter Tariff** with high annual consumption; the combination of the most expensive unit rate and the highest usage produces the largest saving opportunity.

### Business Question 5: Is the evidence strong enough to justify a national switching programme?

Yes, the evidence strongly supports a national switching programme, with conditions.

The data confirms that tariff misalignment affects the majority of customers across all 12 UK regions, that the financial impact is significant particularly for Prepayment and Standard Variable customers and that a clear, actionable priority list can be derived directly from the analysis.

However the campaign must not be blanket. The 378 No Saving customers must be excluded entirely, and the remaining 579 should be approached in tiers based on saving size.

-----

## Key Insights

**1. Most NovaPower customers are on the wrong tariff** Approximately **80.6%** of customers are not on the tariff recommended for their consumption profile. This is the single most important finding of the entire analysis: tariff misalignment is the norm, not the exception.

**2. Prepayment customers are the most overcharged group** At 31.47p/kWh, Prepayment Meter customers pay the highest unit rate in the portfolio. Since their consumption levels are not significantly different from customers on cheaper tariffs, the extra cost is entirely structural; they are paying a premium simply because of the tariff type, not because they use more energy.

**3. Over a third of customers need no intervention but 579 represent a real switching opportunity** The Saving Category breakdown reveals that 378 customers are already on their recommended tariff or would not benefit from switching. However 355 customers stand to save £300+ per year and a further 224 could save £100 - £299 giving NovaPower a combined priority pool of 579 customers where outreach would deliver genuine financial benefit. The 32 Low savers (£1 - £99) represent marginal gains and are a lower priority.

**4. Property type and heating system drive consumption not region** Regional analysis shows savings potential is broadly uniform across all 12 UK regions. Consumption varies strongly by property type and heating system however. Detached houses with electric heating or heat pumps are consistently the highest-consuming and therefore highest-saving customer segment.

**5. Economy 7 customers are well-placed and need no intervention.** Customers already on Economy 7 show the lowest average potential saving. This confirms the tariff is working as intended, attracting and retaining the right customer profile and delivering genuine value. These customers should be excluded from any switching campaign to avoid unnecessary disruption.

**6. Smart meter ownership creates an immediate switching opportunity** Prepayment customers who already have a smart meter can move to a Smart Pay As You Go arrangement (23.0p/kWh) without any hardware change, an immediate saving of 7.5p/kWh. This group represents the quickest win for NovaPower, requiring minimal operational effort.

-----

## Recommendations

**1. Launch an immediate switching campaign targeting Prepayment and Standard Variable customers** These two groups represent the highest average savings and the largest share of misaligned customers. A direct outreach campaign via email, SMS, or in-app notification with a personalized saving estimate for each customer would be the most impactful first step.

**2. Prioritise smart meter prepayment customers first** This subgroup can move to a Smart Pay As You Go arrangement with zero hardware change and immediate savings. They are the easiest and cheapest group to convert and should be the first cohort in any outreach programme.

**3. Use Saving Category to tier the outreach and exclude the No Saving group** The data shows 378 customers would gain nothing or would pay more from switching. These customers must be explicitly excluded from any outreach campaign. The remaining 579 customers should be split into two tiers:

- **Tier 1: High savers (355 customers, £300+ potential saving):** direct personal contact with their exact saving figure. This group justifies the highest cost-per-contact spend.
- **Tier 2: Medium savers (224 customers, £100-£299 potential saving):** automated digital outreach via email or in-app notification with a tailored saving estimate.
- **Deprioritise: Low savers (32 customers, £1-£99):** include only in low-cost bulk communications if budget allows.

**4. Do not contact Economy 7 customers** The data confirms Economy 7 customers are already well-matched to their tariff. Contacting them with switching suggestions risks unnecessary churn and undermines customer trust. This group should be flagged as excluded in the system.

**5. Build property type and heating system into the recommendation engine** These two factors are stronger predictors of consumption and therefore tariff suitability than region alone. Any future recommendation engine should use them as primary inputs.

**6. Set a 2024 review checkpoint using Contract End Dates** Contract end dates have now been calculated for all 1,010 customers. NovaPower should trigger automatic switching reviews at the point each customer’s contract expires when exit fees no longer apply. This turns the analysis from a one-off exercise into an ongoing customer lifecycle management process.

-----

## Limitations

- The dataset is synthetic. While built to reflect realistic UK energy market patterns and Ofgem pricing structures, it was generated programmatically and may not capture all real-world nuances such as postcode-level standing charge variation, time-of-use pricing, or seasonal consumption shifts.
- The Recommended_Tariff logic uses simplified consumption thresholds. A production-grade recommendation engine would incorporate time-of-day usage patterns, EV charging schedules, solar panel generation, and behavioural data unavailable in this dataset.
- Customer_Satisfaction_Score had 30+ missing values imputed with the median. This may slightly flatten the satisfaction distribution and understate dissatisfaction among certain tariff groups.
- The analysis does not account for switching costs, exit fees, or contract lock-in periods, which are material considerations in real tariff switching decisions and could reduce the effective saving for customers still within their contract term.

-----

## Conclusion

This analysis of NovaPower UK’s residential customer base reveals a widespread and financially significant tariff misalignment problem affecting approximately 80.6% of the portfolio. The most important story in the data is straightforward: the majority of customers are overpaying, the overcharging is concentrated in two specific tariff types, and the solution is clear and actionable.

Prepayment Meter and Standard Variable Tariff customers represent the highest-priority switching candidates, with average potential savings well above £200 per year. A structured national outreach programme targeting the 579 customers in the High and Medium saving bands while explicitly excluding the 378 customers who would not benefit would deliver meaningful household savings and strengthen NovaPower’s position as a trusted energy management partner.

The analysis also confirms that Economy 7 is working exactly as intended for high-consumption households, and that smart meter ownership creates an immediate, low-effort switching opportunity for prepayment customers that requires no hardware change.

With proper execution of the tiered recommendations outlined in this report combined with the integration of contract end dates into the outreach calendar NovaPower UK is well-positioned to deliver measurable customer savings, reduce tariff misalignment across its portfolio, and build the kind of data-driven customer relationship management that distinguishes a market leader from a reactive service provider.
