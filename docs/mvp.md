# MVP and User Flows

This document defines the initial MVP for the r/ProgrammingBuddies Devvit group activity app.

The MVP should stay small, Reddit-native, and focused on the core loop: users join a buddy/group post, the app tracks membership, members receive check-ins, and mods can manage group state safely.

## MVP Decisions

The first version should use the following product and technical direction:

- Use **Direct Join** for membership. A user who clicks `Join` is added to the group if the group is open and not full.
- Store group and check-in state in **Devvit Redis**.
- Use **Reddit private messages** to confirm successful joins, notify OPs when someone joins, and send weekly check-ins.
- Use the **original post/comment thread** as the group coordination hub.
- Use **one recurring Devvit Scheduler job** to process weekly check-ins for active groups.
- Include **basic mod controls** through Devvit menu actions/forms.
- Keep approval-based joining and automatic Reddit group chat creation as future considerations.
- Treat leaderboards, badges, and weekly sticky recaps as later features after the Join + tracking + check-in loop is working.

## Goals

The MVP should help r/ProgrammingBuddies users find and maintain active buddy/group connections without leaving Reddit.

For the first version, the app should focus on:

- Letting users join a buddy/group from a relevant subreddit post.
- Tracking basic group membership in Devvit Redis.
- Supporting a simple weekly check-in flow to measure whether groups are active.
- Giving mods enough visibility/control to manage groups safely.
- Keeping the first implementation small enough for community contributors to ship.

## MVP Scope

The MVP includes the following features.

### 1. Join Group Action

Relevant posts should have a Reddit-native `Join` action that lets users join the OP's buddy/group.

At minimum, this should allow a user to:

- Click a `Join` action on an eligible post.
- Be added directly to a group/member record for that post.
- Receive a Reddit PM confirming they joined.
- Avoid joining if they are the OP.
- Avoid joining if they have already joined the group.
- Avoid joining if the group is full, closed, removed, or otherwise unavailable.

When someone clicks `Join`:

- If no group record exists yet, the app creates one for that post.
- The OP becomes the group owner.
- The joiner is added to the group if the group is open and not full.
- Group/member state is stored in Devvit Redis.
- The joiner receives a Reddit PM confirming they joined.
- The OP receives a Reddit PM notifying them that someone joined.
- The original post can receive or update a status comment showing that group tracking is active.

### 2. Group Coordination Thread

For MVP, the original Reddit post/comment thread is the group coordination hub.

The app can add or update a status comment such as:

> Group activity tracking is enabled for this post. Current members: 3/5. Members can coordinate in this thread or by Reddit PM.

Members can use the post thread and Reddit PMs to coordinate. Automatic Reddit group chat creation can be explored later if Devvit/Reddit provides documented support for it.

### 3. Basic Group Tracking

The app should track enough data to know which posts have active groups and who is in them.

At minimum, the app should track in Devvit Redis:

- Source post ID.
- Group owner / OP.
- Member usernames or user IDs.
- Max group size.
- Group status: `open`, `full`, `closed`, or `removed`.
- Created timestamp.
- Last check-in timestamp.
- Activity streak.
- Latest check-in result.

The exact Redis schema should be handled in a separate design/implementation issue.

### 4. Weekly Check-In

The app should periodically ask group members whether the group met or made progress.

For MVP, one recurring Devvit Scheduler job should process active groups centrally. The app should not create one scheduler job per group.

At minimum, the check-in flow should:

- Run on a weekly schedule.
- Find active groups that are due for check-in.
- Send Reddit PM check-ins to members.
- Ask group members whether they met or collaborated that week.
- Record simple responses such as `yes`, `no`, or no response.
- Update streak/status fields in Devvit Redis.
- Mark groups as active, at risk, or inactive based on responses.

### 5. Activity Status Model

The MVP should use a simple group activity status model:

- `active`: recent positive check-in.
- `at_risk`: missed one check-in.
- `inactive`: missed multiple check-ins.
- `closed`: closed by OP or mod.
- `removed`: removed by mod.

This gives users and mods useful information without requiring leaderboard or badge logic in the first version.

### 6. Basic Mod Controls

Mods should have enough control to prevent stale, abusive, or incorrect group records.

At minimum, mods should be able to:

- View or identify active tracked groups.
- Close a group.
- Remove a group from tracking.
- Reset a group's streak/status.
- Override a group status.
- Disable tracking for a post.

These controls can be implemented through Devvit menu actions/forms. The exact Devvit UI for these controls can be decided during implementation.

## Future Features

The following items are intentionally outside the MVP and should wait until the Join + tracking + check-in loop is working.

### Leaderboard and Badges

- Public leaderboard.
- Badges such as Quality, Sustained Quality, or Rising.
- Weekly sticky leaderboard or recap posts.
- Activity scoring/ranking for leaderboard and badge thresholds.

### Future Considerations

- Approval-based joining, if spam, abuse, or group-quality concerns require it.
- Automatic Reddit group chat creation, if Devvit/Reddit provides documented support for it.

## User Flow: Join Group

This is the MVP flow for a user joining a group from a subreddit post.

