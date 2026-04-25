# 升級紀錄

## venvia Fork 的自訂 Patch

### Patch 1: Stream fallback 強化（2026-04-25）

**檔案：** `rust/crates/tools/src/lib.rs` — `stream_with_provider` 函數

**問題：** 當 vLLM 的 stream 斷掉時，`stream.next_event().await?` 直接拋錯，non-stream fallback 永遠執行不到，導致 `edit_file` 噴 `missing field new_string`。

**修法：** 把 stream loop 包在 async block 裡，失敗時 fallback 到 `send_message(stream: false)`。

**Commit：** `7a0886e`

---

## 上游狀態（2026-04-25 確認）

- **上游 repo：** `ultraworkers/claw-code` — **Issues 關閉**（`has_issues: false`），不接受外部貢獻
- **無法開 PR 或 issue**，fork 需自行維護
- **Remote：** `upstream` → `git@github.com:ultraworkers/claw-code.git`

---

## 升級上游流程

當 ultraworkers/claw-code 有新版時：

```bash
cd ~/projects/claw-code

# 1. 拉取上游最新
git fetch upstream

# 2. 檢查上游更新了什麼
git log origin/main..upstream/main --oneline

# 3. Rebase 我們的 patch 到上游最新
git rebase upstream/main

# 若有衝突：
# - 解決衝突
# - git add <衝突檔案>
# - git rebase --continue

# 4. 驗證編譯
cd rust && cargo build --release --bin claw

# 5. Push 到 venvia
cd .. && git push origin main

# 6. 安裝新版本
cp rust/target/release/claw ~/.local/claw-code/rust/target/debug/claw
```

**注意：** 如果上游已經修了 stream fallback（merge 了類似的 patch），可以用 `git log` 檢查並考慮 drop 我們的 patch：

```bash
# 檢查 upstream 是否已有類似修正
git log upstream/main --oneline --grep="stream.*fallback" --all
```

## 安裝路徑

編譯後的 binary 安裝到：

```
~/.local/claw-code/rust/target/debug/claw
```

`.zshrc` 的 `CLAW_BINARY` 指向這裡，`claw-qwen3.6-27b` 和 `claw-minimax` 都會用到。

> 🧠 qwen3.6-27b-fp8 · Think: high
