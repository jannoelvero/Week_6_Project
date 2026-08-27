Vanguard Digital Experience Analysis
Project Overview
This project evaluates Vanguard's digital A/B test to determine whether the redesigned online experience should replace the existing interface.

The analysis focuses on customer experience, process completion, navigation behaviour, completion time, and statistical evidence from the Test and Control groups.

The project uses four raw files representing three logical datasets:

Client Profiles

Experiment Roster

Digital Footprints Part 1

Digital Footprints Part 2

The Digital Footprints files are combined before customer journey analysis.

Business Questions
The project aims to answer:

Has the user experience improved?

Is the process completed more often?

Are clients taking less time to complete the process?

Do clients experience fewer navigation problems?

Which parts of the process have improved?

Which steps remain bottlenecks?

Should Vanguard deploy the redesigned interface?

Project Structure
Week_6_Project/

data/
    raw/

notebooks/
    01_data_quality.ipynb
    02_customer_profile_group_comparison.ipynb
    03_customer_journey_analysis.ipynb
    04_ab_test_statistical_analysis.ipynb
    05_business_recommendations.ipynb


tableau/

images/

README.md

Analytical Workflow
Day 1: Data Quality

Business question:

Can we trust the data?

The four source files were inspected for structure, missing values, duplicates, identifier consistency, unusual values, outliers, experimental coverage, and customer journey data quality.

Day 2: Customer Profile and Group Comparability

Business question:

Who are Vanguard's customers, and are the Test and Control groups comparable?

Client demographics, account characteristics, and historical engagement were compared between Test and Control before analyzing experimental outcomes.

Day 3: Customer Journey Analysis

Business question:

How do customers navigate through the process?

Customer sessions and journey events will be reconstructed to evaluate funnel progression, completion, abandonment, repeated actions, backward navigation, and process bottlenecks.

Day 4: Statistical Analysis

Business question:

Are the observed differences statistically meaningful?

Appropriate statistical tests will be used to evaluate Test and Control differences.

Day 5: Business Recommendation

Business question:

What should Vanguard do?

Statistical evidence, customer experience findings, and business relevance will be combined into the final deployment recommendation.

Data Quality Decisions
Client Identifier

Finding:

The Client Profiles and Experiment Roster each contain 70,609 unique clients. No duplicate client_id values were found, and both datasets contain exactly the same client population.

Decision:

client_id is retained as the experimental unit and primary client level identifier.

Reason:

The project documentation defines client_id as the experimental unit, and the data quality checks support its uniqueness.

Missing Client Profile Data

Finding:

Only 15 clients contain missing demographic information.

Fourteen clients have all profile variables missing except client_id. One additional client is missing only clnt_age.

Among experimental clients, 7 affected clients belong to Test and 6 belong to Control.

Decision:

Missing demographic values will not be imputed.

Reason:

The proportion of missing data is extremely small. Creating replacement demographic or financial values would introduce unsupported assumptions.

Impact:

Analyses requiring a missing variable will use available observations only.

Gender Categories

Finding:

The gendr field contains:

F

M

U

X

and missing values.

The supplied metadata does not define the meaning of U or X.

Decision:

The original gender categories will be preserved.

U will not be changed to Gender Neutral.

Reason:

There is no evidence in the source documentation that U represents Gender Neutral. Recoding it would introduce an unsupported interpretation.

Impact:

If clearer presentation labels are required later, a separate derived field may be created while preserving the original source column.

Client Age

Finding:

The experimental population ranges from 17 to 96 years old.

No IQR age outliers were identified.

The full Client Profiles dataset contains 374 clients below age 18.

Decision:

Age observations will be retained.

Reason:

The IQR analysis did not identify age outliers in the experimental population, and age alone does not provide sufficient evidence that a record is invalid.

Age and Tenure Consistency

Finding:

574 clients have recorded tenure in years greater than their recorded age.

This represents approximately 0.81% of the Client Profiles dataset.

Decision:

These records will be retained but documented as a data quality limitation.

Reason:

The available data does not establish whether age or tenure is incorrect.

Impact:

These observations should not be automatically corrected because doing so would require an unsupported assumption.

Tenure Year and Month Consistency

Finding:

clnt_tenure_yr and clnt_tenure_mnth were compared.

The average absolute difference between tenure in years and tenure calculated from months was approximately 0.50 years.

