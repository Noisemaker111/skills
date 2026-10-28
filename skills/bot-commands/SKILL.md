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
