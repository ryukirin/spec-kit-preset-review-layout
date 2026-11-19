# Spec Review Layout

[English](README.md) | [简体中文](README.zh-CN.md) | 日本語

[Spec Kit](https://github.com/github/spec-kit) の preset。`spec.md` をレビューしやすい版面にする。Mermaid 図による全体図を加え、受入シナリオ、Edge Cases、Key Entities を表にし、機能要件を 1 行の本文とラベル付きの子項目、条件表で書く。spec に書く内容は変わらない。

## spec.md の変更点

| 節 | 書き方 |
|---|---|
| Overview Map（新設、User Scenarios & Testing の前） | Screen flow、Data sources、Interactions、Lifecycle の 4 種類の図のうち、機能に合うものだけを残す（1 つ以上）。図には名前と番号（`US1`、`FR-012`）だけを書く。Lifecycle 図の状態は状態 × 操作の表と一致させ、開始と終了の `[*]` は描いてよい。画面がある機能では Screen layout の表を加える |
| 受入シナリオ | 列が `# \| Given \| When \| Then \| Refs` の表。1 行に 1 つの場合を書く。`When` には操作か事象（通信の切断など）を 1 つだけ書く。`Then` の `•` 1 つが確認項目 1 つに当たる |
| Edge Cases | 列が `Case \| Outcome \| Refs` の表 |
| Functional Requirements | 各 FR は行頭の `- **FR-###**:` を保ち、細かい決まりは `**Label**:` の子項目に書く。条件の組み合わせの表と状態 × 操作の表は FR の直下、子項目より前に置く。状態表のセルには `→ 状態` を書き、利用者の操作ができない組み合わせは `— (not allowed)`、起きない事象は `— (cannot occur)` と書く。子項目を指すときは `FR-012 (Sort)` と書く |
| Key Entities | 列が `Entity \| One record is \| Attributes \| Used by` の表 |

削除したシナリオと FR は番号を残し、`(removed YYYY-MM-DD: reason)` と書く。

本文は利用者が求める言語で書く。見出し、ラベル、列名、記号、図のタイトルなどの固定の文字は、テンプレートどおり英語のままにする。テンプレートの角括弧内の文字は内容の置き場所である。

## 構成

| ファイル | 種類 | 方式 | 役割 |
|---|---|---|---|
| `templates/spec-template.md` | template | replace | Spec Kit v1.4.2 の spec テンプレートに Overview Map、表形式のシナリオ、FR の子項目を加えたもの |
| `commands/speckit.specify.md` | command | append | `spec.md` を書く前に版面の規則を読む。テンプレートにも同じ規則の場所が書いてある |
| `commands/speckit.clarify.md` | command | append | clarify が回答を書き戻すときも版面を保つ（箇条ではなく表の行を足すなど） |
| `rules/spec-layout.md` | 規則 | — | 版面の規則。両方のコマンドが読む |

版面の規則はテンプレートのコメントではなく `rules/spec-layout.md` に置く。`spec.md` を書き終えるとコメントは消されるため、コメントにだけ書いた規則は `/speckit.clarify` から読めない。

## 向いている場面

- spec を人がレビューする（チームのレビュー、pull request など）
- 画面、状態の変化、条件の組み合わせが多い機能

次の場合は向かない。

- Given/When/Then の文を解析する拡張（`bdd`、`reqnroll-bdd`、`test-first-governance` など）を使っている
- `spec-template` の上流の変更を自動で取り込みたい。この preset はテンプレート全体を置き換える

## 動作環境

- Spec Kit CLI 1.0.0 以降。1.0.6 と 1.4.2 で確認済み
- `specify init` を実行済みのプロジェクト
- Claude Code の integration（skills モード）で確認済み。generic integration は preset のコマンド上書きを登録しないため、テンプレートだけが効く
- ほかの拡張には依存しない

## インストール

プロジェクトのルートで実行する。

```bash
specify preset add --from https://github.com/ryukirin/spec-kit-preset-review-layout/archive/refs/tags/v0.6.1.zip
```

ローカルのフォルダーから入れる場合:

```bash
specify preset add --dev <このフォルダーのパス>
```

Spec Kit 1.2.4 以降は `.presetignore` に従って `.git/` をコピーしない。それより前のバージョンでは、クローンしたリポジトリの `.git/` もプロジェクトにコピーされ、Windows ではその後の `specify preset remove` が読み取り専用の git ファイルで失敗する。これらのバージョンでは、上の release アーカイブから入れるか、`.git/` を含まないコピーを指定する。

## ほかの preset との併用

この preset は `spec-template` を丸ごと置き換え、`speckit.specify` と `speckit.clarify` に内容を追加する。Spec Kit はテンプレートとコマンドを 1 つずつ解決する。priority の数字が小さいほど優先され、同じ priority のときは preset ID の文字順に並ぶ。`replace` の層はその下の層をすべて隠し、`append` と `wrap` の層は重なる。

両方とも既定の priority（10）のときの結果は次のとおり。例は 2026 年 11 月時点のコミュニティカタログから取った。

| 相手の preset | 例 | 結果 |
|---|---|---|
| `spec-template` を置き換え、先に並ぶ | `mde`、`screenwriting`、`sicario-core` | この preset のテンプレートは使われない |
| `spec-template` を置き換え、後に並ぶ | `toc-navigation` | 相手のテンプレートは使われない |
| `spec-template` に追加または包む形で、先に並ぶ | `a11y-governance`、`okr-bet`、`security-governance` | 相手の内容がこの preset のテンプレートに加わり、両方とも効く |
| `spec-template` に追加または包む形で、後に並ぶ | `specassay`、`test-first-governance`、`workflow-preset` | 相手の内容は使われない |
| `speckit.specify` を置き換え、先に並ぶ | `canon-core`、`command-density` | この preset が specify に追加した指示は使われない。テンプレートに版面の規則の場所が書いてあるため、相手のコマンドが spec テンプレートを使う限り版面は効く |
| `speckit.clarify` を置き換え、先に並ぶ | `canon-core`、`claude-ask-questions`、`command-density` | この preset が clarify に追加した規則は使われず、clarify が回答を書き戻すときに版面が保たれない |

`speckit.specify` や `speckit.clarify` に追加または包む形の preset は、順序にかかわらずこの preset と重ねて使える。

順序を変えるときは、インストール時に `--priority` を指定する。`specify preset set-priority` は書き込み済みのコマンドファイルと skill ファイルを更新しない（github/spec-kit#5087）ため、いったんアンインストールし、新しい priority で入れ直す。2 つの preset が同じテンプレートやコマンドを置き換える場合、順序にかかわらず片方しか効かない。

同梱の `lean` preset には対応しない。`lean` の specify コマンドは spec テンプレートを使わない。

## インストールの確認

```bash
specify preset resolve spec-template
```

出力の末尾が `(top layer from: spec-review-layout v0.6.1)` になる。Claude Code では、`.claude/skills/speckit-specify/SKILL.md` と `.claude/skills/speckit-clarify/SKILL.md` の末尾に `## Review Layout (spec-review-layout preset)` の節が入る。

## 使い方

`/speckit.specify` と `/speckit.clarify` をいつもどおり実行する（Claude Code では `/speckit-specify` と `/speckit-clarify`）。`/speckit.specify` の実行後、`checklists/requirements.md` に版面の確認項目を並べた Review Layout の節が入る。

## 既知の制限

- テンプレートは全体を置き換える。Spec Kit が `spec-template` のほかの節を変えたときは手で取り込む。節単位で置き換える方式が上流で予定されている（github/spec-kit#5421）。
- プロジェクトに `.specify/templates/overrides/spec-template.md` があると、この preset のテンプレートは使われない（github/spec-kit#5337）。
- コマンドの上書きはインストール時に agent のコマンドファイルまたは skill ファイルへ書き込まれる。`specify preset disable` では消えないため、`specify preset remove` を使う。
- Windows で `python3` が Microsoft Store のアプリ実行エイリアスになっていると、Spec Kit の `resolve-template.ps1` がそれを選び、`Invalid preset manifest` で失敗する。どの preset を入れても起きる。Spec Kit 1.4 以降では、解決に失敗すると `/speckit.specify` が止まる。「設定 > アプリ > アプリの詳細設定 > アプリ実行エイリアス」で `python3` をオフにすると直る。

## アンインストール

```bash
specify preset remove spec-review-layout
```

アンインストール後、spec テンプレートと 2 つのコマンドは次の層（ほかの preset または Spec Kit core）のものに戻る。

## ライセンス

MIT
