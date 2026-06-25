---
title: "Development Lifecycle"
weight: 30
description: "How work moves from idea to shipped and maintained — pre-development readiness, development practices, the code review playbook, release and post-release, and the Jira habits that keep it all visible."
lastUpdated: 2026-06-25
goDeeper:
  - group: Tools
    title: "Google Engineering Practices — Code Review"
    url: "https://google.github.io/eng-practices/review/"
    why: "The most complete public guide to what a reviewer should look for and how an author should respond — the backbone of the code-review section here."
  - group: Books
    title: "Accelerate — Forsgren, Humble & Kim"
    url: "https://itrevolution.com/product/accelerate/"
    why: "The research linking small batches, fast review, and continuous delivery to both performance and team well-being."
  - group: Books
    title: "Making Work Visible — Dominica DeGrandis"
    url: "https://itrevolution.com/product/making-work-visible/"
    why: "Why keeping the board honest matters — surfacing hidden work and WIP is the whole point of the Jira habits below."
---

A development lifecycle is just the path a piece of work takes from idea to shipped and maintained. You don't need a heavyweight process to have one — you need a shared, lightweight understanding of what good looks like at each stage so work doesn't stall, ship half-baked, or disappear into someone's head. Here's how I think about each stage.

## Pre-development

In an ideal world, before anyone starts work on an epic or ticket, a few things are true:

- The epic or ticket meets the team's **Definition of Ready (DoR)** — one of the [team norms]({{< relref "/handbook/team-health-operations/team-ceremonies.md" >}}) worth agreeing on explicitly up front.
- Scope and acceptance criteria (AC) are clear — and if they aren't, you clarify them *before* writing code, not after.
- Work that's too big is broken down into appropriately sized tickets.

The cheapest bug to fix is the one you catch as a misunderstanding in refinement. Pre-development is where you spend a little time to save a lot.

## Development

The development stage is where the work happens, but a few practices reliably separate smooth delivery from painful delivery:

- **Feature flags** — so you can merge and deploy continuously and decouple release from rollout.
- **Reasonable PR sizes** — small PRs get reviewed faster, reviewed better, and broken less. This is the single biggest lever on review speed.
- **[Accessibility]({{< relref "/handbook/engineering-foundations/accessibility-a11y.md" >}}) considerations** — built in from the start, not bolted on before launch.
- **Telemetry** — instrument as you build, so you can actually answer "is this working in production?"
- **Automated testing** — new code ships with tests, and the suite stays green.

## Code review

Code review is a required and critical step in our process. It improves code quality, spreads knowledge across the team, and keeps work moving. Delayed or incomplete reviews are one of the most common — and most fixable — sources of team friction. Prioritizing timely, thorough reviews keeps work flowing while building a culture of collaboration and shared accountability.

{{< protip >}}
These are guidance and reinforced best practices, not a prescription. Adapt them to your style as long as they still serve the team's goals: high-quality code, real collaboration, and minimal delays. If your current approach already gets there, that's perfectly fine.
{{< /protip >}}

### Make review a routine

- Block dedicated time on your calendar each day for reviewing pull requests, and treat it as a real part of your job. Even a 30-minute daily block makes a big difference.
- A simple habit that works: start the day by clearing pending PRs before diving into your own work.

### Set notifications and prioritize

