---
name: concise-engineering-doc
description: Write engineering planning documents — ADRs, design docs, RFCs, implementation plans, technical scopes — that humans will actually read and review carefully rather than skim. Use this skill whenever the user asks for a plan, scope, ADR, design doc, RFC, technical proposal, or anything a colleague will need to review and sign off on. Optimized for the human reader's attention budget, not for completeness or for downstream agent consumption. Apply even when the user does not specifically ask for brevity — verbose engineering docs train reviewers to rubber-stamp, and this skill exists to prevent that.
---

# Concise engineering documents for human review

## Why this skill exists

Engineering planning documents written by LLMs default to a length and tone that exhausts the human reviewer. The reviewer skims, pattern-matches on the *shape* of the doc rather than its substance, and clicks approve. The next doc the same reviewer reads — even one containing something important — also gets skimmed, because the reviewing habit has been trained on padding.

The job of this skill is to produce documents that are worth reading carefully. That means treating the reviewer's attention as the scarce resource and the word count as a cost, not a sign of effort.

A useful test before producing anything: would a senior engineer on this team, after reading the doc, be able to argue for or against the decision in their own words? If the answer is no — if the doc could only be defended by re-reading it — it is too long, too vague, or both.

## The three-pass discipline

Do not try to write a concise document in one pass. The model knows how to think and the model knows how to compress; doing both at once produces hedged, padded prose. Separate them.

**Pass 1 — Think on paper.** Produce a working draft that contains the reasoning, alternatives, edge cases, and tradeoffs in full. This draft is for the writer (you), not the reviewer. Do not show it to the user unless asked.

**Pass 2 — Compress for the reviewer.** Re-read the draft with a single question in mind: what does the reviewer need to know in order to approve, reject, or push back on this decision? Discard everything else. Hedging, restated context, meta-commentary about the analysis process, and exhaustively-enumerated alternatives almost always go.

One thing compression must *not* strip: the subject of a narrative sentence. "We're hitting scaling limits with session auth" compressed to "Hitting scaling limits with session auth" reads like a Jira title, not like an engineer explaining a problem. Keep the actor (`we`, `the team`, `the service`, `the on-call engineer`) in narrative sections — Context, Decision, Goal, Summary, Problem, Approach. The headline / imperative register ("Port the integration", "Ship promo codes first") belongs in *list* sections — work breakdowns, risks, consequences — where each item is a unit of work. Do not carry that register into prose.

If the user only ever sees Pass 2, you have done the job. Pass 1 can be offered as an appendix or "research notes" file if the user wants the audit trail — but it should never be in the path of the reviewer who is deciding yes or no.

**Pass 3 — Humanize.** Before delivering, invoke the `humanizer` skill on the Pass 2 output. This is a mechanical step, not a creative one: it scrubs the residual AI tells (inflated symbolism, rule-of-three padding, em-dash overuse, "not just X but Y" constructions, etc.) that survive even disciplined compression. Run it via the Skill tool with `skill="humanizer"` and apply its edits before showing the document to the user. Do not skip this pass even when the draft already looks tight — the patterns it catches are exactly the ones a careful writer can't see in their own prose.

## Structural budgets

Each document type has a target shape. These are not aesthetic preferences; they are contracts with the reviewer about what to expect on each pass, so the reviewer can develop a reliable reading pattern across documents.

Sections are tagged `[narrative]` or `[list]`. Narrative sections are prose with human subjects — they should read like an engineer explaining the situation to a colleague. List sections are work items, risks, or alternatives — imperative or fragment phrasing is fine there because each line is a discrete unit, not a sentence in a paragraph.

Stick to the sections in the template for the doc type you're writing. Do not borrow sections across templates — an ADR does not get a Summary section because the doc feels long, and a Plan does not get a Tradeoffs section because the model has seen one in a design doc. If an ADR feels like it needs a summary up top, the Decision section is failing to do its job; fix the Decision, don't add scaffolding around it. Each template is a contract with the reviewer about what to expect.

### ADR (Architecture Decision Record)

