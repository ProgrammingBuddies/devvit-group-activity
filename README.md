# 🚀 r/ProgrammingBuddies – Devvit Group Activity & Matchmaking System

This repo contains the **open-source Devvit app** for r/ProgrammingBuddies to automate buddy pairing and group activity tracking **entirely inside Reddit** — no off-platform hosting or moderation.

---

## 📌 Project Overview

**The Goal:**  
We get *lots* of “looking for buddy” posts every week. This project will:

- Add a **Join** button to relevant posts so people can instantly join the OP’s group/buddy pair.
- Automatically create a **Reddit group chat** for the OP + joiners.
- Send **weekly “Did you meet?”** pings to track activity.
- Give **badges** (🔥 Quality, 🏆 Sustained Quality, 🌱 Rising) to active groups.
- Show a **leaderboard** in a subreddit widget and weekly sticky.

---

## 📚 Devvit Documentation

If you’re new to Reddit’s Devvit API, start here:  
- [Devvit Overview](https://developers.reddit.com/docs/devvit/overview)  
- [KV Store API](https://developers.reddit.com/docs/devvit/kvstore)  
- [Scheduler API](https://developers.reddit.com/docs/devvit/scheduler)  
- [Reddit API Access](https://developers.reddit.com/docs/devvit/redditapi)  
- [UI & Post Actions](https://developers.reddit.com/docs/devvit/ui)

---

## 🗂 Project Structure & Roles

This is a **self-managed community project**. Mods will oversee direction, but contributors own the work.  
You can take on **one or more** roles:

- **PM / Requirements** – Refine features, maintain the backlog, make sure nothing is blocked.  
- **Backend Dev** – Implement KV store logic, scheduler jobs, and API calls.  
- **Frontend Dev** – Build Devvit UI (post actions, forms, widget).  
- **Reviewer / QA** – Review pull requests, test app features.  
- **Documentation** – Keep README, issues, and Devvit setup guides up to date.  

---

## ✅ Requirements (MVP)

1. **Post Action: Join Button**
   - Auto-attach to new posts with certain flairs/keywords.
   - Add joiner to that post’s private queue in KV store.
   - OP can cap joiners or mark group full.

2. **Group Connection**
   - PM both OP and joiner when someone joins.
   - Optional Reddit group chat creation.
   - Stop adding to chat when full.

3. **Weekly Check-In**
   - Scheduler sends “Did you meet?” to all group members.
   - One-click **Yes** = +1 streak.
   - No click = streak reset/drop.

4. **Leaderboard & Badges**
   - Track streaks and activity score.
   - Apply badges based on thresholds.
   - Update leaderboard in widget + weekly sticky comment.

5. **Mod Tools**
   - Remove group.
   - Reset streaks.
   - Override badge status.
   - Change global settings (timeouts, max size).

---

## 📋 Current TODOs

We track all tasks as GitHub Issues. **If you see something missing, open a new issue**.  
Suggested starter issues:

- [ ] Outline MVP feature list (✅ this README is a start)
- [ ] Write user flows for joining a group and weekly check-ins
- [ ] Create KV data schema for groups, joiners, streaks
- [ ] Build Post Action “Join” prototype
- [ ] Implement PM + optional group chat creation
- [ ] Add Scheduler job for check-ins
- [ ] Leaderboard calculation logic
- [ ] Widget + sticky updater
- [ ] Mod menu actions
- [ ] Badge assignment system
- [ ] Testing + QA plan

---

## 🔄 How to Contribute

1. **Pick a role** – Decide if you want to PM, code backend, code frontend, review, or document.
2. **Find or create an issue** – Check [Issues](../../issues) for open tasks, or make a new one.
3. **Discuss before coding** – Comment on the issue with your plan so we avoid overlap.
4. **Work in a branch** – Name it like `feature/join-button` or `docs/setup-guide`.
5. **Open a Pull Request** – Link it to the issue, request at least one reviewer.
6. **Review & Merge** – All merges require approval from someone else.

---

## 🧭 Project Principles

- **Reddit-native only** – No external sites, all data in Devvit KV store.
- **Open & transparent** – All code, issues, and decisions in this repo.
- **Mod-controlled longevity** – Mod team owns the app + repo.
- **Small, iterative steps** – Ship simple features first, refine later.

---

## 🙋 Join Us

If you’re interested:
- Comment in [r/ProgrammingBuddies](https://www.reddit.com/r/ProgrammingBuddies/)
- Or open an issue in this repo with `[INTRO]` in the title and tell us what role you want.

Let’s make r/ProgrammingBuddies the easiest place to find — and keep — an active programming buddy. 💻🤝🔥
