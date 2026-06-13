# LifeChange — Food Chart Guide

This guide has two parts:
1. **A ready-to-use prompt** you (or anyone) can give to an AI to generate a brand-new 5-week food chart in the exact format the app needs.
2. **A technical reference** for how the food data is structured, in case you want to edit it by hand.

---

# PART 1 — THE PROMPT (copy-paste this to generate a new chart)

Copy everything in the box below, fill in your own family details where marked, and send it to Claude (or any capable AI). It will produce a complete 5-week meal chart formatted for the LifeChange app.

```
I need a 5-WEEK family meal chart for a habit-tracking app called LifeChange.
Generate it in the EXACT plain-text format described at the end so it can be
converted into the app's data block.

ABOUT MY FAMILY (edit these):
- People to track protein for: Me, Husband, Daughter (toddler).
  [Change names/number of people if different — but keep it to a small fixed set.]
- My health goals: fix low iron / anemia, build strength, breastfeeding-friendly
  (no calorie cutting), sustainable weight.  [Replace with your own goals.]
- Daughter's needs: iron + calcium, soft/mashable foods, no choking risks.
  [Edit as needed.]
- Cuisine / diet: vegetarian Indian, egg optional on the side for some people.
  [Replace with your cuisine and any restrictions.]
- Default daily protein goals: Me 50g, Husband 80g, Daughter 14g.
  [Set your own target grams.]

WHAT TO GENERATE:

1) MEALS — 5 weeks, 7 days each (Mon–Sun), 5 meal slots per day in this order:
   Breakfast (~9:30am), Lunch (~1:30pm), Mid-Morning Snack (~11am),
   Evening Snack (~5pm), Dinner (~8pm).
   For EACH meal give:
   - the dish name
   - an optional alteration note (e.g. "Dau: mash soft. Hus: +1 egg") — can be blank
   - protein in grams for EACH person (me / husband / daughter)
   Also for each DAY give:
   - an optional note banner (a short tip, e.g. "Lemon on dal triples iron")
   - an optional tag banner (e.g. "Possible dance day", "Rest day")
   - a "prep for tomorrow" line (e.g. "Soak rajma overnight")
   Make weeks varied but realistic, reuse a sensible grocery base, and make
   Sundays a light "rest day" with order-out options.

2) GROCERY — for each of the 5 weeks, TWO orders:
   - "Order 1 — Mon/Tue" and "Order 2 — Thu/Fri"
   - each grouped into categories: Vegetables, Fruits, Dal & Legumes,
     Grains & Flour, Dairy, Other (categories can be adjusted).
   Plain shopping lists (no quantities needed unless you want them).

3) TIPS — two short lists:
   - "Non-Negotiables": daily/weekly must-dos (e.g. iron tablet timing, milk at 5pm).
   - "Goals Supported": what this plan is trying to achieve.

OUTPUT FORMAT (use EXACTLY this layout so it's easy to convert):

### Week 1
**Monday**
- Tag: <optional tag banner, or omit>
- Note banner: <optional note, or omit>
- Breakfast: <dish> | alt: <alteration or blank> | protein me Xg, husband Yg, daughter Zg
- Lunch: <dish> | alt: ... | protein me Xg, husband Yg, daughter Zg
- Mid-Morning Snack: <dish> | alt: ... | protein me Xg, husband Yg, daughter Zg
- Evening Snack: <dish> | alt: ... | protein me Xg, husband Yg, daughter Zg
- Dinner: <dish> | alt: ... | protein me Xg, husband Yg, daughter Zg
- Prep: <prep for tomorrow>
(...repeat for Tuesday–Sunday, then Weeks 2–5...)

## GROCERY
### Week 1
**Order 1 — Mon/Tue**
- Vegetables: ...
- Fruits: ...
- Dal & Legumes: ...
- Grains & Flour: ...
- Dairy: ...
- Other: ...
**Order 2 — Thu/Fri**
- (same categories)
(...repeat for Weeks 2–5...)

## TIPS
**Non-Negotiables:** item; item; item; ...
**Goals Supported:** item; item; item; ...

Keep all protein numbers as plain grams. Don't invent extra meal slots — always
exactly 5 per day. Always 7 days. Always 5 weeks.
```

