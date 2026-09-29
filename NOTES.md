# NOTES

## Versions

- anchor-cli 1.2.0 (program is anchor-lang 1.1.2)
- solana-cli 4.1.2 (Agave)
- node v24.13.0
- @codama/cli 1.6.3
- @codama/renderers-js 2.5.0
- @codama/nodes-from-anchor 1.5.6
- @solana/kit 8.3.0

## TODO 3: why fundraiser and vault had to be passed

Codama can only derive a PDA when every one of its seeds is something the builder already holds: a constant, an instruction argument, or another account's address. `contributorAccount` is seeded by `["contributor", fundraiser, contributor]` and `contributorAta` by `[contributor, token_program, mint_to_raise]`, which are all addresses already in the input, so the Async builder derives them (and `tokenProgram` / `systemProgram` have fixed addresses). In `contribute`, `fundraiser` is seeded by `fundraiser.maker` and `vault` by `[fundraiser, token_program, fundraiser.mint_to_raise]`, i.e. by fields stored inside the fundraiser account's data, which the builder cannot see without fetching it, so those two stay required (unlike `initialize`, where `fundraiser` is seeded by the `maker` signer and is derived).

## Running on Windows

- `npx codama init` / `codama run js` fail on Windows (the `env -S` shebang, then an `e:` drive path passed to the ESM loader), so I generated the client with the same pipeline in a few lines of Node: `rootNodeFromAnchor(idl)` then `visit(root, renderVisitor("clients/js"))`. `codama.json` is committed as the checkpoint asks.
- `anchor test` shells out to WSL and `solana-test-validator` cannot create a ledger here, so the suite ran on Surfpool with the program deployed from `target/deploy`: codama 4/4 (TODO 1–3 + bonus), fundraiser 7/7, time-window 3/3. `time-window-bankrun.ts` needs bankrun's native binary, which has no Windows build.
