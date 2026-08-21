# 上線步驟（一次性，四步）

程式與資料都備好了，只差這個 repo 還不存在。**這四步要在 Mac 的終端機自己跑** ——
我不代為登入 GitHub，也不在你的機器上跑 git。

## 1. 搬到位並建 repo

```bash
mv ~/outbox/broker-research-digest ~/broker-research-digest
cd ~/broker-research-digest
git init -b main
git add -A
git commit -m "init: 站台骨架與 2026-W34 的圖"
gh repo create GunDamnBoy/broker-research-digest --public --source=. --push
```

沒有 `gh` 的話，先在 GitHub 網頁上開一個空的 `broker-research-digest`（不要勾
README／gitignore／license，那會製造第一次 push 的衝突），然後：

```bash
git remote add origin https://github.com/GunDamnBoy/broker-research-digest.git
git push -u origin main
```

> **第一期的資料刻意不在這次 commit 裡。** `data/` 只有一個空的 `index.json`。
> 那一期由第 3 步的排程走完整條發布路徑寫進來 —— 手工放進去雖然快一秒，
> 但那樣就沒有人驗證過發布這條線真的會動，而**第一次會動與第五十次會動
> 是同一件事，要在第一次就知道**。

## 2. 開 Pages

GitHub → repo → Settings → Pages → Source 選 **GitHub Actions**。
`.github/workflows/deploy.yml` 已經在裡面，推上去就會跑。
網址會是 `https://gundamnboy.github.io/broker-research-digest/`。

## 3. 裝發布排程

```bash
mkdir -p ~/outbox/research
cp ~/kb-core/launchd/com.kenny.kbpublish.research.plist ~/Library/LaunchAgents/
launchctl load ~/Library/LaunchAgents/com.kenny.kbpublish.research.plist
launchctl list | grep kbpublish.research
```

## 4. 把新資料夾接上 Cowork

Cowork 設定裡把 `~/broker-research-digest` 加進來 ——
不接的話，每週那一輪把圖複製進 repo 的那一步會做不到，
而**草稿仍然會發布成功、只是圖是 404**。

---

## 驗收（做完四步之後）

```bash
cat ~/outbox/research/*.receipt.json     # 第一期的回執，要 exit 0
open https://gundamnboy.github.io/broker-research-digest/
```

回執如果是 `exit 14 @ rebase`，多半是還沒設 upstream；`exit 10 @ verify`
是內容被閘門擋下來，訊息會指名是哪一條檢查。
**沒有回執代表 publish 根本沒跑**（排程沒載入，或 outbox 路徑不對）——
那跟「回執說失敗」是兩件事。

## 之後每一週

不用做任何事。週日 23:00 排程會跑完整輪，發布走
`com.kenny.kbpublish.research`，網站自動重建。要手動補跑就手動觸發那個排程。
