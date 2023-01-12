# Bringing Business and Agile Together

- Choose a focus, and then **stay focused** for 1-3 months.
- Suspiciously **measure** that choice with both outcome and output metrics.
- Listen to the measurement data and **adapt** with an open mind to **try new things**.

There are at least 3 key factors that can help successfully bring business and agile together.

- [Choose a Focus and Stay Focused for Short Time Intervals](#choose-a-focus-and-stay-focused-for-short-time-intervals)
  - [Staying focused with sub-goals and reiteration](#staying-focused-with-sub-goals-and-reiteration)
- [Suspiciously Measure your Focus with Outcomes and Outputs](#suspiciously-measure-your-focus-with-outcomes-and-outputs)
  - [Why suspicious?](#why-suspicious)
  - [Potential ways to measure](#potential-ways-to-measure)
  - [The criticality of buy-in](#the-criticality-of-buy-in)
- [Keep an Open Mind and Listen to the Data](#keep-an-open-mind-and-listen-to-the-data)
  - [Don't ignore the data](#dont-ignore-the-data)
  - [Update your output measures until you get it right](#update-your-output-measures-until-you-get-it-right)
- [Conclusion](#conclusion)

## Choose a Focus and Stay Focused for Short Time Intervals

The company had established their yearly Business Goals and Objectives. 5 bold statements that we all agreed to and, more-or-less, we agreed on how to accomplish them. However, by the end of January the 1:1 with my manager was all too familiar. "Oh, add we need infrastructure. Oh, and we need better QA. Oh, add we need to think about a couple of new dashboards." These were all good ideas, but they didn't directly roll up to the business goals. Worse yet, while the business was focused on features, the engineers were focused on quality as a distinctly separate goal. Unsurprisingly, when the deadlines approached, quality was deemed out of scope.

Of all the things that could be said about this reoccurring scene, think about the impact of focus. The worst thing we can do as leaders and managers is continue to say "and one more thing". Perhaps one reason we do this is that while we wait in our ivory towers for our teams to accomplish the goals, we dream of new futures. Or perhaps, in an attempt to help our teams we do our own solutioning and excitedly share it with the team, hoping it will inspire them. With yearly goals, we have way too much time to fall into this trap.

### Staying focused with sub-goals and reiteration

We leaders must see our jobs as enabling our teams. Instead of the above approach, we should:

1. Reiterate and clarify the vision.
2. Ask your teams what's slowing them down and do something about it.
3. Even harder, try just doing what they ask you to do.

If you follow PI Planning, keep that Business Context and Architecture sections crisp and clear. If you are a start-up, enter every weekly planning meeting with a 2 min re-iteration of the vision and goals. In both contexts, re-iterate the vision or goals in every 1:1.

Every new idea you have, write it down on an ideas board that your leadership team can refine later. Not right when you have the idea.Consider making this a backlog that is regularly refined.

"But, Dan, we have yearly goals! We can't spend the whole year reiterating!" Yep. Whatever leadership level you are at, your job is to break down those goals into sub-goals that can be evaluated high quicker intervals. Once per quarter is a really good starting point. This should be incrementally broken down at each level and re-iterated at each level so that the engineer understands how this line of code he's struggling with fits into the overall goal.
Yes, this takes work, but you have all that time in your ivory tower, right? It's amazing to me how we can we always find time to spend on the wrong things, while the right things go unloved.

The job doesn't stop at staying focused though. If we're evaluating our goals every 3 months, we must have a mechanism to know when to pivot or persevere.

## Suspiciously Measure your Focus with Outcomes and Outputs

The number on the powerpoint was big and red: 3 per sprint. I was reading our organizations Change Failure Rate (CFR). This was measuring across roughly 6 teams, 24 services, and a hundred code changes per sprint. This might not look so bad. In fact, the leaders reading the numbers would often pat everyone on the back and say, "We're ahead of the industry standard, go team!"
However, under another measure, we were only releasing customer facing features every 3 or 4 months. Each of which often had at least 1 critical bug. When we dug further into CFR, we realized that this was actually combining Deploy Frequency and CFR. Further, we did not account for opposing metrics like Feature Lead Time.

The good news is that we were basing our measurement strategy on the time tested DORA metrics. The bad news is that we didn't have the right calculations and we weren't tracking all of them. Only after napkin-mathing each of the metrics over the past 6 months did we realize huge areas of improvement to our operational performance.
And, in fact, we were below industry average.

### Why suspicious?

By the time we've chosen a focus area, it's often been through the Mad Max Thunderdome and emerged as a champion worth worshiping. We are emotionally attached. And after all, the decision team did agree it was the most important focus area. We barely feel the need to measure, or to be suspicious. This is no excuse. We must have a right measure for two reasons.

- To prove it's right focus.
- To show the impact of the focus.

What metrics to measure is always tough. And yes, of course, as soon as you being to measure some folks will game the system. But that's not a metrics problem, that's a culture problem.
OKRs and DORA metrics are both focused on what they call "outcome" metrics. These are critical to establish so that you know the impact. We shouldn't stop here. If you're doing OKRs every quarter, then do what is called "output" metrics weekly or monthly.
It's worth noting the Rule of 3. You must try something at least 3 times in order to know if it's working. Don't take one measurement and pivot right away. After 3 solid attempts, pivot right away - or fix your measurement.

### Potential ways to measure

One thing I want to make clear: To measure something often requires cost. You often have to instrument your processes to get the measures you want. Sometimes that's a spreadsheet that is updated on a cadence. Sometimes it's a software integration of systems. In either case, plan on spending some money to get the accurate measures you need to run the business.

Let's go back to the DORA metrics example. When measuring success (outcomes) you want to measure these two opposing metrics of delivering fast, Change Lead Time, and accurate, Change Failure Rate.
To measure these, you'll need plugins in Jira or GitHub to show you how long it takes to move a feature from Started to Done. Thankfully your organization is full of intelligent engineers, utilize them to build the tooling or suggest a plugin.
But then, you have certain behaviors (outputs) that you expect to move those numbers. For example, more unit tests should result in a decrease in CFR. But... does it? Well, you have to measure that too. Setup Coveralls to measure code coverage, and run it for 3 sprints, or 3 deploys, or whatever your cycle is, in order to see the impact.

### The criticality of buy-in

In order to measure effectively, you need two kinds of buy in. From your leadership and your teams.

To get leadership buy in, you have to first finish the conversation around focus. Measure what they need measuring, not what you need measure so you can make a point. You also need to give them a seat at the table. Ensure they understand the metrics you are trying to measure and how they can use those metrics.
If it's successful they can tout them to their stakeholders. If the  metrics show failure, they can tout their ability to have visibility and to adapt quickly. This helps your organization and stay focused on the 20% of work that yields the 80% of value.

To get the team's buy in, again, give them a seat at the table. Solicit their feedback for better or different measures. In the end, ensure they understand how they'll be measured and remind them it's not a measure of performance. We're all in the same game, learning together.
Once teams buy in, you can gamify it. Create a leaderboard. Even if it's just a local whiteboard, teams do better with some healthy competition.

So now you've got the focus and you've got the measurements. A month goes by and you start to see the numbers. Great! But not great, the number's do look good. What do you do?

## Keep an Open Mind and Listen to the Data

// insert Nick Cage on his back.

During my first week on the new project, a technical lead showed me a system diagram. Roughly 15 services were called out. It was their plan to break their monolith into microservices. It was well thought out and relevant. There was only one problem. This picture was year old and only a few of the services had actually been created. It got worse. A year after that meeting, I was still around and while a few more services were created, we didn't seem to be going any faster as an organization. In fact, we had traded the monolith deployment for a distributed-monolithic deployment. Microservices could not be easily deployed on their own.

### Don't ignore the data

The focus and measures of the organization were clear enough.

1. Focus: Deliver at Scale
2. Outcome Metric: Teams can deliver features quickly and independently.
3. Output Metric: Number of new services created.

However, after just a few months, and a few microservices were created the data results were in. We weren't moving any faster. Cycle times were the same. The cost and complexity to deploy had risen. Harder bugs we're cropping up.

What we should have done, is refocused a sub-goal to find out what the issue was. In our case, we needed better practices and tooling around creating these microservices. The first one (ok, the first three - rule of 3 right?), should have been measured against the outcome measure and we should have continuously updated the output measures until we got the gains we wanted. That's a little ambiguous, let's try to break it down.

### Update your output measures until you get it right

Instead of focusing on an easy number like number of microservices created. We could have explored:

1. Creating new processes for a microservice first approach.
2. Using technical practices to decouple services (ex. api versioning and feature flags)
3. Exploring tooling to help coordinate environment health. Managing deploys and rollbacks.
4. Ditched microservices and explored the modu-lith approach.

This is why is so necessary for every organization of every stage of maturity to embrace the scientific approach in everything they do. They must treat every assertion as an assumption to be proved. This does not mean I am advocating for a bunch of bitter, skeptical folks - we have enough of those already, don't we? No, it means be curious, ask the 5 whys to understand where assumptions are coming from so you can ensure you're removing all bias from the equation.

Side Note: There are times when your data is wrong. Be like a data scientist. Make it the highest priority to fix your measures.

## Conclusion

Stay focused for long enough for your measures to provide data. Get your whole team together and brainstorm or celebrate with the data you get. This quick tuning of your accuracy either at the business level or the project level will result in amazing speed at build the right thing.

At [Jemini.io](https://jemini.io), we're dedicated to helping our client succeed. Employing vertically-connective, team-driven activities like this helps organizations keep their competitive advantage. Reach out to find out more about how we can help you're organization succeed!
