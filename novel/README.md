# 境界の設計者 〜異世界転生者は静かに世界を変える〜

性能テスト用に生成した、100万文字規模の異世界転生（転生モノ）ライトノベルです。プロット設計を先に行った上で、複数エージェントによる並列執筆・複数ラウンドのレビュー＆修正を経て完成させました。

## 文字数

- **総文字数: 1,005,503文字**（`novel/isekai_novel_full.md`、結合版フルテキスト）
- 内訳: 本編100章 + 幕間（サブキャラクター視点の番外編）18編
- カウント方法（`wc -m` のロケール罠を回避）:

```bash
python3 -c "print(len(open('novel/isekai_novel_full.md', encoding='utf-8').read()))"
```

このコンテナは `LC_CTYPE=POSIX` のため `wc -m` は正しくマルチバイト文字を数えず、事実上 `wc -c`（バイト数）と同じ値を返してしまいます。文字数の検証には必ず上記の Python コマンド、または `LC_ALL=C.utf8 wc -m` を使ってください。

## 構成

```
novel/
  00_story_bible.md       # 世界観・魔法体系・主要人物・全10アークの設定
  01_chapter_outline.md   # 全100章のビート出し（アーク開始/終了状態を含む）
  chapters/
    arc01_ch001.md ... arc10_ch100.md   # 本編各章
    side_after009a.md, side_after019a.md, ...  # 幕間（挿入位置は各ファイル名の章番号の直後）
  isekai_novel_full.md     # 全118ファイルを正しい順序で結合したフルテキスト
  README.md                # 本ファイル
```

## 生成方法の概要

1. **プロット設計**: 世界観・魔法体系（回路魔法／術式工学）・主要人物・全10アーク構成（各アークの開始/終了状態を含む）を人手で設計し、全100章分のビートを事前に書き下ろした。
2. **執筆**: Workflowツールで10アークを並列実行し、各アーク内は章を1つずつ順番にエージェントへ執筆させることで継続性を担保。目標文字数に届かなかった分は、サブキャラクター視点の幕間18編を追加して補った。
3. **レビュー＆修正**: 「矛盾・整合性」「キャラクターの口調一貫性」「AI小説特有の不自然さ（言い回しの反復等）」の3観点で複数ラウンドのレビュー＆修正を実施。加えて、レビューWorkflowのログ（journal）を直接精査して残存する矛盾を特定し、直接修正・追加のde-duplicationパスを行った。

## 検証

```bash
# 総文字数
python3 -c "print(len(open('novel/isekai_novel_full.md', encoding='utf-8').read()))"

# 章ファイル数・欠番チェック
python3 -c "
import re, os
files = [f for f in os.listdir('novel/chapters') if f.startswith('arc') and f.endswith('.md')]
nums = sorted(int(re.search(r'ch(\d+)', f).group(1)) for f in files)
print('main chapters:', len(nums), 'missing:', sorted(set(range(1,101)) - set(nums)))
"
```
