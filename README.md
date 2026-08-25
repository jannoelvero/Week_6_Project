# Vanguard Digital Experience Analysis

## Project Overview

This project evaluates Vanguard's digital A/B test to determine whether the redesigned online experience should replace the existing interface.

The analysis focuses on customer experience, process completion, navigation behaviour, completion time, and statistical evidence from the Test and Control groups.

The project uses four raw files representing three logical datasets:

1. Client Profiles
2. Experiment Roster
3. Digital Footprints Part 1
4. Digital Footprints Part 2

The Digital Footprints files are combined before customer journey analysis.

## Business Questions

The project aims to answer:

1. Has the user experience improved?
2. Is the process completed more often?
3. Are clients taking less time to complete the process?
4. Do clients experience fewer navigation problems?
5. Which parts of the process have improved?
6. Which steps remain bottlenecks?
7. Should Vanguard deploy the redesigned interface?

## Project Structure

```text
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
Day 1: Data Quality and Preparation

Business Question: Can we trust the available data before analysing the A/B test?

The Client Profiles, Experiment Roster, and Digital Footprints datasets were inspected for missing values, duplicates, data types, identifier consistency, experimental assignment, and customer journey anomalies.

Key findings and decisions:

client_id was retained as the primary client level identifier and experimental unit. The Client Profiles and Experiment Roster contain the same 70,609 unique clients.
Missing demographic values were not imputed because the amount of missing data was very small and replacing them would require unsupported assumptions.
The two Digital Footprints files were combined into one event dataset. date_time was converted to datetime to support chronological journey analysis.
Exact duplicate web events were removed, reducing the event dataset from 755,405 to 744,641 records. Repeated actions occurring at different timestamps were retained because they may represent genuine customer behaviour.
Customer sessions will be identified using client_id together with visit_id because some visit_id values are associated with multiple clients.
Multiple starts, multiple confirms, incomplete journeys, repeated steps, backward navigation, and unusually long sessions were retained rather than automatically removed. These behaviours may represent genuine retries, abandonment, correction, navigation difficulty, or inactivity and will be handled according to the KPI being calculated.

Conclusion: The data is sufficiently reliable to proceed. However, customer journeys require explicit preprocessing rules, and experimental comparisons should use only clients with valid Test or Control assignments.

### Day 2: Customer Profile and Group Comparability

**Business question:**

Who are Vanguard's customers, and are the Test and Control groups comparable before evaluating experimental outcomes?

**Activities completed:**

1. Restricted direct A/B comparison to clients assigned to Test or Control.
2. Examined customer age distributions.
3. Compared gender composition between the experimental groups.
4. Analyzed client tenure.
5. Compared number of accounts.
6. Examined account balance distributions.
7. Investigated account balance outliers and skewness.
8. Compared historical telephone engagement using calls.
9. Compared historical digital engagement using logons.
10. Used descriptive statistics and visualizations to assess baseline comparability.
11. Reviewed mean, median, distribution shape, and outlier behaviour where appropriate.
12. Documented methodological decisions before analyzing experimental outcomes.

**Result:**

The Test and Control groups appear descriptively comparable across the observed baseline customer characteristics. Formal statistical equivalence is not assumed and will be evaluated separately where appropriate.

### Day 3: Customer Journey Analysis

**Business question:**

How do customers navigate through the digital process, and does the redesigned Test experience improve customer behaviour?

**Activities completed:**

1. Reconstructed customer journeys using client_id and visit_id.
2. Ordered digital events chronologically using date_time.
3. Identified journey starts and confirmations.
4. Evaluated chronological funnel progression from start through confirmation.
5. Calculated completion rate by experimental group.
6. Calculated stage level funnel conversion and drop off.
7. Calculated total completion time for valid completed journeys.
8. Calculated effective progression time across chronological funnel stages.
9. Investigated completion time distributions and outliers.
10. Identified backward navigation using the ordered process sequence.
11. Calculated step back counts and step back rates.
12. Examined the distribution and severity of step back behaviour.
13. Calculated abandonment rate among started journeys.
14. Compared all major KPIs between Control and Test.
15. Validated KPI populations, missing values, distributions, unexpected values, and outliers.
16. Documented KPI assumptions and methodological limitations.
17. Prepared the analytical datasets for formal statistical analysis.

**Key descriptive results:**

| KPI | Control | Test |
| --- | ---: | ---: |
| Completion Rate | 48.26% | 54.07% |
| Median Completion Time | 4.12 minutes | 3.33 minutes |
| Step Back Rate | 20.23% | 26.80% |
| Abandonment Rate | 51.74% | 45.93% |

The Test experience shows higher completion, lower abandonment, and faster median completion time. However, Test also shows a higher step back rate, indicating that the redesigned experience may introduce additional backward navigation or customer friction.

These descriptive differences will be formally evaluated during Day 4 statistical analysis.

Day 4: Statistical Analysis

Business question:

Are the observed differences statistically meaningful?

Appropriate statistical tests will be used to evaluate Test and Control differences.

Day 5: Business Recommendation

Business question:

What should Vanguard do?

Statistical evidence, customer experience findings, and business relevance will be combined into the final deployment recommendation.

# Analytical Decision Documentation

This section records the main analytical decisions made during the first two stages of the Vanguard A/B test project. Detailed code, validation and statistical outputs are available in the corresponding notebooks.

## Day 1: Data Quality and Preparation

### Dataset Integration

**Decision:** Combine the demographic data, experiment roster and web event data using the appropriate customer identifiers.

**Reason:** The datasets provide complementary information required for the experiment. Demographic data describes the customers, the experiment roster identifies Control and Test assignment, and the web logs record customer interactions with the digital process.

### Missing Values

**Decision:** Missing values were investigated before analysis rather than automatically removed.

**Reason:** Removing incomplete observations without understanding their source could unnecessarily reduce the sample or introduce bias. Missing information was handled according to its analytical relevance.

### Duplicate Records

**Decision:** Exact duplicate events were treated as data quality issues, while repeated process steps were investigated separately rather than automatically classified as duplicates.

**Reason:** Repeated steps can represent genuine customer behaviour, such as retrying a step or navigating backward, and therefore contain useful information about the customer journey.

### Identifier Selection

**Decision:** Different identifiers are used according to the analytical question.

`client_id` represents the customer and is used for customer level analysis.

`visitor_id` represents the digital visitor identity.

`visit_id` represents an individual web session and is used to reconstruct customer journeys.

**Reason:** The web dataset contains event level observations. Treating every event as an independent customer or completed journey would produce incorrect KPI calculations.

### Experimental Assignment

**Decision:** Experimental assignments were validated before comparing Control and Test.

**Reason:** Each client used in the experiment must have a reliable treatment assignment. Experimental metadata cannot be assumed to be correct without checking completeness, uniqueness and consistency.

### Repeated Process Steps

**Decision:** Repeated `step_1`, `step_2` and `step_3` events were retained.

Repeated `step_1` occurred in approximately 18.37% of sessions, repeated `step_2` in 11.53%, and repeated `step_3` in 7.14%.

**Reason:** These events may represent genuine customer behaviour and can provide evidence of friction, hesitation or navigation difficulties. Removing them would remove useful journey information.

### Multiple Starts and Confirms

**Decision:** Multiple `start` and `confirm` events were investigated rather than automatically deleted.

**Reason:** They may represent repeated attempts, navigation behaviour or tracking characteristics. Their meaning depends on the sequence of events within each visit.

### Backward Navigation

**Decision:** Backward navigation was retained as part of the customer journey.

**Reason:** Moving backward through the process may indicate usability friction and is therefore analytically meaningful when evaluating the digital experience.

### Exceptionally Long Sessions

**Decision:** Long sessions were identified using the IQR method but were not automatically removed.

The upper IQR threshold was approximately 13.83 minutes and approximately 7.89% of sessions exceeded this threshold.

**Reason:** Long sessions may result from genuine behaviour, inactivity, interrupted sessions or tracking characteristics. Duration alone is insufficient evidence that a session is invalid.

### Day 1 Decision

The data was considered sufficiently reliable for subsequent analysis after the identified quality issues and journey complexities were documented.

Unusual customer journeys were generally retained when there was insufficient evidence that they represented data errors. This preserves genuine customer behaviour for later KPI and journey analysis.


## Day 2: Control and Test Group Comparability

### Comparison Strategy

**Decision:** Control and Test groups were compared using demographic, financial and historical engagement characteristics before analysing experimental outcomes.

**Reason:** Differences that existed before exposure to the new digital experience could potentially confound the interpretation of the A/B test.

Relative distributions, descriptive statistics and statistical tests were used because the Control and Test groups have different sample sizes.

### Age

**Decision:** Age was tested using an independent samples t test after evaluating the relevant assumptions.

The test produced `p = 0.0157` and Cohen's `d = 0.0216`.

**Decision:** Reject H0 statistically, but treat the difference as practically negligible.

**Reason:** Although statistically significant, the effect size is extremely small and does not indicate a meaningful age imbalance.

### Gender

**Decision:** Gender was evaluated using a Chi square test after removing the extremely rare `X` category because its expected frequencies violated the assumptions of the test.

The final test produced `p = 0.3910` and Cramér's `V = 0.0061`.

**Decision:** Fail to reject H0.

**Reason:** There is insufficient evidence of a meaningful association between gender distribution and experimental assignment.

### Client Tenure

**Decision:** Client tenure was evaluated using the Mann Whitney U test.

The result produced `p = 0.0869` and effect size `r = 0.0076`.

**Decision:** Fail to reject H0.

**Reason:** The groups showed no statistically significant tenure difference and the effect size was negligible.

### Number of Accounts

**Decision:** Number of accounts was evaluated using the Mann Whitney U test.

The result produced `p = 0.0302` and `r = 0.0069`.

**Decision:** Reject H0 statistically, but treat the difference as practically negligible.

**Reason:** The statistical difference has an extremely small effect size and does not represent a meaningful baseline imbalance.

### Account Balance

**Decision:** Account balance was evaluated using the Mann Whitney U test.

The result produced `p = 0.1486` and `r = 0.0064`.

**Decision:** Fail to reject H0.

**Reason:** There is insufficient statistical evidence of a balance difference, and the measured effect size is negligible.

### Calls in the Previous Six Months

**Decision:** Historical call activity was evaluated using the Mann Whitney U test.

The result produced `p = 0.0005` and `r = 0.0153`.

**Decision:** Reject H0 statistically, but treat the difference as practically negligible.

**Reason:** The large sample makes very small differences detectable statistically. The effect size demonstrates that the magnitude of the difference is negligible.

### Logons in the Previous Six Months

**Decision:** Historical digital engagement was evaluated using the Mann Whitney U test.

The result produced `p = 0.0008` and `r = 0.0147`.

**Decision:** Reject H0 statistically, but treat the difference as practically negligible.

**Reason:** Although statistically significant, the magnitude of the difference is too small to represent a meaningful imbalance between the groups.

### Statistical Versus Practical Significance

**Decision:** Experimental comparability was not determined from p values alone.

**Reason:** The large sample size means very small differences can become statistically significant. Effect sizes and descriptive differences were therefore considered alongside hypothesis test results.

Age, number of accounts, calls and logons were statistically significant, but all showed negligible effect sizes.

Gender, tenure and balance did not show statistically significant differences.

### Day 2 Decision

The Control and Test groups were considered sufficiently comparable for subsequent A/B test analysis.

No substantial demographic, financial or historical engagement imbalance was identified that would make the experimental comparison materially unfair.

Statistically significant baseline differences will still be acknowledged when interpreting later results, but their negligible effect sizes indicate that they are not material threats to the validity of the experiment.


## Overall Decision After Days 1 and 2

The project can proceed to customer journey and KPI analysis.

Day 1 established that the data is sufficiently reliable for analysis while preserving unusual but potentially meaningful customer behaviour.

Day 2 established that the Control and Test groups are sufficiently comparable at baseline.

Together, these decisions provide the analytical foundation for evaluating whether Vanguard's redesigned digital experience changes customer behaviour and performance outcomes.

## Day 3 Customer Journey and KPI Decisions

### Journey Identifier

**Decision:**

Customer journeys are identified using the combination of `client_id` and `visit_id`.

**Reason:**

Some `visit_id` values are associated with multiple clients. Using `visit_id` alone could incorrectly combine interactions belonging to different customers.

### Experimental Population

**Decision:**

Only customers assigned to Test or Control are included in direct experimental comparisons.

**Reason:**

Customers without a recorded experimental assignment cannot be reliably classified into either group.

### Journey Ordering

**Decision:**

Events within each customer journey are ordered chronologically using `date_time`.

**Reason:**

Chronological ordering is necessary to reconstruct funnel progression, identify backward navigation, and calculate valid completion times.

### Repeated Process Events

**Decision:**

Repeated process events with different timestamps are retained.

**Reason:**

They may represent genuine customer behaviour such as restarting, reconsidering, repeating a step, or navigating backward. Only exact duplicate event records were removed during data quality preparation.

### Completion Rate

**Decision:**

Completion is measured at the journey level among journeys with an observed start.

A journey is considered completed when it subsequently reaches confirmation.

**Reason:**

Restricting the denominator to started journeys provides a meaningful measure of whether customers who entered the process successfully completed it.

### Completion Time

**Decision:**

Completion time is measured only for valid completed journeys with observable chronological start and confirmation events.

The primary completion time KPI is elapsed time from start to confirmation.

**Reason:**

Incomplete journeys or journeys without a reliable starting event cannot provide a valid full completion duration.

### Completion Time Outliers

**Decision:**

Exceptionally long completion times are identified using the IQR method but are not automatically removed.

**Reason:**

Long durations may represent genuine interruptions or difficult customer journeys. Removing them without evidence could hide meaningful customer behaviour.

Median completion time is emphasized because the distribution is right skewed.

### Effective Time

**Decision:**

Effective time is calculated from chronological forward progression through start, step_1, step_2, step_3, and confirm.

**Reason:**

This provides an additional measure of progression time while reducing the influence of repeated or nonprogressive activity.

### Step Back Definition

**Decision:**

A step back occurs when the numerical position of the current process step is lower than the immediately preceding process step within the same client and visit journey.

**Reason:**

This provides a reproducible behavioural definition of backward navigation without automatically assuming that every backward movement is an error.

### Step Back Rate

**Decision:**

Step back rate is measured as the percentage of journeys containing at least one backward navigation event.

Individual backward movements are also counted to evaluate the severity of repeated step back behaviour.

**Reason:**

The journey level rate measures how frequently customers encounter backward navigation, while the count provides additional information about repeated friction.

### Abandonment Rate

**Decision:**

Abandonment is measured among started journeys.

A started journey is classified as abandoned when it does not reach confirmation.

**Reason:**

This definition directly measures failure to complete after entering the digital process.

Under this methodology, completion and abandonment are complementary outcomes and therefore should not be interpreted as independent KPIs in statistical testing.

### KPI Analytical Populations

**Decision:**

Each KPI uses the population required by its analytical definition rather than forcing every KPI to use an identical sample.

Final analytical populations are:

1. Completion and abandonment: 64,181 started journeys.
2. Completion time: 32,561 valid completed journeys.
3. Step back analysis: 69,447 experiment journeys.

**Reason:**

Different KPIs require different eligibility criteria. Completion time requires a valid completed journey, while navigation behaviour can be observed in journeys that do not ultimately complete.

### Statistical Testing

**Decision:**

Day 3 focuses on KPI construction, descriptive comparison, validation, and statistical preparation.

Formal inferential conclusions will be made during Day 4.

**Reason:**

Observed differences between Test and Control should not be interpreted as statistically meaningful until the appropriate hypothesis tests, assumptions, effect sizes, and business relevance are evaluated.