---
date: '2026-04-26T07:21:54+02:00'
draft: false
title: 'Stop Writing One Big CLAUDE.md'
description: 'CLAUDE.md, sub-directory CLAUDE.md, rules, and codebase structure: how to give Claude Code the right context without bloating every prompt.'
tags: ['claude-code', 'ai-coding', 'productivity', 'documentation']
---

> ℹ️ This is the second post in a series on configuring Claude Code. The last post was about [configuring Claude Code permissions]({{< ref "claude-permissions" >}}). I would recommend reading it, but it's not required for the understanding of this post.

## The Consultant Mental Model

Imagine a consultant joins your team for a week — not the suited-up McKinsey type, but a sharp, fast developer here to help you ship the feature or fix the bug you've been putting off. The only thing between you and a productive week is whether you can onboard them to the part of the codebase they need to touch.

This consultant isn't human. They'll read every word you give them — but in the absence of guidance, they'll start reading the codebase itself, file by file, burning an hour and half their working memory before writing a line.

## The Entry Point

Unless you are brand new to Claude Code, you already know of the [CLAUDE.md](https://code.claude.com/docs/en/best-practices#write-an-effective-claude-md) file. For this post you only need to understand that there are three different variants of this file.

- `~/.claude/CLAUDE.md` - Your user-scoped version. The content of this is included in every prompt, in every project.
- `<your-project>/CLAUDE.md` - The project-scoped version. The content of this is included in every prompt, when Claude Code is started from that directory (i.e. when you are working on that project).
- `<your-project>/Feature/CLAUDE.md` - Sub-directory versions. You can add as many as you want. Included whenever Claude Code touches code in that directory (including sub-directories).

The key takeaway is that two of these three files are included in every prompt. The project one will vary between projects, but it will always be there. That means you should **only include general information** in these.

> 💡 Aim to keep the user- and project-scoped `CLAUDE.md` files to less than 100 lines.

## Sub-directory CLAUDE.md

You can add `CLAUDE.md` files in any sub-directory as well (e.g. `<your-project>/Feature/CLAUDE.md`). This version will only be loaded when Claude Code touches files inside that directory, or any directory below it. **I believe this is the most underrated Claude Code feature**.

When you are working in a session with Claude Code, you almost always focus on a subset of the codebase. Using local `CLAUDE.md` files gives you an opportunity to add important context on a specific part of the codebase (maybe a particularly tricky part), without introducing noise, when you are working on other parts (you can also do this with [rules](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/) — we will get to that later). You may add as many — or few — of these files as you want. You can even nest files, so you can add `<your-project>/Feature/Types/CLAUDE.md` if you have some context which is relevant only to that part of the code.

> 💡 Liberally add sub-directory `CLAUDE.md` files. Even if they only contain 1-2 bullet points of extra context.

## Rules

[Claude Code rules](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/) are markdown files placed in `.claude/rules/` — either user- or project-scoped. You can add filters to rules, but if you don't, they work exactly like `CLAUDE.md` and are included in every prompt. You can add a path filter, to narrow the scope of a rule:

```md
---
paths:
  - "Feature/Types/"
---
```

This has the same effect as a sub-directory `CLAUDE.md` file. I prefer the latter, as it is located where it applies, not hidden in a specific rules directory. The good use case for rules is if you use the path filter to target specific extensions:

```md
---
paths:
  - "**/*.md"
---
```

This can be useful if you have rules which apply to a specific file type (in the example above, Markdown). It is particularly effective if it's a type of file which there are relatively few of, but they are scattered across the project.

> 💡 Use rules to add context for specific file types — especially file types which aren't dominant in the project.

## Comments, Naming and Structure

The most important things when onboarding other developers to a project are _comments_, _naming_ and _structure_. Without these, the difficulty of understanding the codebase drastically increases. GenAI hasn't changed this. If anything, it has made it even more important. Claude Code favors tools like **grep**, **glob** and **find** for searching the codebase. They work on file names and content. So if you do something like:

```
src/
├── Billing/
│   ├── InvoiceCalculator.cs
│   ├── BillingRepository.cs
│   └── UsageTracker.cs
└── Auth/
    ├── SessionValidator.cs
    └── PasswordHasher.cs
```

...instead of:

```
src/
├── Service.cs
├── Repository.cs
├── Manager.cs
└── Helpers.cs
```

A `grep -r "billing"` against the first tree lands on the `Billing/` folder and `BillingRepository.cs` immediately. Against the second, it returns nothing — Claude Code has to open every file and read it to figure out which one handles billing. Multiply that across a session, and Claude Code becomes slower, uses more tokens, and is more likely to make mistakes.

Comments can act as a file-scoped `CLAUDE.md`. I often start my C# classes (or interfaces) with a brief description of their purpose and any non-trivial quirks worth knowing when working on them. But use this as a last line of defense. Aim to build the majority of the context from structure and naming.

> 💡 Aim to make your codebase understandable by looking at its structure alone. Use in-file comments to fill any gaps.

## Pulling It Together

Each layer only loads when it's relevant: global files everywhere, project files per project, sub-directory `CLAUDE.md` and path-filtered rules where they apply, and naming and structure carrying the rest. Claude Code spends its tokens on your problem, not on figuring out where things live.