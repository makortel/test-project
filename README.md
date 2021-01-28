# Prototype for managing framework development with GitHub project boards

## Issues

I'm thinking we'd benefit from distinguishing two types of issues: _topic issues_ and _work issues_.
A topic issue would represent e.g. a feature to be developed or a bug to be fixed, and would roughly correspond an issue in [cms-sw/cmssw](https://github.com/cms-sw/cmssw/issues) repo.
A work issue would represent a step towards completing a topic issue, and would roughly correspond to one week of work.
Ideally a work issue would lead to a Pull Request.

A topic issue that could be resolved within a week would serve as a work issue as well.

A topic issue that needs several weeks to complete would be split into multiple work items.
A milestone would be created to track progress on the topic (note that this milestone is an "implementation detail" and should not be confused with "official milestones").

These issues should have no technical discussion (that should go into issues in [cms-sw/cmssw](https://github.com/cms-sw/cmssw).
Instead, they would have mostly links to other issues (especially those in [cms-sw/cmssw](https://github.com/cms-sw/cmssw)), and could have "managerial discussion" (if such is needed).

Issues would have labels like
* Priority: "Low priority", "Medium priority", "High priority" (corresponding to the columns in "Main view", see below)
* Kind: "New feature", "Bug fix"
  * or is this useful?
* Estimated effort (only for topic issues), e.g. something along
  * Low: less than few days
  * Medium: about a week or two
  * High: about a month or more

## Project structure

I'm thinking about a "Main view" board, and then a board for each quarter (somewhat following the [example from DMWM](https://github.com/dmwm/WMCore/projects/11))

### Main view

Main view would deal with topic issues, and would have the following columns
* Pool of ideas: topic issues whose priority has not been decided yet (ideally only new issues)
* Three columns for topics that have been decided to work on some time in the future
  * Low priority: minor performance or productivity improvement (in near future, or becomes "medium" in distant future)
  * Medium priority: e.g. major performance or productivity improvement (in near future, or becomes "high" in distant future)
  * High priority: is or will become a blocker in near future
* Column for each quarter (add new column when starting a quarter)
* Done: work done (mostly a automated sink for closed issues)

### Quarterly project board

Quarterly project board would deal with work issues that are agreed goals for the quarter, and would have the following columns
* To Do: work issues agreed to be addressed on that quarter
* In progress: issues being actively worked on
* In Review: PR of the work issue is in review
  * I'm not fully sure of the usefulness, but sometimes the review can take a lot of time
* Done: work done

## Workflow

### Start working on an issue

1. Developer starts to work on a work issue
   * They assign themself to the issue, and move it from "To Do" column to "In progress" column in the project board of the current quarter
2. Developer completes a work issue that leads to a Pull Request
   * Developer uses one of the [keywords](https://docs.github.com/en/github/managing-your-work-on-github/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword) in the PR description for that PR to close all relevant issues
     * For example, `Resolves #32597. Resolves makortel/test-project#10.`
     * This works also across repositories (tested).
   * Developer moves the work issue from "In progress" column to "In review" column in the quarterly project board.
3. Developer goes to look for next work item in the "To Do" column of the quarterly project board
   * Possibly discussing with L2 too

### A new issue is opened in [cms-sw/cmssw](https://github.com/cms-sw/cmssw)

We would need a corresponding "topic issue" here, that has a link to the original issue.
That could be created manually, but I'm wishing for some automation.
I can think of enhancing cms-bot with something along
* a new `assign framework new-issue` command, or
* add all `assign core` and filter out non-framework issues manually

that would create a new "topic issue" with a link to the original issue, and add it to the "Pool of ideas" column of "Main view" board.
L2 decides if the issue needs an immediate action, or if an action can be discussed in the next "weekly" meeting.
Upon decision, the "topic issue" is moved to the corresponding priority column and assigned the corresponding label.
If the issue needs to be worked on the current quarter, it will be moved to the column of the current quarter in the "Main view" board, and also added to the "To Do" column of the quarterly board.

## Open questions

### How to deal with "quick fixes"?

Going through the "create issue in [cms-sw/cmssw](https://github.com/cms-sw/cmssw)", "create issue here manually or automatically", "add issue card to relevant column in quarterly project board" sounds rather heavy.

### How to deal with design effort?

### How to deal with bug fixes done outside of framework?

In principle it would be nice to to account them.

### How to deal with dependencies between issues?

Add links in both directions? And keep dependent issues in "Pool of ideas" (in "Main view" for topic issues) or "To Do" (in quarter board for work issues)

### What does "one week of work" mean for people with different FTE fractions?

### Is it more important for a "work issue" to correspond about one week of work, or correspond to a Pull Request to [cms-sw/cmssw](https://github.com/cms-sw/cmssw)?
