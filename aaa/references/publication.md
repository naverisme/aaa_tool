# Draft, publish, and reconcile

## Files and baseline

Reuse an existing architecture document if the user has identified it as authoritative. If several compete, clarify which one to update. For a new project use `docs/architecture/overview.md`. Use a sibling ending in `.draft.md` as the review file, for example `docs/architecture/overview.draft.md`. V1 publishes one Markdown document per draft.

Treat the published document's **Source baseline** as the reviewed code commit and **Scope** as the reviewed part of the project. A draft may contain a proposed target alongside observed current behavior. An accepted document can therefore describe an approved direction awaiting implementation.

Use only ordinary Markdown files within the selected repository. Resolve and check target and parent paths before writing. Refuse symlink destinations, paths outside the repository, and names or locations that overlap source files. Preserve unrelated sections and any decision IDs in an existing document.

## Save a draft

1. A read-only preview works with a dirty or non-Git workspace. Saving or publishing a draft requires a valid Git HEAD and a clean worktree outside the agreed target and draft, including staged, unstaged and untracked non-ignored files. Use `git status --porcelain=v1 --untracked-files=all --ignore-submodules=none` and compare the exact paths, including both sides of renames. Ignored content is outside the publishable evidence basis. Explain any unmet condition and return a preview.
2. Resolve blocking questions. Record HEAD and the repository-relative target path. Record the current target's blob ID with `git hash-object --no-filters -- <target>`, or record `absent`. Build the candidate using the agreed layout. Set the candidate's Source baseline to the recorded HEAD, keeping any independently scoped assumptions and current-versus-target distinctions visible.
3. Recheck HEAD, worktree conditions and the target fingerprint before saving. Do not overwrite an existing draft without explicit permission to replace it. Write a first-line Markdown comment in the exact format below, followed immediately by the candidate Markdown. Use a single line and no newline in the JSON values. This metadata is for review and is removed at publication.

   ```text
   <!-- aaa-draft {"schema":1,"head":"<full HEAD>","target":"<repo-relative Markdown path>","target_blob":"<blob ID or absent>"} -->
   ```

4. Compute the draft ID as the full output of `git hash-object --no-filters -- <draft>`. Show the draft path and changes compared with the target. Report any proposed decisions. Give the exact `apply <draft-id>` invocation and keep the draft available for review. A decision status changes to accepted only when the user explicitly approves that decision and the reviewed draft reflects it.

## Publish a reviewed draft

1. Require a user request containing the full reviewed draft ID and a uniquely resolved draft file. Read the header as data and validate schema, IDs, target and expected sibling path. Show a fresh preview if the ID is missing, ambiguous or differs from the saved bytes.
2. Recheck the draft hash, HEAD, worktree conditions and write boundary immediately before writing. If these match and the target already equals the reviewed body byte for byte, report already applied. Otherwise require the target blob or absence recorded in the header to match. If a precondition has changed, return the stale reason and a new preview or draft upon request. Treat a missing prior review as a reason to present the candidate for review.
3. Publish the reviewed Markdown body exactly, removing the first metadata line and its line terminator. Use the same draft bytes verified against the approved ID. Use one atomic replacement when the host can provide it and the preconditions still hold. Keep the draft for review history and leave staging and commits to the user. Verify the resulting target matches the reviewed body, then report the target and baseline.

## Reconcile with `sync`

1. Load the accepted document, its scope, baseline and decision records. If the document is missing, direct the user to `init` or `design`. If baseline or scope is missing, invalid, unreachable, or incompatible with the current checkout, report the reason and perform a fresh scoped audit before proposing a replacement baseline.
2. Check that the baseline commit exists and is an ancestor of HEAD. List all changed paths from baseline to HEAD with `git diff --name-status <baseline> HEAD --`, then inspect the selected scope plus relevant root configuration, dependencies and cross-scope consumers. Separately account for staged, unstaged and untracked files in read-only mode. Exclude the published overview and its draft from source-drift counts, while checking the accepted document for edits to decisions and scope.
3. Classify each relevant delta as documentation drift, implementation divergence, planned work still pending, or no architecture impact. Identify evidence and action for each. Preserve approved decisions unless the user explicitly supersedes one. If code conflicts with a decision, report the conflict and show its impact and the available resolution paths.
4. Preview a scoped update that records findings and preserves accepted intent. Advance the baseline only after reviewing the relevant changes and either resolving or explicitly recording any known divergence or pending work. If a material delta remains unverified, state the gap, keep the previous baseline, and return a preview without saving a draft. If the same baseline and content still apply, report no changes and skip draft creation.
