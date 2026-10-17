# Lance 13.0.0 patches for remora

This branch holds two Lance 13.0.0 crates with the corrections that remora needs.
remora selects them through `[patch.crates-io]` git entries, pinned to one commit.
The branch `v12.0.0` holds the earlier Lance 12.0.0 patch set, and `main` holds the Lance 1.0.4 set.

## How to verify

1. Commit `ebe189a` imports the two published crates unchanged. Compare it with the crates.io archives:

   | Crate | Archive SHA-256 |
   | --- | --- |
   | lance-arrow 13.0.0 | `bab6e64549f9ad9a6380a35b56d7dca612555a6272c07b3abca84e59ded6f463` |
   | lance-encoding 13.0.0 | `3c04a91e545b505b5e6ec98854c7eadc56689cbc072f138dd26805ef0095e81a` |

   Both come from upstream revision `0923349baa19fd9af776b7d4626dc758dc610c80` (tag `v13.0.0`).
   Both values match the crates.io index checksum.
2. Commit `78c9f7b` adds the upstream Apache-2.0 `LICENSE` (SHA-256 `6f4030a4f059f5618cf00375afd8f5d18fe48e0fc2be1c2e98b2c32c90c44938`).
   The published crates do not contain it.
3. Each other code commit is one correction. `git log -p 78c9f7b..` shows the complete patch set.

## Corrections

| Commit | Crate | Correction | 12.0.0 commit | Upstream status (checked 2026-10-17) |
| --- | --- | --- | --- | --- |
| `94c9898` | lance-arrow | `pushdown_nulls` aligns with the child data offset | `a0e06c8` | Local; absent from v13.0.0 |
| `a02eb6b` | lance-encoding | `ZstdBufferCompressor::compress` reserves the bound instead of zero-filling it; the compressed bytes are the same | `871cd76` | The compress half of #9918; in v14.0.0-beta.16 and later, not in v13.0.0 |
| `b96fcd9` | lance-encoding | The column cache counts the rep/def levels that the encoder keeps for each batch, so a column of empty lists flushes | `ba3beae` | Local; v13.0.0 and main have the same code |

Each correction is the 12.0.0 commit, applied unchanged.

## Corrections that this branch drops

| 12.0.0 commit | Crate | Correction | Reason for removal |
| --- | --- | --- | --- |
| `a92733b` | lance-encoding | `do_record_validity` counts special entries | Upstream #9268 is in v13.0.0 (`repdef.rs:841`) |
| `309227a` | lance | `WriteParams::file_writer_options` reaches the data file writer | Upstream #9192 is in v13.0.0; remora no longer patches lance |

The `v12.0.0` README lists the corrections that the 12.0.0 set had already dropped.

Issue and pull-request numbers refer to https://github.com/lance-format/lance.
Lance 13.0.0 needs Arrow 58 and DataFusion 54.

## Removal rule

Remove a correction only when an adopted upstream Lance release includes it and passes the same remora checks.
An upstream version change alone is not proof.
The remora checks are the test suites of the remora crates that use Lance, one crate at a time:
remora-core, remora-storage, remora-engine, remora-builder, remora-capture and remora-stream.
For `b96fcd9`, the remora-storage tests `the_valuations_writer_holds_flat_memory` and
`the_sweeps_writer_holds_flat_memory` fail without the correction.

## License

The crates are Apache-2.0, as published by the Lance authors. See each crate's `LICENSE`.