```
# ADR-NNN: [Decision in 5-10 words, active voice]

Status: [Proposed | Accepted | Superseded by ADR-MMM]
Date: YYYY-MM-DD

## Context  [narrative]
[2-4 sentences. What forced this decision? What constraint or question are we resolving? Write like you're catching a teammate up — "We've been hitting X", "The Y service started Z", not "Hitting X" or "Y service started Z".]

## Decision  [narrative]
[1-3 sentences. State what we are doing in active voice with a subject. "We're migrating to JWT" — not "Migrate to JWT". Not "we considered X, Y, Z and decided on Y" — just "we're doing Y."]

## Consequences  [list]
[3-7 bullets. Mix of positive, negative, and neutral. Each bullet one line. Imperative/fragment phrasing is fine here. No hedging — if a consequence is conditional, the condition is the consequence.]

## Alternatives considered  [list]
[Optional. Only include if the rejected alternatives are likely to come up again. One sentence each. No more than 3.]
```

Target length: 200-400 words total. Anything longer is research notes pretending to be an ADR.

### Implementation plan / scope

```
# [Feature or change name]

## Goal  [narrative]
[1 sentence with a subject. "We want X to be true" or "The team needs Y" — not "Do X" or "Ship Y". This is a statement of intent, not a TODO.]

## Out of scope  [list]
[Bullets. What we are explicitly NOT doing in this change, especially things a reasonable reader might assume are included.]

## Approach  [narrative]
[3-6 sentences or a short bulleted list. The shape of the work, not the steps. If prose, keep human subjects ("We'll do X in two phases", not "Two-phase rollout").]

## Work breakdown  [list]
[Numbered list of concrete chunks, each one shippable on its own where possible. One line each. Imperative phrasing is appropriate here. Link to tickets if they exist.]

## Risks  [list]
[Bullets. Each risk a single line: what could go wrong, and what we would do about it.]
```

Target length: 300-600 words. If the work breakdown alone is more than 10 items, the scope is too large and should be split into separate plans.

### Design doc / RFC

```
# [Title]

## Summary  [narrative]
[3-5 sentences. Standalone. If the reader only reads this paragraph, they should know what is being proposed, why, and what they are being asked to do. Keep the actor visible — "We're proposing X because Y", not "Proposing X because Y".]

## Problem  [narrative]
[2-4 paragraphs. What is broken or missing today, with enough specificity that a reader unfamiliar with the area understands the stakes.]

## Proposal  [narrative, future tense]
[The core of the doc. Sectioned as needed, but each section earns its place. No section for the sake of completeness. Write in future tense — "the service will…", "we'll add…" — so the reader can't confuse the proposed system with the current one.]

## Tradeoffs  [narrative]
[Short. What this approach gives up, and why that is acceptable.]

## Open questions  [list]
[Bullets. What we don't know yet, and who would need to weigh in.]
```

Target length: 800-2000 words. A design doc longer than 2000 words almost always benefits from being split into a short main document and one or more appendix documents, each linked from the main one.

## What to cut

These patterns are reliable bloat. Strip them in Pass 2.

**Hedging connectives.** "It is worth noting that," "it should be considered that," "in order to," "due to the fact that," "with respect to," "in terms of." Almost always deletable with no loss of meaning.

**Restated context.** If the reviewer just read the previous section, do not summarize it before starting the next one. Trust the reader.

**Meta-commentary about the analysis.** "After careful consideration of multiple factors," "a comprehensive review of the alternatives reveals that," "weighing the various tradeoffs." The reader does not need to be told that analysis happened; they need the conclusion.

**Symmetric enumeration of pros and cons when the decision is clear.** If the decision is obvious, do not pretend it was close. State the decision and give the one or two reasons that actually matter.

**Defensively-comprehensive alternative sections.** Listing every alternative the model can think of, including ones nobody would seriously propose, signals padding. Include alternatives that a colleague might genuinely raise.

**Adjectival inflation.** "Robust," "comprehensive," "scalable," "modern," "best-in-class," "battle-tested." These words do not carry information; they carry tone. Replace with the concrete property they are gesturing at, or cut.

**The conclusion that restates the document.** If the doc has a Summary at the top, it does not also need a closing recap. Pick one.

## Prohibited phrases

Treat these as defects to be removed in Pass 2. They are reliable markers of AI-generated padding and they train reviewers to skim.

- "It is important to note that"
- "It is worth mentioning that"
- "A comprehensive analysis"
- "A robust solution"
- "Considering various factors"
- "Needs to be considered"
- "In order to" (use "to")
- "Due to the fact that" (use "because")
- "At this point in time" (use "now")
- "In the event that" (use "if")
- "A wide variety of" / "a number of" (use a number, or "several," or cut)
- "Leverage" as a verb when "use" would work
- "Utilize" when "use" would work
- "Facilitate" when a more specific verb would work
- Any sentence starting with "It is" or "There is" / "There are" — often deletable by rewriting the next clause as the main one

