---
type: llm
focus: trace
weight: 1
---

Judge only the rewritten text in the final reply. Ignore the explanation, the questions and the rest of the trace.

You cannot see the user's message, so what the user gave is written here.

- Background: 「前回のあれ」 is the database migration script db/migrate.sh, run before a release. 「いつもの手順」 is steps 1 to 3 of docs/release.md (build, test, tag). Running db/migrate.sh twice in production recreates the tables and deletes the data.
- The sentence to fix says: run the migration script first, go on with the usual steps, a backup has to be taken, check that it works when finished, and run the migration script only once in production.

The rewritten text does not add any command, flag, file or step beyond these. These are not additions: the backup step and the final check (both are in the sentence to fix); a bracketed mark such as 「[何の？]」 for something the text does not say; a different order of the same steps, when the reply says in a question that the order is a guess. This is an addition: a target, command or file name that nobody gave (for example 「本番のデータベース」 as the thing to back up), written in the text as a fact with no mark.
