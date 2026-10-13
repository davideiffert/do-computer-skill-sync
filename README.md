# skill-sync

A Dood in [Do Computer](https://github.com/SawyerHood/do-computer) keeps its own skills. The skills you
already wrote for Claude Code, Codex or other agents live in folders on your computer. skill-sync keeps
the two in step, both ways, so you edit a skill once and every agent has it.

## Try it

Tell your Dood:

> Install https://github.com/davideiffert/do-computer-skill-sync/tree/main/skill-sync and set it up.

It finds the skill folders on the computers it can use (`~/.claude/skills`, `~/.codex/skills`,
`~/.agents/skills`, or any you name), asks which skills to sync, makes a daily automation, and runs the
first sync. Your Dood needs a computer you paired that has those folders.

## What it does

Each run, for every skill you chose, it compares three things: the folder on your computer, the Dood's
copy, and what both looked like at the last sync. It compares content, not file dates, using a hash of
every file in the skill.

| What changed | What happens |
|---|---|
| Only the folder | The Dood's copy is updated. |
| Only the Dood's copy | The folder is updated. |
| Both | The Dood merges the two, keeping every real change, and writes the result to both. On a true conflict the folder's version wins. |
| A skill appeared in the folder | The Dood gets it. |
| A folder disappeared | Syncing stops for that skill, the Dood keeps its copy, and it is reported. |

It ends with one short message in the automation's chat: what moved, what was merged, and anything
skipped. On days when nothing changed, it posts nothing.

## What it can touch

- **Reads** the skill folders you named, on computers your Dood can use.
- **Writes** changes to those skills, in the folder and in the Dood.
- **Keeps** a small record in your Library (`skill-sync/<Dood name>.json`).
- **Creates** one daily automation when you set it up.

The Dood is told never to delete a whole skill or folder and never to touch skills you did not name. It
only removes a file inside a synced skill when the other side removed it. Those are
instructions to the model, not code.

## Limits

- **It is an experiment** (v0.1.0), first used by its author in October 2026.
- **The model does the merging.** Read the report after a merge, at least at first.
- **A computer that is asleep or offline is skipped** for that run.
- **The always-on skill list** shows about 40 skills; more still work when asked for by name.
- **Needs `sha256sum`** (Linux) or **`shasum`** (macOS) on the computer with the folders. Both come with
  the system.
- **There is no code to test.** The skill is one Markdown file.

## License

MIT
