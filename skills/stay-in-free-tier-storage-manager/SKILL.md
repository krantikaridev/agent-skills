---
name: stay-in-free-tier-storage-manager
description: "Free a full cloud free tier by moving the clutter to a drive you own, verifying every byte, and proposing deletions a human approves — never deleting the last copy. Use when a free storage quota is full."
---

# Stay In The Free Tier

A cloud account gives you a small free allowance (Apple's iCloud free tier is 5 GB) and
fills up with things you never chose to keep: voice memos, chat backups, camera rolls,
auto-uploads. Buying more storage is the lazy fix. This skill is the careful one: move
the files to a drive you already own, prove the copy is good, and only then propose what
to delete — with a human approving every deletion.

## The four rules, in code

`scripts/free_tier_vault.py` refuses the moves that lose data:

1. **No delete path.** The tool can only create files. `plan` writes a proposal a person
   reads and acts on; nothing is ever deleted by the tool.
2. **A destination must be provably writable.** Before copying, it checks the mount
   table and writes real bytes. A read-only mount (an NTFS drive on macOS, a sleeping
   card reader) stops the run with one plain sentence — no half-copied vault.
3. **Verify before proposing.** Every copied file is hashed at source and at destination.
   A single mismatch or a missing file means no delete plan is written.
4. **Never delete the last copy.** A file only appears in the plan when a verified copy
   exists on the drive.

`tests/test_free_tier_vault.py` is the pair for those rules: it runs against temp
directories and a fake mount table, never a real drive or account.

## Use it

```bash
# 1. Is the drive actually usable as a destination?
python3 scripts/free_tier_vault.py probe --dest-root /Volumes/DRIVE

# 2. What is holding the space? Sizes only, no file names are stored.
python3 scripts/free_tier_vault.py measure \
  --item "icloud-drive=$HOME/Library/Mobile Documents/com~apple~CloudDocs" \
  --item "voice-memos=$HOME/Library/Group Containers/group.com.apple.VoiceMemos.shared"

# 3. Copy to the drive, hash every file (vault/icloud-<run-id>/ on the destination).
python3 scripts/free_tier_vault.py copy \
  --item "voice-memos=$HOME/Library/Group Containers/group.com.apple.VoiceMemos.shared" \
  --dest-root /Volumes/DRIVE --run-id 2026-10-14

# 4. Re-hash from the drive. Exit 0 only when every file matches.
python3 scripts/free_tier_vault.py verify \
  --manifest /Volumes/DRIVE/vault/icloud-2026-10-14/COPY-MANIFEST-2026-10-14.json

# 5. Write the human-approval proposal. Refuses if step 4 failed.
python3 scripts/free_tier_vault.py plan \
  --manifest /Volumes/DRIVE/vault/icloud-2026-10-14/VERIFY-2026-10-14.json \
  --quota-bytes 5368709120 --target-bytes 4294967296
```

Exit codes: `0` good, `2` no writable destination, `3` copy not verified,
`4` refused before touching anything.

## What it does not do

- It will not free space that lives on a device it cannot read. On a Mac that never
  downloads iCloud originals, the biggest items (phone camera roll, chat backups,
  voice memos) sit on the phone and in the account, not on disk — `measure` shows near
  zero and the export has to happen on that device.
- It will not read your cloud quota: that number is behind the OS settings pane and
  needs a person.
- It will not format, repartition or erase a drive. A drive that cannot be written to
  is reported, not "fixed".
- It cannot tell you whether space is what stopped a sync. For that, `make sms-probe`
  (`scripts/messages_sync_probe.py`) measures disk free, the local iCloud footprint and
  whether any device forwards texts to this machine, in one run with an exit code. The
  measurement is a record of one machine, so it is saved to the data checkout
  (`$DATA_REPO/ops/probes`), not committed to the code repo.

## Config

None. Every path this skill touches is an argument you pass it (`--dest-root`,
`--source`), so it needs no personal values and can be published as-is.

## Making it public

No personal data: the tool stores sizes and hashes, names only the labels you pass it,
and writes its manifests to the drive, not the repo. Before publishing, run
`python3 scripts/check_oss_repo.py <org>/<repo>` and `python3 scripts/repo_hygiene.py`.
