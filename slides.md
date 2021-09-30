---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: true
drawings:
  persist: false
---

# ElmアーキテクチャでGUIプログラミング
土田快斗
2021/10/1

---

# 自己紹介
<style>
li {
    line-height: 2.5;
}
</style>
<div class="text-3xl">

- 名前：土田快斗
- 所属：公立千歳科学技術大学理工学部情報システム工学科
- 関数型言語歴：<br>
  入門Haskellプログラミング一冊読んだ程度の初心者
</div>

---

# Elmアーキテクチャ<br>
<style>
li {
    line-height: 2.5;
}
</style>
<div class="text-3xl">

- Model-View-Update<br>

- わかりやすいロジックでWebのUIが実装できる<br>

- デスクトップのGUIアプリでも利用したい
</div>
---

# 今回紹介する言語とライブラリの紹介
<style>
li {
    line-height: 2.5;
}
</style>
<div class="grid grid-cols-[50%,50%] text-xl"><div>

## F#
- OCamlベースのマルチパラダイム言語
- VB、C#と同じく.Net上で実装
  - Javaに対するScalaみたいな感じ？
  - ライブラリを共有
</div>
<div>

## Avalonia FuncUI
- C#のGUIライブラリ`Avalonia UI`のラッパー
- クロスプラットフォーム
- Elmish採用
- XAMLは使わない
</div></div>

---

# F#
## 変数と関数
<br>

```f#
// 変数
let x = 100

// 関数
let f x = x * 2
printfn $"{f x}"
// 出力:2

// ラムダ式
let lambda = fun x -> $"{x}+1000={x + 1000}"
printfn $"{lambda x}"
// 出力:100+1000=1100
```

---

## 型とパターンマッチ
<br>

```f#
open System

// レコード
type Solid =
    { width: float
      height: float
      depth: float }

// 判別共用体
type Shape =
    | Circle of float
    | Rectangle of width: float * height: float
    | Rectangular of Solid

// パターンマッチング
let calc =
    function
    | Circle r -> Math.PI * r ** 2.0
    | Rectangle (w, h) -> w * h
    | Rectangular { width = w; height = h; depth = d } -> w * h * d

let rectangle = calc <| Rectangle(3.0, 4.0)
printfn $"{rectangle}"
// 出力:12
```

---

# Avalonia FuncUI
## Counter
<div class="grid grid-cols-[55%] items-center justify-center gap-5">

![カウンター](/fsharp.png)
</div>


---

# Avalonia FuncUI
<div class="grid grid-cols-[55%,45%] justify-center gap-5">
<div>

## Model
<br>

```f#
type State = { count : int }
let init = { count = 0 }
```
<br>

## Update&Message
<br>

```f#
type Msg =
    | Increment
    | Decrement
    | Reset

let update (msg: Msg) (state: State) : State =
    match msg with
    | Increment -> { state with count = state.count + 1 }
    | Decrement -> { state with count = state.count - 1 }
    | Reset -> init
```
</div>

![カウンター](/fsharp.png)
</div>

---

# Avalonia FuncUI
## View
<br>
<div class="h-[80%] grid grid-cols-[70%,30%] items-center justify-center gap-5">
<div class="h-full overflow-auto">

```f#
let view (state: State) (dispatch) =
    DockPanel.create [
        DockPanel.children [
            Button.create [
                Button.dock Dock.Bottom
                Button.onClick (fun _ -> dispatch Reset)
                Button.content "reset"
            ]
            Button.create [
                Button.dock Dock.Bottom
                Button.onClick (fun _ -> dispatch Decrement)
                Button.content "-"
            ]
            Button.create [
                Button.dock Dock.Bottom
                Button.onClick (fun _ -> dispatch Increment)
                Button.content "+"
            ]
            TextBlock.create [
                TextBlock.dock Dock.Top
                TextBlock.fontSize 48.0
                TextBlock.verticalAlignment VerticalAlignment.Center
                TextBlock.horizontalAlignment HorizontalAlignment.Center
                TextBlock.text (string state.count)
            ]
        ]
    ]
```
</div>