The maximum difference was 1 year.

Decision:

The tenure variables are considered internally consistent.

Reason:

The observed differences are small and consistent with one field providing a less precise representation of tenure.

Impact:

Where more precise tenure is required, clnt_tenure_mnth will be preferred.

Tenure Outliers

Finding:

The IQR method identified 599 tenure outliers above 31 years among experimental clients.

This represents approximately 1.19% of clients with available tenure information.

Outlier rates were:

Test: approximately 1.21%

Control: approximately 1.16%

Decision:

Tenure outliers will be retained.

Reason:

Long term Vanguard client relationships are plausible, and the outliers are similarly distributed between Test and Control.

Account Balance Outliers

Finding:

Account balance is strongly right skewed.

The experimental population contains 5,728 IQR balance outliers, representing approximately 11.35% of clients with available balance information.

Outlier rates were:

Test: approximately 11.15%

Control: approximately 11.57%

Decision:

Balance outliers will be retained.

Reason:

High account balances are plausible in an investment management context and may represent commercially important customers.

Removing them would exclude a substantial client segment.

Impact:

Both mean and median balance will be reported because the mean is strongly influenced by high value accounts.

Experiment Assignment

Finding:

The Experiment Roster contains:

Test: 26,968 clients

Control: 23,532 clients

No recorded Variation: 20,109 clients

Decision:

Direct A/B comparisons will include only clients assigned to Test or Control.

Reason:

Clients without a known experimental assignment cannot be validly classified into either experiment group.

Experimental Coverage

Finding:

All 50,500 Test and Control clients appear in the Digital Footprints dataset.

No experimental participant is completely missing from the web interaction data.

Decision:

The 50,500 assigned clients form the primary experimental population.

Additional Web Clients

Finding:

The Digital Footprints dataset contains 120,157 unique clients.

A total of 49,548 web clients do not appear in the Experiment Roster.

Decision:

These clients will be excluded from direct Test versus Control comparisons.

Reason:

Their experimental assignment is unknown.

Their raw interactions remain preserved in the original data.

Digital Footprints Combination

Finding:

Digital Footprints Part 1 and Part 2 contain identical columns and compatible data types.

Decision:

The two web files are combined vertically before journey analysis.

Reason:

They represent two parts of the same event dataset.

Date and Time

Finding:

date_time was initially stored as text.

The recorded web activity ranges from March 15, 2017 to June 20, 2017, matching the documented experiment period.

Decision:

date_time is converted to a pandas datetime data type.

Reason:

Chronological ordering is required for customer journey reconstruction and completion time analysis.

Exact Duplicate Web Events

Finding:

The combined Digital Footprints dataset initially contains 755,405 events.

A total of 10,764 additional exact duplicate rows were identified.

The duplicates contain identical:

client_id

visitor_id

visit_id

process_step

date_time

Decision:

Exact duplicate copies are removed.

The cleaned Digital Footprints dataset contains 744,641 events.

Reason:

When every recorded event attribute is identical, the additional copy does not provide evidence of a separate customer interaction.

Important:

Repeated process steps with different timestamps are retained because they may represent genuine customer behaviour.

Visitor Identifier Conflicts

Finding:

1,645 visitor_id values are associated with more than one client_id.

Decision:

The issue is documented rather than corrected.

Reason:

The source data does not establish whether this represents shared devices, identifier mapping behaviour, or another cause.

Visit Identifier Conflicts

Finding:

1,012 visit_id values are associated with more than one client_id.

Decision:

Customer session analysis will use the combination of:

client_id

and

visit_id

Reason:

Using visit_id alone could combine interactions belonging to different clients.

Multiple Start Events

Finding:

49,873 client sessions contain more than one start event.

This represents approximately 31.34% of client sessions.

Decision:

Multiple start events are retained.

Reason:

Exact duplicate events have already been removed. Remaining repeated starts occur at different timestamps and may represent genuine customer restarts or repeated attempts.

Multiple Confirm Events

Finding:

8,248 client sessions contain multiple confirm events.

This represents approximately 5.18% of sessions.

Decision:

Multiple confirm events are retained during initial journey reconstruction.

Reason:

They may represent genuine customer actions and require journey level interpretation rather than automatic deletion.

Sessions Without Start

Finding:

13,277 sessions have no recorded start.

This represents approximately 8.34% of sessions.

