---
layout: post
title: "Technical fanatism"
date: 2014-10-24 15:21:07 +0100
comments: true
categories: meta storyboards
---
Whenever I meet some developer, I can always get a really interesting conversation. Be it tools, opinions on platforms, favorite blogs, books, or
some new technique I didn't know about.
<br/>
That's why it's important to discuss with other people in the field their opinions, they challenge your assumptions and you can gain useful
insights which otherwise would be hidden to you for longer. But sometimes, you hit a wall. Somebody does not listen.
<!-- more -->
#Fanatism
Definition of [discussion](http://dictionary.cambridge.org/dictionary/british/discussion):
>**The activity in which people talk about something and tell each other their ideas or opinions:**
>
>- Management are holding/having discussions with employee representatives about possible redundancies
>- The matter is still under discussion (= being considered)
>- A discussion group/document
<br/>

By definition, if you were to discuss something with another person, it's always with the goal of sharing opinions, and learn from each other.
Then you discuss a controversial topic with a fellow developer:

- I do like storyboards, they have good use cases

Then you may receive such answers:

- My friend at Apple tells me they are a marketing move, you shouldn't use them

- Some very known group of devs (unimportant who), says they don't like storyboards. Why do you still use them?

My response is to shrug and try to explain that there is a use case for that tool. I give concrete examples, as well as my understanding where the drawbacks of using it lay. In other words, I choose a tool for the job at hand, knowing its advantages and disadvantages.
#The tool for the job
The previous It's surprising that young people, in 'modern' countries and 21st century, can still be religious about software platforms, programming languages
and tools. As for myself, discussing a technical topic with somebody makes more sense if the other person has different views. That way you learn and change your
assumptions a bit. That way you become better developer.

Strong opinions are good, they maintain you firm in your position. But you must know **why** you have such opinion, and **why** you chose that stand. Blind
execution and recital is best left for ants. When somebody will challenge your opinion, it may be good to reassure and check your assumptions, verify you think the same
**after** hearing somebody's use case. Maybe, after all, that other person has some knowledge to share with you.

That leads me to thinking why we get paid when we work as engineers. We are not paid to execute, but to think how to execute, and choose the best tool for
the job. The reason is simple, as an engineer you have the knowledge to decide how, and to be really effective at building something. Choose
the tool that fits your use case.
#Better answers
I would ask more questions, or would give such answers:

- Storyboards don't scale if you have many devs in the team, so keep them small
- Storyboards couple View Controllers with Navigation, it's easy to couple controllers to their environment. But I see your use case makes sense because
those three Controllers are not reusable by themselves, but the whole group is
- Storyboards are hard understand visually if you have many custom subviews. All you will see is white boxes. But yes, for your simple case
it may make sense. I just prefer using XIBs
- If you use multiple small storyboards, then you don't see the whole navigation across an application. But then as you said, the app is complex enough
that it doesn't fit in a single storyboard anyway. Dividing makes sense then. You can't have a perfect solution for everything
