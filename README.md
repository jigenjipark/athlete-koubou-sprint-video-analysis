# AKT骨格分析アプリ

短距離走選手向けのフォーム分析Webアプリ。動画を選択すると、AI姿勢推定によって骨格線と関節角度をリアルタイムに重ねて表示する。

複数コーチが練習現場でスマホ/PCから使うことを想定しており、インストール不要・URLを開くだけで利用できる。

## 現在バージョン

**v2.2.0**

## 主な機能

- ローカル動画ファイル選択(MP4/MOV/WebM等、縦動画・横動画どちらも対応)
- 骨格線・関節点のオーバーレイ表示
- 関節角度のリアルタイム表示・色分け
  - 膝(緑)
  - 肘(水色)
  - 股関節(紫)
  - 足首・つま先方向(オレンジ)
- 動画操作
  - 再生 / 一時停止
  - 1フレーム送り・戻し
  - 0.1秒送り・戻し
  - 再生速度変更(×0.25〜×2.0)
  - ループ再生
  - シークバー / 現在時間・全体時間表示
- 読み込み・処理状況のログ表示

## 起動方法

`index.html` を静的ホスティング(GitHub Pages / Vercel等)で公開し、ブラウザでアクセスするだけで動作する。サーバサイド処理は行わず、姿勢推定はすべてブラウザ内(クライアントサイド)で実行される。

ローカルで確認する場合はWebサーバ経由で開く必要がある(`file://` で直接開くとカメラ/動画関連の一部APIが制限される場合がある)。

```bash
python -m http.server 8000
# http://localhost:8000/index.html にアクセス
```

## 使用技術

- HTML / CSS / JavaScript(単一ファイル構成)
- [MediaPipe Tasks Vision](https://developers.google.com/mediapipe) (`@mediapipe/tasks-vision@0.10.10`)
- BlazePose (`pose_landmarker_lite`, 33ランドマーク)

なぜMoveNetではなくBlazePoseを使っているか、なぜLiteモデルを選んだかの経緯は [HANDOVER.md](./HANDOVER.md) を参照。

## 推奨ブラウザ

- Chrome / Safari の最新版(スマホ・PC問わず)
- LINE等のアプリ内ブラウザでは正常に動作しない場合があるため、標準ブラウザで開くこと

## 関節角度の定義

BlazePoseのランドマーク番号(体格ではなく被写体本人の左右基準)。

| 部位 | 左 | 右 |
|---|---|---|
| 膝 | Hip(24)-Knee(26)-Ankle(28) | Hip(23)-Knee(25)-Ankle(27) |
| 肘 | Shoulder(12)-Elbow(14)-Wrist(16) | Shoulder(11)-Elbow(13)-Wrist(15) |
| 股関節 | Shoulder(12)-Hip(24)-Knee(26) | Shoulder(11)-Hip(23)-Knee(25) |
| 足首 | Knee(26)-Ankle(28)-FootIndex(32) | Knee(25)-Ankle(27)-FootIndex(31) |

## 既知の課題

- 1フレーム送り・戻しが `FRAME = 1/30`(30fps前提)で固定されており、60fps/120fps動画では正確な1フレーム送りにならない
- 推論は約20fpsに間引いているため、高fps動画を通常速度で再生すると全フレームは解析されない

詳細な今後の開発候補は [HANDOVER.md](./HANDOVER.md) を参照。
