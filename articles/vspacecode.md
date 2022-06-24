---
title: "VSCode上のVim操作を快適にする拡張「VSpaceCode」の紹介"
emoji: "🔭"
type: "tech"
topics: ["vscode", "vim"]
published: false
---

[VSpaceCode](https://vspacecode.github.io/)は，[Spacemacs](https://www.spacemacs.org)のようなkey bindingをVSCode上で実現するための拡張です．本記事では，VSpaceCodeについて簡単に紹介します．

https://marketplace.visualstudio.com/items?itemName=VSpaceCode.vspacecode
https://vspacecode.github.io/


## VSpaceCodeとは
VSpaceCodeは，冒頭に述べたとおり[Spacemacs](https://www.spacemacs.org)のようなkey bindingをVSCode上で実現する拡張です．簡単な使い方の流れは以下のようになります．


- VimでNormalモードの時にleader（デフォルトはspaceキー`␣`）を押すと，コマンドパレットが表示される
- 続く候補のコマンドのためのキーを押す
- command（bindings・transient）を実行

公式のデモが分かりやすいです
例えば`␣ + w + /` は，windowを分割するコマンドになります．

![image](https://res.craft.do/user/full/848d1156-cbdb-deb2-8dde-19b0dc06f053/doc/60D51D43-4EF3-4E9E-BA09-810D4218841B/5EDA8FA6-E923-41D5-863E-52979707669D_2/S9MYhTh5nwnwe9Vy6ddeI2oICxxwB6Iqi0QyOpdy4xQz/AnimatedImage.gif)

この`␣`をベースとしたコマンド実行は，Spacemacsに由来します．

画面上側のように入力に応じてコマンドが表示されるので，コマンドが覚えやすいのが，VSpaceCodeを使う大きな利点です．

もともとSpacemacsは，Emacsのdistributionの一つで，Vimの入力インタフェースにEmacsの拡張性を加えたエディタとして紹介されています．

https://qiita.com/nobkz/items/994c6b3e6f42a0e33fef

## どんな人がVSpaceCode使うと良さそうか？

- [VSCodeVim](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim)や[VSCode NeoVim](https://marketplace.visualstudio.com/items?itemName=asvetliakov.vscode-neovim)ユーザー
- `.vimrc`をガンガンにカスタマイズしているわけではないVimユーザー
- SpacemacsからVSCodeに移行する必要のあるユーザー

上記に当てはまる方に，フィットすると思います．

VSCode Vim（NeoVim）を使っていない方は，[vscode-which-key](https://github.com/VSpaceCode/vscode-which-key)を入れると利用可能になるとのことです．僕はVSCode NeoVimを使っているので，vscode-which-keyは試してはいません．
>  This extension is published as an extension pack, i.e. it installs all the necessary extensions for the best-of-the box experience. However, if you do not like the bundled extensions or you are not using VSCode Vim, please go to the [vscode-which-key](https://github.com/VSpaceCode/vscode-which-key) extension for a standalone which key menu function.

## インストール
以下からインストールできます．VSCode開いてる方は検索して入れるでOKです．
https://marketplace.visualstudio.com/items?itemName=VSpaceCode.vspacecode

VSpaceCodeをインストールすると，内部で利用している[Which Key](https://github.com/VSpaceCode/vscode-which-key)という拡張も同時にインストールされます．Which Keyは[emacs-which-key](https://github.com/justbur/emacs-which-key)をVSCode上で動くように移植したものという認識でいいと思います．
インストール後，以下のように設定をどうするか聞かれます．
![image](https://res.craft.do/user/full/848d1156-cbdb-deb2-8dde-19b0dc06f053/doc/60D51D43-4EF3-4E9E-BA09-810D4218841B/61F1918E-0BF2-467B-B52D-056E53A93671_2/mdlJzOLmtex3dxLhgVVIlPHPbqSxoZUDneZOYqiSRw0z/Image.png)

`settings.json`や`keybindings.json`を書き換えられたくない場合，Manuallyにしましょう．
`settings.json`とか`keybindings.json`を書き換えられてもいい（特に設定していない or VSpaceCodeに従う）場合は，Automaticallyにするといいと思います．

基本的にはManuallyがおすすめです．

## VSpaceCodeでよく使っている操作

VSpaceCodeがやってるのは，bindingを追加して，便利なものの一部を以下から引用+簡単に訳して紹介します．
https://vspacecode.github.io/docs/default-keybindings

key bindingに対応するものは種別として command(s), bindings, transient, conditionalがあるようです．

### Help
Helpを表示するbindingです．

| Key Binding        | Name                          | Type    | Command(s)                                   |
| ------------------ | ----------------------------- | ------- | -------------------------------------------- |
| `␣ h d` | Open VSCode Documentation     | command | `workbench.action.openDocumentationUrl`      |
| `␣ h k` | Open global key bindings      | command | `workbench.action.openGlobalKeybindings`     |
| `␣ h D` | Open VSpaceCode Documentation | command | `vspacecode.openDocumentationUrl`            |
| `␣ h I` | Report VSCode Issue           | command | `workbench.action.openIssueReporter`         |
| `␣ h T` | Open VSCode Tutorial          | command | `workbench.action.showInteractivePlayground` |

`␣ h k`でグローバルのkey bindingsに飛べるのが便利です．
困ったらこれ見るといいです．

### `␣ w` (Window)
window操作を行うbindingです．

| Key Binding        | Name                              | Type                         | Command(s)                                    |
| ------------------ | --------------------------------- | ---------------------------- | --------------------------------------------- |
| `␣ w -` | Split window below(windowを下方向に分割)                | command                      | `workbench.action.splitEditorDown`            |
| `␣ w /` | Split window right(windowを右方向に分割)                | command                      | `workbench.action.splitEditor`                |
| `␣ w =` | Reset window sizes(windowサイズをリセット)                | command                      | `workbench.action.evenEditorWidths`           |
| `␣ w [` | Shrink window(windowを縮小)                     | [transient](#shrink-window)  | `workbench.action.decreaseViewSize`           |
| `␣ w ]` | Enlarge window(windowを拡大)                    | [transient](#enlarge-window) | `workbench.action.increaseViewSize`           |
| `␣ w o` | Switch frame(frameの切り替え)                      | command                      | `workbench.action.quickSwitchWindow`          |

windowはbindingかなり多いので，一部のみ引用しています．
詳しくは[Default Kyebindings](https://vspacecode.github.io/docs/default-keybindings)へ．
縮小と拡大は連続して押せるので便利です．


### `␣ e` (Error)
エラー表示を行うbindingです．

| Key Binding        | Name            | Type                          | Command(s)                         |
| ------------------ | --------------- | ----------------------------- | ---------------------------------- |
| `␣ e .` | Error transient | [transient](#error-transient) | N/A                                |
| `␣ e e` | Show error       | command                       | `editor.action.showHover`          |
| `␣ e f` | Fix error       | command                       | `editor.action.quickFix`           |
| `␣ e l` | List errors     | command                       | `workbench.actions.view.problems`  |
| `␣ e n` | Next error      | command                       | `editor.action.marker.nextInFiles` |
| `␣ e p` | Previous error  | command                       | `editor.action.marker.prevInFiles` |
| `␣ e N` | Previous error  | command                       | `editor.action.marker.prevInFiles` |

エラー表示(Show Error)と書いていますが，Command(s)を見て分かるように，実際のbinding先は `editor.action.showHover`です．
なのでエラーに限らずドキュメント表示もできます（この辺は改善すべき点なのか？）．

### `␣ :` (Tass)
タスク実行に関連するbindingです．

| Key Binding        | Name                  | Type    | Command(s)                                   |
| ------------------ | --------------------- | ------- | -------------------------------------------- |
| `␣ : .` | Rerun last task | command | `workbench.action.tasks.reRunTask`           |
| `␣ : :` | Run task | command | `workbench.action.tasks.runTask`             |
| `␣ : b` | Run build tasks | command | `workbench.action.tasks.build`               |
| `␣ : c` | Configure task runner | command | `workbench.action.tasks.configureTaskRunner` |
| `␣ : g` | Show running tasks | command | `workbench.action.tasks.showTasks`           |
| `␣ : l` | Show task log | command | `workbench.action.tasks.showLog`             |
| `␣ : t` | Run test task | command | `workbench.action.tasks.test`                |
| `␣ : x` | Terminate task | command | `workbench.action.tasks.terminate`           |
| `␣ : R` | Restart running task | command | `workbench.action.tasks.restartTask`         |

よく利用するタスクを設定しておくと良いのではないかと思います．

### `␣ ?` (saerch keybindings)
key bindingをincremental searchするbindingです．（`␣ ?`で完結）

- 通常，`␣` を押したにあるキー（候補として存在するキー）が押されたら，その操作が実行されます．
- `␣ ?` を押すと，候補の操作をincremental searchできます．


### 他にも色々

Buffers・Compile/Comments・Debug・File・Git・Layouts・Diff/Compareなど他にも色々なbindingが用意されています．
https://vspacecode.github.io/docs/default-keybindings


## 余談：VSCode NeoVimユーザーのためのワークアラウンド
NeoVimとVSpaceCodeを併用しようとして，`␣`を押した時にVSpaceCodeが動かなくなってしまいました．
簡単で雑な解決方法ですがkeybindings.json に以下のbindingを追加して，spaceの挙動を上書きすれば直ります．

```json
{  
    "key": "space",  
    "command": "vspacecode.space",  
    "when": "editorTextFocus && neovim.mode == normal"
},
```

下記issueのリアクションついてるコメントでは直らなかったので，ひとまず上の方法で放置してます（VSCode拡張のbindingなのでkeybindings.jsonで良いかなと）．

https://github.com/VSpaceCode/VSpaceCode/issues/34

## 終わりに
簡単にVSpaceCodeについて紹介しました．
公式ドキュメントのページ数は少ないので，ぜひ眺めてみてください．

https://marketplace.visualstudio.com/items?itemName=VSpaceCode.vspacecode
https://vspacecode.github.io/
https://github.com/VSpaceCode/VSpaceCode