Decision:

These sessions are retained for navigation analysis.

However, they will not be used straightforwardly for full start to confirm completion time calculations.

Reason:

A full journey duration cannot be calculated reliably when the starting event is not observed.

Sessions Without Confirm

Finding:

68,474 sessions contain no recorded confirm.

This represents approximately 43.04% of sessions.

Decision:

These sessions are retained.

Reason:

They may represent incomplete or abandoned customer journeys.

Removing them would create survivorship bias and would undermine the analysis of process completion.

Backward Navigation

Finding:

62,880 backward navigation events were identified.

39,949 sessions contain at least one backward transition.

This represents approximately 25.11% of sessions.

The most frequent backward transition is:

step_1 to start

Decision:

Backward navigation events are retained and analyzed as potential indicators of customer friction.

Reason:

Backward movement may represent correction, reconsideration, restart behaviour, or navigation difficulty.

It should not automatically be classified as an error.

Day 2 Baseline Comparability Decisions
Age

Finding:

Test and Control show very similar age distributions.

Control mean age: approximately 47.50 years

Test mean age: approximately 47.16 years

Control median: 48.5 years

Test median: 47.5 years

Decision:

No descriptive age imbalance is identified.

Age is approximately symmetric but does not closely follow a normal distribution.

Formal statistical testing is reserved for the statistical analysis stage.

Gender

Finding:

The proportions of F, M, and U are very similar between Test and Control.

Decision:

Gender composition is considered descriptively balanced.

Original category labels are preserved.

Tenure

Finding:

Control mean tenure: approximately 12.09 years

Test mean tenure: approximately 11.98 years

Both groups have a median tenure of 11 years.

Decision:

No meaningful descriptive tenure imbalance is identified.

Number of Accounts

Finding:

Control mean: approximately 2.26 accounts

Test mean: approximately 2.25 accounts

Both groups have a median of 2 accounts.

Approximately 78.25% of Control clients and 79.04% of Test clients have 2 accounts.

Decision:

Number of accounts is considered descriptively balanced.

Account Balance

Finding:

Control mean balance: approximately $150,147

Test mean balance: approximately $148,963

Control median: approximately $66,024

Test median: approximately $65,468

Decision:

Balance is considered descriptively similar between the groups.

Because the distribution is strongly right skewed, median values and outlier rates are considered alongside the mean.

Calls

Finding:

Control mean calls: approximately 3.13

Test mean calls: approximately 3.06

Both groups have a median of 3 calls.

Decision:

Historical telephone engagement is considered descriptively similar.

Logons

Finding:

Control mean logons: approximately 6.17

Test mean logons: approximately 6.10

Both groups have a median of 6 logons.

Decision:

Historical digital engagement is considered descriptively similar.

Current Analytical Conclusion

After completing Days 1 and 2, the data is considered suitable for continued A/B analysis with documented limitations.

The Test and Control groups appear descriptively comparable across the observed baseline client characteristics.

This does not yet establish statistical equivalence.

Formal statistical testing will be performed separately after customer journeys and business KPIs have been constructed.
## Day 4 Statistical Analysis and Research Question Decisions

Formal statistical testing was used to determine whether the observed differences between the Control and Test groups were statistically supported. An alpha level of 0.05 was used throughout the analysis.

### RQ1 Overall Completion Rate

The Control group had a completion rate of **65.59%**, while the Test group had a completion rate of **69.29%**. The Test group improved by **3.71 percentage points**, equivalent to a **5.65% relative improvement**.

A one tailed two proportion Z test produced **Z = 8.8745** with **p < 0.05**. The 95% confidence interval for the difference ranged from **2.89 to 4.53 percentage points**. Cohen's h was **0.0791**, indicating a very small effect size.

**Decision:** Reject H0.

There is statistically significant evidence that the redesigned Test process increased the client completion rate. The redesign is beneficial for completion performance, although other usability measures must be considered before deployment.

### RQ2 Step Back Rate

The Control group had a Step Back rate of **26.10%**, compared with **33.41%** for Test. This represents an increase of **7.31 percentage points** or **28.02% relative increase**.

A one tailed two proportion Z test produced **Z = 17.8884** with **p < 0.05**. The 95% confidence interval for the difference ranged from **6.52 to 8.11 percentage points**. Cohen's h was **0.1602**, below the conventional small effect threshold.

