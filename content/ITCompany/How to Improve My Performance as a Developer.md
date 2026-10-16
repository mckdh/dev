---
title: "How to Improve My Day-to-Day Performance as a Developer"
parent: "[[index|Ryan's Dev & Game Lab]]"
---

# Small Habits That Turn Good Coders into Great Teammates

> [!NOTE] A Personal Reminder & Living Log
> None of these habits come naturally to me. When deadlines loom, it is easy to get lazy, skip documentation, or rush onto the next ticket just like anyone else. I wrote this note as a personal reminder and a living log of habits I am actively trying to build in my day-to-day work.

I have been writing software for a long time, and for years I assumed the "best" developers were just built different. You know the archetype: the coding prodigy who dreams in algorithms, or the machine who pushes pull requests at 3 a.m.

I am neither of those.

At some point, I stopped worrying about becoming an overnight 10x developer and asked a simpler question: **What actually makes someone genuinely great to work with?**

Writing good code is baseline table stakes. But once you meet that bar, your real impact comes from how smoothly work moves through you, how few surprises you create, and how easily teammates can rely on you.

Here are three habits I am actively trying to practice, with more to come as I keep learning.

---

## 1. Practice Writing-Oriented Work: Document as You Go

Too much valuable engineering context lives only inside people's heads. You spend two days digging through an obscure library bug, find the fix, merge the PR, and move on. Two weeks later, that entire discovery is forgotten, and another teammate hits the exact same brick wall.

I try to practice **writing-oriented work**. I try to treat short, clear writing as an active part of solving a problem, not an afterthought.

Whenever I run into something tricky, I try to leave a quick trail:
- **Discovered a library quirk or gotcha?** Drop a short note on the team wiki before closing the ticket so nobody else wastes hours on it.
- **Made a subtle architectural choice?** Write down *why* we chose approach A over B so future maintainers do not have to guess.
- **Answered a repetitive QA question on Slack?** Turn it into a quick FAQ entry and paste the link next time.

This does not mean writing heavy, formal manuals. It just means making your daily hands-on experience visible and reusable. When you work in writing, the whole team moves faster without having to tap you on the shoulder every twenty minutes.

![[attachments/meme-two-buttons-slack-faq.png]]

---

## 2. Catch Small Details Before They Turn into Fire Drills

During sprint planning and standups, people constantly drop valuable hints: *"Hey, this API might fail silently if the payload is empty,"* or *"We really should add an index to this table."*

Most of the time, everyone nods, moves to the next topic, and completely forgets. Three months later, that exact edge case takes down a production job on a Saturday morning.

Whenever an issue or loose end comes up, I try to jot down a quick line in my scratchpad. No rigid templates, no formal fields:

`Empty payload returns 200, check validation before release`

That is it. It takes five seconds.

You do not need a mini Jira ticket or a polished format. Just capture the thought in your own words so it does not vanish into thin air. I have to remind myself of this constantly: trust is not built on big promises. It is built when your team notices that things you touch do not slip through the cracks.

---

## 3. Steal Ten Minutes a Week for Your Own Tools

Everyone complains about tech debt and clunky local setups, but nobody feels like they have time to fix them. You are always rushing to hit the next release deadline.

The mistake is waiting for a magical "free week" where nobody assigns you tickets. That week never arrives.

Instead, I try to carve out ten minutes on Friday afternoon. Just ten minutes.
- I might clean up a messy shell alias.
- I might update an outdated setup doc that tripped me up on Tuesday.
- I might organize my working notes or archive stale branches.

I do not manage to do this every single week, but whenever I do, it pays off. Ten minutes feels almost too small to bother with. But compounded over six months, those tiny tune-ups turn a frustrating dev environment into a smooth, headache-free workspace.

---

## Closing: An Ongoing Journey

Being a senior developer is not about knowing every obscure compiler flag or being the loudest voice in the room.

It comes down to simple consistency:
1. **Try to work in writing** so team knowledge compounds instead of evaporating.
2. **Try to follow through on small details** before they turn into emergencies.
3. **Try to invest ten minutes a week** so your tools work for you, not against you.

None of this is about being perfect. These are habits I still struggle with and actively try to practice every day. I will continue to test, document, and add new reflections here as I keep building.