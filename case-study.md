
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

