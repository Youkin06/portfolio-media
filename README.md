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
