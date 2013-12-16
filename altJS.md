# AltJSさわってみた

## AltJSって

Alternative JavaScript ・・・ つまりJSの代わりになる言語。

* Coffee Script
* Dart
* Haxe
* JSX
* Kotlin
* TypeScript

あたり。

コンパイルすることによってJSに変換される。
→ 実行時はJSになる。

## JSといえば・・・

### 型がない。ゆるふわBoolean変換。

PHPと同じでゆるふわなチェック。

```javascript
"1" == 1    // true
""  == true // false
1   == true // true
```

### 冗長的な表記

```javascript
function(k){return k + 2;}
```

```coffee
(k) => k + 2
```
だけで良いよね？

### わかりにくいPrototype

```javascript
function Hoge(value) {
	this.value = value;
}
Hoge.prototype.getValue = function() {
	return this.value;
}
```

さらに


```javascript
var h = Hoge("hello").getValue();     // <- NG
var w = new Hoge("hello").getValue(); // <- OK
```



### コールバック地獄

```javascript
hoge(function(){
    piyo(function(){
        moga(function(){
        	alert("WRYYYYYYYY");
        });
    });
 });
```

### this

コンソールログにはなんと出力されるでしょう？

```javascript
console.log((function(){ return this; })() === window);
```

via [じゃあ this の抜き打ちテストやるぞー](http://qiita.com/KDKTN/items/0b468a07410d757ac609)

### あああああもう

上記の理由からJavascriptからの脱却を図りたい。

JSの代わりになるものを探そう。

できればJSを避けたいがクライアント(ブラウザ)で動く言語のなかで最もハードルが低い。

ActiveX -> Java(Applet) -> ActionScript(Flash) -> Javascript

Javascriptを避けては通れない。

## ということでAltJS

今日の話に出てくるのはいままでに自分が触ったことあるやつ。

* CoffeeScript
* TypeScript

## 結論から言うと

一長一短。

現在のプロジェクトではTypeScriptを採用。

それでは見て行きましょう。

## CoffeeScript

言わずもがなAltJSの代表格。Ruby,Pythonぽい。

ブロック```{}```の代わりにインデントを利用する。

文字列への代入(String Interpolation)や複数行正規表現リテラルなど、JSにはない機能が沢山。分割代入も便利。

```coffee
class Hello
  constructor: (@name = "world") ->
  say : ->
    "Hello, #{@name}."

res = (new Hello().say() is "Hello, world." ) # -> true

```

#### インストール

npm経由でインストール

```npm install -g coffee-script```

### 良い点

表現力はすごく高い。余計な記号(```(),{}```)がないため可読性も高い。
文脈っぽくなるので書いていて気持ちいい。テンションあがる。

### 悪い点

* 型がないため、メソッドの戻り値の型がわからない。

    > あぶない。 #突然の死

* JSそのものとはかけ離れている。=> 学習コスト

    > 最初1週間は本を見ながら出ないとかけなかった。

* 長続きする技術？

### 開発環境
 
* テキストエディタ
* WebStorm : 補完 ✓
* VisualStudio(2012+) : 補完 ✓

## TypeScript

標準化JavascriptであるECMAScript 6のスーパーセット。

Microsoft製。

```TypeScript
class Hello {
  name:string;
  constructor(name:string = "world"){
    this.name = name;
  }
  say:() => string = () => "Hello, " + this.name + "."
}
```

### インストール

MS製だからといってWindowsじゃないと開発できないわけではない。

npm経由のインストールする。

```npm install -g typescript```

WindowsではVisualStudioのプラグインとしてインストールすることもできる。

[TypeScript for Visual Studio 2012 and 2013](http://www.microsoft.com/en-us/download/details.aspx?id=34790)

### 開発環境

* テキストエディタ
* WebStorm : 補完 ✓
* VisualStudio(w/ plugin) : 補完 ✓

いろいろ試したけどVisualStudioが一番良く出来てる。

### 良い点
未来のJSの形に近い(はず)

基本的にJS。JSのコードをそのまま貼っつけても動く。(型情報がないので警告がでるが)

型があり、インターフェース、実装、**ジェネリクス**等も利用できる。

既存のJSも利用することができる。様々な既存ライブラリの型情報は[DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped)にある。 

既存コードも型情報だけ定義すれば利用できる。

#### 悪い点

型定義情報ファイルを別途コンパイル必要がある。

```TypeScript
declare Hello
```

既存JSの実装がゆるふわだとジェネリクス等をうまく型定義できず、
any(javaのObjectに相当)で定義せざるを得なくなる。

tsファイル単体でコンパイルすると正常なのに
Gruntからまとめてコンパイルする際にコンパイルエラーが出る・・・

> 依存関係の問題？

コンパイラ不安定

> Version 0.9.5.0 (2013/12/10現在)

すでに問題にぶち当たった。

> [http://typescript.codeplex.com/workitem/1570](http://typescript.codeplex.com/workitem/1570)

### そのほかのAltJS

#### LiveScript

CoffeeScriptを更に拡張した言語。

> LiveScript <- Mono <- CoffeeScript

関数型を強く意識している。ほぼHaskell。(OOPもできる(いちおう))

> 型情報がないので辛いけども・・・

```haskell
[1 to 5] |> map (^2) |> filter even |> fold (+), 0 #=> 20
```

## 今回採用したのはTypeScript

* ジェネリクスがある

    処理の大半を占めるリスト操作が静的型で実装できる
    > 確実なコードをかける。
    
* 学習コストが低い。

    JSに近い構文なので、あまり勉強しなくても入れる。
    
* 将来的にデファクトスタンダードとなりうる

    ECMAScript6ほぼそのまま。

* CoffeeScriptを採用する理由がない。

## CoffeeScriptが悪いわけではない

* デファクトスタンダードである

    ```Gruntfile.coffee``` とかあるし(震え声)
    
* GithubのBot(hubot)だってCoffeeScriptだよ！

    散々CoffeeScript書いた。




## まとめ

**JS書いてる場合じゃねぇ！**

![image](ttps://photos-5.dropbox.com/t/0/AAByBM6ZwOX8YfByk-AhEuhKB5hAINoqqs30hVfedo-6xw/12/20057885/jpeg/1024x768/3/1386856800/0/2/%E3%81%88%E3%83%BC%E3%83%9E%E3%82%B8%E3%83%BCJS%EF%BC%9F.jpg/tX88PxC3uvlSJmX35g9lB79H6K35kPxgDZBVAwil1LI)

**AltJS使おう！**