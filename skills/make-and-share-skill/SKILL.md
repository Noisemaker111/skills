---
name: make-and-share-skill
description: Turn a post someone shared on X into an Agent Skill, publish it to github.com/Noisemaker111/skills so it has a skills.sh page, and reply under the post with the link through Jon's own browser via Owl. Use when Jon says "make and share skill", usually by tagging @bot under a post.
---

# Make and share skill

Writing the skill is the only step that needs judgement. Every other step is a fixed tool call:
copy the paths, arguments and texts below exactly.

## 1. Find the source

- Tagged with @bot on X: the source is the post Jon replied under (the parent of his reply), not
  his own reply. Read it with the X plugin. If it is part of a thread by the same author, read the
  whole thread.
- Sent in chat: the source is the post or link Jon sent.
- Keep the source post's URL in the form `https://x.com/<handle>/status/<id>`. You need it twice.

If the source has nothing that can be turned into steps or knowledge an agent can follow, stop and
tell Jon why. Do not publish an empty or vague skill.

## 2. Choose the name

- Lowercase letters, digits and hyphens, 2 to 5 words, at most 40 characters, saying what the
  skill does (`postgres-index-tuning`, not `cool-tip`).
- Check it is free: Executor `tools["github-mcp-repo-writes-enabled"].get_file_contents` with
  `owner: "Noisemaker111"`, `repo: "skills"`, `path: "skills/<name>/SKILL.md"`. If that file
  exists, add `-2` (then `-3`) and check again.

## 3. Write SKILL.md

```
---
name: <name>
description: <what it does, then "Use when ...". One or two sentences, at most 300 characters.>
---

# <Title>

<The steps or knowledge from the source, rewritten as instructions an agent can follow:
concrete commands, settings, numbers and checks. Keep everything the source teaches; drop hype,
greetings and engagement bait.>

## Source

Adapted from <source post URL> by @<author handle>.
```

Rules: `name` must equal the folder name. No private data: nothing about Jon, BTG, customers,
accounts or keys. Do not invent steps the source does not support.

## 4. Publish

Executor `tools["github-mcp-repo-writes-enabled"].push_files` with:

- `owner`: `"Noisemaker111"`
- `repo`: `"skills"`
- `branch`: `"main"`
- `files`: `[{ "path": "skills/<name>/SKILL.md", "content": "<the SKILL.md text>" }]`
- `message`: `"Add <name> skill, adapted from <source post URL>"`

## 5. Check the link

The link is `https://skills.sh/noisemaker111/skills/<name>`. Open it. It works when the page shows
the skill's SKILL.md. If the page says the skill "isn't available in this repository", wait 20
seconds and open it again, at most 3 times. If it still fails, tell Jon and stop: do not reply on X
with a broken link.

## 6. Reply on X through Owl

Post the reply only through Owl, which uses Jon's own signed-in Chrome. Never post with the X
plugin or any other tool.

1. Executor `tools.owl.interact_start` with:
   - `mode`: `"browser"`
   - `timeout_seconds`: `180`
   - `task`: `Open <source post URL> in Chrome. Reply to that post, not to any other post, with exactly this text and nothing else: "Made this into an agent skill: https://skills.sh/noisemaker111/skills/<name>". Press the Reply button once. Then check that the reply shows under the post and stop. Do not like, repost, follow or open anything else.`
2. Executor `tools.owl.answer` with the returned `call`. It gives the Owl task id, or a state:
   - `offline`: Owl is not running or its link is off.
   - `refused`: remote tasks are switched off on the PC.
   In either case tell Jon the link so he can post it himself, and stop. Do not retry.
3. Every 20 seconds, at most 12 times: `tools.owl.interact_status` with the task id, then
   `tools.owl.answer` with that call. Stop when the state is `done`, `blocked` or `failed`.

## 7. Report to Jon

One short message:

- the skill name and what it does, in one line
- the link `https://skills.sh/noisemaker111/skills/<name>`
- install: `npx skills add noisemaker111/skills --skill <name>`
- the reply: posted, or why not
