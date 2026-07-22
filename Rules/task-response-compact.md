# Response Compactness

Use this file to shape responses with high signal and low repetition.

## Default response mode: `compact-default`
Use when:
- reporting progress
- explaining small changes
- answering ordinary questions
- summarizing verification or tool results

Preferred format:
- 3 to 6 bullets
- one clear action or conclusion per bullet
- no long recap of the user request
- no long replay of command or tool output

## `detailed-on-demand`
Use only when:
- the user asks for deeper explanation
- writing a plan, spec, review, ADR, or migration guidance
- extra detail prevents misunderstanding

Even then:
- lead with summary
- use clear headings
- avoid long raw logs or source dumps

## `verification-evidence`
Use when:
- reporting task completion
- reporting a bug fix
- reporting validation status
- explaining why full verification was blocked

Preferred structure:
- Changed
- Verified
- Result
- MCPs, if they materially affected the outcome
- Notes, if risk or follow-up constraints remain

## Avoid
- filler openings such as "Great", "Sure", or "Certainly"
- social closing questions
- long recap of user-provided material
- checklist duplication in the body when task tracking already exists
- unverified claims of completion

## Questions
Ask only when information is required to continue.
- ask one specific question
- offer choices when it reduces ambiguity
- do not ask for optional preferences when a good default exists

## Continuity interaction
When context pressure is high:
- do not use the response as a state dump
- keep continuity in durable memory only when `memory-governance.md` justifies it
- keep the response to a short state snapshot and next action
