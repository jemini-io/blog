# Helping Business Succeed with Agile Thinking

## A Sadly Common Conflict

Business and Agile mindsets do not always get along. We’ve seen too many software projects where developers burn out, the business loses money, and the end users do not get what they want. We’ve also been a part of a rare few unicorn projects that have found the balance. These projects were not only some of the most fun to work on, they were also the most successful in generating revenue. This is not a coincidence.

How can leaders who are building word class software products find that balance on their projects? That’s going to be the subject of this post, which will hopefully be 1 of 3.

Perhaps one reason for the common conflict is that the average business is required to make big bets on yearly goals that will hopefully increase revenue while the average Agile team is overly focused on code and various sorts of quality. A second problem in bringing these two communities together is the problem of scale. As one well-functioning agile team turns into 5 or 20 teams, the game seems to change. I’ve seen many engineers complain that SAFe is just Waterfall. Yet I’ve seen projects fall into anarchy without it.

We’ll look at the simple Agile loop of “build, measure, learn” at scale and give some practical measures so you know how well your business and agile mindsets complement each other. To run a successful business fluent, scaled agile software project, you must:

- Choose a focus, and then **stay focused** for 1-3 months (build - this post).
- Suspiciously **measure** your choice with both outcome and output metrics (measure).
- Listen to the measurement data and **adapt** with an open mind to **try new things** (learn).

## Choose a Focus and Stay Focused for Short Time Intervals

The company I was working for had established their yearly Business Goals and Objectives. They published 5 bold statements that we were familiar with and we knew roughly how to build them. However, by the end of January, the one on one with my manager was all too familiar. "Oh, and we need to build more infrastructure. Oh, and we need to build better QA. Oh, and we need to think about building a couple of new dashboards." These were all good ideas, but it wasn’t clear how they rolled up to the business goals. Worse yet, while the business leaders were focused on delivering features, the engineering department was focused on quality as a distinctly separate goal. Unsurprisingly, when the deadlines approached, quality was deemed out of scope and many of the engineering efforts were shelved, half implemented.

### **Add focus by subtracting distractions**

Think about the impact of the lack of focus. The engineering manager did not make the wrong choice per se, they made too many choices. The time spent on QA was not just shelved, it was wasted time. That additional time could have been spent on feature delivery. Ideally, we deliver each feature with an agreed upon level of quality. “Make quality a requirements problem” as the Pragmatic Programmer says ([tip number 8](https://pragprog.com/tips/)). If QA needs special focus in our organization it should be its own focus area for an interval. In the end, it’s always a tradeoff of short term value and long term sustainability. The good news is, we get to make the tradeoff.

As [Franklin Covey says](https://resources.franklincovey.com/the-4-disciplines-of-execution/discipline-1-focus-on-the-wildly-important), we need to focus on the wildly important. The worst thing we can do as leaders is continue to say "just one more thing". We're not being clever like Columbo, we're scope creeping like table 5 who orders something else every time we refill their water. Many leaders got to their position by being some combination of a visionary and a thorough practitioner. Which means they’re always thinking of new things. Not to mention they’re always getting new things thrown at them from external sources. No matter how well intentioned these ideas are, to the team receiving them, it's more distracting than helpful.

### **Staying focused with sub-goals and reiteration**

We leaders must see our jobs as enabling our teams and keeping them focused. Instead of the above approach, we should:

1. Reiterate, connect, and clarify the goals.
2. Break goals down into sub-goals.
3. Get continuous feedback from the team and take action to enable them to achieve the goals.

Here’s a few examples of what that can look like. If you follow PI Planning, keep the Business Context and Architecture sections crisp and clear. If you are a start-up, enter every weekly planning meeting with a 2 min re-iteration of the goals and key results. Re-iterate the goals in every 1:1. In every case, connect the weekly activities of what your team is doing with the goals.

But what about all those great ideas you’re having while the team is working on the goals? Write it down. Create an ideas board that your leadership team can refine later; not right when you have the idea. Consider capturing these ideas in their own backlog that is regularly refined by the appropriate group of folks.

You might be protesting, "But, Dan, we have yearly goals! We can't spend the whole year reiterating!" Yep, absolutely true. In whatever leadership level you are, your job is to break down those goals into sub-goals that can be evaluated for their effectiveness at quicker intervals. Once per quarter is a really good starting point. These sub-goals should be reiterated at each level so that the most junior engineer understands how the line of code he's struggling with fits into the yearly company goal. This often takes a surprising amount of work. Google’s [OKR system](https://www.whatmatters.com/resources/google-okr-playbook) is set up to create this connectivity from the top of your organization to the bottom.

### **The business benefits of Iterative focus**

So far, I’ve primarily used the term Goals. Goals, Objectives, or Focus Areas should all refer to the same basic idea, but I’m going to switch to using the term “focus area” as I believe it communicates the message slightly better. When we choose a focus area and put an evaluation time on it, we are not creating a death march deadline. We will discuss more about this in later posts. For now, I’ll leave you with this: after 3 months of a focus area you may realize you’re halfway to your goal, but you’ve seen real progress. In that case, you continue with the same focus.

But what is the CEO or Board of Directors to do with this? They are in the mindset of “Go/No Go” decisions and budgets that must prove definitive returns on investment. Well, with this agile, iterative approach you now have more options than before. Now you can show the paper-trail of sub goals that yielded results, or learnings and resulting educated pivots. Additionally, with a system like OKRs you have data for all the sub-goals that can be drawn on to show improvements, not just one yearly number that tries to rule them all.

This approach builds trust through transparency. Not just with your boss but with all your subordinates as well.

### **Iterative focus is agile at its finest**

We are simply adding discipline to our scale. At the lowest levels of software project execution, kanban applies focus at the story boundary: once the story is accepted you cannot change it. In scrum, it’s at the sprint boundary. Even at this low level, it’s hard to implement purely. How much more at scale? When we have teams of tens or hundreds of developers, a new product offering may require changes to our core platform, our data team, and three different user facing apps. To live into the [Agile manifesto’s principle](https://agilemanifesto.org/principles.html) that “highest priority is to satisfy the customer through early and continuous delivery of valuable software” simply takes more time and more support structure at scale. The boundary of focus might change, but the requirement of disciplined focus does not.

The final and perhaps most important way that choosing a business focus is merged with the agile world, is that we make it a team activity. We should bring our engineering, marketing, and other stakeholder groups into those focus area conversations. We should solicit and include their ideas and allow time for clarifying why our bosses have chosen this particular goal.

This is one reason I actually like [PI Planning](https://www.scaledagileframework.com/pi-planning) when it’s done correctly. Part of doing it correctly, means highly tailoring it to your environment. Everyone, even senior leadership, are in the room. Everyone has a seat at the table. Granted, most of the engineers are hidden behind their computer screens. But at least they have a seat and a say in what decisions are made and how the larger group collaborates on efforts. On one of my projects, we had about 8 teams on a cycle of 1 day of PI Planning followed by three 2-week sprints. The balance of focus, collaboration, and autonomy was one of the best I’d ever seen.

## **Conclusion**

While we’ve seen the value of focus and some practical ways to improve it, the job doesn't stop at staying focused. If we're evaluating our objectives every 1 to 3 months, we must have a measurement to know when to pivot or persevere. That will be the subject of the next post.

At [Jemini.io](http://jemini.io/), we’re dedicated to helping our customers succeed. Employing a strategy of iterative focus helps your business quickly adapt to and stay focused on the highest value activities. Reach out to find out more about how we can help your business!
