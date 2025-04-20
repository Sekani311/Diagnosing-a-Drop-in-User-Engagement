
### **Hypothesis 1: Did Activation Trends Change?**
**Question:** Are fewer users becoming active after signing up?

**SQL:**
```sql
-- Analyzing Baseline Metrics
SELECT 
	DATE_TRUNC('day', created_at) AS day,
	COUNT(*) AS all_users,
	COUNT(CASE WHEN activated_at IS NOT NULL THEN user_id 
	ELSE NULL END) AS actived_users
FROM tutorial.yammer_users
GROUP BY 1
ORDER BY 1;
```

![daily signups](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/daily-signups.png)


I grouped all users by their signup date and compared how many of them were marked as “activated.”

**Insight:** Daily user signups showed a steady growth which proved to be high during the weekdays and low on weekends. Considering weekends are not so busy for most companies, the change is gradual over the span of a few months and did not seem to have contributed to the drop in engagement.

---

### **Hypothesis 2: Did Engagement Drop Differently Across Regions?**

**Question:** Was the drop in engagement uniform across regions?

**SQL:**
```sql
-- Geographic Segmentation
  SELECT 
  DATE_TRUNC('week', events.occurred_at):: DATE AS start_week_date,
  sub.geographic_region,
  COUNT(DISTINCT events.user_id) as num
FROM
  (SELECT
    user_id,
    CASE
      WHEN location IN ('Indonesia', 'Korea', 'Singapore', 'Israel', 'Malaysia', 'Hong Kong', 'Saudi Arabia', 'Philippines', 'Turkey', 'United Arab Emirates', 'Taiwan', 'Thailand', 'India', 'Iran', 'Japan', 'Iraq', 
                        'Russia', 'Pakistan')
      THEN 'Asia'
      WHEN location IN ('Venezuela', 'Colombia', 'Argentina', 'Chile', 'Brazil')
      THEN 'South_America'
      WHEN location IN ('Sweden', 'Ireland', 'Portugal', 'Finland', 'France', 'Netherlands', 'Spain', 'Belgium', 'Italy', 'United Kingdom', 'Germany', 'Greece', 'Denmark', 'Switzerland', 'Norway', 'Austria', 'Poland')
      THEN 'Europe'
      WHEN location IN ('United States', 'Canada', 'Mexico')
      THEN 'North_America'
      WHEN location IN ('Nigeria', 'Egypt', 'South Africa')
      THEN 'Africa'
      WHEN location IN ('Australia')
      THEN 'Oceania'
    ELSE null
    END AS geographic_region
  FROM tutorial.yammer_events) sub
JOIN tutorial.yammer_events events
  ON events.user_id = sub.user_id
WHERE events.event_type = 'engagement'
GROUP BY DATE_TRUNC('week', occurred_at):: DATE,
    sub.geographic_region
ORDER BY DATE_TRUNC('week', occurred_at):: DATE;
```

![geographic location](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/geographic-location.png)

Countries were grouped into continents, and login activity was compared week by week.

**Insight:** North America and Asia showed a dramatic fall-off in login activity around July end compared to other regions. This suggested that the issue might have been specific to certain regions possibly related to localized features, new product rollout issues, or holiday related factors.

---

### **Hypothesis 3: Are Certain Devices Driving the Drop?**

**Question:** Did device type influence engagement changes?

**SQL:**
```sql
-- Device Segmentation
  WITH device_type AS(
SELECT device,
 CASE WHEN device in ('iphone 5','iphone 4s','iphone 5s','nexus  5','samsung galaxy s4','htc one','nokia lumia 635','samsung galaxy note','amazon fire phone') THEN 'Phone'
 WHEN device in ('windows surface','kindle fire','ipad mini','samsumg galaxy tablet','ipad air','nexus 7','nexus 10') THEN 'Tablet'
 WHEN device in ('dell inspiron notebook','macbook air','macbook pro','lenovo thinkpad','acer aspire notebook','asus chromebook','acer aspire desktop','mac mini','hp pavilion desktop','dell inspiron desktop') THEN 'Desktop' 
 END AS device_type
FROM tutorial.yammer_events),

device_count AS(
  SELECT device,
  COUNT(CASE WHEN date_trunc('month', occurred_at) BETWEEN '2014-07-01' AND '2014-07-31' THEN user_id ELSE NULL END) AS July_Sales,
  COUNT(CASE WHEN date_trunc('month', occurred_at) BETWEEN '2014-08-01' AND '2014-08-31' THEN user_id ELSE NULL END) AS August_Sales
  FROM tutorial.yammer_events
  WHERE event_type='engagement' 
  AND event_name = 'login'
  AND occurred_at BETWEEN '2014-07-01' AND '2014-08-31'
  GROUP BY 1
  )
SELECT device_count.device, device_type.device_type, August_Sales, July_Sales, (August_Sales - July_Sales) as diff
FROM device_count
JOIN device_type 
 ON device_count.device = device_type.device 
GROUP BY 1, 2, 3, 4, 5
ORDER BY 5;
```

![device type drop](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/geographic-location.png)

I aggregated login activity by device (desktop, phone, tablet) and compared July vs. August.

**Insight:** Mobile devices especially the iPhones experienced the steepest declines in user engagement. Desktop engagement remained relatively stable. This suggested a possible mobile-related issue perhaps due to UX bugs, app updates, or performance problems on mobile platforms.

---

### **Hypothesis 4: Weekly Engagement Trends by Device**

**Question:** When exactly did the drop begin, and how did it vary by device type?

