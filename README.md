# Renovate datasource changelog comparison

Reproduces changelog behavior using real packages from https://packages.unity.com.
The custom regex manager uses Renovate's built-in `unity3d-packages` datasource;
this fixture does not require custom datasource schema support.

## Fixture

| Package | Installed | Target limit |
| --- | --- | --- |
| com.unity.inputsystem | 1.13.0 | 1.15.0 |
| com.unity.cinemachine | 3.1.0 | 3.1.2 |

Both dependencies are grouped into one PR. Automerge is disabled.
Keep the fixture versions unchanged while comparing Renovate implementations.

## Compare

Run Renovate against this repository using the baseline, the branch implementing
only `perDependencyNotes`, and the full implementation from
https://github.com/renovatebot/renovate/pull/45867.

Use the same bot settings and repository configuration for each run. Save the
generated PR body before the next run, since it may update the same PR.
Repository configuration does not select which Renovate implementation runs;
select that in your self-hosted runner or local checkout.

Expected behavior:

| Implementation | Expected notes |
| --- | --- |
| Baseline | Shared repository/source deduplication can suppress one dependency's supplied notes. |
| Only perDependencyNotes | Both dependency sections, containing their target release's notes. |
| Full #45867 | Both sections, also including supplied intermediate notes for Input System 1.14.0 and Cinemachine 3.1.1. |

These expectations are derived from code inspection, not an end-to-end run.
The live registry was checked on 2026-09-24: all four target/intermediate versions
have nonempty `_upm.changelog` fields. Repository metadata may change over time,
which can affect whether the deduplication collision reproduces.

For a separate aggregation-only comparison, remove `groupName` from the first
package rule. Each package then gets its own PR: baseline and perDependencyNotes
should show target-only notes; full #45867 should include intermediate notes.

## Existing public examples

- https://github.com/bdovaz/renovate_test/pull/15 updates Input System
  1.13.0 to 1.19.0 but displays only the target's notes.
- https://github.com/FilippoGurioli-master-thesis/collektive.unity/pull/41
  updates Cinemachine 3.1.2 to 3.1.5 but displays only the target's notes.

These closed PRs demonstrate target-only notes, not a confirmed grouped
deduplication failure.
