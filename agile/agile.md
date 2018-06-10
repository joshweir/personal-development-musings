# Agile Notes

## Estimating in Ideal Days vs Story Points

Benefits of story points:
* Story points help drive cross-functional behaviour.
* Story-point estimates do not decay.
* Story points are a pure measure of size.
* Estimating in story points typically is faster.
* My ideal days are not your ideal days.

Benefits of ideal days:
* Ideal days are easier to explain outside the team.
* Ideal days are easier to estimate at first.

reference: Agile Estimating and Planning - Cohn

## Maintain Product Backlog

* Product Owner is responsible for maintenance of the product backlog.
*	Initial Estimate – Before any sprint planning:
  1. Scrum Master / SMEs provide initial estimate by breaking the story down into it's constituent tasks, estimating the ideal hours required to complete each task, this forms the total ideal hours to complete the story.
  2. Scrum Master / SMEs also provide a story point estimate for the story which is a measure of the story size in comparison to other stories in the backlog - time is irrelevant for this measure.
  3. If say a large product backlog needs to be estimated for duration longer than a sprint (eg. for release planning), then a good technique to save time and come up with initial estimates for all stories, is to do the task breakdown estimation as in point 1. above, for a dozen or so stories. Then once we have a decent chunk of stories estimated in detail, and hence a decent range of stories' story points, we can then compare new stories at a higher level (without the task breakdown) based on how the compare to existing stories that are already estimated. For this to work, we would need to have a decent understanding of what the story entails, if unsure then do the task breakdown.
*	Importance – A number to determine priority.
*	How To Demo – A short explanation how this would be demoed in the sprint demo.
*	Product Owner should define the Scope and Importance for each item, the team (including scrum master – not the product owner) will define the estimate (during sprint planning)
* Maintain a register of stories categorised by story points, total ideal hours estimate, classification of the stories type / complexity (eg. data fix - complex, data fix - medium, data fix - simple). Can use this register to when estimating new stories to quickly find similar stories to obtain a similar estimate.

## Release Planning

For the following subsections, remember that team sizes should range between 4 and 12 with the sweet spot being somewhere around 7. We cant reliably estimate what will happen when increasing / decreasing team size past these limits.

The next sub-sections give strategy for estimating team size (assuming duration and scope is fixed) and duration (assuming team size and scope is fixed). If only one factor (team size, duration, scope) is known then the customer would need to nail down what else should be fixed in order to estimate the remaining factor.

### Forecast team velocity  
* Determine number of ideal man hours available within a sprint, factor these into "realistic" man hours (to cover company meetings, emails, phone calls):
ideal man hours x focus factor (between 0.55 and 0.7 depending on how efficient your company is, how much red tape).
* Note each team members skill capability, the tasks they could work on. eg. we wouldn't assign development tasks to a tester. Also note the number of realistic man hours available per team member.
* Iterate through product backlog stories taking a random assortment (vary size and types of stories)
* Break down the story into tasks, estimate the tasks in terms of ideal man days / hours and also the type of task (which team member(s) could work on that task), add task estimate to an available team member who has the capable skills' task estimate tally. Stop this process once a team member no longer has available "realistic" man hours, this is the teams estimated velocity in terms of story points and man hours.
* Apply a story points forecasted range using the cone of uncertainty, multiple estimated velocity by 60% and 160% to determine velocity range.

### Estimating Team Size (required: fixed duration and scope)

* Identify for each story, the skill capabilities required to complete each of those stories. This gives a view of the estimated skill mix required in the team. eg. if stories look to be 40% front end, 30% back end, 30% database then the team mix should probably match this mix: eg. 1 front end devs, 1 back end devs, 1 database devs or 2 full stack devs, 1 database dev. We could effectively come up with a *minimum team* based on this.
* Estimate team velocity (TODO: add link to estimate team velocity section above) based on the *minimum team*, extrapolate the number of sprints required at this velocity, if it is more than the fixed duration, increase the team size and repeat this process until the fixed duration is achieved.

### Estimating Duration (required: fixed team size and scope)

Estimate team velocity, then extrapolate to how many sprints are required:

*Can we run 1 or more sprints first before estimating duration?*
*Yes:* Velocity obtained from actual sprint velocity.
*No:*
  *Has the team worked on a previous project recently?*
  *Yes:* Use the avg team velocity from the other project.
  *No:* then forecast team velocity (TODO: add link to estimate team velocity section above)

