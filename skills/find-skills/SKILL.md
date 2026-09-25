---
name: find-skills
description: Find and install agent skills from the Skilly catalog using skillycli or the public search API when a user asks for a skill, workflow, or specialized capability.
---

# Find Skills with Skilly

Use the Skilly catalog, `skillycli`, or the public Skilly API to discover agent skills. The CLI searches Skilly at `https://skilly.sh`; do not use the Vercel Skills CLI or its directory for this workflow.

## Find a skill

1. Turn the user's request into a specific search query. Include the task and relevant framework or language.
2. Search with `npx skillycli find <query>` or the public API below. Use `--owner <github-owner>` when the user wants skills from a particular publisher.
3. If results are weak, try a related query or browse [Skilly](https://skilly.sh/). Do not treat install counts alone as a quality signal.
4. Inspect each promising result's description, source repository, and `SKILL.md` before recommending or installing it. Check that its instructions fit the user's task and that the source is trustworthy.
5. Give the user the skill name, what it does, its source, the Skilly result link when available, and a working install command.

Examples:

```bash
npx skillycli find react performance
npx skillycli find pr review
npx skillycli find accessibility --owner microsoft
```

## Search through the public API

`GET https://skilly.sh/api/v1/skills/search` needs no authentication. Supply `q` for broad search across skill names, descriptions, categories, source repositories, and `SKILL.md` instructions. The query must have at least two characters. For a targeted search, use one or more of `name`, `description`, `category`, `source`, and `instructions`; these filters combine with AND. `owner` restricts results to a GitHub owner. `limit` defaults to 50 and cannot exceed 200.

URL-encode query values. For example:

```bash
curl --get 'https://skilly.sh/api/v1/skills/search' \
  --data-urlencode 'q=database migration' \
  --data-urlencode 'limit=10'

curl --get 'https://skilly.sh/api/v1/skills/search' \
  --data-urlencode 'name=frontend' \
  --data-urlencode 'category=Development' \
  --data-urlencode 'format=text'
```

JSON is the default response. Its `data` array includes each result's name, description, source, install URL, and canonical URL. Add `format=text` for a plain-text result list. If the API responds with HTTP 429, wait for the `Retry-After` interval before retrying. See [Skilly's search guide](https://skilly.sh/docs/search) for the current query syntax.

## Install a selected skill

Use the source and skill name reported by Skilly. The CLI accepts a repository shorthand and a skill selector:

```bash
npx skillycli add <owner/repo> --skill <skill-name> -g -y
```

`-g` installs at user level and `-y` skips CLI prompts. Omit `-g` for a project-level install. If the user has not asked for installation, provide the command without running it.

## Other commands

```bash
npx skillycli list
npx skillycli check
npx skillycli update
npx skillycli init my-new-skill
```

If no suitable skill exists, say so and offer to handle the task directly or create a new skill with `npx skillycli init`.
