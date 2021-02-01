# Prototype for managing framework development with GitHub project boards

## Goal

This exercise has two goals
1. Have a global view of all planned framework developments (features, bug fixes, user requests etc) with some prioritization
2. Have a quarterly monitoring and accounting of completed and pending tasks

I'd like to minimize management overheads of these, and therefore I'd like to avoid e.g. spreadsheets for that.

## Issues

I'm thinking we'd benefit from distinguishing two types of issues: _feature issues_ and _task issues_.
A feature issue would represent e.g. a feature to be developed or a bug to be fixed, and would roughly correspond an issue in [cms-sw/cmssw](https://github.com/cms-sw/cmssw/issues) repo.
A task issue would represent a step towards completing a feature issue.
The result of a taks issue would be e.g. a Pull Request, creation or an update of design, update of documentation.

If a feature issue can be naturally split into multiple steps, those steps could be sketched in a task list in the issue description. Then the number of tasks would be visible in issue list and in the project boards, serving one kind of a measure for the effort required to develop that feature. Later, when development for the feature starts, a task issue would be created for each task (in the "Quarterly project board). At this stage milestone would be created to automatically track progress on the feature (note that this milestone is an "implementation detail" and should not be confused with "official milestones").

If feature issue that does not naturally split into more than one task, the feature issue would serve as a task issue as well. 

These issues should have no technical discussion (that should go into issues in [cms-sw/cmssw](https://github.com/cms-sw/cmssw).
Instead, they would have mostly links to other issues (especially those in [cms-sw/cmssw](https://github.com/cms-sw/cmssw)), and could have "managerial discussion" (if such is needed).

Issues would have labels like
* Priority: "Low priority", "Medium priority", "High priority" (corresponding to the columns in "Main view", see below)
* Kind: "New feature", "Bug fix", "User request"
  * Probably useful only for feature issues, if even for those?

## Project structure (see [example](https://github.com/makortel/test-project/projects))

I'm thinking about a "Main view" board, and then a board for each quarter (somewhat following the [example from DMWM](https://github.com/dmwm/WMCore/projects/11))

### Main view (see [example](https://github.com/makortel/test-project/projects/1))

Main view would deal with feature issues, and would have the following columns
* Pool of ideas: feature issues whose priority has not been decided yet (ideally only new issues)
* Three columns for features that have been decided to work on some time in the future
  * Low priority: minor performance or productivity improvement (in near future, or becomes "medium" in distant future)
  * Medium priority: e.g. major performance or productivity improvement (in near future, or becomes "high" in distant future)
  * High priority: is or will become a blocker in near future
* Column for each quarter (add new column when starting a quarter)
* Done: work done (mostly a automated sink for closed issues)

### Quarterly project board (see [example](https://github.com/makortel/test-project/projects/2))

Quarterly project board would deal with task issues that are agreed goals for the quarter, and would have the following columns
* To Do: task issues agreed to be addressed on that quarter
* Paused: work started, but paused for longer time for some reason (would be good to write down the reason in the issue)
* In progress: issues being actively worked on
* In Review: PR of the task issue is in review
  * I'm not fully sure of the usefulness, but sometimes the review can take a lot of time
* Done: work done

## Repository

Even though using a project board directly in [cms-sw/cmssw](https://github.com/cms-sw/cmssw/) would minimize duplication of issues, I'm thinking a separate repository (e.g. `cms-sw/cms-framework`) instead because
* I imagine we want different privileges for users for this repo
* I want to be able to assign people directly to issues without going through cms-bot
* I want to use different set of labels than in [cms-sw/cmssw](https://github.com/cms-sw/cmssw/)
* I'm concerned that "polluting" [cms-sw/cmssw](https://github.com/cms-sw/cmssw/issues) with our task issues would create confusion there


## Workflow

This section only sketches a few development use cases.

### Meeting

I suppose the overall workflow to function we'd need a weekly (or bi-weekly) meeting. I'd aim for a short meeting, primarily intended to check that everything is progressing, bring up concerns, discuss if priorities need to be adjusted, discuss if any new issues need to be addressed in the current quarter. Essentially an opportunity to adjust the plan of the quarter if needed. 

The last meeting of a quarter would be a bigger one with planning of work for the next quarter.

### Start working on an issue

1. Developer starts to work on a task issue
   * They assign themself to the issue, and move it from "To Do" column to "In progress" column in the project board of the current quarter
2. Developer completes a task issue that leads to a Pull Request
   * Developer uses one of the [keywords](https://docs.github.com/en/github/managing-your-work-on-github/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword) in the PR description for that PR to close all relevant issues
     * For example, `Resolves #32597. Resolves makortel/test-project#10.`
     * This works also across repositories (tested).
   * Developer moves the task issue from "In progress" column to "In review" column in the quarterly project board.
3. Developer goes to look for next work item in the "To Do" column of the quarterly project board
   * Possibly discussing with L2 too

### A new issue is opened in [cms-sw/cmssw](https://github.com/cms-sw/cmssw)

We would need a corresponding "feature issue" here, that has a link to the original issue.
That could be created manually, but I'm wishing for some automation.
I can think of enhancing cms-bot with e.g. with a new command along `issue framework` to cms-bot that would create a new "feature issue" with a link to the original issue, and add it to the "Pool of ideas" column of "Main view" board.
L2 decides if the issue needs an immediate action, or if an action can be discussed in the next "weekly" meeting.
Upon decision, the "feature issue" is moved to the corresponding priority column and assigned the corresponding label.
If the issue needs to be worked on the current quarter, it will be moved to the column of the current quarter in the "Main view" board, and also added to the "To Do" column of the quarterly board.

## How to get started?

I would not implement everything sketched here immediately, but start gradually and adjust the plan based on experience (including "trash the plan" option).

In the first step I'd
* Create the new repo `cms-sw/cms-framework` and the project boards described above
* Add feature issues for all developments currently in development or in review, and for the near-term plans
* Add task issues and GitHub milestones for the developments currently in development or in review
* We agree on a weekly meeting time
* We try to out using these issues to track our progress for about a month, then evaluate the experience

The following step could include
* Adding more feature issues for the future work
* Starting to work on automation with cms-bot

## Open questions

### How to deal with "quick fixes"?

Going through the "create issue in [cms-sw/cmssw](https://github.com/cms-sw/cmssw)", "create issue here manually or automatically", "add issue card to relevant column in quarterly project board" sounds rather heavy.

One possibility would be to utilize cms-bot, minimally with the `issue framework` command mentioned above, which would require a bit manualy work to move to the right quarterly board and column in there. I'm not sure if trying to automate the "board + column" part would make the process easier or more confusing.

### How to deal with design effort?

### How to deal with bug fixes done outside of framework?

In principle it would be nice to to account them.

### How to deal with dependencies between issues?

Add links in both directions? And keep dependent issues in "Pool of ideas" (in "Main view" for feature issues) or "To Do" (in quarter board for task issues)