![カウンター](/fsharp.png)
</div>

---

# Avalonia FuncUI
## TabControl
<br>
<div class="h-[80%] grid grid-cols-[70%,30%] items-center justify-center gap-5">
<div class="h-full overflow-auto">

```f#
let view (state: State) (dispatch) =
    DockPanel.create [
        DockPanel.children [
            TabControl.create [
                TabControl.tabStripPlacement Dock.Top
                TabControl.viewItems [
                    TabItem.create [
                        TabItem.header "Counter Sample"
                        TabItem.content (Counter.view state.counterState (CounterMsg >> dispatch))
                    ]
                    TabItem.create [
                        TabItem.header "About"
                        TabItem.content (About.view state.aboutState (AboutMsg >> dispatch))
                    ]
                ]
            ]
        ]
    ]
```
</div>

![カウンター](/fsharp.png)
</div>

---

# Avalonia FuncUI

<div class="text-xl grid grid-cols-[50%,50%]">
<div>

## その他のUI
- テキストボックス
- チェックボックス
- ラジオボタン
- アコーディオンメニュー
- カレンダー
- Avalonia UIをラップすれば…？
</div><div>

## その他の機能
- Cmdによる非同期処理
  - F#のasyncコンピュテーション式と合わせて柔軟な処理
- スタイリング
  - inline
  - XAML
</div></div>


---

<div class="flex flex-col justify-center items-center h-full text-3xl">

ここまでF#でやってきましたが…<br>

他の言語はどうでしょうか？
</div>

---

# Haskell
## Monomer
<br>
<div class="h-[80%] grid grid-cols-[70%,30%] items-center justify-center gap-5">
<div class="h-full overflow-auto">

```haskell
newtype AppModel = AppModel {
  _clickCount :: Int
} deriving (Eq, Show)

data AppEvent
  = AppInit
  | AppIncrease
  deriving (Eq, Show)

makeLenses 'AppModel

buildUI
  :: WidgetEnv AppModel AppEvent
  -> AppModel
  -> WidgetNode AppModel AppEvent
buildUI wenv model = widgetTree where
  widgetTree = vstack [
      label "Hello world",
      spacer,
      hstack [
        label $ "Click count: " <> showt (model ^. clickCount),
        spacer,
        button "Increase count" AppIncrease
      ]
    ] `styleBasic` [padding 10]
```
</div>

![Haskell](/haskell.png)
</div>

---

# Rust
## Iced
<br>
<div class="h-[80%] grid grid-cols-[80%,20%] items-center justify-center gap-5">
<div class="h-full overflow-auto">

```rust
#[derive(Default)]
struct Counter {
    value: i32,
    increment_button: button::State,
    decrement_button: button::State,
}

#[derive(Debug, Clone, Copy)]
enum Message {
    IncrementPressed,
    DecrementPressed,
}

impl Sandbox for Counter {
    type Message = Message;

    fn new() -> Self {
        Self::default()
    }

    fn title(&self) -> String {
        String::from("Counter - Iced")
    }

    fn update(&mut self, message: Message) {
        match message {
            Message::IncrementPressed => {
                self.value += 1;
            }
            Message::DecrementPressed => {
                self.value -= 1;
            }
        }
    }

    fn view(&mut self) -> Element<Message> {
        Column::new()
            .padding(20)
            .align_items(Alignment::Center)
            .push(
                Button::new(&mut self.increment_button, Text::new("Increment"))
                    .on_press(Message::IncrementPressed),
            )
            .push(Text::new(self.value.to_string()).size(50))
            .push(
                Button::new(&mut self.decrement_button, Text::new("Decrement"))
                    .on_press(Message::DecrementPressed),
            )
            .into()
    }
}
```
</div>

![Rust](/rust.png)
</div>

---

# 最後に
<div class="flex flex-col justify-center items-center h-full text-3xl">

ElmアーキテクチャはWeb以外にも利用できる<br>

関数型プログラミングに詳しい方は是非色々教えてください<br>

- Twitter：[@totto2727](https://twitter.com/totto2727)
- GitHub：[totto2727](https://github.com/totto2727)
</div>