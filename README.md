# 強化学習の問題整理

強化学習を問題の性質・構造から考察するためのリポジトリ。

## 目的
- 疎報酬
- 遅延報酬
- 長期依存
- 確率遷移
- 部分観測
- 非定常性
などの観点から、問題の難しさを整理する。

## Contents
- PDF report
- future notes
- possible taxonomy drafts

そのエラーはだいたい VSCode が指定されたファイルを見つけられていない状態です。
ただし「何のファイルがないか」で原因が変わります。

今回、特に怪しいのはこの3つです。

1. Python本体がない
2. 実行したい .py ファイルがない
3. gdb がない

⸻

まず確認：仮想環境なしなら program はこれ

仮想環境を使っていない場合、launch.json のここは：

"program": "/usr/bin/python3"

でOKです。

ただし、WSL内で本当に存在するか確認してください。

which python3

たとえば結果が：

/usr/bin/python3

なら、launch.json はこれ。

"program": "/usr/bin/python3"

もし結果が違えば、その結果をそのまま使います。

例：

/home/ユーザー名/miniconda3/bin/python

なら：

"program": "/home/ユーザー名/miniconda3/bin/python"

⸻

次に確認：args のPythonファイル名

たとえば launch.json がこうだとします。

"args": ["train.py"]

この場合、VSCodeはこの場所を探します。

${workspaceFolder}/train.py

つまり、VSCodeで開いているフォルダ直下に train.py が必要です。

確認：

pwd
ls

train.py がそこにないなら、正しい相対パスを書く必要があります。

例えば実際にはこうなら：

project/
  scripts/
    train.py

args はこうです。

"args": ["scripts/train.py"]

⸻

重要：${workspaceFolder} の意味

${workspaceFolder} は、VSCodeで今開いているフォルダです。

たとえばVSCodeでこのフォルダを開いているなら：

/home/user/project

これは：

"${workspaceFolder}"

と同じ意味です。

だから：

"cwd": "${workspaceFolder}"

は：

/home/user/project

で実行する、という意味です。

⸻

よくあるミス

ミス1：.vscode はあるが、VSCodeで開いている場所が違う

正しい構造はこれです。

project/
  .vscode/
    launch.json
  simulation_manager/
  cpp_build.sh
  train.py

このとき、VSCodeでは project フォルダそのものを開いている必要があります。

ダメな例：

project/
  src/
    .vscode/
      launch.json

とか、

project/
  simulation_manager/
    .vscode/
      launch.json

になっていると、workspaceFolder が想定とズレます。

⸻

いったん絶対パスで固めると分かりやすい

まずは曖昧さを消すために、絶対パスで書くのが良いです。

WSLで：

pwd
which python3
which gdb

を打つ。

例えば結果が：

pwd
/home/jifu/rllib-rensyu
which python3
/usr/bin/python3
which gdb
/usr/bin/gdb

なら、launch.json はこう。

{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "C++ Debug Python",
      "type": "cppdbg",
      "request": "launch",
      "program": "/usr/bin/python3",
      "args": ["train.py"],
      "cwd": "/home/jifu/rllib-rensyu",
      "MIMode": "gdb",
      "miDebuggerPath": "/usr/bin/gdb",
      "stopAtEntry": false
    }
  ]
}

train.py が直下にないなら、そこだけ変えます。

例：

"args": ["scripts/train.py"]

⸻

gdbがない場合

これも「ファイルがありません」系の原因になります。

確認：

which gdb

何も出なければ：

sudo apt update
sudo apt install gdb

⸻

まずやる確認コマンド

WSLのプロジェクトフォルダで、これを順番に打ってください。

pwd
which python3
which gdb
ls
ls .vscode
cat .vscode/launch.json

見るポイントはこれです。

which python3 の結果 = launch.json の program
which gdb の結果     = launch.json の miDebuggerPath
pwd の結果           = launch.json の cwd
実行したい .py の位置 = args

⸻

典型的な完成形

仮想環境なし、train.py がプロジェクト直下にあるなら：

{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "C++ Debug Python",
      "type": "cppdbg",
      "request": "launch",
      "program": "/usr/bin/python3",
      "args": ["train.py"],
      "cwd": "${workspaceFolder}",
      "MIMode": "gdb",
      "miDebuggerPath": "/usr/bin/gdb",
      "stopAtEntry": false
    }
  ]
}

train.py がないなら、args が間違っています。
/usr/bin/python3 がないなら、program が間違っています。
/usr/bin/gdb がないなら、miDebuggerPath が間違っています。

