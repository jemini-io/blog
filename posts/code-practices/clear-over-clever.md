# Clear over Clever

## The Temptation of Clever Code

- [The Temptation of Clever Code](#the-temptation-of-clever-code)
- [Clever Code is just a Secret Cost](#clever-code-is-just-a-secret-cost)
- [Don’t Stop at Clear Code](#dont-stop-at-clear-code)
- [Please, be Clear for the Children’s Sake](#please-be-clear-for-the-childrens-sake)

We’ve all seen that “senior” developer roll into the code review on his Segway grinning a mischievous grin.

“Did you see my PR? I think I reached a new height this time.”

Concerned, you pull open the file to find something like this:

```tsx
function union(...arr) {
  return arr.reduce((f, u) => [...new Set(f.concat(u))]);
}
const r = union([1, 4, 6, 5, 3], [4, 3, 6, 9], [9, 7, 4, 6, 2]);
console.log(r);
```

His eyes scan you, like a terminator, top to bottom while you struggle to understand what this code does. He bellows a laugh and reminds you that someday you’ll get it. He proceeds to expound and exult his code as you slump into your seat wondering how you’ll ever be a 10x engineer. You briefly consider doing cocaine, but realize that’s probably not the right response.

## Clever Code is just a Secret Cost

In “The Pragmatic Programmer”, Andrew and David remind us that the essence of good design is that it’s Easy To Change. I used to tell the eager coding bootcamp graduates in my life to write their code for the “dumbest engineer you know”. While this might be a bit offensive, it serves to nest the idea deep in the brain.

Probably the largest cost to maintaining a system is developers untangling confusing code from past ghosts. Sometimes, that ghost is yourself. The good news is now you know the easiest way to save cost on your current project. Spend a 10 mins now to make it clear, and you’ll save a future developer and PM’s pocketbook 30 minutes later.

So, how would the real 10x Engineer wright the above? Something like this.

```tsx
function mergeUniqueElementsOfArrays(arrays) {
  const uniqueValues = new Set();
  for (const array of arrays) {
    for (const element of array) {
      uniqueValues.add(element);
    }
  }
  return Array.from(uniqueValues.keys());
}
const arrays = [
  [1, 4, 6, 5, 3],
  [4, 3, 6, 9],
  [9, 7, 4, 6, 2],
];
const mergedArray = mergeUniqueElementsOfArrays(arrays);
console.log(mergedArray);
```

1. Names are far more clear.
2. There are no splats (`…`). Avoid them.
3. Replace `reduce` with simple for loops.
4. Avoid jargon like `union` in favor of english like `mergeUniqueElements`.

If, and only if, you can make a performance argument should you opt for more clever code. If you do this, document you code so others understand the tradeoff you made.

## Don’t Stop at Clear Code

For this reason, I avoid Generics, Abstractions, and other patterns until the last possible moment when the code is proving to be messy and needs something to help clean it up. But why stop at code?

In the epic saga of Monolith to Microservices, we’ve become star struck with promises of microservices and decided to go “all in” without understanding the cost. There is immense complexity with microservices. Most companies will go through an exercise of decomposing their monolith into all of its atomic parts. Even if there are just tens of parts, this will also drive your engineers into kilos of angel dust in an effort to keep their sanity.

Instead, consider following the tenants of Domain Driven Design and breaking it into its *major* parts first.

![monolith-to-domains](./img/monolith-to-domains.png)

This allows your deployment model to only need to mature only slightly. Teams are created about the domain and are decoupled to deploy as needed, but they still only have one service to manage instead of 3 or 4.

## Please, be Clear for the Children’s Sake

For the sake of the children that will be maintaining your code many years from now, please take the time to be clear and avoid clever code.

1. Prefer Classes to currying functions - yes, classes in JS!
2. Prefer small functions with good names to many if/else statements in a functions.
3. Avoid language short cuts like `reduce` when readable loops do the same job.

At [Jemini.io](http://Jemini.io) we strive to practice principles like this one. This way, we not only quickly and clearly build systems we (or you) can easily maintain them long into the future.

Reach out to us to find out more about what we can do for your project!
