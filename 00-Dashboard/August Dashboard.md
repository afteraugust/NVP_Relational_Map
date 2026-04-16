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

## Current Priorities

People, orgs, and projects tagged `#currentPriority`, plus everyone adjacent to them.

### Priority People

```dataviewjs
const isPriority = (p) => (p.file.tags || []).includes("#currentPriority");

const people = dv.pages('"People"').where(p => p.type === "person" && isPriority(p));
const interactions = dv.pages('"Interactions"').where(i => i.type === "interaction");

const rows = [];
for (const person of people) {
  const personInteractions = interactions
    .where(i => i.participants && i.participants.some(x => String(x).includes(person.file.name)))
    .sort(i => i.date, "desc");

  const lastDate = personInteractions.length > 0 ? personInteractions[0].date : null;
  let lastText = "Never";
  if (lastDate) {
    const last = new Date(lastDate.toString());
    const daysAgo = Math.floor((new Date() - last) / 86400000);
    lastText = `${lastDate} (${daysAgo}d ago)`;
  }

  const orgs = person.orgs
    ? (Array.isArray(person.orgs) ? person.orgs : [person.orgs]).filter(o => o)
    : [];

  rows.push([person.file.link, orgs.length ? orgs : "—", person.owner || "—", lastText]);
}

if (rows.length > 0) {
  dv.table(["Person", "Orgs", "Owner", "Last Contact"], rows);
} else {
  dv.paragraph("*No priority people tagged yet. Add `#currentPriority` to a person's frontmatter tags.*");
}
```

### Connected Orgs

```dataviewjs
const isPriority = (p) => (p.file.tags || []).includes("#currentPriority");
const stripLink = (v) => String(v).replace(/\[\[|\]\]/g, "").split("|")[0].trim();
const asArray = (v) => v ? (Array.isArray(v) ? v : [v]) : [];

const priorityPeople = dv.pages('"People"').where(p => p.type === "person" && isPriority(p));
const allOrgs = dv.pages('"Organizations"').where(o => o.type === "organization");
const allPeople = dv.pages('"People"').where(p => p.type === "person");

const orgNames = new Set();
for (const person of priorityPeople) {
  for (const o of asArray(person.orgs)) if (o) orgNames.add(stripLink(o));
}
for (const org of allOrgs.where(o => isPriority(o))) orgNames.add(org.file.name);

const rows = [];
for (const name of orgNames) {
  const org = allOrgs.find(o => o.file.name === name);
  if (!org) { rows.push([name + " (page missing)", "—", "—"]); continue; }

  const priorityMembers = [];
  const otherMembers = [];
  for (const p of allPeople) {
    const pOrgs = asArray(p.orgs).map(stripLink);
    if (!pOrgs.includes(name)) continue;
    if (isPriority(p)) priorityMembers.push(p.file.link);
    else otherMembers.push(p.file.link);
  }

  rows.push([
    org.file.link,
    priorityMembers.length ? priorityMembers : "—",
    otherMembers.length ? otherMembers : "—"
  ]);
}

if (rows.length > 0) {
  dv.table(["Org", "Priority Members", "Other Members"], rows);
} else {
  dv.paragraph("*No connected orgs yet.*");
}
```

### Connected Projects

```dataviewjs
const isPriority = (p) => (p.file.tags || []).includes("#currentPriority");
const stripLink = (v) => String(v).replace(/\[\[|\]\]/g, "").split("|")[0].trim();
const asArray = (v) => v ? (Array.isArray(v) ? v : [v]) : [];

const priorityPeople = dv.pages('"People"').where(p => p.type === "person" && isPriority(p));
const priorityNames = new Set(priorityPeople.map(p => p.file.name));
const allProjects = dv.pages('"Projects"').where(pr => pr.type === "project");

const rows = [];
for (const proj of allProjects) {
  const leadName = proj.lead ? stripLink(proj.lead) : "";
  const partnerNames = asArray(proj.partners).map(stripLink);

  const hit =
    isPriority(proj) ||
    priorityNames.has(leadName) ||
    partnerNames.some(n => priorityNames.has(n));

  if (!hit) continue;

  const partners = asArray(proj.partners).filter(p => p);
  rows.push([proj.file.link, proj.status || "—", proj.lead || "—", partners.length ? partners : "—"]);
}

if (rows.length > 0) {
  dv.table(["Project", "Status", "Lead", "Partners"], rows);
} else {
  dv.paragraph("*No connected projects yet.*");
}
```

### Adjacent People

Other people sharing an org with a priority person, not yet flagged themselves. Scan this to decide who else to reach out to.

```dataviewjs
const isPriority = (p) => (p.file.tags || []).includes("#currentPriority");
const stripLink = (v) => String(v).replace(/\[\[|\]\]/g, "").split("|")[0].trim();
const asArray = (v) => v ? (Array.isArray(v) ? v : [v]) : [];

const priorityPeople = dv.pages('"People"').where(p => p.type === "person" && isPriority(p));
const allPeople = dv.pages('"People"').where(p => p.type === "person");

const orgToPriority = new Map();
for (const p of priorityPeople) {
  for (const o of asArray(p.orgs)) {
    const name = stripLink(o);
    if (!name) continue;
    if (!orgToPriority.has(name)) orgToPriority.set(name, []);
    orgToPriority.get(name).push(p.file.link);
  }
}

const rows = [];
for (const p of allPeople) {
  if (isPriority(p)) continue;
  const orgs = asArray(p.orgs).map(stripLink).filter(x => x);
  const shared = orgs.filter(o => orgToPriority.has(o));
  if (shared.length === 0) continue;

  const connectedByPath = new Map();
  for (const o of shared) {
    for (const link of orgToPriority.get(o)) {
      const key = link.path || String(link);
      if (!connectedByPath.has(key)) connectedByPath.set(key, link);
    }
  }

  rows.push([p.file.link, shared, Array.from(connectedByPath.values())]);
}

if (rows.length > 0) {
  dv.table(["Person", "Shared Org(s)", "Connected to Priority"], rows);
} else {
  dv.paragraph("*No adjacent people yet. Adjacency is computed via shared orgs.*");
}
```

### Recent Interactions with Priorities

```dataviewjs
const isPriority = (p) => (p.file.tags || []).includes("#currentPriority");
const stripLink = (v) => String(v).replace(/\[\[|\]\]/g, "").split("|")[0].trim();

const priorityPeople = dv.pages('"People"').where(p => p.type === "person" && isPriority(p));
const priorityNames = new Set(priorityPeople.map(p => p.file.name));

const cutoff = new Date();
cutoff.setDate(cutoff.getDate() - 60);

const interactions = dv.pages('"Interactions"')
  .where(i => i.type === "interaction" && i.date)
  .where(i => new Date(i.date.toString()) >= cutoff)
  .where(i => i.participants && i.participants.some(x => priorityNames.has(stripLink(x))))
  .sort(i => i.date, "desc");

if (interactions.length > 0) {
  dv.table(
    ["Date", "Participants", "Context", "About"],
    interactions.map(i => [i.date, i.participants, i.context || "—", i.about || "—"])
  );
} else {
  dv.paragraph("*No interactions with priority people in the last 60 days.*");
}
```

---

