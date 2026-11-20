# 第0話: Codex 開発前の準備（Mac）

> このキットに `spec.md` は入っていません。以前作成した本物の `spec.md` を、あなた自身がリポジトリのルートへコピーしてください。こちらの `AGENTS.md` は仕様を置き換えるものではありません。

## A. 本人が手動でやること（最初に必要）

- [ ] GitHub で空のリポジトリを作成する。アプリのソースコード用に MIT License を選んでよい。外部のモデル・声まで MIT になるわけではない。
- [ ] `git clone ...` でローカルに複製する。
- [ ] `spec.md` と、このキットの `AGENTS.md`、`.gitignore` をリポジトリ直下に配置する。`PREPARATION_CHECKLIST.md` と `CODEX_FIRST_PROMPT.md` は配置しても手元に置くだけでもよい。
- [ ] **Apple Silicon (arm64) か確認**。`mlx-whisper` は Apple Silicon 前提。Intel Mac の場合は使用ライブラリの変更を要検討。
- [ ] マイク・ヘッドホン/スピーカーを用意し、macOS のマイクアクセスを実行アプリへ許可する（許可ダイアログは本人が操作）。ハウリング防止に初回はヘッドホン推奨。
- [ ] 使用する声の規約を確認し、YouTube公開・収益化・クレジット表示・「AI彼女」のオリジナルキャラクターとの組合せが許されるか判断する。将来の声変換・学習・歌唱の権利は別途確認。

### ホスト情報を確認するコマンド

```bash
uname -m
sw_vers
sysctl -n hw.memsize
df -h .
git --version
python3 --version
command -v brew || true
command -v ollama || true
command -v codex || true
```

`sysctl -n hw.memsize` はメモリ容量を **バイト数** で返します。8GB程度なら軽いモデル、16GB以上なら4Bモデルを最初の候補にしますが、Whisper・TTS・アプリとの同時稼働やMac上の他アプリ次第で要調整です。空き容量は数GB以上、モデル切替を試すなら余裕をもたせてください。

## B. 本人がインストール・ログインするもの（Codexから補助可能）

- [ ] Codex を利用できる状態にする（既に使用中なら追加作業なし）。
- [ ] Pythonの動作環境を確認する。Codexに仕様に合うPythonと仮想環境のセットアップを任せてもよい。
- [ ] ローカルLLMの実行環境 **Ollama** をインストールし、必要に応じて初回起動・macOS側の許可を済ませる。Codexにインストールを依頼してもよいが、OSレベルの承認は自分で行う。
- [ ] TTSは、最初に実際の声が出る **VOICEVOXデスクトップアプリ**を導入・起動するのが簡単。`http://127.0.0.1:50021/speakers` で音声一覧が返るか確認できる。**AivisSpeech**を使う場合は通常 `http://127.0.0.1:10101/speakers` を確認する。どちらも起動状態と実際のエンジン設定に依存する。
- [ ] Groqによる比較を行う時だけ、GroqCloudのアカウント作成とAPIキー発行を行う。**ローカルだけで開発するなら不要**。キーは `.env` か個人の環境変数へ置き、GitHubには送らない。料金・上限は利用時にダッシュボードで確認。

## C. Codexに任せること

- [ ] `spec.md` の全体チェック・マイルストーン分解・受入条件の整理
- [ ] Python/依存ライブラリの仮想環境、依存ロック、実行スクリプトとテストの作成
- [ ] **Silero VAD** の導入（アプリの依存関係として）
- [ ] **mlx-whisper Small** の導入とモデル自動取得・キャッシュ（Apple Silicon）
- [ ] **OllamaにQwen3.5を取得**させる手順や導入スクリプトの実装
- [ ] TTSへのHTTP接続、`/speakers` から話者IDを取得・設定する機能
- [ ] Ollama / GroqのLLM切替・ローカル初期値・外部送信の明示的な同意
- [ ] PySide6 UI、録音、VAD、STT、LLM、TTS、再生、停止、例外処理と会話履歴の実装
- [ ] モックを使うユニットテスト、統合テスト、診断コマンド、README、`.env.example`、サードパーティ通知の作成
- [ ] モデル重量ファイル・個人情報・鍵をGit管理しないように保護

