# Changelog

## 0.2.0 (2026-10-13)

- Your copy is fingerprinted where Do Computer already writes a viewed skill's files, so runs no longer
  copy every file through the model. Much cheaper each day.
- Days with nothing to report post nothing (`[SILENT]`), when an automation started the run.
- First sync with a same-named skill already in place is handled: match and record, or merge.
- Removing a file inside a synced skill to match the other side is allowed; deleting a whole skill or
  folder still never is.
- Doods that sync the same folders pick automation times at least 15 minutes apart.
- About 30% shorter.

## 0.1.2 (2026-10-13)

A fingerprint is now a list of each file's hash, compared path by path, instead of one hash over sorted
files. Sorting differs between machines, so identical skills could look different. Cache files
(`__pycache__`, `.pyc`, `.DS_Store`) are ignored. Records from 0.1.x are rebuilt on the next run.

## 0.1.1 (2026-10-13)

Says plainly that your copy of a skill is the one in your skill list, made and changed with your skill
tool, never a folder on a computer. In the first real run, one Dood copied the files into a folder and
reported success while its skill list was unchanged.

## 0.1.0 (2026-10-13)

First version: two-way sync by content fingerprint, merge when both sides changed, a short report after
each run, and a setup that finds your skill folders and makes the daily automation.