1. A user creates a relevant r/ProgrammingBuddies post, such as a buddy/group request.
2. If the post is eligible, the app exposes a Reddit-native `Join` action on the post.
3. Another user clicks `Join`.
4. The app checks whether:
   - The post is eligible.
   - The user is not the OP.
   - The user has not already joined this group.
   - The group is open.
   - The group has not reached its member limit.
   - The group has not been closed or removed.
5. If no group record exists yet, the app creates one in Devvit Redis.
6. The app stores the OP as the group owner.
7. If the join is allowed, the app stores the joiner as a group member in Devvit Redis.
8. The app sends a Reddit PM to the joiner confirming they joined.
9. The app sends a Reddit PM to the OP notifying them that someone joined.
10. The app creates or updates a status comment on the original post showing that group tracking is active.
11. If the group reaches its member limit, the app marks the group as full.

## User Flow: Group Coordination Thread

This is the MVP flow for connecting members after they join.

1. The app posts or updates a status comment on the original post.
2. The comment explains that group activity tracking is enabled.
3. The comment shows basic group status, such as current member count and max size.
4. Members coordinate in the original thread and/or by Reddit PM.
5. If membership or group status changes, the app can update the status comment.

Example status comment:

> Group activity tracking is enabled for this post. Current members: 3/5. Members can coordinate in this thread or by Reddit PM.

## User Flow: Weekly Check-In

This is the MVP flow for weekly activity tracking.

1. A single recurring Devvit Scheduler job runs once per week.
2. The app finds active groups that are due for check-in.
3. The app sends a Reddit PM check-in prompt to group members.
4. The prompt asks a simple question, such as:

   > Did your buddy/group meet or make progress this week?

5. Each member can answer with a simple response such as:
   - Yes
   - No
6. The app records responses for that weekly check-in period.
7. The app updates the group's latest check-in result in Devvit Redis.
8. The app updates the group's activity streak.
9. The app updates the group's activity status:
   - `active` for a recent positive check-in,
   - `at_risk` after one missed check-in,
   - `inactive` after multiple missed check-ins.
10. If a group repeatedly receives no positive responses, it can be marked inactive or flagged for mod review.

## User Flow: Mod Intervention

This is the MVP flow for a moderator managing tracked groups.

1. A mod opens the relevant Devvit menu action/form.
2. The mod selects or identifies a group by post.
3. The mod chooses an action, such as:
   - close group,
   - remove group from tracking,
   - reset streak/status,
   - override group status,
   - disable tracking for a post.
4. The app validates moderator permissions.
5. The app updates the group record in Devvit Redis.
6. Future join actions and scheduler jobs respect the mod change.

## Basic Data to Track

The exact Redis schema should be handled in a separate design/implementation issue, but the MVP likely needs records for groups and check-ins.

### Group Record

A group record may need:

- Group ID.
- Source post ID.
- Source post title.
- OP username/user ID.
- Member usernames/user IDs.
- Group status: `open`, `full`, `closed`, `removed`.
- Activity status: `active`, `at_risk`, or `inactive`.
- Max group size.
- Created timestamp.
- Last updated timestamp.
- Last check-in timestamp.
- Activity streak.
- Latest check-in result.
- Status comment ID, if the app posts/updates a group status comment.

### Check-In Record

A check-in record may need:

- Check-in ID.
- Group ID.
- Week/start date.
- Users notified.
- User responses.
- Number of positive responses.
- Number of negative responses.
- Number of non-responses.
- Resulting activity status.

### Settings Record

A settings record may eventually need:

- Eligible flairs or keywords.
- Default max group size.
- Check-in frequency.
- Inactivity threshold.

For MVP, these settings could start as constants in code and move to mod-configurable settings later.

## Open Questions

These questions can be answered by mods/maintainers before or during implementation planning.

1. Which post flairs or keywords should make a post eligible for the `Join` action?
2. What should the default group size limit be?
3. Should OPs be able to manually mark a group as full or closed?
4. Should users be allowed to join multiple groups at the same time?
5. What should happen if a user joins and then becomes inactive?
6. How many positive check-in responses are required for a group to count as active?
7. How many missed check-ins should mark a group inactive?
8. Should deleted/removed posts automatically disable their groups?
9. What anti-spam or abuse protections are needed for launch?
10. Should usernames or user IDs be stored in Redis, and are there any privacy/moderation concerns to account for?

## Suggested Implementation Issues After MVP Agreement

Once the MVP direction is agreed on, the work can be split into focused issues:

1. Devvit app skeleton.
2. Redis group schema.
3. Join action prototype.
4. PM notifications.
5. Group status comment.
6. Weekly scheduler check-in.
7. Check-in response handling.
8. Mod controls.
9. Simple active-groups widget.
10. Future leaderboard/badges.

## Resources

- [Devvit Documentation](https://developers.reddit.com/docs/)
- [Devvit Discord](https://discord.gg/xYEqrHGs7E)

## Notes

This draft intentionally avoids detailed implementation decisions where possible. The main purpose is to agree on product direction and user flows before contributors start building the Devvit app.
