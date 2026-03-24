---
type: interaction
date: <% tp.date.now("YYYY-MM-DD") %>
participants:
  - "[[<% tp.system.suggester(["August", "Savannah"], ["August", "Savannah"], false, "Select team member participant") %>]]"
  - "[[]]"
context: <% tp.system.suggester(["call", "email", "text", "event", "meeting"], ["call", "email", "text", "event", "meeting"], false, "Select interaction type") %>
about:
  - "[[]]"
logged-by: "[[<% tp.system.suggester(["August", "Savannah"], ["August", "Savannah"], false, "Who is logging this?") %>]]"
tags:
  - interaction
---

## Summary
What happened:


## Key Takeaways
What we learned or what shifted:


## Action Items
- [ ]

