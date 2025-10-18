---
name: blog-review
description: Review technical blog posts (markdown files) for a Hugo static site. Use when asked to review, check, or provide feedback on blog post drafts. Checks Hugo frontmatter, SEO optimization, content quality (conciseness, clarity, fluff), code formatting consistency, and technical accuracy.
---

# Blog Review

## Blog Context

This blog is a Hugo static site about software development topics, published at https://builditbusk.github.io/blog/. Posts are written in markdown with YAML frontmatter.

**Content goals:**
- **Concrete**: Actionable tips users can apply immediately
- **Concise**: Short posts, no unnecessary words
- **Consistent**: Uniform formatting and style

**Audience**: Software developers looking for practical everyday tips

## Review Checklist

When reviewing a blog post, check these areas systematically:

### 1. Hugo Frontmatter

**Required fields:**
```yaml
---
date: '2025-02-18T15:51:33+01:00'
draft: false
title: 'Post Title'
description: 'Clear meta description for SEO (120-160 chars)'
tags: ['tag1', 'tag2', 'tag3']
---
```

**Check:**
- `description` field exists (Hugo doesn't require it, but SEO does)
- `tags` field exists with 3-5 relevant tags
- `title` is descriptive and specific (not vague)

### 2. SEO Optimization

**Title issues:**
- ❌ Vague: "Five things you may need to do with Git"
- ✅ Specific: "5 Essential Git Commands for Daily Development"
- ✅ Specific: "Git Commands to Simplify Your Workflow"

**Checks:**
- Title includes searchable keywords (what would someone Google?)
- Description summarizes value proposition in 120-160 characters
- Tags are relevant technical terms people search for
- Headings (H2, H3) use clear, descriptive phrases

### 3. Content Quality

**Opening paragraphs:**
- ❌ Fluffy: "Git is incredibly powerful, but many developers stick to the same basic commands without exploring its full potential."
- ✅ Concrete: "Here are 5 Git commands that will save you time every day. You probably know Git basics—these commands handle the common annoyances that slow you down."

**Flag these patterns:**
- Generic introductions ("incredibly powerful", "many developers", "without exploring its full potential")
- Unnecessary hedging ("you might", "you may want to")
- Blogging clichés ("in this post I'll walk you through", "let's dive in")
- Repetitive explanations (saying the same thing twice)

**Enforce:**
- Get to the point immediately
- Every sentence must add value
- Use active voice and simple language
- Keep examples minimal but sufficient

### 4. Code Formatting Consistency

**Check:**
- Inline code for simple commands: `git add -A`
- Code blocks for multi-line examples or output
- Consistent syntax highlighting (specify language: ```bash, ```python, etc.)
- Code examples are minimal and focused

**Format:**
```markdown
Use `inline code` for commands and short snippets.

Use blocks for longer examples:
```bash
git commit --amend --no-edit
```
```

### 5. Technical Accuracy

**Verify:**
- Command syntax is correct
- Explanations match actual behavior
- Examples work as shown
- Edge cases or warnings are mentioned when important

**Example of good warning:**
> ⚠️ **If you ever commit a secret and push it to a remote repository, reverting is not enough — change the secret immediately.**

### 6. Post Length

Target: 300-800 words (roughly 2-5 minute read)
- Too short (<300): May lack substance
- Too long (>800): Contradicts "short posts" goal

## Review Output Format

Provide feedback in this structure:

**Critical Issues** (must fix before publishing):
- Missing/incorrect frontmatter
- SEO problems (vague title, missing description, no tags)
- Technical inaccuracies

**Improvements** (should fix):
- Fluffy language to cut
- Formatting inconsistencies
- Better word choices

**Suggestions** (optional):
- Alternative phrasing
- Additional examples or context
- Related topics to link

**Strengths** (what works well):
- What the post does right

Be specific. Quote the problematic text and suggest concrete alternatives.

## Example Review Feedback

**Critical Issues:**
1. **Missing SEO fields** - Add `description` and `tags` to frontmatter
2. **Vague title** - "Five things you may need to do with Git" → "5 Essential Git Commands for Daily Development"

**Improvements:**
1. **Fluffy opening** - Cut "Git is incredibly powerful, but many developers stick to the same basic commands without exploring its full potential." Start with: "Here are 5 Git commands that will save you time every day."
2. **Inconsistent code formatting** - Use inline code for all simple commands: `git add -A`

**Strengths:**
- Clear structure with numbered sections
- Good warning about secrets in commits
- Practical examples throughout
