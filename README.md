# Vanguard Digital Experience Analysis

## Project Overview

This project evaluates Vanguard's digital A/B test to determine whether the redesigned online experience should replace the existing interface.

The analysis examines customer behaviour across the Test and Control groups, focusing on process completion, completion time, navigation behaviour, abandonment, and statistical evidence.

The project uses four raw files representing three logical data sources:

1. Client Profiles
2. Experiment Roster
3. Digital Footprints

The Digital Footprints data is provided in two files and combined during data preparation.

---

## Business Questions

The project aims to answer:

1. Has the redesigned user experience improved customer behaviour?
2. Is the process completed more often in the Test group?
3. Are Test customers taking less time to complete the process?
4. Do Test customers experience fewer navigation problems?
5. Which parts of the process have improved?
6. Which process steps remain potential bottlenecks?
7. Should Vanguard deploy the redesigned interface?

---

## Project Structure

```text
Week_6_Project/
│
data/
    raw/
    cleaned/
        experiment_clients.csv
        web_clean.csv
        client_kpi_analysis.csv
        client_completion_time.csv
│
├── notebooks/
│   ├── 01_data_quality.ipynb
│   ├── 02_customer_profile_group_comparison.ipynb
│   ├── 03_customer_journey_analysis.ipynb
│   ├── 04_ab_test_statistical_analysis.ipynb
│   └── 05_business_recommendations.ipynb
│
├── images/
├── sql/
├── tableau/
├── requirements.txt
└── README.md

Analytical Workflow
Day 1: Data Quality and Preparation
Business Question

Can we trust the available data before analysing the A/B test?

Activities Completed
Inspected the Client Profiles, Experiment Roster, and Digital Footprints datasets.
Checked missing values and data types.
Checked duplicate records.
Validated customer identifiers.
Validated experimental assignments.
Combined the two Digital Footprints datasets.
Converted date_time to datetime format.
Investigated repeated process steps.
Investigated multiple starts and confirmations.
Investigated incomplete and unusually long customer journeys.
Key Findings

The Client Profiles and Experiment Roster contain the same 70,609 unique clients.

Exact duplicate web events were removed, reducing the event dataset from 755,405 to 744,641 records.

Repeated actions occurring at different timestamps were retained because they may represent genuine customer behaviour.

Some visit_id values are associated with multiple clients. Customer journeys therefore require both client_id and visit_id for reliable identification.

Day 1 Conclusion

The data was considered sufficiently reliable for further analysis.

Unusual customer journeys were retained when there was insufficient evidence that they represented data errors. These behaviours may represent genuine retries, abandonment, correction, navigation difficulty, or inactivity.

Day 2: Customer Profile and Group Comparability
Business Question

Who are Vanguard's customers, and are the Test and Control groups sufficiently comparable before evaluating experimental outcomes?

Activities Completed
Restricted A/B comparisons to clients assigned to Test or Control.
Examined customer age.
Compared gender composition.
Analysed client tenure.
Compared number of accounts.
Examined account balance.
Investigated account balance outliers and skewness.
Compared historical telephone engagement.
Compared historical digital engagement.
Used descriptive statistics and visualisations to evaluate baseline comparability.
Conducted appropriate statistical tests.
Evaluated effect sizes alongside statistical significance.
Statistical Results
Variable	Statistical Result	Effect Size	Interpretation
Age	p = 0.0157	Cohen's d = 0.0216	Significant but negligible
Gender	p = 0.3910	Cramér's V = 0.0061	No meaningful difference
Client Tenure	p = 0.0869	r = 0.0076	No meaningful difference
Number of Accounts	p = 0.0302	r = 0.0069	Significant but negligible
Account Balance	p = 0.1486	r = 0.0064	No meaningful difference
Calls	p = 0.0005	r = 0.0153	Significant but negligible
Logons	p = 0.0008	r = 0.0147	Significant but negligible
Day 2 Conclusion

The Control and Test groups were considered sufficiently comparable for subsequent A/B test analysis.

Age, number of accounts, calls, and logons produced statistically significant differences, but their effect sizes were negligible. Gender, tenure, and account balance did not show statistically significant differences.

No substantial baseline imbalance was identified that would make the experimental comparison materially unfair.

Day 3: Customer Journey and KPI Analysis
Business Question

How do customers navigate through the digital process, and does the redesigned Test experience improve customer behaviour?

Activities Completed
Reconstructed customer journeys using client_id and visit_id.
Ordered events chronologically using date_time.
Identified journey starts and confirmations.
Reconstructed chronological funnel progression.
Calculated completion rate.
Calculated stage conversion and drop off.
Calculated total completion time.
Calculated effective progression time.
Investigated completion time distributions and outliers.
Identified backward navigation.
Calculated step back counts and rates.
Examined the severity of step back behaviour.
Calculated abandonment rate.
Compared major KPIs between Test and Control.
Validated KPI populations and missing values.
Investigated unexpected values and outliers.
Prepared the analytical datasets for statistical testing.
KPI Results
KPI	Control	Test
Completion Rate	48.26%	54.07%
Median Completion Time	4.12 minutes	3.33 minutes
Step Back Rate	20.23%	26.80%
Abandonment Rate	51.74%	45.93%
Day 3 Interpretation

The redesigned Test experience shows several positive descriptive outcomes.

Test customers have a higher completion rate, a lower abandonment rate, and a faster median completion time.

However, the Test group also has a substantially higher step back rate. This suggests that although customers are completing the process more successfully and more quickly, the redesigned interface may introduce additional backward navigation or friction.

These differences require formal statistical testing before conclusions about the effectiveness of the redesign are made.

KPI Methodology
Completion Rate

Definition: Percentage of started journeys that reach confirmation.

Population: Started customer journeys.

Journey identifier: Combination of client_id and visit_id.

Decision: A journey is completed when it starts and subsequently reaches confirmation.

Reason: This measures whether customers who enter the process successfully complete it.

Completion Time

Definition: Elapsed time between the valid start and confirmation of a completed journey.

Population: Valid completed journeys.

Decision: Median completion time is emphasized because completion time is right skewed and contains unusually long observations.

Outlier treatment: Outliers are identified using the IQR method but are not automatically removed.

Reason: Long completion times may represent genuine customer difficulty, inactivity, or interrupted sessions.

Effective Time

Definition: Time associated with chronological forward progression through:

start → step_1 → step_2 → step_3 → confirm

Reason: This provides an additional view of progression time while reducing the influence of repeated or nonprogressive activity.

Step Back Rate

Definition: Percentage of customer journeys containing at least one backward navigation event.

A step back occurs when the numerical position of the current process step is lower than the immediately preceding process step within the same customer journey.

Population: Experimental customer journeys.

Reason: Backward navigation may indicate customer hesitation, correction, or interface friction.

Individual step back counts are also retained to measure the severity of repeated backward navigation.

Abandonment Rate

Definition: Percentage of started journeys that do not reach confirmation.

Population: Started customer journeys.

Decision: A journey is classified as abandoned when it has an observed start but does not subsequently reach confirmation.

Important: Under this methodology, completion and abandonment are complementary outcomes.

Therefore:

Completion Rate + Abandonment Rate = 100%

They should not be treated as independent outcomes during statistical testing.

KPI Analytical Populations

Different KPIs use different analytical populations because their eligibility requirements differ.

Analysis	Population
Completion and Abandonment	64,181 started journeys
Completion Time	32,561 valid completed journeys
Step Back Analysis	69,447 experimental journeys

Completion time requires a valid completed journey, while navigation behaviour can also be observed in journeys that do not ultimately complete.

KPI Validation

The KPIs were validated before formal statistical analysis.

Validation included:

Checking analytical population sizes.
Checking missing values.
Checking impossible or negative values.
Examining KPI distributions.
Investigating completion time outliers.
Examining step back frequency and severity.
Confirming completion and abandonment sum to 100%.
Reviewing differences in analytical populations.
Documenting methodological assumptions and limitations.
Completion Time Outliers

Completion time is strongly right skewed.

Using the IQR method:

Q1 = 2.38 minutes

Q3 = 5.97 minutes

IQR = 3.58 minutes

Upper bound = 11.34 minutes

A total of 2,423 completed journeys were identified as outliers, representing approximately 7.44% of completed journeys.

These observations were retained because unusually long journeys may represent genuine customer behaviour.

Step Back Distribution

Most journeys contain no backward navigation.

Control:

79.77% had no step back.

Test:

73.20% had no step back.

More severe step back behaviour was also more common in the Test group.

This supports further investigation of navigation friction during statistical analysis.

Analytical Decisions
Day 1 Decisions
Missing Values

Missing values were investigated rather than automatically removed.

Very small amounts of missing demographic information were not imputed because doing so would introduce unsupported assumptions.

Duplicate Events

Exact duplicate events were removed.

Repeated events occurring at different timestamps were retained because they may represent genuine customer behaviour.

Customer Identification

client_id is used for customer level analysis.

visitor_id represents the digital visitor identity.

Customer journeys are identified using the combination of client_id and visit_id.

Unusual Journeys

Repeated steps, multiple starts, multiple confirmations, backward navigation, incomplete journeys, and unusually long sessions were investigated rather than automatically deleted.

These behaviours may contain meaningful evidence about customer experience.

### Day 3B: Client Level KPI Analysis

The primary A/B test KPIs were reconstructed at the client level because Vanguard assigned experimental variation by `client_id`.

Each randomized client contributes one independent observation to the primary experimental analysis.

The final experimental population contains 50,500 unique clients:

Control: 23,532 clients

Test: 26,968 clients

#### Client Level KPI Results

| KPI | Control | Test |
| --- | ---: | ---: |
| Completion Rate | 65.59% | 69.29% |
| Noncompletion Rate | 34.41% | 30.71% |
| Median Completion Time | 4.52 minutes | 3.95 minutes |
| Step Back Rate | 26.10% | 33.41% |
| Mean Step Back Count | 0.41 | 0.60 |

Completion is defined as reaching `confirm` at least once.

Completion time is calculated only for clients with a valid start followed by confirmation within the same journey. When a client has multiple successful journeys, the first successful completion is retained.

Step Back Rate measures the percentage of randomized clients who experienced at least one backward navigation event.

Journey level metrics from Notebook 03 remain useful as secondary funnel diagnostics, while the primary A/B test uses the client as the independent analytical unit.

The client level KPI datasets were saved for statistical analysis in Day 4.

Day 2 Decisions
Baseline Comparability

Control and Test were compared using demographic, financial, and historical engagement characteristics before analysing experimental outcomes.

Statistical significance was interpreted alongside effect size.

This was necessary because the large sample size can make very small differences statistically significant.

Overall Decision

The groups were considered sufficiently comparable for A/B testing.

No practically meaningful baseline imbalance was identified.

Day 3 Decisions
Journey Level Analysis

Customer journeys are identified using both client_id and visit_id.

This prevents sessions belonging to different customers from being incorrectly combined.

Chronological Ordering

Events are ordered using date_time before funnel progression, completion time, or backward navigation is calculated.

Repeated Events

Repeated process events with different timestamps are retained.

Completion

Completion is measured among started journeys that subsequently reach confirmation.

Completion Time

Completion time is calculated only for valid completed journeys.

Median completion time is emphasized because of right skewness and outliers.

Step Back

Backward navigation is defined objectively from the numerical process sequence.

A journey is considered to contain a step back when at least one backward movement occurs.

Abandonment

Abandonment is measured among started journeys that never reach confirmation.

Completion and abandonment are complementary outcomes.

Different KPI Populations

Each KPI uses the population required by its definition rather than forcing every KPI to use the same sample.

Current Analytical Conclusion

After Days 1 through 3, the data and analytical populations are sufficiently prepared for formal A/B test evaluation.

Day 1 established data quality and customer journey rules.

Day 2 established that Test and Control are sufficiently comparable at baseline.

Day 3 established the primary behavioural KPIs and identified an important tradeoff in the redesigned experience:

Test customers complete the process more often and faster, but they also navigate backward more frequently.

Formal statistical testing is required to determine whether these observed differences are statistically meaningful and practically important.

Day 4: Statistical Analysis
Business Question

Are the observed differences between Test and Control statistically significant and practically meaningful?

Day 4 will evaluate the major experimental KPIs using appropriate hypothesis tests, assumption checks, critical values, effect sizes, and business interpretation.

Day 5: Business Recommendation
Business Question

Should Vanguard deploy the redesigned digital experience?

The final recommendation will combine statistical evidence, KPI performance, customer experience findings, and business relevance.