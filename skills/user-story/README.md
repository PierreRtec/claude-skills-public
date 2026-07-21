# User Story Builder - From Vibe-Coding to Implementable Spec

Turns a raw, informal one-liner ("the chat sends when I hit enter on my phone its annoying") into a structured user story with context, file references, testable acceptance criteria, named test scenarios, and an explicit out-of-scope section. The output is precise enough that any agent or developer can implement it without asking a question.

**Approximate token count:** ~1,300 tokens (SKILL.md)

## Features

- **Type detection**: classifies the request as `feat:`, `fix:`, `improve:`, `remove:` or `refactor:` from the user's own wording
- **Codebase-grounded**: reads the relevant files first, so the story cites real paths and line numbers instead of guesses
- **Infer-first policy**: asks at most ONE question, and only when ambiguity fully blocks the writing
- **Testable acceptance criteria**: every AC must translate into a test assertion, no "it should be better"
- **Named test scenarios** following `test_[function]_[scenario]` with `_nominal` / `_empty` / `_error` suffixes
- **Explicit out-of-scope** section to kill scope creep before it starts
- **One story = one thing**: multi-request messages get split into separate stories
- Decision flow documented as a `dot` digraph

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@user-story
```

## Usage

Just describe what you want in whatever words come to mind:

```
> the export button times out on big accounts, and also the CSV has no headers
```

The skill splits that into two stories, reads the export code, and presents both for validation before any implementation starts.

### Example output

```
## fix: Enter on mobile sends the message instead of inserting a line break

**As a** mobile user
**I want** to insert line breaks with the Enter key of the virtual keyboard
**So that** I can write multi-line messages without sending them by accident

### Context
- Currently `handleKeyDown` in `Chat.tsx:167` sends on Enter unless Shift is held
- On mobile, the virtual keyboard has no accessible Shift key

### Acceptance criteria
- [ ] AC1: On a touch device, the Enter key inserts a line break
- [ ] AC2: On a touch device, only the send button sends the message
- [ ] AC3: On desktop, Enter still sends (Shift+Enter for a line break)

### Test scenarios
- test_handleKeyDown_mobile_enter: Enter on a touch device does not send
- test_handleKeyDown_desktop_enter: Enter on desktop sends

### Out of scope
- Changing the desktop behavior
```

## Why it matters

Most agent failures start upstream of the code: a vague request gets implemented as a vague guess, and three rounds of "no, that's not what I meant" follow. Spending 30 seconds turning intent into a validated spec removes the guesswork, and the acceptance criteria double as the test plan.

## License

MIT
