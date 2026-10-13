---
name: skill-sync
description: Keep some of your skills in step with skill folders on your computers (for example ~/.claude/skills or ~/.agents/skills), both ways. Use when an automation or the person asks to sync skills.
---

# Skill sync

You keep some of your skills in step with folders on computers you can use, so the person edits a skill
in one place and you have it natively, and what you improve reaches their other tools. Both copies stay
in the same `SKILL.md` format, so nothing is translated.

## Setting up

When the person asks you to set up skill sync, or just installed this skill and said "set it up":

1. **Find their skill folders.** On each computer you can use, look for `~/.claude/skills`,
   `~/.codex/skills` and `~/.agents/skills`, and any folder they name. A skill folder holds a `SKILL.md`.
   Folders may be symlinks; follow them. Show what you found: each folder and how many skills are in it.
2. **Ask what to sync.** Whole folders, some skills from a folder, or a folder except a few. Suggest a
   choice from what you know of them, and say that your always-on skill list shows about 40 skills
   before it stops growing (the rest still work, by name).
3. **Make the automation.** Create one daily automation, at 5:00 in their time zone unless they want
   another time. Its instructions are "Use skill-sync." followed by the sources exactly as they chose
   them, for example: "Use skill-sync. Sources: my laptop, every skill in ~/.claude/skills except
   drafts; and my server, from ~/.agents/skills: writing, research."
4. **Run the first sync now**, and end with the report below.

To change what is synced later, they edit the automation, or ask you to.

## What you are told

The request (usually an automation's instructions) names one or more **sources**. Each source is a
computer you can use and the skills on it, given in any of these ways:

- A skill folder, such as `~/.agents/skills/writing-voice`. The folder's name is the skill's name.
- A folder of skills plus names, such as "from `~/.agents/skills`: writing-voice, research".
- A whole folder of skills, such as "every skill in `~/.claude/skills`", optionally "except" some names.
  A skill folder is any folder directly inside it that holds a `SKILL.md`.

Only the skills named this way are synced. Your other skills are yours alone, and other folders on the
computer are left alone. If two sources offer the same skill name, the one named first is synced and the
other is reported.

A computer that is not reachable right now is skipped for this run and reported; nothing about its skills
changes.

## Your copy

Your copy of a skill is the one in your own skill list, which you manage with your skill tool: `list`
shows them, `view` reads one (and its other files, with `path`), `create` adds one, and `write_file` and
`patch` change its files. Your copy is never a folder on a computer. Copying files into a folder does not
give you the skill; only the skill tool does. After a run, every synced skill must show in `list`.

## The record

Keep a record in your Library at `~/library/skill-sync/<your name>.json` (create it if needed). For each
synced skill it holds where the skill came from, the **fingerprint** both copies had at the last sync,
and when:

```json
{ "skills": { "writing-voice": { "source": "my-server:~/.agents/skills/writing-voice", "fingerprint": "3f1a...", "syncedAt": "2026-10-13T08:00:00Z" } } }
```

A fingerprint is a hash of a skill folder's exact contents: every file's path and text, in a fixed order.
From inside the folder:

```sh
find . -type f ! -name .DS_Store | LC_ALL=C sort | while IFS= read -r f; do printf '%s\n' "$f"; cat "$f"; done | sha256sum
```

On a Mac use `shasum -a 256` in place of `sha256sum`. Fingerprint your own copy the same way: read
each of its files with your skill tool, write them into a scratch folder in your working folder, run the
command there, then remove the scratch folder. Equal fingerprints mean equal
content. Do not compare file dates; copying a file changes its date without changing it.

## Each run

For every skill, fingerprint the source folder (F) and your copy (Y), compare both with the record (R),
and do exactly one of these:

| Situation | Do |
|---|---|
| F = Y | Nothing, except write the fingerprint to the record if it is missing. |
| F changed, Y = R | Update your skill from the folder. |
| Y changed, F = R | Write your version into the folder, file by file. |
| Both changed | Merge (below), then write the result to both. |
| Folder exists, you have no copy | Create your skill from the folder. |
| You have it, the folder is gone | Stop syncing it: keep your copy, drop it from the record, report it. |
| Neither exists | Report the path as wrong. |

After a change, fingerprint both again. They must match; if they do not, say so and leave that skill's
record as it was, so the next run tries again. Then save the record.

## Merging

When both copies changed, make one skill that keeps every real change from both sides: added steps, fixed
facts, new examples, tightened wording. Where the two truly contradict, keep the folder's version, since
the person's other tools already use it, and note the conflict. Keep the frontmatter valid, with `name`
matching the folder and a `description`. Do not restyle or shorten text that only one side touched.

## Report

End every run with one short message in the chat the run belongs to:

- What changed, one line per skill and direction ("writing-voice: the edit on my-server came in";
  "slide-formatter: my fix went out").
- For each merge: what came from each side, and any conflict and how you settled it.
- Computers skipped and anything you could not do, with the reason.

When nothing changed, say "Skills in sync" and nothing more.

## Rules

- Never touch a skill or folder that the request does not name.
- Never delete anything. Report instead.
- Skills you write yourself stay yours until the person names them in a source.
- Syncing moves and merges. It does not change what a skill says.
- Do not sync this skill itself. It is installed from wherever the person got it and updated from there.
