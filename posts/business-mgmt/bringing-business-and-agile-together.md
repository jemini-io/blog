# Bringing Business and Agile Together

There are at least 3 key factors that can help successfully bring business and agile together.

- Choose a focus, and then **stay focused** for 1-3 months.
- Suspiciously **measure** that choice with both outcome and output metrics.
- Listen to the measurement data and **adapt** with an open mind to **try new things**.

- [Executive Summary](#executive-summary)
- [Choose a Focus and Stay Focused for Short Time Intervals](#choose-a-focus-and-stay-focused-for-short-time-intervals)
  - [Staying focused with sub-goals and reiteration](#staying-focused-with-sub-goals-and-reiteration)
- [Measure Outcomes](#measure-outcomes)
  - [](#)
- [Adaptation](#adaptation)

## Choose a Focus and Stay Focused for Short Time Intervals

The company had yet again, established their yearly Business Goals and Objectives. 5 bold statements that we all agreed to - and more-or-less agreed on how to accomplish them. However, by the end of January, the 1:1 with my manager was all too familiar. "Oh, add we need infrastructure. Oh, and we need better QA. Oh, add we need to think about couple new dashboards." These were all good ideas, but they didn't directly roll up to the business goals. Worse yet, while the business was focused on features, the engineers we're trying to focus on quality as a distinctly separate goal. Unsurprisingly, when the resources grew thin, quality deemed "out of scope".

Of all the things that could be said about this reoccurring scene, think about the impact of focus. The worst thing we can do as leader and managers is continue to say "and one more thing". Perhaps one reason is for this is that while we wait in our ivory towers for our teams to accomplish the goals, we dream of new futures or solution the problems ourselves. And with yearly goals, we have way too much time to fall into this trap.

### Staying focused with sub-goals and reiteration

Obviously, we leaders must see our jobs as enabling our teams. Instead of the above approach, we should:

1. Reiterate and clarify the vision.
2. Ask your teams what's slowing them down and do something about it. Try just doing what they ask you to do.

If you following PI Planning, keep that Business Context and Architecture sections crisp and clear. If you are a start up, enter every weekly planning meeting with a 2 min re-iteration of the vision and goals. Re-iterate the vision in every 1:1.

Every new idea you have, write it down on an ideas board that your leadership team can refine later, not now. Consider making this a backlog that is regular refined.

One tricky thing is that you are still often dealing with yearly goals. Whatever leadership level you are at, your job is to break down those goals into sub goals that can be evaluated no more than once per quarter. This should be incrementally broken down at each level and re-iterated at each level so that the engineer understands how this line of code he's struggling with fits into the overall vision. Yes, this takes work, but you have all that time in your ivory tower, right?

The job doesn't stop at staying focused though. Remember, we have to evaluate our goals every 3 months at most. We must have a mechanism to know when to pivot or persevere.

## Suspiciously Measure your Focus with Outcomes and Outputs

The number on the powerpoint was big and red: 3 per sprint. I was reading our organizations Change Failure Rate (CFR). This was measuring across roughly 6 teams, 24 services, and a hundred code changes per sprint. This might not look so bad. In fact, the leaders reading them numbers would often pat everyone on the back and say, "we're ahead of the industry standard, go team!"
However, under another measure, we were only releasing customer facing features every 3 or 4 months. Each of which often had at least 1 critical bug. When we dug further into CFR, we realized that this was actually combining Deploy Frequency and CFR and did not account for opposing metrics like Feature Lead Time.

While we were trying to measure DORA outcome metrics, we didn't have the right calculation and we weren't tracking all of them. Only after napkin-mathing each of the metrics over the past 6 months did we realize huge areas of improvement to our operational performance.

### Why suspicious?

By the time we choose a focus area, it's often been through the mad-max ringer and emerged as a champion worth worshiping. We are emotionally attached. And after all, the decision team did agree it was most important. So, we barely feel the need to measure, or to be suspicious. But in order to not only prove it's right focus, but also show the impact of the focus, we must rigorously measure.

What metrics to measure is always tough. And yes, of course, as soon as your measure some folks will game the system. But that's not a metrics problem, that's a culture problem. OKRs and DORA metrics are both focused on what they call "outcome" metrics. These are critical to establish so that you know the impact. We shouldn't stop here. If you're doing OKRs every quarter, then do output based metrics weekly or monthly. This is the Rule of 3. You must try something at least 3 times in order to know if it's working.

### Potential ways to measure

One thing I want to make clear. To measure something often requires cost. You often have to build the measures. Sometimes that's a spreadsheet. Sometimes it's a software integration of systems. In either case, plan on spending money to get the accurate measures you need to run the business.

Let's go back to the Operational Performance example. When measuring "outcomes" you want to measure these two opposing metrics of delivering fast, Change Lead Time, and accurate, Change Failure Rate.
To measure these, you'll need plugins in Jira or GitHub to show you how long it takes to move a feature from Started to Done. Thankfully you're organization is full of intelligent engineers, utilize them to build the tooling.
But then, you have certain behaviors that you expect to move those numbers. For example, more unit tests should result in a decrease in CFR. But... does it? Well, you have to measure that too. Setup Coveralls to measure code coverage, and run it for 3 sprints to see the impact.

### The criticality of buy-in

In order to measure effectively, you need two kinds of buy in.

- Leadership
- Teams

To get leadership buy in, you have to first finish the conversation around focus. You also need to give them a seat at the table. Ensure they understand the metrics you are trying to measure and how they can use those metrics. If it's successful they can tout them to their stakeholders. If the  metrics show failure, they can tout their ability to adapt quickly and stay focused on the 20% of work that yields the 80% of value.

To get teams buy in, again, give them a seat at the table. Solicit their feedback for better or different measures. In the end, ensure they understand how they'll be measured and remind them it's not necessarily a measure of performance. We're all in the same game, learning together.
Once teams buy in, you can gamify it. Create a leaderboard, even if it's just a local whiteboard and

1. State the principle. Outcomes not outputs.
2. Anecdote: Velocity?? We were measuring CFR in correctly.
3. Practical Advice: Rule of 3. Consider measurement real work. Have teams measure themselves. People can

## Adaptation

// insert Nick Cage on his back.

1. Principle.
2. Anecdote: We spent 2 years trying to "break down the monolith" without changing our strategy of how to do it.
3. Practical Advice:
