---
date: '2026-05-30T12:41:57+02:00'
draft: false
title: 'Most Claude Code Skills Are Too Big'
description: 'Why a 10-line Claude Code skill often beats a 500-line bundle with reference docs and scripts — and when the big skill is actually worth it.'
tags: ['claude-code', 'ai-coding', 'productivity', 'skills']
---

> ℹ️ This is the third post in a series on configuring Claude Code. The previous post was about [giving Claude Code the right context]({{< ref "claude-code-memory" >}}) without bloating every prompt.

## The Mental Model

You have skills. In fact, you probably have many skills. You know how to cook pasta. You know how to operate your oven, with all its quirks. You may also have some less common skills, like changing the wheels on your car. None of these alone are big things — knowing how to cook pasta isn't a 40-page manual, it's a handful of steps you reach for when you're standing at the stove. And you don't hold "change a wheel" in your head while cooking; it surfaces only when you need it. But each of these small, compact skills plays an important role in your life.

Skills for Claude Code are the same. It's not the individual skills alone that give Claude Code what it needs to solve your problems. It's the entirety of all its skills (and other tools, which we covered in earlier posts) — each one small, and each one recalled only when it's relevant.

## What Should You Make a Skill For?

First of all, it depends (surprise). But don't skip lightly over this. In fact, I would argue that it's more important to write skills for the right things, than it is to write skills the right way. The perfect skill for the wrong task won't help you. A sub-optimal skill, which solves the right task, will. But what is the right task?

Claude Code has gotten to a point where it is extremely powerful at general tasks, without any additional instructions. So don't give it skills for using Git, writing C# or any other generic task. But Claude Code isn't psychic (yet). It doesn't know exactly how **you** prefer things done. So while it knows **how to use Git**, it does not know **how you like your commit messages**. So a Git skill which focuses on your Git preferences can be useful. And notice how small that skill can be: you don't re-explain Git, you write a few lines about your commit style. Claude already holds the general scaffolding — you only document the difference.

Another thing Claude Code also doesn't know about is your company's 15 year old legacy codebase, which has been designed by 17 different architects since its origin. Think back to when you started at your company. All the things you had to read up on. All the *skills* you had to acquire, to be able to work on that codebase. Maybe you need a skill that tells Claude Code to clear a folder and create a fresh SQLite database before running the test suite. Because this is not general knowledge.

> 💡 Skills are good for tasks/areas which are specific to your situation. Avoid making skills for general knowledge areas.

## How to Write a Good Skill

While there are exceptions, most skills should start out very small. And just like writing code should be an iterative process, so should writing skills. You don't need to nail it in the first attempt (you won't). At worst, the skill will start off slightly annoying, but assuming that you have a [healthy setup for Claude Code]({{< ref "claude-permissions" >}}), you won't *accidentally* write a harmful skill. For example, if you want a Git commit skill with your preferences, try this:

```md
---
name: commit
description: Commit changes to Git using the user's preferences for the commit message.
---
Use the following conventions when writing a Git commit message:
- Use imperative mood - e.g. "Fix bug (...)" instead of "Fixed bug (...)"
- For small changes write a single line concise commit message
- If changes are more complex write a title in the first line, then a blank line and a bullet point list with each change description (avoid long prose)
```

The above skill is not perfect. But it's a good start. If you try it, you will get commit messages, which are more consistent with your preferences, even if it sometimes doesn't work exactly as intended. And when that happens, you update the skill targeting that specific misbehavior, so that next time it will do better. Crucially, adjusting a skill doesn't only mean adding to it. Just as often the fix is to re-phrase a line that was misread, or delete one that wasn't pulling its weight. The goal is a skill that stays small and sharp — not one that grows a new paragraph every time it slips.

> 💡 If you have an idea for a skill, just write a quick draft and try it. Then adjust it, when it doesn't do exactly as intended.

## When Bigger is Better

While skills are **often** better off small, it may not always be possible. A skill earns its size when the knowledge it carries is large, precise, and absent from Claude's training — something uncommon, proprietary, or that has to be exactly right. At work, I have a skill which explains to Claude Code how to communicate with Azure DevOps, using `curl`. The skill alone is 200 lines, includes 3 references and a shell script. This was necessary, because using Azure DevOps this way isn't all that common. I tried less, but it simply didn't work. But the skill started off small, and only grew when necessary. If you do it this way, you are more likely to create healthy-sized skills. Even when they need to be larger.

> 💡 Sometimes small skills aren't right for a certain task. But you should still aim to start with less than you expect to end with.

## Conclusion

I feel that most of the posts I see about skills are overcomplicating it. In the end Claude Code skills boil down to: If you keep re-explaining the same thing to Claude Code over and over again (for example your commit message preferences), make a skill for it. Getting the feel for writing skills for the right things is much harder than writing a good skill.

Don't overthink it. Worst case scenario, you just remove the skill again (I have done that quite a lot).
