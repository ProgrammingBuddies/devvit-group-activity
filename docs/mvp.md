# MVP and User Flows

This document is an initial planning draft for the first version of the r/ProgrammingBuddies Devvit group activity app.

The goal of this draft is to define a small, Reddit-native MVP that contributors can discuss before implementation begins. Nothing here is final; this should be refined with feedback from mods, maintainers, and community members.

## Goals

The MVP should help r/ProgrammingBuddies users find and maintain active buddy/group connections without leaving Reddit.

For the first version, the app should focus on:

- Letting users join a buddy/group from a relevant subreddit post.
- Tracking basic group membership in Devvit KV storage.
- Supporting a simple weekly check-in flow to measure whether groups are active.
- Giving mods enough visibility/control to manage groups safely.
- Keeping the first implementation small enough for community contributors to ship.

## MVP Scope

The proposed MVP includes the following features.

### 1. Join Group Action

Relevant posts should have a Reddit-native way for users to express interest in joining the OP's buddy/group.

At minimum, this should allow a user to:

- Click a `Join` action on an eligible post.
- Be added to a group/member record for that post.
- Receive a Reddit-native confirmation that they joined or requested to join.
- Avoid joining if the group is already full, closed, or otherwise unavailable.

### 2. Basic Group Tracking

The app should track enough data to know which posts have active groups and who is in them.

At minimum, the app should track:

- The source post ID.
- The OP/user who started the group.
- Users who joined the group.
- Whether the group is open, full, closed, or removed.
- When the group was created.
- When the group was last active or checked in.

### 3. Weekly Check-In

The app should periodically ask group members whether the group met or made progress.

At minimum, the check-in flow should:

- Run on a weekly schedule.
- Ask group members whether they met or collaborated that week.
- Record simple responses such as `yes`, `no`, or no response.
- Update the group's activity status based on responses.

### 4. Basic Mod Controls

Mods should have enough control to prevent stale, abusive, or incorrect group records.

At minimum, mods should be able to:

- View or identify active tracked groups.
- Remove/disable a tracked group.
- Mark a group closed or inactive.
- Reset group activity if needed.

The exact Devvit UI for these controls can be decided later.

## Out of Scope for v1

To keep the MVP small, the following should likely be deferred until after the initial version works:

- Public leaderboard.
- Badges such as Quality, Sustained Quality, or Rising.
- Weekly sticky leaderboard posts.
- Advanced scoring/ranking.
- Advanced matching between users.
- Complex user profiles.
- External websites, dashboards, or APIs.
- Large configurable settings panels.

Automatic Reddit group chat creation may also be out of scope for the first version unless Devvit supports it cleanly and the mods confirm that it is required for MVP.

## User Flow: Join Group

This is the proposed first-pass flow for a user joining a group from a subreddit post.

1. A user creates a relevant r/ProgrammingBuddies post, such as a buddy/group request.
2. If the post is eligible, the app exposes a Reddit-native `Join` action on the post.
3. Another user clicks `Join`.
4. The app checks whether:
   - The post is eligible.
   - The user is not the OP.
   - The user has not already joined this group.
   - The group is still open.
   - The group has not reached its member limit.
5. If the join is allowed, the app stores the joiner in the group's KV record.
6. The app confirms the action to the joiner.
7. The app notifies the OP that someone joined or requested to join.
8. If the group reaches its member limit, the app marks the group as full or closed.

### Possible Join Variants

There are two possible versions of the join flow:

#### Option A: Direct Join

A user clicks `Join` and is immediately added to the group.

Pros:

- Simple.
- Lower friction.
- Easier to build for MVP.

Cons:

- OP has less control.
- May require stronger mod tools if spam occurs.

#### Option B: Request to Join

A user clicks `Join`, and the OP approves or rejects the request.

Pros:

- OP has more control.
- Better for groups that want specific requirements.

Cons:

- More complex.
- Requires additional UI and state tracking.

For MVP, `Option A: Direct Join` may be the best starting point unless mods prefer an approval step.

## User Flow: Weekly Check-In

This is the proposed first-pass flow for weekly activity tracking.

1. A scheduler job runs once per week.
2. The app finds groups that are open or recently active.
3. The app sends a Reddit-native check-in prompt to group members.
4. The prompt asks a simple question, such as:

   > Did your buddy/group meet or make progress this week?

5. Each member can answer with a simple response such as:
   - Yes
   - No
6. The app records responses for that weekly check-in period.
7. The app updates the group's activity state.
8. If a group repeatedly receives no positive responses, it can be marked inactive or flagged for mod review.

## Basic Data to Track

The exact KV schema should be handled in a separate design/implementation issue, but the MVP likely needs records for groups and check-ins.

### Group Record

A group record may need:

- Group ID.
- Source post ID.
- Source post title.
- OP username/user ID.
- Member usernames/user IDs.
- Pending users, if approval flow is used.
- Group status: `open`, `full`, `closed`, `inactive`, `removed`.
- Max group size.
- Created timestamp.
- Last updated timestamp.
- Last check-in timestamp.

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
- Whether direct join or approval-based join is used.

For MVP, these settings could start as constants in code and move to mod-configurable settings later.

## Open Questions

These questions should be answered by mods/maintainers before or during implementation planning.

1. Which post flairs or keywords should make a post eligible for the `Join` action?
2. Should joining be immediate, or should the OP approve join requests?
3. What should the default group size limit be?
4. Should OPs be able to manually mark a group as full or closed?
5. Should users be allowed to join multiple groups at the same time?
6. What should happen if a user joins and then becomes inactive?
7. What is the preferred Reddit-native notification method: private message, comment reply, Devvit UI, or another option?
8. Does Devvit currently support creating Reddit group chats in a way that fits this project?
9. Is automatic group chat creation required for MVP, or can MVP start with notifications only?
10. How many positive check-in responses are required for a group to count as active?
11. How many missed check-ins should mark a group inactive?
12. What basic mod controls are required before the first version can be tested on the subreddit?
13. Should deleted/removed posts automatically disable their groups?
14. What anti-spam or abuse protections are needed for launch?
15. Should usernames or user IDs be stored in KV, and are there any privacy/moderation concerns to account for?

## Suggested Implementation Issues After MVP Agreement

Once the MVP direction is agreed on, the work could be split into smaller issues:

1. Define KV schema for groups, members, and check-ins.
2. Create Devvit app skeleton.
3. Add eligible post detection for selected flairs/keywords.
4. Build `Join` post action prototype.
5. Store and update group membership in KV.
6. Add OP/joiner notification flow.
7. Add weekly scheduler job.
8. Add check-in response handling.
9. Add basic mod tools for removing or closing a group.
10. Write testing/QA notes for MVP behavior.

## Notes

This draft intentionally avoids implementation details where possible. The main purpose is to agree on product direction and user flows before contributors start building the Devvit app.
