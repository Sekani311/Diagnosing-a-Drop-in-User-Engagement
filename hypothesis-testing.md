
### **Hypothesis 1: Did Activation Trends Change?**
**Question:** Are fewer users becoming active after signing up?


![daily signups](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/daily-signups.png)


I grouped all users by their signup date and compared how many of them were marked as “activated.”

**Insight:** Daily user signups showed a steady growth which proved to be high during the weekdays and low on weekends. Considering weekends are not so busy for most companies, the change is gradual over the span of a few months and did not seem to have contributed to the drop in engagement.

---

### **Hypothesis 2: Did Engagement Drop Differently Across Regions?**

**Question:** Was the drop in engagement uniform across regions?


![geographic location](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/geographic-location.png)


Countries were grouped into continents, and login activity was compared week by week.

**Insight:** North America and Asia showed a dramatic fall-off in login activity around July end compared to other regions. This suggested that the issue might have been specific to certain regions possibly related to localized features, new product rollout issues, or holiday related factors.

---

### **Hypothesis 3: Are Certain Devices Driving the Drop?**
**Question:** Did device type influence engagement changes?


![device type drop](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/geographic-location.png)


I aggregated login activity by device (desktop, phone, tablet) and compared July vs. August.

**Insight:** Mobile devices especially the iPhones experienced the steepest declines in user engagement. Desktop engagement remained relatively stable. This suggested a possible mobile-related issue perhaps due to UX bugs, app updates, or performance problems on mobile platforms.

---

### **Hypothesis 4: Weekly Engagement Trends by Device**

**Question:** When exactly did the drop begin, and how did it vary by device type?


![device engagement](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/device-engagement.png)


I broke down login activity weekly and segmented it by device.

**Insight:** The most severe drop in mobile usage happened in the last week of July. The sharp decline, especially among phone users, confirmed that the problem was highly time-specific and potentially tied to a mobile app release or feature rollout.

---

### **Hypothesis 5: Could It Be a Tracking Issue?**

**Question:** Could a bug have disrupted how login events were tracked?


![tracking code](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/tracking-code.png)

I compared volumes of different user actions week by week.

**Insight:** While ‘message’ and ‘homepage’ events dropped significantly in July, events like ‘login’ and ‘search’ remained more consistent. This discrepancy hinted at a tracking bug affecting the loading of the homepage. It also seemed there was an issue on keeping users engaged in the platform.

---

### **Hypothesis 6: Are Newer Users Disengaging Faster?**

**Question:** Are younger user cohorts dropping off more?


![cohort analysis](https://github.com/Sekani311/Diagnosing-a-Drop-in-User-Engagement/blob/main/images/cohort-analysis.png)


I calculated login engagement over time based on the week users first activated.

**Insight:** Users in younger cohorts (activated closer to August) exhibited steeper drop-offs than those who activated earlier in the year. This suggested that onboarding may have become less effective. The decline in engagement for old users might also suggest a lack of retention from them.