- Tune your GitHub notifications to flag PRs where you're tagged as a reviewer.
- Use the [GitHub Slack integration](https://slack.github.com/) to get notified about new review requests and reminders about outstanding ones.
- GitHub's [review-requested view](https://github.com/pulls/review-requested) shows every PR awaiting your review.
- Prioritize by urgency — PRs blocking other people come first.

### Feedback cadence

- If a PR is too big to review in one sitting, leave comments incrementally to keep momentum and show you're engaged.
- Aim to respond to review requests within 24 hours to keep the pipeline flowing.
- If you can't get to a review within 24 hours, tell the author so they can find someone else if needed.

### Why we do code reviews

When we review, we look at each other's work and give feedback — suggesting alternatives, flagging potential problems, and making sure the code is readable and maintainable. Well-written PRs also improve our Git history, giving future readers the context behind a change. And of course, reviews catch bugs and vulnerabilities before they cause trouble, saving everyone time and headaches down the line.

### So you're writing a pull request

Before soliciting reviews:

- Follow the PR template if there is one. Link the ticket. Write the PR description *you* would want to read — explain technical decisions so reviewers understand your thought process and where extra context helps.
- Tag reviewers as soon as the PR is made, but leave it in **Draft** until you're ready for a real review. If you want early thoughts before you're code-complete, say so explicitly.
- If you tag reviewers outside your team, include links (in the ticket or the PR) to supporting research, product briefs, etc., so they can get context fast.
- Add screenshots, example API calls, and the like when they help illustrate the problem or solution.
- Add detailed testing instructions, including test accounts when relevant, so reviewers can help with testing and QA.
- Review your own code first, and leave helpful comments for reviewers where useful.
- Make sure new code has tests and the suite is passing.
- Note: many repos auto-tag teams. Use the **Draft** state while wrapping up so a team isn't pulled into formal review before things are ready.

Responding to feedback:

- Reviews aren't personal — don't take them that way, and don't make them that way. Treat them as an attempt to help you and the codebase.
- Respond to every comment, even if you disagree (an emoji can count).
- Explain your reasoning when you're *not* making a suggested change. If the feedback is valid but better as a follow-up, file a ticket, reference it in the code and the comment, and tell the reviewer you'll address it later.
- Request a re-review after addressing feedback so the reviewer gets pinged.
- If a reviewer took the time to leave comments, treat an approval as necessary unless they say their comments are non-blocking.
- Re-request review after significant changes, even if you already had an approval.
- When a PR is approved and ready, merge it promptly — that keeps the codebase current, unblocks others, and reduces merge conflicts.

### So you were tagged on a pull request

- If you can't review within one business day, tell the author. They may be happy to wait, or may want to find another reviewer.
- If you can review, do it as soon as you can without interrupting a focused task. Reviewing is part of the job, so budget time for it daily.
- If a team tag pulled in several engineers, sort out among yourselves who takes it.
- A handy link: [all open PRs you're tagged on](https://github.com/pulls/review-requested).

### So you're reviewing a pull request

- **Read the description.** The author explained their context for a reason — understand it before reading the code. If the context is thin, ask.
- Review every line you've been asked to review (yes, the tests too). If you're only covering part of the PR because of your expertise area, say so, so the author gets full coverage.
- Use an ephemeral environment if you have one, or pull the changes locally, to help test and QA that the code does what it claims.
- Review for **functionality, design, complexity, testing, and naming**.
- Be kind and constructive. Avoid writing the solution for them when you can; for minor syntactic changes, use GitHub's "suggested change" feature so it's trivial to apply.
- Prefix comments with **[Nit]**, **[Blocking]**, **[Non-Blocking]**, **[Design]**, etc. so the author knows the priority.
- If you see something great, say so too.
- Read other reviewers' comments and respond where it makes sense.
- Advocate for changes you believe improve the code, but be willing to compromise.
- If you've found a blocking issue, use **Request changes**. That's standard, recommended practice — not a personal slight to author or reviewer.

### What happens when we disagree

- Technical facts and data overrule opinions and personal preferences.
- Seek consensus, but allow yourself to **disagree and commit** to keep things moving when you need to.
- Stay polite and acknowledge that you hear each other, even when you disagree.
- When consensus is hard over comments, move to Slack or a quick call — then record the outcome back on the PR for future readers.

## Release

A high-level shape of the release process:

- **Bug hunt as a team** before launch — exercise every type of customer and user across all the relevant workflows, not just the happy path.
- **Deployment steps** — release the code, account for any feature-flag work, do an internal release, validate in production, and coordinate with Product on timing and messaging.

## Post-release

Shipping isn't done; it's a state change. After release:

- **Monitor** — watch your telemetry and error rates, ideally through [monitoring and alerting]({{< relref "/handbook/operational-excellence/monitoring-alerting.md" >}}) you trust. The feature flag you added in development is what lets you roll back calmly instead of turning a bad deploy into an [incident]({{< relref "/handbook/operational-excellence/incident-response.md" >}}).
- **Communicate** — keep stakeholders (Product, Data, and others) in the loop on rollout, results, and anything you're watching.

## Jira workflow tips

Jira is the primary tool for tracking work, keeping it visible, and aligning the team. Good Jira hygiene keeps the board accurate, surfaces blockers early, and makes collaboration seamless. A daily cadence keeps tickets reflecting the reality of the work — which is what makes the board trustworthy.

{{< protip >}}
Like the code-review tips, these are guidance, not rules. Adapt them to your workflow as long as they still serve the goal: an up-to-date board that reflects real status and gives the team visibility. If your current approach already does that, keep it.
{{< /protip >}}

### Start your day with Jira

- Review your assigned tickets first thing — move them to the correct status ("In Progress," "Blocked," etc.) and check for updates, comments, or new assignments.
- Plan the day by priority, and finish moving in-flight work to its next step or to merged *before* starting something new.

### Update tickets as you work

- Treat ticket updates as part of finishing the task, not paperwork you do later.
- Document intermediate steps — linked PRs, dependencies — when they aren't already captured.
- Transition tickets as soon as the state changes (e.g., "In Progress" → "Code Review").

### End-of-day check-in

- Spend 5–10 minutes reviewing your board before you log off: make sure tickets reflect your actual progress, and tag stakeholders for any follow-ups.

### Use notifications to stay current

- Set up Jira notifications or dashboards to highlight updates on the tickets you own, and check them as part of your routine (after lunch, between meetings).

### Tools

- The **Your Work** page (`../jira/your-work`) is a great single view of everything in flight plus recent activity relevant to you.
- Keep your team's **sprint dashboard** handy for an overall view of what's happening in the current sprint.
