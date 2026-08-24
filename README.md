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