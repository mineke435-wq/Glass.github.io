# Glass Sampler

Web Audio サンプラー（パッド／ピアノ／ボコーダー／スペクトラルゲート／グラニュラー／ドラム／オートプレイ）と、
音に反応する 3D ルームビジュアライザーを 1 ファイルにまとめた PWA です。

**v2: ハンドトラッキングで球体を「揉む」** — 従来の動き検出によるフィルター／リバーブ操作は廃止し、
MediaPipe Hands による手指キャプチャに置き換えました。

## ハンドトラッキング

ビジュアライザー下の **✋ HAND** でカメラを起動し、カメラに手を翳します。

| 操作 | ビジュアライザー | 音への既定マッピング |
|---|---|---|
| 指先・関節・手のひらで押す | 接触部分がへこむ（ジェリーの余韻つき） | SQUEEZE → Gate Focus / Gate Mix |
| 親指と人差し指でつまんで引く | 球体をつかんで伸ばす | PULL → Reverb |
| 手を左右へ動かす | 仮想の手が追従 | HAND X → Gate Freq |
| 手を上下へ動かす | 同上 | HAND Y → Filter |
| 手の動きの速さ | – | SPEED → Pitch Bend |
| 奥（画面の中）へ手を引く | 仮想の手が球体の奥へ入る | – |
| 両手で挟む | 両側から同時に変形 | SQUEEZE に加算 |

- 手が映っていない間は、ビジュアルも音も従来どおり（モジュレーション量は 0）。
- **Hand Kneading** カード：Finger（指の太さ）/ Push / Pull / Jelly（バネの硬さ）/ Damping / Smooth（ジッター除去）/ Glow / Depth（奥行き感度）。
  既定値は 1.0。`HAND → SOUND` の各行でソース（SQUEEZE, PULL, HAND X/Y, SPEED, OPEN⇄FIST）・行き先・量（−1〜+1）を変えられます。
  行き先は既存の Gate 各値・Filter・Reverb・Dynamics に加え Pitch Bend / Vibrato / Tape Speed。
- `FULL 高精度 / LITE 軽量` で MediaPipe のモデルを切り替え（重い端末は LITE）。
- 設定は localStorage（`gs_hand`）に保存。既存プリセットには含まれません（互換性維持）。

## 動かし方

カメラと Service Worker には **HTTPS または localhost** が必要です（`file://` では動きません）。

```sh
python3 -m http.server 8000   # → http://localhost:8000
```

GitHub Pages: リポジトリの Settings → Pages で `main` / `(root)` を公開すると、そのままスマホにインストールできます。

## ファイル

```
index.html      アプリ本体（HTML/CSS/JS 一体）
sw.js           オフライン用 Service Worker
manifest.json   PWA マニフェスト
icon-192.png / icon-512.png
```

外部ライブラリ（CDN）: three.js r128、@mediapipe/hands 0.4.1675469240、Google Fonts。
初回のみ通信し、以降は Service Worker がキャッシュします。

ライセンスは未設定です。公開時にお好みのものを追加してください。