### Estimating impact of team size change on velocity

[Using this method](https://www.mountaingoatsoftware.com/blog/predicting-velocity-when-teams-change-frequently), track the median velocity of the last 5 sprints with stable team in spreadsheet with columns:
* Team / Scrum Master
* Team sprint duration
* Initial Team Size: eg. 6
* New Team Size: eg. 7
* Median velocity last 5 sprints (team must be stable over that period, take lesser number of sprints if not)
* % change velocity sprint +1
* % change velocity sprint +2
* % change velocity sprint +3

Can use this information to estimate:
* the negative impact on velocity in the short term due to knowledge sharing within the team.
* the stabilised improved velocity after around the 3rd sprint.

#### What if I don't have the data?

I would guesstimate that adding a resource will require another existing resource for 2 weeks (for handover / training) and then 20% of a resource for another 2 weeks, and then estimate the team velocity to be increased by the percentage of that resource being added to the team.

eg. adding a 6th resource to a 5 person team would:
* reduce team velocity by %20 in first 2 weeks.
* reduce team velocity by %5 in the 2nd 2 weeks.
* increase team velocity by %20 from the 5th week.

## Estimating and Sprint Planning

### Pre Meeting

* Maintain the product backlog (TODO: include link here to section above)
* Using the story register (see Maintain the product backlog section for details), Identify stories that are either similar work or similar size to the highest priority backlog items that will be proposed during sprint planning. Comparing stories proposed to existing similar stories will help in determining the correct estimate - as story points estimate is always relative.
* Determine team's average velocity (in ideal man days or story points whichever is chosen).
* Determine the team's estimated productive hours which is:
Estimated Productive Hours = (Available Man Hours - non project work (such as known meetings)) x focus factor (usually between 0.55 and 0.75)
* Prepare index cards of stories that have a chance of being included in sprint. Index cards include:
tracking#, Name, Importance, How To Demo, Initial Estimate, Notes

### The Meeting

~4h for a 3 week sprint

Identify sprint goal and demo date.

Determine daily scrum time / place

Iterate through the product backlog by importance:
* Each story broken down into smaller stories if necessary (strive for 2-8 story points).
* Scope is clarified for each story. Re-arrange index cards change importance where necessary.
* Break down the story into tasks, estimate the tasks in terms of ideal man days / hours and also the type of task (which team member(s) could work on that task), add task estimate to an available team member who has the capable skills' task estimate tally. Stop this process once a team member no longer has available "realistic" man hours, this is the teams estimated velocity in terms of story points and man hours. If the task allocation is really lop sided to particular skills, determine / negotiate with product owner incorporating other lower priority stories that could be fulfilled by team members with remaining capacity. Tasks eg: Design and build applet, design and build service to do this, unit testing, peer review, SIT, update regression test.
* Planning poker played for each story. All play cards (based on story points – ideal estimate), if there are outliers then need to discuss why (and probably task breakdown) and then replay based on discussion. Initial Estimated story points updated accordingly.
* Clarify the definition of “done” for each story – usually this would either be “deployed to prod” or “ready for acceptance test”.

## Daily Scrum Meeting

* Take stories and child tasks from the scrum planning meeting and allocate to task board based on priority (importance).
* Identify what progress was made for each developer yesterday and estimate for task in prog remaining (update task slip). Any impediments? Scrum master should take those as action items.
* Check out story tasks and write persons name on task for new tasks.
* Add any unplanned items to unplanned items (assign to person). Once done add to done.
* Once done, add up all time remaining not in “Done” column and plot this onto the burndown chart. Burndown is days on x axis (not weekends) and story points remaining on Y axis.

## Sprint Retrospective

Goal: Identify what went well, what went badly, improvement focus for next sprint.

TODO: More details here

Examples of improvements we've made:

* Automated the build, made it 2 times a week allowing system test to be completed within the sprint
* Scripted data fix tests, created a template for this
* Stopped people dealing directly with devs, instead requests were funnelled through scrum master

## TODO

* should I update the story points for a story after knowing more later? Say the scope was too unknown? No because scope should be pretty well known if its to be worked on

* include somewhere: If a story is only partially completed, the story points are not counted against the current sprint as long as you expect the story to be completed in the next sprint - the velocity will increase in the following sprint, and we are more interested in avg velocity. However if the story is likely to not be finished in the next sprint either, then break down the story and estimate the amount of story points completed into it's own story and create a new story for the remaining work and estimate that.
