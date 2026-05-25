# 🍱 Meal Planner Agent

> A no-code AI agent that generates a personalised weekly South Indian meal plan and emails it to you every Monday morning — automatically.

Built with Make.com (free plan), Google Sheets, and AI by Make. No coding required.

---

## What It Does

Every Monday at 7am, this agent:

1. Reads your **fridge stock** from Google Sheets
2. Reads your **recipe library** from Google Sheets
3. Reads your **household config** (preferences, rules) from Google Sheets
4. Sends all of this to an **AI model** with a detailed prompt
5. Gets back a **7-day meal plan** with breakfast, lunch, and dinner for each day
6. Emails it to you in a **clean, formatted HTML email**

The meal plan:
- Prioritises ingredients that expire soonest
- Follows your cooking time constraints (weekday vs weekend)
- Respects dietary rules (e.g. no non-veg on Tuesdays)
- Includes reuse tips to avoid cooking from scratch every meal
- Flags spoilage alerts per day
- Ends with a spoilage summary and shopping list for next week

---

## Pipeline

```
Google Sheets         Google Sheets         Google Sheets
(Fridge Stock)   →   (Recipe Library)  →   (Agent Config)
      ↓                     ↓                     ↓
Text Aggregator       Text Aggregator       Text Aggregator
      ↓                     ↓                     ↓
                    AI by Make (Make AI Toolkit)
                    Simple Text Prompt — Large model
                              ↓
                         Gmail
                    Send formatted HTML email
```

---

## Tech Stack

| Layer | Tool | Cost |
|---|---|---|
| Automation platform | Make.com | Free (1,000 ops/month) |
| Data storage | Google Sheets | Free |
| AI model | AI by Make (gpt-5-mini) | ~32 credits/month |
| Email | Gmail via Make | Free |

Total cost: **$0/month** on the free plan.

---

## Google Sheets Structure

### Sheet 1: Fridge Stock

| Item | Quantity | Unit | Expiry (days from today) | Notes |
|---|---|---|---|---|
| Spinach | 1 | bunch | 2 | Use first |
| Potato | 3 | pcs | 10 | |
| Rice | ∞ | stock | — | Always available |

Update this sheet every week before Monday 7am.

### Sheet 2: Recipe Library

| Dish | Prep Time | Type | Key Ingredients |
|---|---|---|---|
| Veg sambar | 15-30 mins | Lunch - side dish | toor dal, mixed vegetables, tamarind |
| Chappathi & gravy | 30-60 mins | Dinner - main course | wheat flour, onion, tomato |

Add your household's known dishes here. The AI will only suggest dishes from this list.

### Sheet 3: Agent Config

| Setting | Value |
|---|---|
| Household size | 2 |
| Cuisine type | South Indian |
| No non-veg on | Tuesday |
| Weekday max cook time | 30 mins |
| Weekend max cook time | 60 mins |
| Email recipient | your@gmail.com |

---

## The AI Prompt

The prompt tells the AI:
- What pantry staples are always available
- What's currently in the fridge (from Sheet 1)
- Which dishes are allowed (from Sheet 2)
- Household rules (from Sheet 3)
- Strict rules: never assume batter exists, only use available ingredients, use expiring items first
- Output format: HTML-formatted email with emojis, bold labels, daily spoilage alerts

See `prompt.txt` for the full prompt.

---

## How to Replicate

1. **Create the Google Sheets file** with 3 tabs: Fridge Stock, Recipe Library, Agent Config
2. **Create a Make.com account** (free)
3. **Create a new scenario** called "Meal Planner - Weekly Menu"
4. **Add modules in order:**
   - Google Sheets → Get Range Values (Fridge Stock, A1:E20)
   - Tools → Text Aggregator (source: Fridge Stock)
   - Google Sheets → Get Range Values (Recipe Library, A1:D25)
   - Tools → Text Aggregator (source: Recipe Library)
   - Google Sheets → Get Range Values (Agent Config, A1:B10)
   - Tools → Text Aggregator (source: Agent Config)
   - AI by Make → Simple Text Prompt (Large model)
   - Gmail → Send an Email
5. **Paste the prompt** from `prompt.txt` into the AI module, mapping the three Text Aggregator outputs into the relevant sections
6. **Configure Gmail** with your email and the HTML template from `email-template.html`
7. **Set the schedule** to Weekly → Monday → 7:00 AM → your timezone
8. **Turn the scenario ON**

---

## Files in This Repo

| File | Description |
|---|---|
| `README.md` | This file |
| `prompt.txt` | Full AI prompt used in the Make AI Toolkit module |
| `fridge-stock-template.csv` | Template for the Fridge Stock sheet |
| `recipe-library-template.csv` | Template for the Recipe Library sheet |
| `agent-config-template.csv` | Template for the Agent Config sheet |
| `email-template.html` | HTML wrapper used in the Gmail module |
| `screenshots/` | Canvas screenshot and sample email output |

---

## Sample Output

```
🍱 Your Weekly Meal Plan

Monday, 26 May 2026
🌅 Breakfast: Yellow moong dhal Kadaiyal (20 mins | ~280 cal/person)
☀️ Lunch: Veg sambar with spinach + rice (25 mins | ~350 cal/person)
🌙 Dinner: Brinjal curry leftover + rice (15 mins reheated | ~400 cal/person)
💡 Reuse tip: Make sambar for 2 meals — use leftovers for Tuesday lunch.
⚠️ Spoilage alert: Use spinach and brinjal curry today — both expire soon.

...

Spoilage Summary
- Spinach → used Monday lunch ✓
- Brinjal curry → used Monday dinner ✓

Shopping needed next week
- Fresh spinach, radish, carrot, beans
```

---

## Lessons Learned

- **Search Rows in Make creates one bundle per row** — causes the scenario to run N times. Use **Get Range Values** instead to return all rows as one bundle.
- **Text Aggregators** collect multiple bundles into one text block before passing to AI.
- **The prompt is the brain** — strict rules in the prompt matter more than the data structure.
- **AI by Make is free and surprisingly capable** — gpt-5-mini handles complex meal planning with multiple constraints well.
- Make's free plan (1,000 ops/month) is more than enough for a weekly scenario using ~32 ops/month.

---

## Built By

**Soundarya Alagesan** — designer with a background in ML engineering, building AI-forward products.

Built as part of the **She Vibes AI Learning Cohort (C0)** — a 66-day program exploring practical AI applications.

- [TinyTutor](https://tiny-tutor-dusky.vercel.app) — another project: a bite-sized AI learning platform

---

*Especially useful for households that want zero food waste and zero decision fatigue on weekday mornings.* 🌿
