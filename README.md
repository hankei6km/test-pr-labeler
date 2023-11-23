# test-pr-labeler

pull request の内容によって手動で label を付るのは少し厳しくなってきた。とくにリリースノート作成で利用する場合は付け忘れがあるのも困る。

よって、GitHub Actions による自動化を検討。

## 試す

[Labeler action](https://github.com/actions/labeler) を試してみた。

この action は変更されたファイル PATH を元に label を付加する。5.x(現時点では beta 版)であれば branch 名を元に label を付加することも可能。

- 変更した箇所(scope)別に label を付けるような場合は便利そう
- semantic pull request(conventional commits) の type 別に label を付加するような用途向けではない
  - branch 名を元にある程度ルールを操作できるがやりにくい
    - pull request の branch 名は変更できない(type変わるなら close でもよいのだが)
    - breaking change など含めにくい

なお、変更されたファイルの PATH による判定だけでは、修正した目的を推測することはできないので、その点にも注意が必要。
たとえば、`src/lib.rs` が変更されていた場合、テストのコードを変更している可能性もある。

## semantic pull request

試してみた感じでは、やはり「[semantic pull request](https://github.com/amannn/action-semantic-pull-request) の type などを元に label を付加する」のが今回の目的(リリースノート作成用)にあっていると思われた。

ここで問題なのは「そうそう都合のよい action はなさそう」だったこと(semantic pull request 公式?の actioon はルールに沿っているかのチェックを行うもの)。

よって、action を作った。
- `sem-title`: タイトルの文字列を渡すと type などを output 経由で返してくる
- `sem-to-label`: type などを渡すと pull request の label を付け替える

簡単にためした限りでは pull request のタイトルで label を操作することができている。このあと、それぞれを複合アクションにする予定。

## 考慮点

### scope

[Labeler action](https://github.com/actions/labeler) を利用することで `client` などの変更した機能別の label を付加できるが、label の情報をリリースノートに反映させるのは難しい(グループ化はできるが、`feat/client` のような階層はできないはず)。

できれは、scope は逆に自動でタイトルに埋め込む(あるいは警告する)ようにしたいが、今回は見送り。

例:
```
feat: Support json format
```
上記のようタイトルを自動で

```
feat(client): Support json format
```
のように変更したい。

### semantic release の npm package

タイトルから type などを切り出す処理はなんとなくで作ったが、本来なら上記のような package を使うべき(semantic pull request の action では使っている)。