## D. モデルの取り扱い方（重要）

| コンポーネント | 初期案 | 重量ファイルの置き場 | 自動化 |
|---|---|---|---|
| VAD | Silero VAD | pipパッケージ/キャッシュ | Codexでほぼ可 |
| STT | `mlx-community/whisper-small-mlx` | Hugging Faceのローカルキャッシュ等 | Codexで可。初回通信あり |
| ローカルLLM | `qwen3.5:2b` or `qwen3.5:4b` | Ollama管理のモデル格納領域 | `ollama pull` 等で可 |
| TTS | VOICEVOX互換API | 外部のVOICEVOX/AivisSpeechアプリと各音源の管理領域 | 接続コードは可。インストール承認と声の権利判断は本人 |
| 比較用LLM | GroqCloud | ローカルにはモデル不要 | API接続コードは可。APIキー取得は本人 |

**原則として上記モデルをGitHubリポジトリへ直接コピーしない**。ソースコードと「再取得する手順」を公開すれば十分です。

## E. 事前/初回の動作確認

Codex がアプリを作成した後、次の順で確認します。

1. Ollama起動時に `curl http://127.0.0.1:11434/api/tags` が通る。
2. `ollama list` で指定モデルが見える。モデルが無ければCodex作成のセットアップコマンド、または `ollama pull qwen3.5:2b` / `ollama pull qwen3.5:4b` を使う。
3. VOICEVOX起動時に `curl http://127.0.0.1:50021/speakers`、またはAivisSpeech起動時に `curl http://127.0.0.1:10101/speakers` が通る。話者IDは実際のレスポンスに従う。
4. 自動テストが通る → マイク録音 → 文字認識 → ローカルLLM応答 → 指定した声で再生 → 停止・再開の順に実機確認する。
5. オフライン時でも、ローカルモデルの取得後なら会話できるか確認する。初回の依存取得・アプリ更新は通信が必要な場合がある。
6. Groqモードは実験する時だけ明示的に有効化し、送信対象・履歴非引継ぎ・費用/レート制限を確認する。

## F. 公開前のチェック

- [ ] MIT `LICENSE` と第三者コンポーネントの規約を混同していない。
- [ ] PySide6 / Qt / AivisSpeech Engine等のLGPLライブラリを使用する場合、配布形態に必要なライセンス表示などを確認した。
- [ ] 音声エンジン・**話者/声モデル**・生成音声の規約をそれぞれ確認した。VOICEVOX利用時はクレジット表記や音源別の条件にも従う。
- [ ] 音源・モデル・キャラクター素材を無断でリポジトリに同梱していない。
- [ ] APIキー・`.env`・録音・会話ログ・キャッシュ・重量モデルがコミット/履歴に含まれていない。
- [ ] GitHub Actions等で実機マイク・声モデルが無くても最低限のテストが動く。
- [ ] 現時点の技術選択と、将来の歌唱・声変換・複数キャラへの拡張予定を混同せず説明している。

## 公式の主な確認先（2026-11-19時点）

- Ollama: https://ollama.com/download/mac / https://ollama.com/library/qwen3.5
- mlx-whisper: https://github.com/ml-explore/mlx-examples/tree/main/whisper
- Whisper Small MLX: https://huggingface.co/mlx-community/whisper-small-mlx
- Silero VAD: https://github.com/snakers4/silero-vad
- VOICEVOX: https://voicevox.hiroshiba.jp/ / https://voicevox.hiroshiba.jp/term/
- AivisSpeech: https://aivis-project.com/faq / https://github.com/Aivis-Project/AivisSpeech-Engine
- GroqCloud API: https://console.groq.com/keys / https://console.groq.com/docs/models
- Codex（AGENTS.md）: https://developers.openai.com/cookbook/examples/codex/iterating-development-workflows-with-codex
