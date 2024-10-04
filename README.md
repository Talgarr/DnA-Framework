# DnA-Framework

DnA Framework is a framework to build defense and attack (DnA) CTF.

## Objectif

This is the basic objectives and features I would like to add.

1. Purple teaming: Every one has a vulnerable app. Every n minutes, the modification made by the teams is pushed. Everyone can attack everyone else during this time. Team has a flag which reset every iterations.
2. No social justice: Attackers try to exploit while defenders try to protect.
3. King of the hill: One vulnerable app, if you succeed at exploiting it, you need to retain your presence, by patching vulnerabilities.

## Health

How to make something patchable while preventing any functionnality of the thing being taken down? I feel like it is the main problem with this kind of CTF.

- TryHackMe King of the hill set the rules and monitor the service. If people doesn't respect the rules, they are ban.
- A healthcheck bot can be made to verify the defined functionnalities are still available and periodically look it up. Obviously, it shouldn't be easily detectable so a team could block all traffic but the bot one. And a change could make the bot not work even if the service is still perfectly up, like a timeout change.
- The application files could be mounted as Read-Only volume. I don't think this actually fix the problem. If we want defense, we need to be able to add security around the app and those still can prevent the functionnality.
- Make the root user unhackable and the service runned by him. I think those have the same problem as the Read-Only volume.