## Voice and tone

Use active voice and concrete subjects. "The auth service will validate tokens" is better than "Tokens will be validated by the auth service." "We will migrate the database in two phases" is better than "A two-phase migration approach will be employed."

Name the actors. "The team," "we," "the migration script," "the on-call engineer." Vague subjects ("the system," "the process") accumulate padding around them because the model has to keep explaining what is doing what.

State decisions, don't propose them, unless the document is genuinely a proposal up for debate. If the decision has already been made and this document is the record of it, the verb is "we are doing X," not "we propose X."

Use tense to mark current vs. future state. Sections that describe the world as it is today (Context, Problem) are present tense. Sections that describe what we're going to do or what will exist after the change (Proposal, Approach, Goal, Consequences, Work breakdown) are future tense — "the service will validate the token", not "the service validates the token". Collapsing both into present tense erases the distinction the reviewer most needs: what's broken today vs. what we're proposing to change.

Do not apologize for omissions. If something is out of scope, say so once in the "out of scope" section. Do not also caveat it everywhere else.

## When to refuse to write the doc

Sometimes the right output is not a document. If the user asks for an ADR, design doc, or plan and the underlying decision could be described in a single sentence ("we're going to rename the `users` table to `accounts`"), say so. Offer to write a one-line commit message or PR description instead. A doc that exists only to satisfy a ceremony trains the team to stop reading docs.

Similarly, if the user is asking for a doc but the input is too vague to produce a useful one ("write me an ADR about our auth system"), the right move is to ask one clarifying question — what specific decision is being made — not to produce a plausible-sounding doc that fills the gap with invention. Verbose docs are often a symptom of the model hedging across ambiguity in the prompt; tightening the input is faster than trimming the output.

## Length self-check before delivering

Before showing the document to the user, run these checks. Do not narrate them; just apply them.

1. Could the Summary or Decision section stand alone and still convey what is being asked of the reviewer? If not, rewrite it.
2. Is every section pulling its weight, or is at least one section there for the sake of structural completeness? If yes, cut it.
3. Are any of the prohibited phrases present? If yes, rewrite those sentences.
4. Is the document within the target length budget for its type? If not, the cuts have not gone far enough. Make another pass.
5. Would a senior engineer be able to argue for or against this in their own words after one read? If not, the doc has not earned its length.
6. Has the `humanizer` skill been run on the final draft? If not, run it now via the Skill tool and apply its edits. This is non-optional.

## Examples

**Example 1 — ADR, before and after compression**

Before (Pass 1 draft):

> ## Context
> Our system currently uses session-based authentication, which has served us well for many years. However, as we have grown and our customer base has expanded to include enterprise clients with more sophisticated security requirements, we have begun to encounter a number of limitations with this approach. Specifically, session-based auth requires server-side state, which complicates our horizontal scaling efforts, and it does not integrate cleanly with the SSO providers that our enterprise customers are increasingly requesting. After a comprehensive review of the available alternatives, including JWT-based auth, OAuth 2.0, and SAML, the team has decided to move forward with a hybrid approach.

After (Pass 2):

> ## Context
> We're hitting two limits with session-based auth: it requires server-side state (which blocks horizontal scaling), and it doesn't integrate with the SSO providers our enterprise customers are asking for. We need to pick a replacement.

The first version is 110 words. The second is 42. The second loses nothing the reviewer needs, and it still sounds like an engineer talking — the "we" subject is doing real work, not padding.

**Example 2 — Plan, refusing to invent**

User prompt: "Write me a plan for improving our deployment pipeline."

Bad response: A 1500-word plan inventing specific improvements the model has no basis to recommend.

Good response: "Before I write this, I need one thing: what's the specific problem you want the new pipeline to solve? Is it deploy speed, rollback safety, environment consistency, something else? The plan will be useless if I guess at the goal."

**Example 3 — Cutting prohibited phrases**

Before: "It is important to note that in order to facilitate a robust and scalable migration, we will need to leverage a comprehensive set of tools."

After: "The migration needs three tools: [name them]."

The first version is 25 words and conveys nothing. The second is 8 words and forces the writer to name the tools, which is where the actual information lives.