When the AI gives you the filled-in chart in that format, **send it to me (Claude)** and I'll convert it into the app's data block and hand you an updated `index.html` to upload. You never have to touch code.

---

# PART 2 — TECHNICAL REFERENCE (for editing by hand)

All food data lives in ONE block in `index.html`. Search for:

```
FOOD / MEAL PLAN DATA  —  EDIT THIS BLOCK TO UPDATE THE CHART
```

…down to:

```
END OF FOOD / MEAL PLAN DATA
```

Everything below the END line is app logic — leave it alone.

## The four pieces inside the block

### 1. Meals — MEAL_PLAN
5 weeks, each with 7 days. One day looks like:

```javascript
mon: {
  tag: 'Possible dance day',          // optional banner, or omit
  note: 'Lemon on dal triples iron',  // optional tip banner, or omit
  meals: {
    breakfast: m('Suji chilla + curd', 'Dau: mash soft', 20, 27, 8),
    lunch:     m('Chana dal + chapati', 'Lemon for all', 18, 18, 8),
    midsnack:  m('Seasonal fruit', 'Dau: mash', 1, 1, 1),
    evesnack:  m('Makhana + dates', 'Dau: fewer', 3, 3, 2),
    dinner:    m('Bhindi + chapati', 'Hus: +1 egg', 5, 12, 3),
  },
  prep: 'Soak rajma overnight.'
},
```

The m(...) shorthand is:
m( dish name , alteration note , ME protein , HUSBAND protein , DAUGHTER protein )

Rules:
- Keep the 5 meal keys exactly: breakfast, lunch, midsnack, evesnack, dinner
- Keep the 7 day keys exactly: mon, tue, wed, thu, fri, sat, sun
- tag, note, and the alteration text may be '' or omitted
- Protein values are plain numbers (grams)

### 2. Grocery — GROCERY
Per week, two orders, each grouped by category:

```javascript
1: {
  o1: { title:'Order 1 — Mon/Tue', cats:{
    'Vegetables':'onion, tomato, palak, ...',
    'Fruits':'banana, papaya, ...',
    'Dal & Legumes':'chana dal, rajma, ...',
    'Grains & Flour':'...',
    'Dairy':'milk, curd, ...',
    'Other':'sattu, dates, ...',
  }},
  o2: { title:'Order 2 — Thu/Fri', cats:{ ... }},
},
```

Category names are free text — rename/add/remove freely.

### 3. Tips — FOOD_TIPS
```javascript
const FOOD_TIPS = {
  nonneg: ['Lemon on dal (3x iron)', 'Iron tablet after breakfast', ...],
  goals:  ['Fix anemia / low iron', 'Build strength', ...],
};
```

### 4. Default protein goals — PROTEIN_GOALS_DEFAULT
```javascript
const PROTEIN_GOALS_DEFAULT = { me:50, hus:80, dau:14 };
```
Starting targets only — you can also change them live with the Goals button.

---

## How the app uses this data (so you know what each piece drives)

- Tickable meals on Today page: each meal becomes a tap-to-complete card with its protein shown per person.
- Protein calculation: as you tick meals, the 3 bars (You / Husband / Daughter) fill toward the goal and turn green when met. The Goals button edits goals live.
- Next-day prep + "what I actually ate": the prep text and a free-text "ate" box appear at the end of the food section each day.
- Food tab: ring chart of % meals followed, tap any day to see that day's 5 meals, a by-meal-type breakdown, and the week's food notes.
- Progress tab: a food ring near "This week at a glance"; food % is also saved into each weekly report for history.
- Grocery: shown read-only under the Metrics tab, as Order 1 and Order 2.
- Tips: shown under the Drive tab.
- 5-week rotation: the week you start = Week 1, then 2-3-4-5-back to 1, anchored to your first Monday. The chart content does not reset when it rotates.

---

## Important notes
- Your logged data (meal ticks, notes, weights) is stored on your phone separately and is NEVER affected when the chart is swapped.
- Always keep exactly 5 meal keys, 7 day keys, and 5 weeks, or a day will not load.
- If you lock the Food pillar in the Goals tab, food disappears from Today and reports, but grocery, tips, and the Food tab stay — and nothing is deleted.
