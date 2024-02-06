# Retention, Cohorts, Churn

## Overview
Weekly subscription cohort analysis to comprehend retention over six weeks, utilizing BigQuery and Google Spreadsheets

## Project Structure
Explain the organization of your project, including key folders and files.

## Tools and Technologies
Big Query & Google Spreadsheets

## Data Source
A single table containing information about your users’ subscriptions curated by the educational institute. This table contains user id, start, end dates of subscriptions and some additional customer-related information such as Country and category (desktop, mobile, tablet).

## Code 
SQL code snippets to get the Retention cohorts analysis chart
```sql
 SELECT
    user_pseudo_id AS users,
    subscription_start AS sub_start,
    subscription_end AS sub_end,
    DATE_TRUNC(subscription_start,WEEK) AS cohort_week,
    DATE_TRUNC(subscription_end, WEEK) AS cancel_week,
  -- category,country,
  FROM
    `data_analytics.subscriptions`
  ORDER BY
    subscription_start
```
Retention from weeks 0-6 in terms of count of subscriptions
```sql
SELECT
  cohort_week,
-- category,country,
  COUNT(sub_start) AS count_subscriptions,
  SUM(CASE
      WHEN sub_end IS NULL OR cancel_week>= DATE_ADD(cohort_week,INTERVAL 1 WEEK) THEN 1
  END
    ) AS week_0,
/*indicates activity in the week the first time subscription was made and if users unsubscribed in the same week as subscription then the rentention in week 0 is less than 100%, can be replaced with logic cohort_week!= cancel_week*/
  SUM(CASE
    WHEN sub_end IS NULL OR cancel_week>= DATE_ADD(cohort_week,INTERVAL 2 WEEK) THEN 1
END
  ) AS week_1,
/*values under this weekly retention columns show subscriptions that are still active in that week(week1, 2...etc), so subscriptions that stayed 1, 2, 3,.. weeks after they initially subscribed*/
  SUM(CASE
      WHEN sub_end IS NULL OR cancel_week>= DATE_ADD(cohort_week,INTERVAL 3 WEEK) THEN 1
  END
    ) AS week_2,
----so on
FROM
  cohorts --cohorts is the name of subquery
GROUP BY
 cohort_week;
```
## Analysis and Insights
#### PARAMETERS:
+ Time period of analysis : 01.11.2020 to 31.01.2021
+ No. of Subscribers : 270154
+ No.of Subscribers (with more than 1 subscription) : 4182
+ No. of Subscriptions : 274362
+ No. and Types of Categories : 3 - Desktop, Tablet, Mobile
+ No. Countries with subscribers : 109

#### INSIGHTS:
+ Overall good customer retention rates between week 0 and week 6
+ Week of Dec 6, 2020 - Dec 12, 2020 had maximum number of new subscriptions
+ Subscriptions made in weeks Dec 20, 2020 and Dec 27,2020, though low in count, have the best retention rates (97% - 92%) over the following 5 weeks
+ In terms of count of weekly new subscriptions by country, USA ranks the highest with an average of 9181 subscriptions per week.
+ In terms of count of weekly new subscriptions by category, number of desktop (12566) subscriptions are the highest followed by mobile(11270) subscriptions.

## Visualizations
[Retention Cohort][Retention, Cohorts & Churn - Weekly retention.pdf](https://github.com/ammu993/Retention-cohorts-churn/files/14173111/Retention.Cohorts.Churn.-.Weekly.retention.pdf)
![churn](https://github.com/ammu993/Retention-cohorts-churn/assets/74145869/b0f663cf-eae5-4dac-ab81-741ec4ca22df)