**SQL:**
```sql
-- Engagement by Device Type
SELECT 
  DATE_TRUNC('week', e.occurred_at) AS week,
  CASE 
    WHEN e.device IN ('iphone 5', 'iphone 4s', 'iphone 5s', 'nexus 5', 'samsung galaxy s4', 'htc one', 'nokia lumia 635', 'samsung galaxy note', 'amazon fire phone') THEN 'Phone'
    WHEN e.device IN ('windows surface', 'kindle fire', 'ipad mini', 'samsumg galaxy tablet', 'ipad air', 'nexus 7', 'nexus 10') THEN 'Tablet'
    WHEN e.device IN ('dell inspiron notebook', 'macbook air', 'macbook pro', 'lenovo thinkpad', 'acer aspire notebook', 'asus chromebook', 'acer aspire desktop', 'mac mini', 
                      'hp pavilion desktop', 'dell inspiron desktop') THEN 'Desktop'
    ELSE 'Other'
  END AS device_type,
  COUNT(DISTINCT e.user_id) AS engagement_count
FROM 
  tutorial.yammer_events e
WHERE 
  e.event_type = 'engagement'
  AND e.event_name = 'login'
  AND e.occurred_at BETWEEN '2014-07-01' AND '2014-08-31'
GROUP BY 
  week, device_type
ORDER BY 
  week, device_type;
```

![device engagement](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/device-engagement.png)

I broke down login activity weekly and segmented it by device.

**Insight:** The most severe drop in mobile usage happened in the last week of July. The sharp decline, especially among phone users, confirmed that the problem was highly time-specific and potentially tied to a mobile app release or feature rollout.

---

### **Hypothesis 5: Could It Be a Tracking Issue?**

**Question:** Could a bug have disrupted how login events were tracked?

**SQL:**
```sql
-- Feature Engagement
SELECT 
  cast(date_trunc('week', occurred_at) as date) AS week,
  COUNT(CASE WHEN event_name IN ('login') THEN user_id ELSE NULL END) AS login, 
  COUNT(CASE WHEN event_name IN ('home_page') THEN user_id ELSE NULL END) AS homepage, 
  COUNT(CASE WHEN event_name IN ('view_inbox','like_message','send_message') THEN user_id ELSE NULL END) AS message, 
  COUNT(CASE WHEN event_name IN ('search_autocomplete', 'serach_run', 'search_click_result_1','search_click_result_2','search_click_result_3','search_click_result_4','search_click_result_5','search_click_result_6',
                                'search_click_result_7','search_click_result_8','search_click_result_9','search_click_result_10') THEN user_id ELSE NULL END) AS search_click 
FROM tutorial.yammer_events 
GROUP BY 1;
```

![tracking code](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/tracking-code.png)

I compared volumes of different user actions week by week.

**Insight:** While ‘message’ and ‘homepage’ events dropped significantly in July, events like ‘login’ and ‘search’ remained more consistent. This discrepancy hinted at a tracking bug affecting the loading of the homepage. It also seemed there was an issue on keeping users engaged in the platform.

---

### **Hypothesis 6: Are Newer Users Disengaging Faster?**

**Question:** Are younger user cohorts dropping off more?

**SQL:**
```sql
-- User Age Cohort Analysis
SELECT 
  DATE_TRUNC('week',z.occurred_at) AS "week",
  AVG(z.age_at_event) AS "Average age during week",
  COUNT(DISTINCT CASE WHEN z.user_age > 70 THEN z.user_id ELSE NULL END) AS "10+ weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 70 AND z.user_age >= 63 THEN z.user_id ELSE NULL END) AS "9 weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 63 AND z.user_age >= 56 THEN z.user_id ELSE NULL END) AS "8 weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 56 AND z.user_age >= 49 THEN z.user_id ELSE NULL END) AS "7 weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 49 AND z.user_age >= 42 THEN z.user_id ELSE NULL END) AS "6 weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 42 AND z.user_age >= 35 THEN z.user_id ELSE NULL END) AS "5 weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 35 AND z.user_age >= 28 THEN z.user_id ELSE NULL END) AS "4 weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 28 AND z.user_age >= 21 THEN z.user_id ELSE NULL END) AS "3 weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 21 AND z.user_age >= 14 THEN z.user_id ELSE NULL END) AS "2 weeks",
  COUNT(DISTINCT CASE WHEN z.user_age < 14 AND z.user_age >= 7 THEN z.user_id ELSE NULL END) AS "1 week",
  COUNT(DISTINCT CASE WHEN z.user_age < 7 THEN z.user_id ELSE NULL END) AS "Less than a week"
FROM
  (SELECT
    e.occurred_at,
    u.user_id,
    DATE_TRUNC('week',u.activated_at) AS activation_week,
    EXTRACT('day' FROM e.occurred_at - u.activated_at) AS age_at_event,
    EXTRACT('day' FROM '2014-09-01'::TIMESTAMP - u.activated_at) AS user_age
FROM tutorial.yammer_users u
JOIN tutorial.yammer_events e
  ON e.user_id = u.user_id
  AND e.event_type = 'engagement'
  AND e.event_name = 'login'
  AND e.occurred_at >= '2014-05-01'
  AND e.occurred_at < '2014-09-01'
WHERE u.activated_at IS NOT NULL) z
GROUP BY 1
ORDER BY 1;
```

![cohort analysis](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/cohort-analysis.png)

I calculated login engagement over time based on the week users first activated.

**Insight:** Users in younger cohorts (activated closer to August) exhibited steeper drop-offs than those who activated earlier in the year. This suggested that onboarding may have become less effective. The decline in engagement for old users might also suggest a lack of retention from them.



