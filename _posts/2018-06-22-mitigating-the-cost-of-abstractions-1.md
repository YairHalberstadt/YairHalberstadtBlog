---
title: Mitigating the costs of abstraction part 1. When To, and How To.
---
## Mitigating the costs of abstraction part 1. When To, and How To.

Us C# programmers love our abstractions, don't we! We use properties, interfaces, abstract classes, linq, and lambda functions all over the place. And we rarely bother to think about their performance costs.

And rightly so! 

The cost of an abstraction will almost never change the Big O complexity of your algorithm. It's also rarely that much more expensive either. Whereas focusing on choosing the right algorithm can often lead to a huge reduction in time complexity. Just using a dictionary instead of a list can in some cases turn an O(n) algorithm into an 0(1) algorithm. And realising one can short-circuit a method under most circumstances might increase the average speed of an expensive piece of code a thousandfold!

However abstractions do have a cost, and in the worst of cases it can make code as much as 10 times slower, even using an identical algorithm.

It would be dishonest to vociferously point out that premature optimization is the root of all evil without remembering how the quote continues:

"Programmers waste enormous amounts of time thinking about, or worrying about, the speed of noncritical parts of their programs, and these attempts at efficiency actually have a strong negative impact when debugging and maintenance are considered. We should forget about small efficiencies, say about 97% of the time: premature optimization is the root of all evil. **Yet we should not pass up our opportunities in that critical 3%.**" - Donald Knuth

So when is micro-optimisation of the sort that would improve the performance of abstractions worth it?

#### 1. You're already using the right algorithms.

There is absolutely no point in micro-optimising if you could improve your performance tenfold by using a different sorting algorithm or data structure. Don't even think about micro-optimising till you're sure that any tweaks you do won't have to be removed when you rip up all your code and start from scratch.

#### 2. How many use cases is this geared towards?

Is this a class library that is going to be used by many other projects? In that case there's almost always a value to optimising, as its likely a performance critical piece of code is using it somewhere.

If however the code is geared towards a single use case, such as an app or a website, you only care about one metric: is it performant enough for us. If it is, then you're just wasting your time by trying to increase performance. If not, then you have no choice but to get your hands dirty.

#### 3. How is this code used?

If your class library API is being called synchronously tenthousand times a second, you'd better optimise the hell out of it. Whereas if it's being used to do one thing every 10 minutes asynchronously, who cares?

So if your implementing a GetTime() function it had better be quick. If you're creating a nicely formatted report, it's rare that it will matter whether it takes 1 minute or 2, and it almost certainly doesn't matter if it takes 10 milliseconds or 1000.

#### 4. What is the performance bottleneck?

A wrapper around a SQL query,  which converts the query to an IEnumerable is likely to be called thousands of times a second from some codebases, so really ought to be quick.

However even if you could optimise the wrapper logic to zero, it would still be slow. That's because SQL is slow, and it's the IO that's the performance bottleneck.

As such you're much better off spending your time optimising the efficiency of the generated SQL query than spending it optimising the speed of the conversion, especially as these two aims are often at odds.

As ever, profile, and focus on the bottlenecks.

#### 5. How costly is optimising?

Optimising always has costs as well as benefits. At the very least it takes up time, which might well be better used elsewhere. 

It also is likely to adversely effect readability, usability, extensibility, type safety, complexity and organisation of the codebase. Optimisations which minimise these costs are, all other things being equal, to be preferred to optimisations which are more costly.

In general optimisations which only change the internal implementation of a method have the lowest costs.

Those that change the internal API of your classes will often have slightly higher costs.

Those that force you to rearrange the internal structure of your code are much more likely to be problematic.

Those that force you to change the public API or even worse, the public structure of your codebase are dangerous. They will force you to break backwards compatibility, and are highly likely to lead to a reduction in usability, extensibility, or complexity of the codebase. They should not be undertaken without serious thought, and the question must always be asked: "Is this optimisation really worth it?".

### To Wrap Up

Now that I've waxed lyrical on my opinions about optimisations, it's time to get to the nitty gritty. Over the next few posts I intend to give examples of cases where abstractions led to a decrease in performance, and how I attempted to mitigate them.
