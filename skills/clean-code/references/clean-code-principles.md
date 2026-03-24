# Clean Code Principles (Robert C. Martin)

Micro principles: from variable naming to function structure.

## 1. Naming (Clean Code Ch. 2)

### Rules

- **Intention-revealing names**: `elapsedTimeInDays` not `d`. The name must answer: why it exists, what it does, how to use it.
- **Avoid disinformation**: no `accountList` if it's not a List. No names that differ by a single character.
- **Meaningful distinctions**: no `a1`, `a2`. No noise words (`Info`, `Data`, `Manager`).
- **Pronounceable and searchable names**: no `genymdhms`. Single-letter names = local loop scope only.
- **Classes = nouns**: `Customer`, `Account`, `WikiPage`. No verbs.
- **Functions = verbs**: `postPayment`, `deletePage`, `save`. Consistent prefixes: `get`, `set`, `is`.
- **One word per concept**: no `fetch`/`retrieve`/`get` in the same codebase. Pick one term and stick with it.
- **Domain vocabulary**: use the domain's vocabulary (pattern, algorithm, business concept), not arbitrary technical jargon.

### Examples

```
// Bad
const d = new Date();
const list = getItems();
function process(data) { ... }

// Good
const createdAt = new Date();
const activeUsers = getActiveUsers();
function validateOrder(order) { ... }
```

## 2. Functions (Clean Code Ch. 3)

### Rules

- **Small.** Then even smaller. < 20 lines ideal. If it scrolls, it's too long.
- **One job** (SRP at function level). If the name contains "and" or "or", it's 2 functions.
- **One abstraction level per function** (Stepdown Rule): don't mix `getUser()` and `response.headers['content-type']` in the same function.
- **Arguments**: 0 ideal (niladic), 1 ok (monadic), 2 acceptable (dyadic), 3 = justify (triadic). More = extract an object.
- **No flag arguments**: a boolean parameter = the function does 2 things. Split into 2 functions.
- **No hidden side effects**: if the function is called `checkPassword`, it must NOT initialize the session.
- **Command-Query Separation**: a function does something (command) OR answers a question (query), not both. `set` doesn't return a value.
- **Extract try/catch**: the try/catch block is a function on its own. Error handling is ONE thing.

### Examples

```
// Bad
function processUserData(user, shouldValidate, format, sendEmail) {
  // 50 lines mixing validation, formatting, email sending
}

// Good
function validateUser(user) { ... }
function formatUser(user, format) { ... }
function notifyUser(user) { ... }
```

## 3. Comments (Clean Code Ch. 4)

### Legitimate comments
- TODO with ticket/context: `// TODO(#123): migrate to new API`
- Consequence warning: `// WARNING: this test takes 10 min`
- Intent clarification when code can't express it (complex regex, non-trivial algo)
- JSDoc/docstrings for public APIs

### Toxic comments (delete them)
- Code paraphrase: `i++; // increment i`
- Change log (that's git's job)
- Closing comments: `} // end if`
- Commented-out code (git keeps it, no need)
- Position markers: `// ---- SECTION ----`
- Mandatory comments on obvious getters/setters

### The rule
> The best comment is one you don't need to write. If you need a comment, first ask yourself if you can rename the variable or extract a function.

## 4. Formatting (Clean Code Ch. 5)

- **Newspaper metaphor**: headline at top (public functions), details going down (private functions, helpers).
- **Vertical density**: related concepts = close vertically. No blank line between things that belong together.
- **Vertical distance**: variables declared close to their usage. No declaration at file top if used at line 200.
- **Consistency**: one formatting convention, stick with it across the whole project. Linter + formatter mandatory.
- **File size**: if a file exceeds 300 lines, it probably has multiple responsibilities.

## 5. Error Handling (Clean Code Ch. 7)

- **Exceptions over return codes**: no `return -1` or `return null` to signal an error.
- **Write try/catch first** (TDD approach): define error behavior before the happy path.
- **Don't return null**: return a special object, an empty list, or throw. Every returned `null` is a `NullPointerException` waiting to happen.
- **Don't pass null as argument**: if a function receives null, it's a caller bug, not a case to handle.
- **Fail fast**: fail early and clearly. A precise error message is better than a silent bug 3 layers deep.
- **No silent catch**: `catch (e) {}` is forbidden. At minimum log, ideally propagate or transform the error.

## 6. Tests (Clean Code Ch. 9)

### FIRST
- **Fast**: tests must be quick. Slow tests = tests nobody runs anymore.
- **Independent**: each test is self-contained. No dependency between tests, no required order.
- **Repeatable**: same result in any environment (local, CI, prod).
- **Self-validating**: a test returns pass or fail. No manual verification.
- **Timely**: written just before production code (TDD) or at minimum at the same time.

### Rules
- **One concept per test**: a test that checks 3 things = 3 tests.
- **Descriptive names**: `shouldReturnEmptyListWhenNoUsersFound` not `test1`.
- **AAA pattern**: Arrange (setup), Act (execution), Assert (verification).
- **First-class code**: tests follow the same quality standards as production code. Refactoring tests = normal.
- **Pragmatic coverage**: cover critical cases and edge cases, don't aim for 100% for sport.

## 7. Code Smells (Clean Code Ch. 17)

| Smell | Symptom | Remedy |
|---|---|---|
| Rigidity | A change forces cascading modifications | Decouple via interfaces, DIP |
| Fragility | A change breaks unrelated things | Isolate responsibilities (SRP) |
| Immobility | Impossible to extract for reuse | Extract into independent modules |
| Needless coupling | Dependencies on unused things | ISP, fine-grained interfaces |
| Needless complexity | Preparing for hypothetical future | YAGNI: delete |
| Repetition | Same logic at 3+ places | DRY: extract (if same reason to change) |
| Opacity | Code hard to read | Rename, extract, simplify |
| Dead code | Code never executed | Delete (git keeps it) |
