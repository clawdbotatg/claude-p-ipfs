# ipfs — mirror module artifacts to IPFS (bgipfs)

The availability layer of the trust story: a published module version can be
mirrored as a content-addressed tarball, so a pinned SHA stays fetchable even
if its GitHub repo vanishes. Optional by design — attesters' clones already
satisfy a pinned SHA; this adds a URL-stable mirror.

## What it is

One shell wrapper, `ipfs` (setup/upload/url/mirror), around the official
[bgipfs](https://www.bgipfs.com/SKILL.md) CLI. `mirror <module>` produces a
deterministic `git archive` tarball of a module at its pinned SHA, uploads
it, and prints the CID for the `cid=…` column of `modules.lock`.

## What it needs

- `npm` (node). `./ipfs setup` installs the bgipfs package **into this
  folder** (node_modules, gitignored) — the engine stays dependency-free.
- A bgipfs API key (bgipfs.com account settings), configured once via
  `node_modules/.bin/bgipfs upload config init` (stored in `~/.bgipfs/`).

## Wiring

`./ipfs setup`, then the config init above with the human's API key.

## How to verify

`./ipfs url bafybeigdyrzt5sfp7udm7hu76uh7y26nf3efuylqabf3oclgtqy55fbzdi`
prints a gateway URL (offline). Live: `./ipfs upload MODULE.md` → a CID, and
fetching the gateway URL returns this file.

## What can go wrong

- The API key is a paid-ish credential — it lives in `~/.bgipfs/`, not in
  any repo. Uploads are public and permanent-ish: never mirror anything
  containing secrets (module repos should never contain secrets anyway —
  the audit checks).
- A CID in modules.lock is a claim, not a guarantee of pinning forever;
  the git URL remains the primary source, CID the fallback.
- bgipfs is a community service; degrade to "no mirror" if it's down.

## How to uninstall

`tools/module remove ipfs`. CIDs already in modules.lock keep working (they
point at the network, not this tool). Credentials: `rm -rf ~/.bgipfs`.
