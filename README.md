# fork-sync

The automation branch of this fork, and the default branch on purpose:
GitHub runs scheduled workflows only from the default branch, and the
workflow file cannot live on `master` without making `master` differ from
upstream by more than the memory PR.

This branch holds one workflow and nothing else. It is not a mirror.

## Branch map

- `master` — latest `ggml-org/llama.cpp` master with the `GET /memory`
  work (upstream PR 26130) merged in. This is what lilbee builds.
- `feat/server-memory-metrics` — the branch behind upstream PR 26130.
  Automation never pushes it: a push re-triggers ggml-org CI.
- `fork-sync` — this branch: `.github/workflows/sync-upstream.yml` only.
- `memory-YYYYMMDD-HHMMSS` tags — immutable states of `master`, cut by the
  weekly sync. lilbee's `ENGINE_LLAMA_CPP_REF` pins one of these.

## The weekly sync

`sync-upstream.yml` runs Mondays 08:00 UTC: merge upstream master into
`master`, compile `llama-server` as a gate, push, tag. On a merge conflict
or build failure it stops, keeps `master` untouched, and files an issue
naming the conflicting files. lilbee's `engine-pin-sync` workflow picks up
new tags and proposes the pin bump as a rolling PR.

Ignore GitHub's "Sync fork" button here: it would merge upstream into this
branch. The workflow is the sync mechanism.
