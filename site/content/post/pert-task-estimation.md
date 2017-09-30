+++
title = "Can you give a realistic task estimate?"
description = "No developer likes giving estimates. Especially so, when they are turned into fixed deadlines. So, how can we avoid this while keeping everybody happy?"
tags = []
date = "2017-09-23T06:46:32.031Z"
categories = [
    "Development",
    "Process"
]
+++

It's a pretty commonly held belief - almost to the point of being fact - that no developer likes estimating how long or how much effort a task will take.

That's understandable, there are just too many variables to take into consideration. To be able to give a realistic or reliable figure like '2 weeks' or '21 days' just isn't possible.

Some sad souls may disagree, particularly if they're talking to an interviewer and the job description requires you to be able to "give realistic estimates and stick to deadlines". And that is the problem - estimates being conflated with deadlines.

When a developer gives an estimate, it's usually something along the lines of "it should probably take about 10 days if nothing goes too wrong and I'm not disturbed too much". By the time that makes it up the chain of command, what management here is "this will be completed within the next 10 days".

 > I haven't given an estimate that has committed me to a deadline in over two years.

 > I have lost jobs over it, but I haven't lost any sleep.

Understandably, management generally don't like my initial response of "it will be done when it's done". Given that, I have had to provide estimates, but that never comes with a commitment.

There are a number of different ways to give more reliable task estimates, but often these still come up with a single figure. This figure may or may not be more reliable than the one you picked out of your arse, but it's still a single figure taken as a commitment to a deadline.

 > Deadlines are a thing of the past, and have been for a considerable time now. Take note managers!

So, how can you provide an estimate without a commitment? You say that "it could be completed tomorrow, or it could never be complete". To make this a more palletable response, we actually do attach some numbers.

These numbers - on a line from 0 to infinity (essentially) - give an estimate of how long the task will take, along with the confidence in that estimate, and the probability of it taking any of the times between 0 and infinity.

It's a pretty simple trivariate calculation that takes into account 3 figures:

> 1. The best possible outcome you can imagine, given nothing goes wrong and you're left to it.
> 1. Your initial off-the-cuff estimate, given based on your experience, aka 'the bullshit one you would usually give'.
> 1. The worst possible outcome, following [Murphy's Law](https://en.wikiquote.org/wiki/Murphy%27s_law) (everything that can go wrong, will go wrong).

 > ```
Te = ((best + (4 * guess) + worst) / 6)

SD = ((best - worst) / 6)
```

I've created a little npm module called [pertestimate](https://www.npmjs.com/package/pertestimate) to help with giving these estimates nice and quickly. You can [view it on Github](https://github.com/jacobwarduk/pertestimate) and contribute or leave any feedback there.

It's pretty simple to use, either from the command line for when you need to give those quick estimates, or integrate with something like [Chart.js](http://www.chartjs.org/docs/latest/) to give a more visual representation of what it's going to take.

You can install the module using `npm install --save pertestimate`

It can take 2 inputs. The first required one being the 3 figures we previously discussed. The second one being a callback function to execute on the results.

`pertestimate(guesstimates [, callback(results)]);`

Let's take a look how to use it and up our estimation game in a matter of minutes.

```javascript
const pertestimate = require('pertestimate');

const guesstimates = {
    best: 3,
    guess: 10,
    worst: 20
}

pertestimate(guesstimates);
// --> {estimate: 10.5, deviation: -2.8333333333333335}

pertestimate(guesstimates, res => `Around ${res.estimate}, with a standard deviation of ${res.deviation}. So, most likely somewhere between ${Math.ceil(res.estimate + res.deviation)} and ${Math.ceil(res.estimate - res.deviation)}.`);
// --> "Around 10.5, with a standard deviation of -2.8333333333333335. So, most likely somewhere between 8 and 14."
```

So now you can go tell your boss:

 > It _could_ take forever, but realistically it can be completed within around 8 to 14 days.


