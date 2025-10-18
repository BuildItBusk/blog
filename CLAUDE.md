# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a personal blog built with Hugo (static site generator) and deployed to GitHub Pages. The blog uses the PaperMod theme (included as a git submodule) and is currently hosted at https://builditbusk.github.io/blog.

## Development Commands

### Building and Running

```bash
# Start local development server with live reload
hugo server -D

# Build the site for production (outputs to ./public/)
hugo --gc --minify

# Build with drafts included
hugo -D
```

### Content Management

```bash
# Create a new post using the archetype template
hugo new content/posts/post-name.md

# Note: New posts are created with draft: true by default
# Change to draft: false when ready to publish
```

### Theme Management

The PaperMod theme is included as a git submodule. When cloning or updating:

```bash
# Clone with submodules
git clone --recurse-submodules <repo-url>

# Update submodules after cloning
git submodule update --init --recursive

# Update theme to latest version
cd themes/PaperMod
git pull origin master
cd ../..
git add themes/PaperMod
git commit -m "Update PaperMod theme"
```

## Architecture

### Directory Structure

- `content/posts/`: All blog posts in Markdown format
- `themes/PaperMod/`: Hugo theme (git submodule, do not edit directly)
- `archetypes/default.md`: Template for new posts (includes frontmatter with date, draft status, and title)
- `hugo.yaml`: Site configuration (baseURL, title, theme, navigation menu)
- `.github/workflows/hugo.yaml`: GitHub Actions workflow for automated deployment

### Content Organization

Blog posts live in `content/posts/` and use Hugo's frontmatter format:

```yaml
---
date: 'YYYY-MM-DDTHH:MM:SS+TZ'
draft: false
title: 'Post Title'
---
```

New posts are created as drafts by default. Set `draft: false` to publish.

### Deployment

The site automatically deploys to GitHub Pages on push to main branch via GitHub Actions. The workflow:
1. Builds the site with Hugo 0.143.0 (extended version)
2. Runs `hugo --gc --minify` for production build
3. Deploys the `./public` directory to GitHub Pages

Manual deployment is not necessary - simply push to main.

## Configuration Notes

- **baseURL**: Currently set to `https://example.org/` in hugo.yaml (update when domain changes)
- **Hugo Version**: 0.143.0 (extended) - specified in .github/workflows/hugo.yaml:35
- **Navigation**: Configured in hugo.yaml under `menus.main` (currently: Home and Posts)
- **Theme**: PaperMod is the only theme and is loaded from themes/PaperMod submodule
