---
tags:
  - dashboard
---

# August's Dashboard

**[[Home]]** | **[[Savannah Dashboard]]**

---

## My Overdue Contacts

```dataviewjs
const people = dv.pages('"People/Network"')
  .where(p => p.type === "person" && p.cadence && p.cadence !== "as-needed"
    && p.owner && String(p.owner).includes("August"));
const interactions = dv.pages('"Interactions"')
  .where(i => i.type === "interaction");

const cadenceDays = {
  "weekly": 7, "biweekly": 14, "monthly": 30, "quarterly": 90
};

const rows = [];
for (const person of people) {
  const personInteractions = interactions.where(i =>
    i.participants && i.participants.some(p =>
      String(p).includes(person.file.name)
    )
  ).sort(i => i.date, "desc");

  const lastDate = personInteractions.length > 0
    ? personInteractions[0].date : null;
  const days = cadenceDays[person.cadence] || 30;

  if (!lastDate) {
    rows.push([person.file.link, person.cadence, "Never", "No interactions yet"]);
    continue;
  }

  const last = new Date(lastDate.toString());
  const dueDate = new Date(last.getTime() + days * 86400000);
  const today = new Date();
  const daysOverdue = Math.floor((today - dueDate) / 86400000);

  if (daysOverdue >= 0) {
    rows.push([person.file.link, person.cadence, lastDate,
      daysOverdue === 0 ? "Due today" : daysOverdue + " days overdue"]);
  }
}

rows.sort((a, b) => {
  const aVal = String(a[3]).includes("Never") ? 999 : parseInt(String(a[3])) || 0;
  const bVal = String(b[3]).includes("Never") ? 999 : parseInt(String(b[3])) || 0;
  return bVal - aVal;
});

if (rows.length > 0) {
  dv.table(["Person", "Cadence", "Last Contact", "Status"], rows);
} else {
  dv.paragraph("*All your contacts are within their cadence window.*");
}
```

---

## My Upcoming Touchpoints

```dataviewjs
const people = dv.pages('"People/Network"')
  .where(p => p.type === "person" && p.cadence && p.cadence !== "as-needed"
    && p.owner && String(p.owner).includes("August"));
const interactions = dv.pages('"Interactions"')
  .where(i => i.type === "interaction");

const cadenceDays = {
  "weekly": 7, "biweekly": 14, "monthly": 30, "quarterly": 90
};

const rows = [];
for (const person of people) {
  const personInteractions = interactions.where(i =>
    i.participants && i.participants.some(p =>
      String(p).includes(person.file.name)
    )
  ).sort(i => i.date, "desc");

  const lastDate = personInteractions.length > 0
    ? personInteractions[0].date : null;

  if (!lastDate) continue;

  const last = new Date(lastDate.toString());
  const days = cadenceDays[person.cadence] || 30;
  const dueDate = new Date(last.getTime() + days * 86400000);
  const today = new Date();
  const daysUntilDue = Math.floor((dueDate - today) / 86400000);

  if (daysUntilDue > 0 && daysUntilDue <= 30) {
    rows.push([person.file.link, person.role, person.cadence,
      lastDate, "Due in " + daysUntilDue + "d"]);
  }
}

rows.sort((a, b) => parseInt(String(a[4]).replace(/\D/g, '')) - parseInt(String(b[4]).replace(/\D/g, '')));

if (rows.length > 0) {
  dv.table(["Person", "Role", "Cadence", "Last Contact", "Due"], rows.slice(0, 20));
} else {
  dv.paragraph("*No touchpoints coming due in the next 30 days.*");
}
```

---

## My Recent Interactions

```dataview
TABLE date AS "Date", participants AS "Participants", context AS "Context", about AS "About"
FROM "Interactions"
WHERE contains(logged-by, [[August Elliott]])
  AND date >= date(today) - dur(30 days)
SORT date DESC
```

---

