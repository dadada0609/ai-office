# デスクトップ ⇔ ノートPC ファイル同期手順（GitHub利用）

`C:\Users\dchym\Projects\ai-office` フォルダを、GitHubのプライベートリポジトリ
`https://github.com/dadada0609/ai-office` 経由でデスクトップPCとノートPCの間で同期するための手順書。

## 0. 前提

- 両方のPCに [Git](https://git-scm.com/) がインストールされていること
- GitHubアカウント（`dadada0609`）でログインできること
- リポジトリ `https://github.com/dadada0609/ai-office` は **Private** に設定しておくこと（個人ファイル漏洩防止のため）

```powershell
git --version   # インストール確認
```

初回のみ、両PCでユーザー情報を設定しておく。

```powershell
git config --global user.name "あなたの名前"
git config --global user.email "あなたのメールアドレス"
```

---

## 1. デスクトップPC側：初回セットアップ（実施済み）

対象フォルダ：`C:\Users\dchym\Projects\ai-office`

```powershell
cd C:\Users\dchym\Projects\ai-office
git init
git branch -M main
git remote add origin https://github.com/dadada0609/ai-office.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

`.gitignore` は同フォルダに作成済み（`node_modules/`, `.claude/`, OSの一時ファイルなどを除外）。

初回プッシュ時にGitHubの認証（ブラウザ認証 or Personal Access Token）を求められる場合は指示に従って認証する。

---

## 2. ノートPC側：初回セットアップ

ノートPC側では `git init` は不要。以下のコマンドで clone するだけでよい。

```powershell
cd C:\Users\dchym\Projects
git clone https://github.com/dadada0609/ai-office.git
```

これで `C:\Users\dchym\Projects\ai-office` がノートPC側にも作成される。
（フォルダパスをデスクトップPCと揃えておくと、手順を使い回しやすい）

---

## 3. 日常の同期フロー

**作業を始める前に必ず pull する** ことが、競合を避ける最大のコツ。

### 作業を始める前（PCを切り替えた直後）

```powershell
cd C:\Users\dchym\Projects\ai-office
git pull
```

### 作業が終わったら（別のPCに移る前）

```powershell
cd C:\Users\dchym\Projects\ai-office
git add .
git commit -m "変更内容の簡単な説明"
git push
```

### 基本サイクルまとめ

```
PCを開く → git pull → 作業する → git add . / git commit / git push → PCを閉じる
```

これを両PCで徹底すれば、常に最新状態を保てる。

---

## 4. 競合（コンフリクト）が起きたら

両PCで同じファイルを同期せずに編集してしまうと、`git pull` 時にコンフリクトが発生することがある。

```powershell
git status   # 競合しているファイルを確認
```

該当ファイルを開くと以下のようなマーカーが入っているので、正しい内容だけを残して手動で編集する。

```
<<<<<<< HEAD
（今いるPC側の内容）
=======
（pullしてきた内容）
>>>>>>> origin/main
```

編集後、マーカーを全て削除してから：

```powershell
git add .
git commit -m "Resolve conflict"
git push
```

---

## 5. 運用上の注意点

- **リポジトリは必ず Private のまま運用する**（個人ファイルが公開されるのを防ぐ）
- パスワード・APIキー・トークンなどの機密情報は `.gitignore` で除外し、絶対にコミットしない
- 大容量ファイル（動画、大きなzip等）はGit管理に向かない。必要なら [Git LFS](https://git-lfs.github.com/) を検討するか、クラウドストレージ（OneDrive等）と使い分ける
- 作業を切り替える前の `push` を忘れると、もう一方のPCで古い状態のまま作業してしまうので注意
- 万が一 `push` を忘れて両PCで作業してしまった場合は、慌てず `git pull` してコンフリクトを解消すればよい（データは失われない）

---

## 6. よく使うコマンド早見表

| コマンド | 用途 |
|---|---|
| `git pull` | 最新の変更を取得 |
| `git add .` | 変更したファイルをステージング |
| `git commit -m "メッセージ"` | 変更を記録 |
| `git push` | GitHubへ変更を送信 |
| `git status` | 現在の変更状況を確認 |
| `git log --oneline` | 変更履歴を確認 |
