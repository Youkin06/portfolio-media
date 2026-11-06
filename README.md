# portfolio-media

[ポートフォリオ](https://youkin06.github.io/portfolio/) の作品ページで流す動画置き場です。
GitHub Pages で公開し、ポートフォリオから `https://youkin06.github.io/portfolio-media/videos/<YouTubeの動画ID>.mp4` で読み込みます。

## 動画の作り方

元の動画（YouTube にあげたもの）を、720p・音あり・途中から再生できる形で書き出します。

```sh
ffmpeg -i 元の動画.mp4 -vf "scale=-2:'min(720,ih)'" -c:v libx264 -preset slow -crf 27 \
  -pix_fmt yuv420p -c:a aac -b:a 128k -movflags +faststart videos/<動画ID>.mp4
```

- ファイル名は YouTube の動画 ID にそろえる（ポートフォリオ側は ID からファイル名を決めている）。
- 1ファイル 50MB を超えないようにする（超える場合は `-crf` を 28〜30 に上げる）。
- **重さの目安は 1秒あたり 1Mbps 以下**（遅いスマホ回線の 1.6Mbps でも止まらずに再生できるように）。書き出したら `ffprobe` で確かめ、超えていたら下の「重い動画の作り直し」で作り直す。

## 重い動画の作り直し

動きの激しい動画や 60fps の動画は、上の作り方だと 1秒あたり 1Mbps を超えることがある。そのときは、映像のデータ量を決めて2回に分けて書き出す（2パス）。

```sh
# 映像 900kbps（60fps の動画は 900、24〜30fps の動画は 600 が目安）。音は 96kbps。
ffmpeg -y -i 元の動画.mp4 -vf "scale=1280:-2" -c:v libx264 -preset slow -b:v 900k -maxrate 1800k -bufsize 3600k \
  -pix_fmt yuv420p -profile:v high -g 120 -pass 1 -an -f mp4 /dev/null
ffmpeg -y -i 元の動画.mp4 -vf "scale=1280:-2" -c:v libx264 -preset slow -b:v 900k -maxrate 1800k -bufsize 3600k \
  -pix_fmt yuv420p -profile:v high -g 120 -pass 2 -c:a aac -b:a 96k -movflags +faststart videos/<動画ID>.mp4
```

- 2026-11-06：Sausage Smash（`YkrrAr-tfY4`、60fps）を 900kbps で 18.6MB → 8.7MB、じいや！巨大ロボで紅茶を注ぎなさい！（`aGIQZD2Nkak`、24fps）を 600kbps で 21.3MB → 11.7MB に作り直した。
