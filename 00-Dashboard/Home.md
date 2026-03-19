---
tags:
  - dashboard
---

# NVP Relational Map

**[[August Dashboard]]** | **[[Savannah Dashboard]]**

---

## Overdue Contacts

```dataviewjs
const people = dv.pages('"People/Network"')
  .where(p => p.type === "person" && p.cadence && p.cadence !== "as-needed");
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
    rows.push([person.file.link, person.cadence, "Never", "No interactions yet", person.owner]);
    continue;
  }

  const last = new Date(lastDate.toString());
  const dueDate = new Date(last.getTime() + days * 86400000);
  const today = new Date();
  const daysOverdue = Math.floor((today - dueDate) / 86400000);

  if (daysOverdue >= 0) {
    rows.push([person.file.link, person.cadence, lastDate,
      daysOverdue === 0 ? "Due today" : daysOverdue + " days overdue",
      person.owner]);
  }
}

rows.sort((a, b) => {
  const aVal = String(a[3]).includes("Never") ? 999 : parseInt(String(a[3])) || 0;
  const bVal = String(b[3]).includes("Never") ? 999 : parseInt(String(b[3])) || 0;
  return bVal - aVal;
});

if (rows.length > 0) {
  dv.table(["Person", "Cadence", "Last Contact", "Status", "Owner"], rows);
} else {
  dv.paragraph("*All contacts are within their cadence window.*");
}
```

---

## Upcoming Touchpoints (Next 30 Days)

```dataviewjs
const people = dv.pages('"People/Network"')
  .where(p => p.type === "person" && p.cadence && p.cadence !== "as-needed");
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

  if (!lastDate) continue; // These show in Overdue instead

  const last = new Date(lastDate.toString());
  const days = cadenceDays[person.cadence] || 30;
  const dueDate = new Date(last.getTime() + days * 86400000);
  const today = new Date();
  const daysUntilDue = Math.floor((dueDate - today) / 86400000);

  if (daysUntilDue > 0 && daysUntilDue <= 30) {
    rows.push([person.file.link, person.role, person.cadence,
      lastDate, "Due in " + daysUntilDue + "d", person.owner]);
  }
}

rows.sort((a, b) => {
  const aNum = parseInt(String(a[4]).replace(/\D/g, ''));
  const bNum = parseInt(String(b[4]).replace(/\D/g, ''));
  return aNum - bNum;
});

if (rows.length > 0) {
  dv.table(["Person", "Role", "Cadence", "Last Contact", "Due", "Owner"],
    rows.slice(0, 20));
} else {
  dv.paragraph("*No touchpoints coming due in the next 30 days.*");
}
```

---

## Relational Suggestions

```dataviewjs
const interactions = dv.pages('"Interactions"')
  .where(i => i.type === "interaction")
  .sort(i => i.date, "desc");

const recentDays = 14;
const today = new Date();
const recentInteractions = interactions.where(i => {
  const d = new Date(i.date.toString());
  return (today - d) / 86400000 <= recentDays;
});

const recentPeople = new Set();
for (const i of recentInteractions) {
  if (i.participants) {
    for (const p of i.participants) {
      const name = String(p).replace(/\[\[|\]\]/g, '');
      recentPeople.add(name);
    }
  }
}

const allPeople = dv.pages('"People/Network"').where(p => p.type === "person");
const suggestions = [];

for (const recentName of recentPeople) {
  const recentPerson = allPeople.find(p => p.file.name === recentName);
  if (!recentPerson || !recentPerson.orgs) continue;

  for (const org of [recentPerson.orgs].flat()) {
    const orgName = String(org).replace(/\[\[|\]\]/g, '');
    const colleagues = allPeople.where(p =>
      p.file.name !== recentName &&
      !recentPeople.has(p.file.name) &&
      p.orgs && [p.orgs].flat().some(o => String(o).includes(orgName))
    );

    for (const colleague of colleagues) {
      const collInteractions = interactions.where(i =>
        i.participants && i.participants.some(p =>
          String(p).includes(colleague.file.name)
        )
      );
      const lastContact = collInteractions.length > 0
        ? collInteractions[0].date : null;
      const daysSince = lastContact
        ? Math.floor((today - new Date(lastContact.toString())) / 86400000)
        : 999;

      if (daysSince > 14) {
        suggestions.push([
          colleague.file.link,
          "You spoke with " + recentName + " (" + orgName + ")",
          lastContact ? daysSince + " days ago" : "Never contacted",
          colleague.owner
        ]);
      }
    }
  }
}

const seen = new Set();
const unique = suggestions.filter(s => {
  const key = String(s[0]);
  if (seen.has(key)) return false;
  seen.add(key);
  return true;
});

if (unique.length > 0) {
  dv.table(["Suggested Contact", "Connection", "Last Contact", "Owner"],
    unique.slice(0, 10));
} else {
  dv.paragraph("*No relational suggestions right now.*");
}
```

---

## Funding Pipeline

```dataview
TABLE source-org AS "Source Org", contact AS "Contact", connected-via AS "Connected Via", status AS "Status", focus-areas AS "Focus Areas"
FROM "Funders"
WHERE type = "funder"
SORT status ASC
```

---

## Recent Interactions (Last 30 Days)

```dataview
TABLE date AS "Date", participants AS "Participants", context AS "Context", about AS "About"
FROM "Interactions"
WHERE date >= date(today) - dur(30 days)
SORT date DESC
```

---

## Active Projects

```dataview
TABLE status AS "Status", lead AS "Lead", partners AS "Partners", funded-by AS "Funded By"
FROM "Projects"
WHERE status = "active" OR status = "planning"
SORT choice(status = "active", 1, 2) ASC
```
