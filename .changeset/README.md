Every change that a user of the skills would notice gets a changeset:
`npx changeset`, pick patch or minor, write one or two sentences. The release
workflow turns pending changesets into a version PR that bumps `package.json`,
syncs `.claude-plugin/plugin.json` and writes `CHANGELOG.md`. Merging that PR
tags the release.
