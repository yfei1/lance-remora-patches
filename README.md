# Lance 12.0.0 patches for remora

This branch holds two Lance 12.0.0 crates with the corrections that remora needs.
remora selects them through `[patch.crates-io]` git entries, pinned to one commit.
The branch `main` holds the earlier Lance 1.0.4 patch set.

## How to verify

1. Commit `9772bea` imports the two published crates unchanged. Compare it with the crates.io archives:

   | Crate | Archive SHA-256 |
   | --- | --- |
   | lance-arrow 12.0.0 | `3eb5ac7fd453e7039269ac307554f503ea82729735559659a3e26ae64ce2556c` |
   | lance-encoding 12.0.0 | `7a2c82ceed3511490555c211df37dd774c31e3bca0c0566aa0ae5be7b0701549` |

   Both come from upstream revision `cbeec97cb893a66ad6b3db87f25a57ab0093f358` (tag `v12.0.0`).
   Both values match the crates.io index checksum.
2. Commit `4248a6b` adds the upstream Apache-2.0 `LICENSE` (SHA-256 `6f4030a4f059f5618cf00375afd8f5d18fe48e0fc2be1c2e98b2c32c90c44938`).
   The published crates do not contain it.
3. Each later code commit is one correction. `git log -p 4248a6b..` shows the complete patch set.

## Corrections

| Commit | Crate | Correction | Upstream status (checked 2026-09-25) |
| --- | --- | --- | --- |
| `a0e06c8` | lance-arrow | `pushdown_nulls` aligns with the child data offset | Local; absent from v12.0.0 and main |
| `a92733b` | lance-encoding | `do_record_validity` counts special entries | Same change as #9268; only in v13.0.0-beta.3 and later |

## Corrections that this branch drops

The 1.0.4 set on `main` had five more corrections. This branch does not carry them.

| 1.0.4 commit | Crate | Correction | Reason for removal |
| --- | --- | --- | --- |
| `7114f67` | lance-arrow | `merge_struct_validity` keeps an all-null parent mask all-null | Upstream #8049 is in v10.0.0 and later |
| `cd304a0` | lance-encoding | All-null decode maps root rows to level ranges | The remora checks pass on 12.0.0 without it |
| `c0d1dd0` | lance-encoding | Miniblock level count stays `u64`, with a full-zip fallback | The remora checks pass on 12.0.0 without it |
| `52d7149` | lance-encoding | Booleans expand to bytes for full-zip | The remora checks pass on 12.0.0 without it |
| `7495d31` | lance-linalg | AVX-512 kernel builds with `-mavx512f` | Upstream #7121 is in v8.0.0 and later; remora no longer patches lance-linalg |

MEASURED on 2026-09-25: the six remora checks below pass at `a92733b`, which has only the two corrections above.

Issue and pull-request numbers refer to https://github.com/lance-format/lance.
Lance 12.0.0 needs Arrow 58 and DataFusion 54.

## Removal rule

Remove a correction only when an adopted upstream Lance release includes it and passes the same remora checks.
An upstream version change alone is not proof.
The remora checks are the schema-unification T2/T4 engine fixtures:
`missing_open_interest_preserves_the_sweep_and_later_scoring`,
`live_product_checkpoint_scores_one_sweep_with_exact_manifold_metrics`,
`signal_replay_streams_exact_sweep_snapshot_pairs`,
`replay_stage_backpressures_rows_and_commits_one_final_manifest`,
`cold_rewrite_preserves_frozen_quote_relations_and_one_version`, and
`sparse_lists_round_trip_across_miniblock_level_limit`.

## License

The crates are Apache-2.0, as published by the Lance authors. See each crate's `LICENSE`.
