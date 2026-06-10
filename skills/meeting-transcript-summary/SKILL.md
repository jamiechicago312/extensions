---
name: meeting-transcript-summary
description: >
  This skill should be used when the user asks to summarize a meeting
  transcript from a publicly accessible Google Doc, extract highlights,
  decisions, action items, blockers, or open questions from shared meeting
  notes, or turn a public meeting transcript link into a concise recap.
triggers:
  - meeting transcript
  - google docs transcript
  - meeting notes summary
  - summarize google doc
  - action items from transcript
---

# Meeting Transcript Summary

Read a publicly accessible Google Doc transcript and turn it into a concise,
structured meeting summary in the chat.

---

## When this skill applies

Use this workflow when the user provides a public Google Docs link and asks for:

- meeting highlights
- a concise recap
- decisions made
- action items and owners
- blockers or risks
- open questions or follow-ups
- notable quotes or timeline moments

Do not use this skill for private documents that require login or approval.
If the document is not public, stop and ask the user to either make it public
or paste the transcript directly into the chat.

---

## Inputs to collect

Ask for the minimum information needed:

1. **Document URL** - the Google Docs link containing the transcript
2. **Desired output format** - brief recap, bullets, executive summary, action-item list, etc.
3. **Focus areas** - product decisions, customer feedback, blockers, roadmap changes, hiring updates, etc.
4. **Quote preference** - whether to include direct quotes or only synthesized summaries
5. **Length preference** - short, medium, or detailed

If the user gives only the link, use a sensible default output:
- 1 paragraph executive summary
- 5 to 10 key highlights
- explicit decisions
- action items with owners if named
- open questions and risks

---

## Retrieval workflow

### Step 1 - Validate access

Confirm that the link is a Google Docs URL and appears to be publicly readable.
Do not attempt login, account switching, or form submission.

If the content returns an auth wall, permissions error, or empty export:
- tell the user the doc is not publicly accessible from the sandbox
- ask for a public link or the pasted transcript text

### Step 2 - Normalize the document URL

Extract the document ID from URLs shaped like:

- `https://docs.google.com/document/d/<DOC_ID>/edit`
- `https://docs.google.com/document/d/<DOC_ID>/view`
- `https://docs.google.com/document/d/<DOC_ID>/preview`

Prefer export endpoints over browser rendering because transcript summarization
works best with plain text.

### Step 3 - Prefer plain text export

Try the plain text export first:

```bash
curl -L "https://docs.google.com/document/d/<DOC_ID>/export?format=txt"
```

If the transcript is missing formatting that matters - for example tables,
checklists, or speaker labels collapsed in an unhelpful way - try HTML export:

```bash
curl -L "https://docs.google.com/document/d/<DOC_ID>/export?format=html"
```

If export endpoints are unavailable but the page is still public, fall back to a
normal fetch or browser content extraction and isolate the transcript body.

### Step 4 - Preserve useful structure

Keep information that improves summary quality:

- speaker names
- timestamps
- section headings
- agenda labels
- explicit action-item markers
- decisions and next-step bullets already present in the doc

Strip or ignore obvious Google Docs chrome, navigation text, and duplicate page
boilerplate.

---

## Summarization workflow

### Step 1 - Classify the document shape

Identify whether the document is primarily:

- a verbatim transcript
- meeting notes with partial transcript excerpts
- agenda plus notes
- brainstorming notes with action items

State that classification briefly in the response when it changes confidence or
summary style.

### Step 2 - Chunk long transcripts

If the transcript is too long to summarize reliably in one pass, summarize it in
stages:

1. Break it into logical chunks by heading, time window, or speaker blocks.
2. Produce a short summary for each chunk.
3. Merge those chunk summaries into the final recap.
4. Re-read the final recap for cross-cutting decisions, repeated themes, and
   action items that span multiple sections.

Avoid dropping late-stage decisions that revise earlier conclusions.

### Step 3 - Separate explicit facts from inference

Only mark an item as a decision, owner, deadline, or blocker when the transcript
supports it.

Use labels like these when needed:
- `Explicitly stated:`
- `Implied but not confirmed:`
- `Owner not specified`
- `Deadline not specified`

Do not invent owners, due dates, or commitments.

### Step 4 - Extract the highest-value outputs

Prioritize these categories:

1. **Executive summary** - what the meeting was mainly about
2. **Key highlights** - the most important takeaways
3. **Decisions made** - commitments, approvals, changes in direction
4. **Action items** - task, owner, due date, status if stated
5. **Blockers or risks** - unresolved issues, concerns, dependencies
6. **Open questions** - items deferred or left unanswered
7. **Notable quotes** - only when requested or especially useful

### Step 5 - Ground the summary

When the transcript is ambiguous or dense, support important claims with short
quoted snippets or speaker-attributed references.

Prefer short evidence like:
- `Alex: "Let's ship the beta to five customers next week."`
- `The group agreed to delay the migration until after launch.`

Keep quotes selective. Do not flood the answer with raw transcript text.

---

## Default response template

Use this structure unless the user asks for something else:

```markdown
## Executive Summary

<2 to 4 sentence recap>

## Key Highlights
- ...
- ...

## Decisions Made
- ...

## Action Items
- Owner - Task - Due date/status

## Blockers / Risks
- ...

## Open Questions
- ...
```

If no decisions or action items appear in the source, say so explicitly instead
of leaving the section ambiguous.

---

## Edge cases

### Public link does not work

Stop and explain the access problem clearly. Ask for either:
- a link shared as `Anyone with the link can view`
- a published-to-web link
- pasted transcript text

### Notes are messy or partially structured

Summarize what is explicit, then add a short limitations note such as:
`The document appears to be mixed notes rather than a full transcript, so some owners and decisions may be under-specified.`

### Multiple meetings in one document

Split the output by meeting or date before producing the final synthesis.

### Transcript contains sensitive material

Treat the text as user-provided content and summarize only what is necessary for
the request. Avoid repeating large sensitive excerpts when a concise summary is
sufficient.

---

## Quick operating rules

- Prefer `curl` or direct fetch before using the browser.
- Prefer text export before HTML export.
- Never attempt login for a supposedly public document.
- Ask for formatting preferences only when needed; otherwise summarize directly.
- Keep summaries grounded in the transcript.
- Mark uncertainty clearly.
- Extract action items in a machine-readable bullet format when possible.
