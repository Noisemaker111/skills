---
name: bot-commands
description: Jon's short commands for his Grok Bot and the skill each one runs, plus how to turn a new repeatable flow into a skill. Use whenever Jon gives a short command, by @bot on X or in chat, or asks to make a flow repeatable.
---

# Bot commands

When a message from Jon matches a command below, run its skill exactly as written. Do not
improvise a different way to do the same thing.

| Jon says | Run |
| --- | --- |
| make and share skill | `make-and-share-skill` |
| save this as a command, make this repeatable, make this a skill (about a flow just done) | the steps under "New commands" below |
| reply `<text>` to this, post `<text>`, like this, repost this (anything that acts on X as Jon) | the steps under "Acting on X" below |

## Acting on X

Anything that posts, replies, likes, reposts or follows on X as Jon goes through Owl, which uses
Jon's own signed-in Chrome. Never use the X plugin or any other tool for it. Read posts with the
X plugin as usual.

- "this" is the post Jon replied under when he tagged @bot (the parent of his reply), not his own
  reply. Use its URL in the form `https://x.com/<handle>/status/<id>`.
- When Jon's message names the action and the exact text ("reply hi to this"), that is his
  approval: do it without asking again. If the text or the target is unclear, ask him first.

1. Executor `tools.owl.interact_start` with `mode: "browser"`, `timeout_seconds: 180`, and the
   task for the action:
   - reply: `Open <post URL> in Chrome. Reply to that post, not to any other post, with exactly this text and nothing else: "<text>". Press the Reply button once. Then check that the reply shows under the post and stop. Do not like, repost, follow or open anything else.`
   - post: `Open https://x.com/compose/post in Chrome. Write exactly this text and nothing else: "<text>". Press the Post button once. Then check that the post shows on the profile and stop. Do not like, repost, follow or open anything else.`
   - like or repost: `Open <post URL> in Chrome. Press the <Like or Repost> button on that post once, and only on that post. Then check that it shows as done and stop. Do not reply, follow or open anything else.`
2. Executor `tools.owl.answer` with the returned `call`. It gives the task id, or `offline` (Owl
   is not running) or `refused` (remote tasks are off). On `offline` or `refused`, tell Jon and
   stop. Do not retry, and do not fall back to the X plugin.
3. Every 20 seconds, at most 12 times: `tools.owl.interact_status` with the task id, then
   `tools.owl.answer` with that call. Stop at `done`, `blocked` or `failed`.
4. Tell Jon in one line: done, or blocked or failed. Never press the button a second time
   after `blocked` or `failed`: the post may already be up, so ask Jon to check.

## New commands

When Jon wants a flow you just did to be repeatable:

1. Write it as a skill in the format of `make-and-share-skill`:
   - every tool call with its exact path and fixed arguments
   - every text that gets posted or sent as a template with `<placeholders>`
   - judgement kept to as few steps as possible, and each one named
   - a stop rule for every failure
2. Name it with lowercase words and hyphens, and save it as one of your skills.
3. Publish it with Executor `tools["github-mcp-repo-writes-enabled"].push_files`
   (`owner: "Noisemaker111"`, `repo: "skills"`, `branch: "main"`) at `skills/<name>/SKILL.md`,
   and add its row to the table above in `skills/bot-commands/SKILL.md` in the same commit.
4. Tell Jon the command he can now say, and the skill's link
   `https://skills.sh/noisemaker111/skills/<name>`.

Fixed steps cost no thinking. If a step could be a plain tool call instead of a decision, make it
one.
