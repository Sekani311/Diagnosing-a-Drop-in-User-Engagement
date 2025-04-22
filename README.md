# Diagnosing-a-Drop-in-User-Engagement

![Frame](images/frame)

## Table of Content
- [Project Overview](#project-overview)
- [Dataset Overview](dataset-overview)
- [Goals](#goals)
- [Conclusion](conclusion)
- [Recommendations](recommendations)
- [Reflection](#reflection)

### Project Overview

This case study explores a drop in user engagement observed in August 2014 by Yammer, a social network for communicating with coworkers using the Mode Analytics platform. The goal was to identify the underlying reasons behind this decline through a SQL-based analysis, slicing the data by user activity trends, device category, region, and user cohort. The structure of this analysis follows a step-by-step breakdown: each possible hypothesis is posed, the corresponding SQL is written, and the results are interpreted.

### Dataset Overview

The analysis leveraged four primary datasets from the Mode Analytics platform, each with distinct columns and data points. Here's a quick overview of each dataset and the columns used in this study:

1. **`tutorial.yammer_users`**
    - **Columns:** `user_id`, `created_at`, `activated_at`, `country`, `device`
2. **`tutorial.yammer_events`**
    - **Columns:** `user_id`, `event_type`, `occurred_at`, `device`, `location`
3. **`tutorial.yammer_emails`**
    - **Columns:** `user_id`, `occurred_at`, `action`
4. **`benn.dimension_rollup_periods`**
    - **Columns:** `period_id`, `time_id`, `region`

The first two datasets were queried and joined at various stages to uncover insights regarding user activation, engagement, and potential issues with tracking codes or regional variations.

### Goals

The process of ***Hypothesis Testing*** was employed in this analysis. It was important to explore a list of hypothesis that could help me effectively determine the possible sources of drop in user engagement. In preparing and prioritizing my list of hypothesis, some of my goals were to:

- Understand user activation and engagement trends over time.
- Identify if specific devices contributed to the drop.
- Explore regional variations in engagement.
- Analyze engagement by user cohort (age since activation).
- Check for possible tracking or data collection issues.

For full details on the Exploratory Analysis for each hypothesis
- [queries.sql](queries.sql) contains all investigation SQL codes
- [hypothesis-testing.md](hypothesis-testing.md) contains the complementary excel charts and insights for query interpretation


### Conclusion

Through SQL-based exploration across multiple dimensions, I observed the following key findings:
1. User **activations** remained steady with drops during the weekend.
2. The drop was concentrated among **mobile users**, particularly iPhones.
3. It began sharply in **late July** and affected **North America and Asia** most.
4. **Message and homepage events** dropped, but other event types remained stable, indicating a potential **tracking issue**.
5. **New users** disengaged more quickly, pointing to **onboarding or experience issues**.

### Recommendations

1. **Audit mobile app releases** around late July for potential bugs or regressions.
2. **Check homepage and message tracking** especially for mobile platforms to ensure no data loss.
3. **Review onboarding changes** or A/B tests implemented before August.
4. **Prioritize cohort analysis** for future product experiments to detect early drop-offs.

### Reflection

This case study taught me how to construct a structured analytical process using SQL and how critical it is to combine event data with user metadata (devices, location, cohort). I learnt a little more about the Common Table Expression (CTE) and how different it is from a simple subquery. I also recognize there is a room for a better query structure and analysis and I am clearly open to learning more and improving my value as a Data Analyst.
