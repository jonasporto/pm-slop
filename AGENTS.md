# pm slop, for contributors and agents

One skill is one PM question. The folder name under `skills/` is the question
in kebab-case (`how-are-we-doing-on-this`). Each question also has an alias
folder named after the acronym people actually type (`hawd`). The alias holds
no logic: its `SKILL.md` calls the Skill tool with the full name and falls
back to reading the sibling `SKILL.md`. One body, two names.

Every skill and every alias appears in three places: `README.md` (name
linked to its `SKILL.md`), the `skills` array of `.claude-plugin/plugin.json`,
and its own `agents/openai.yaml` with `display_name` and `short_description`
for the Codex skill picker. Run `claude plugin validate . --strict` after
touching a manifest and `npx skills add . -l` to confirm discovery.

All skills are model-invoked: the frontmatter `description` lists the ways
the question is asked in real life, because that is what fires the skill when
nobody types the slash. Keep it that way.

Rules the skills follow, and the review checks for:

- Evidence or label. A done item names a commit. A next step names its source
  (plan, PR body, review, failing check, TODO) or carries `(inferred)`.
- Read only. A skill may offer to write something back; it never writes
  without a yes.
- Short. The PM wanted one line; give the engineer twenty.
- The prompt stays small: the question, the things to report, one evidence
  rule. Recipes and templates go in the answer, not the prompt.

Prose rules: English, no em-dashes anywhere (rewrite the sentence with a
comma, colon, period or parentheses), absolute dates in examples.

Versioning: bump `version` in `.claude-plugin/plugin.json` and add an entry to
`CHANGELOG.md` in the same commit.
