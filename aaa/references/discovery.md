# Repository discovery and evidence

## 1. Establish the reading boundary

Use the project path named by the user or start from the session directory. Resolve the Git root with `git rev-parse --show-toplevel`, record the full `HEAD` object ID when available, and use `git status --short --branch --untracked-files=normal` to distinguish the checked-out revision from local changes. Report detached HEAD, an unborn branch, or a non-Git workspace explicitly. In a monorepo record the selected subdirectory as well as the Git root. Ask when multiple plausible projects are visible and the selection affects the outcome.

Read the applicable project instructions. Respect Git ignore rules, workspace boundaries and access restrictions. Skip credentials, local environment files, private keys, binary/vendor/generated trees, and repository internals even if tracked. Inspect documented environment variable names or sanitized examples when relevant. Do not follow symlinks out of scope or enter submodules automatically. In a non-Git workspace use a bounded directory listing and mark revision information unavailable.

## 2. Read in this order

1. **Intent and constraints** — user request, root or scoped README, requirements, accepted architecture and decision records. Capture intended behavior and dated or unresolved requirements.
2. **Project map** — manifests and workspace definitions, build and deployment configuration, repository file names. Select the relevant app or module and locate its entry points. Read version constraints from files when they matter.
3. **Execution paths** — entry points, route or job registration, dependency wiring, interfaces, storage adapters and migrations. Trace at least one representative path per critical boundary in scope. Follow the actual caller and callee.
4. **Verification** — inspect tests and CI definitions that cover those boundaries. Describe what is asserted and report static inspection separately from actual test execution or deployment evidence.

Stop when the decisions and requested coverage have sufficient evidence. List important paths or runtime behavior left unverified. If repository documentation conflicts with code, record both sources, explain the mismatch and treat code as evidence of checked-out implementation. A deployment manifest alone establishes configuration intent.

## 3. Resolve uncertainty

Classify material statements by their source.

| Label | Required detail |
| --- | --- |
| Observed | Path plus line or symbol and the observed behavior |
| Confirmed | User answer or accepted project decision and its source |
| Inferred | Evidence and reasoning that connects it to the conclusion |
| Assumed | Provisional value, impact, and when to revisit it |
| Unknown | What evidence is missing and the decision affected |
| Proposed | Target behavior and the reason to adopt it |

Resolve open items using available in-scope sources first. Bundle up to three blocking questions per turn. For each, give the missing fact, what choice it controls, and options when the trade-off is known. Ask when an answer would materially change scope, ownership, compatibility, data lifecycle, operating constraints or reversibility. Use a reversible default for low-impact ambiguity and label it. While waiting, complete independent analysis and mark any dependent design as provisional. Never publish a candidate with a blocking question unresolved.
