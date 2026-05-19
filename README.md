# concise-engineering-doc

A Claude Code skill for writing engineering planning documents — ADRs, design docs, RFCs, implementation plans, technical scopes — that humans will actually read carefully rather than skim.

## Requirement: the `humanizer` skill

This skill depends on the [`humanizer`](https://github.com/blader/humanizer) skill. Pass 3 of the workflow runs `humanizer` on the compressed draft to scrub residual AI tells (inflated symbolism, rule-of-three padding, em-dash overuse, "not just X but Y" constructions, etc.) before delivery. Install it alongside this one — without it, the final pass is skipped and the output will still read like an LLM wrote it.


## Install

First, install humanizer into `~/.claude/skills/humanizer/`:

```sh
git clone https://github.com/blader/humanizer.git ~/.claude/skills/humanizer
```

Then install this skill:
```sh
git clone https://github.com/bendavis78/concise-engineering-doc.git ~/.claude/skills/concise-engineering-doc
```

## What it does

Engineering docs written by LLMs default to a length and tone that exhaust the reviewer. The reviewer skims, pattern-matches on the *shape* of the doc rather than its substance, and clicks approve. This skill treats the reviewer's attention as the scarce resource and word count as a cost.

It enforces a three-pass discipline:

1. **Think on paper** — full reasoning draft, not shown to the user
2. **Compress for the reviewer** — strip everything not needed to approve, reject, or push back
3. **Humanize** — run the `humanizer` skill to scrub residual AI patterns

It ships templates with length budgets for ADRs (200–400 words), implementation plans (300–600 words), and design docs / RFCs (800–2000 words), plus a list of prohibited phrases and bloat patterns to cut.

## Auto-approving `humanizer`

To skip the permission prompt each time the skill invokes `humanizer`, add this to `~/.claude/settings.json`:

```json
{
  "permissions": {
    "allow": ["Skill(humanizer)"]
  }
}
```
