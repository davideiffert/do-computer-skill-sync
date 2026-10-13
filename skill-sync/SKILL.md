---
name: skill-sync
description: Keep some of your skills in step with skill folders on your computers (for example ~/.claude/skills or ~/.agents/skills), both ways. Use when an automation or the person asks to sync skills.
---

# Skill sync

You keep some of your skills in step with folders on computers you can use, so the person edits a skill
once and every agent of theirs has it. Both sides use the same `SKILL.md` format.

## Setting up

When the person asks you to set up skill sync:

1. **Find their skill folders** on each computer you can use: `~/.claude/skills`, `~/.codex/skills`,
   `~/.agents/skills`, and any they name. A skill is a folder holding a `SKILL.md`; follow symlinks.
   Show each folder and how many skills it holds.
2. **Ask what to sync**: whole folders, some skills, or a folder except a few. Suggest a choice, and
   mention that your skill list in the prompt has room for about 40 skills; the rest still work by name.
3. **Make one daily automation** at 5:00 in their time zone, unless they want another time. If another of
   their Doods already syncs any of the same folders (its record in `~/library/skill-sync/` says when),
   pick a time at least 15 minutes away. The automation's instructions are "Use skill-sync. Sources: "
   and the sources as chosen, for example "my laptop, every skill in ~/.claude/skills except drafts; my
   server, from ~/.agents/skills: writing, research."
4. **Run the first sync now.**

## Sources

Each source is a computer and some skills on it: a skill folder, a folder plus names, or a whole folder
with optional exceptions. Only those skills are synced. If two sources offer the same name, the first
wins and you report the other. A computer you cannot reach is skipped this run.

## Your copy

Your copy of a skill is the one your skill tool keeps: `list`, `view`, `create`, `write_file`, `patch`,
`remove_file`. It is never a folder you made. After a run, every synced skill shows in `list`.

## Fingerprints and the record

A fingerprint is each file's path and content hash. On the source, from inside the skill's folder:

```sh
find . -type f ! -name .DS_Store ! -name '*.pyc' ! -path '*/__pycache__/*' -exec sha256sum {} +
```

(`shasum -a 256` on a Mac.) For your copy, `view` the skill: Do Computer writes its files to a folder on
the computer you are working on, `.agents/skills/<name>` in your working folder. Hash exactly the files
the skill lists, in that folder. Compare fingerprints path by path, never by file dates or by sorting
lines, since sorting differs between machines.

Keep the record at `~/library/skill-sync/<your name>.json`: your automation's time, and for each skill
its source, the fingerprint both sides had after the last sync, and when.

```json
{ "time": "05:00", "skills": { "writing": { "source": "my-server:~/.agents/skills/writing", "files": { "SKILL.md": "3f1a…" }, "syncedAt": "2026-10-13T08:00:00Z" } } }
```

## Each run

For each skill, compare the source (F) and your copy (Y) with the record (R):

| Situation | Do |
|---|---|
| F = Y | Nothing; record the fingerprint if it is missing. |
| F changed, Y = R | Update your copy from the source. |
| Y changed, F = R | Update the source from your copy. |
| Both changed, or both exist with no record | Merge, then write the result to both. |
| Only the source exists | Create your copy. |
| Only your copy exists | Stop syncing it, keep your copy, and report it. |
| Neither exists | Report the path. |

Updating a side means making its files match, including removing a file the other side no longer has.
Then fingerprint both again; if they differ, report it and leave that record entry as it was.

**Merging** keeps every real change from both sides. Where they truly contradict, keep the source's
version, since the person's other tools already use it, and report the conflict. Keep `name` equal to
the folder name.

## Report

Reply in the run's chat with one line per change ("writing: edit from my server came in"; "research: my
fix went out"), what each merge took from each side, and anything skipped. If nothing changed and an
automation started the run, reply exactly `[SILENT]` so no message is posted; if the person asked, say
"Skills in sync."

## Rules

- Never delete a whole skill or source folder, and never touch skills the sources do not name.
- Syncing moves and merges. It does not rewrite what a skill says.
- Do not sync this skill. It updates from wherever it was installed from.
