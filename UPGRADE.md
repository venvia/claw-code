# Upgrade Notes

## Custom Patches in this Fork

### Patch 1: Stream fallback hardening (2026-04-25)

**File:** `rust/crates/tools/src/lib.rs` — `stream_with_provider` function

**Problem:** When vLLM's stream drops, `stream.next_event().await?` short-circuits and the non-stream fallback is never reached, causing `edit_file` to fail with `missing field new_string`.

**Fix:** Wrap the stream loop in an async block. On failure, fall back to `send_message(stream: false)`.

**PR:** https://github.com/venvia/claw-code/pull/3

---

## Upstream Status (confirmed 2026-04-25)

- **Upstream repo:** `ultraworkers/claw-code` — **Issues disabled** (`has_issues: false`), no external contributions accepted
- **Cannot open PRs or issues** upstream; fork must be maintained independently
- **Remote:** `upstream` → `git@github.com:ultraworkers/claw-code.git`

---

## Upgrading from Upstream

When `ultraworkers/claw-code` releases a new version:

```bash
cd ~/projects/claw-code

# 1. Fetch upstream latest
git fetch upstream

# 2. Check what upstream changed
git log origin/main..upstream/main --oneline

# 3. Rebase our patches onto upstream latest
git rebase upstream/main

# 4. Verify compilation
cd rust && cargo build --release --bin claw

# 5. Push to venvia
cd .. && git push origin main

# 6. Install new binary
cp rust/target/release/claw ~/.local/claw-code/rust/target/debug/claw
```

## Installation Path

```
~/.local/claw-code/rust/target/debug/claw
```

`.zshrc`'s `CLAW_BINARY` points here, used by `claw-qwen3.6-27b` and `claw-minimax`.
