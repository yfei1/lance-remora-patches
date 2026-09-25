# Lance 1.0.4 patches for remora

This repository holds three Lance 1.0.4 crates with the corrections that remora needs.
remora selects them through `[patch.crates-io]` git entries, pinned to one commit.

## How to verify

1. Commit `e41dfde` imports the three published crates unchanged. Compare it with the crates.io archives:

   | Crate | Archive SHA-256 |
   | --- | --- |
   | lance-arrow 1.0.4 | `b53669b967e3800e3ef80b6d32451ca1f4a98adab5a0ea180b57451b45ef4f7e` |
   | lance-encoding 1.0.4 | `226f5cc09ff8f52f7648ac3aeb0193c9a041f881108de6f7a928cf23a802165d` |
   | lance-linalg 1.0.4 | `e0383ff6369533d9d8ccf6596dc51d64ecd2ebd75a1bd25a58927079c7fc99fc` |

   All three come from upstream revision `a93eaad1f6909a843cf8aa00d5530359012a7aaa` (tag `v1.0.4`).
2. Commit `24dddb9` adds the upstream Apache-2.0 `LICENSE` (SHA-256 `3788e71aa43d61a457749d4aa070d9705c36d25ed978cd52c5766ec4bd361aa9`) and removes crate-local files that a consumer build does not read.
3. Each later commit is one correction. `git log -p e41dfde..` shows the complete patch set.

## Corrections

| Commit | Crate | Correction | Upstream status (checked 2026-09-24) |
| --- | --- | --- | --- |
| `7114f67` | lance-arrow | `merge_struct_validity` keeps an all-null parent mask all-null | Backport of #8049; in v10.0.0 and later |
| `43200ee` | lance-arrow | `pushdown_nulls` aligns with the child data offset | Local; absent from v12.0.0 and main |
| `6f9a150` | lance-encoding | `do_record_validity` counts special entries | Same change as #9268; only in v13.0.0-beta.3 and later |
| `cd304a0` | lance-encoding | All-null decode maps root rows to level ranges | Local; upstream redesigned this path in #7564 |
| `c0d1dd0` | lance-encoding | Miniblock level count stays `u64`, with a full-zip fallback | Local; upstream solved the limit differently in #6787 and #7751 |
| `52d7149` | lance-encoding | Booleans expand to bytes for full-zip | Adapted from #6723; in v10.0.0 and later |
| `7495d31` | lance-linalg | AVX-512 kernel builds with `-mavx512f` | Fixed differently in #7121; in v8.0.0 and later |

Issue and pull-request numbers refer to https://github.com/lance-format/lance.
Lance v10.0.0 and later need Arrow 58 and DataFusion 54. remora uses Arrow 56 and Lance 1.0.4.

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
