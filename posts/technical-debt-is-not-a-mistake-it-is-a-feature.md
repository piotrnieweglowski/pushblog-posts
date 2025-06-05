---
title: "Technical debt is not a mistake. It's a feature."
author: "Piotr Niewęgłowski"
date: "2025-05-25"
description: "Technical debt is often misunderstood. When incurred wisely, it can accelerate a project's success."
slug: "technical-debt-is-not-a-mistake-it-is-a-feature"
tags: ["pushblog", "build-in-public", "technical-debt", "strategic-thinking", "shipping-fast"]
---

## Writing the perfect code in the startup environment can be a waste of time

It's time-consuming, often over-engineered, and brings little business value to the company. These days, everybody expects things to happen almost immediately. People love short videos, TikToks, and you may think that nobody reads long, classic articles in serious newspapers anymore. We might not like it, but that's the reality we live in. Instead of complaining about the younger generation, you may take the best parts of this approach, tune them, and make a profit. Sound like a good plan? Bear with me, and read my view on technical debt.

## A ghost tale

When I was a junior engineer, my senior teammates used to scare me with a few things. The most frequent one was duplicated code. Also making the podium were logic in the database and (surprise, surprise!) technical debt. As I became more senior, I realized that all of those things can be a pain in the neck - or even desirable - depending on the context. Let's take a closer look at technical debt.

## Immature environment = more improvisation

In my opinion, the majority of greenfield projects need to incur some technical debt to be successful. This is not applicable to new projects being started at mature companies with well-defined in-house frameworks, CI/CD pipelines, testing strategies, and so on. They have the right tooling to do the job well. But in the very first phase of the product, you’re in a totally different place. 

As I wrote at the beginning of this post, you simply don't have time to make everything right. Your business will say thank you if you provide more features at a fast pace. They will not do the same if you tell them you provided 95% test coverage. Or even if you say you spent more time on this because you wanted to build a scalable solution for a million users. They won’t thank you because they don’t understand you. And maybe - there’s no need for such things at this stage.

## Time and cost

It's a common thing that people get money for their job. Everybody also accepts the fact that companies need to make money. Let’s oversimplify: `company profit = revenue from the product – salaries`.  
The more polished the code, the greater the risk that the result of the equation will fall below zero.

In a startup environment, the key thing is to build the first version of the product, ship it, and get user feedback. And here, tools like Cursor do the job. Thanks to them, products can be built much faster. Instead of 6 engineers implementing an MVP for half a year, it can be achieved by a team of 2 engineers within 2 months. Once it’s validated by the market and people love it - you’re golden. Otherwise, it will be thrown into the digital trash, and the financial losses will be a few times smaller than in the pre-AI reality.

## Your product is a blast - what should be done now?

The most important thing now is: you’re starting a refactor. Otherwise, your project can become unmaintainable, and the business will feel pain every time your team provides an estimate for a new feature. The second important thing is - you, as an engineer, need to do that in parallel with delivering new features. If you cannot do both of those activities on a daily basis - you’re cooked. You may try to explain this to your business, or just add some factor to every estimate, accounting for the extra effort required.

## A note - it’s not always that simple

Some time-consuming tasks are worth spending time on; others are not. Let me bring up a case where there’s no room for cutting corners, and everything needs to be done properly from day one. Imagine you’re developing software for medical devices. Or for the banking industry. I’m sure nobody would be happy with lowered standards. Customers would not understand why they have less money in their accounts just because you delivered your features within a blink of an eye. We need to remember that software engineering is often a game of finding the right balance. Every domain has its own requirements and constraints.

## How I approach technical debt in PushBlog

My plan is: deliver like a hacker, polish like a pro. In PushBlog, at the moment, I have zero automated tests. That’s technical debt incurred intentionally. Do I need them now? Not really. Does something really bad happen if there’s a bug in the application? Not really either. I’m not building accounting software, and it’s not critical at this stage. The product is not yet mature. I’m in the phase of delivering like a hacker.

Do I plan to add them? Yes! And that will be the phase of polishing like a pro. I built everything with the vision that at some point, I’d need to add tests - and I should be able to do that. As the product matures and more authors use it, it wouldn’t be great if the application crashed on a regular basis. Reputation is earned slowly and can be lost quickly. That’s why even in a “deliver like a hacker” mindset, I keep the long perspective in mind.

And remember - technical debt is not a failure. It's a debt. You just need to be aware of what you're betting on.