**Decision:** Reject H0.

There is statistically significant evidence that the redesigned process increased Step Back behavior. This is treated as a potential usability concern rather than automatically classified as an error because backward navigation may represent review, correction, uncertainty, or friction.

### RQ3 Completion Time

The median completion time decreased from **4.52 minutes** for Control to **3.95 minutes** for Test. This represents a reduction of approximately **0.57 minutes**, equivalent to about **34 seconds** or **12.55%**.

Completion times were strongly positively skewed, and 2,705 observations were identified as IQR outliers. These observations were retained because they may represent genuine client behavior. A one tailed Mann Whitney U test produced **U = 120,771,157.50**, **Z = negative 14.6719**, and **p < 0.05**. The rank biserial correlation was **0.0939**, indicating a very small effect size.

**Decision:** Reject H0.

There is statistically significant evidence that clients using the redesigned Test process complete the process faster. The time saving is operationally relevant even though the statistical effect size is very small.

### RQ4 Step Back Frequency

The Control group recorded an average of **0.407 Step Back events per client**, compared with **0.602** for Test. Both groups had a median of zero Step Back events.

Because the distributions were strongly positively skewed and contained many zero values, a one tailed Mann Whitney U test was used. The test produced **U = 342,403,506**, **Z = 15.3577**, and a one sided **p = 1.000** for the hypothesis that Test would have fewer Step Back events. The rank biserial correlation was **0.0791**, indicating a very small effect size.

**Decision:** Fail to reject H0.

There is insufficient evidence that the redesigned Test process reduced Step Back frequency. The observed difference was in the opposite direction, with Test showing a higher average Step Back count. This reinforces the navigation concern identified in RQ2.

### RQ5 Stage Level Funnel Performance

The effect of the redesigned experience differed across the customer journey.

| Funnel Transition | Control | Test | Difference | Statistical Decision | Interpretation |
| --- | ---: | ---: | ---: | --- | --- |
| Start to Step 1 | 85.83% | 90.71% | +4.88 pp | Reject H0 | Strong improvement |
| Step 1 to Step 2 | 92.46% | 91.66% | negative 0.80 pp | Reject H0 | Significant deterioration |
| Step 2 to Step 3 | 93.06% | 93.60% | +0.54 pp | Reject H0 | Significant but small improvement |
| Step 3 to Confirm | 87.99% | 88.33% | +0.35 pp | Fail to reject H0 | No statistically supported improvement |

**Decision:** The redesign did not improve every stage equally.

The strongest improvement occurred at **Start to Step 1**, while **Step 1 to Step 2** emerged as the clearest area requiring investigation. The final confirmation stage should not be claimed as an improvement because the difference was not statistically significant.

## RQ6 Deployment Decision

### Decision Framework

The deployment decision combines evidence from the previous research questions across five key areas.

| Decision Area | Evidence | Assessment |
| --- | --- | --- |
| User Experience | Efficiency improved, but navigation behavior shows signs of additional friction | Mixed |
| Overall Completion Rate | Completion increased from **65.59% to 69.29%** | Positive |
| Process Completion Time | Median completion time decreased from **4.52 to 3.95 minutes** | Positive |
| Navigation Behavior | Step Back rate increased from **26.10% to 33.41%**, and Step Back frequency was higher in Test | Concern |
| Stage Level Funnel Performance | Strong improvement at Start to Step 1, but significant decline at Step 1 to Step 2 | Mixed |

The new design should be recommended only if the overall benefits are meaningful and the identified weaknesses do not outweigh those improvements.

### Final Decision

**Recommend optimization and retesting before full deployment.**

The redesigned experience improved two important business outcomes. More clients completed the process, and completed clients finished faster. The redesign also substantially improved initial progression from Start to Step 1.

However, these benefits were accompanied by increased Step Back behavior and a statistically significant deterioration at the Step 1 to Step 2 transition. The evidence therefore supports the potential of the redesigned experience, but not an unconditional full deployment in its current form.

### Business Recommendation

Retain the successful elements of the Test design that contributed to higher completion, faster completion, and stronger initial progression. Investigate the causes of increased Step Back behavior and the deterioration between Step 1 and Step 2. After these areas are optimized, conduct another controlled A/B test to determine whether the efficiency gains can be maintained while reducing navigation friction.

**Final recommendation: Optimize and retest before full deployment.**
