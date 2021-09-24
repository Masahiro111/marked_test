
`x-model` は要素に「双方向データバインディング」を追加します。つまり、入力要素の値はコンポーネントの項目データの値と同期します。

> 注意: `x-model` は、テキストインプット、チェックボックス、ラジオボタン、テキストエリア、セレクト、およびマルチセレクトの変更を検出するのに最適です。これらのシナリオで [Vue がどのように](https://vuejs.org/v2/guide/forms.html)動作するのか確認してください。

---

### `x-text`
**例:** `<span x-text="foo"></span>`

**構造:** `<span x-text="[expression]"`

`x-text` は `x-bind` と同様に機能しますが、属性値を更新する代わりに要素の `innerText` を更新します。

---

### `x-html`
**例:** `<span x-html="foo"></span>`

**構造:** `<span x-html="[expression]"`

`x-html` は `x-bind` と同様に機能しますが、属性の値を更新する代わりに要素の `innerHTML` を更新します。

---

### `x-ref`
**例:** `<div x-ref="foo"></div><button x-on:click="$refs.foo.innerText = 'bar'"></button>`

**構造:** `<div x-ref="[ref name]"></div><button x-on:click="$refs.[ref name].innerText = 'bar'"></button>`

`x-ref` は、コンポーネントから生 DOM 要素を取得する便利な方法を提供します。要素に `x-ref` 属性を設定することで、すべてのイベントハンドラで `$refs` というオブジェクト内から利用できるようになります。

これは、ID を設定し、あらゆる場所で `document.querySelector` を使用する代替手段に役立ちます。

---

### `x-if`
**例:** `<template x-if="true"><div>Some Element</div></template>`

**構造:** `<template x-if="[expression]"><div>Some Element</div></template>`

`x-show` では不十分な場合（`x-show` が false の場合、要素を `display： none` に設定します）、`x-if` を使用して DOM から要素を完全に削除できます。

Alpine は仮想 DOM を使用しないため、`<template></template>` タグで `x-if` を使用することが重要です。この実装により、Alpine は堅牢性を保ち、実際の DOM を使用してその仕様を働かせることができます。

> 注意: `x-if` には、`<template></template>` タグ内に単一要素のルートが必要です。

---

### `x-for`
**例:**
```html
<template x-for="item in items" :key="item">
    <div x-text="item"></div>
</template>
```

`x-for` は、配列の各アイテム毎に新しい DOM ノードを作成する場合に使用します。これは、Vue の `v-for` に似ています。ただし、通常の DOM 要素ではなく、`template` タグに存在する必要があります。

> 注意: `：key` バインディングはオプションですが、強く推奨しています。

---

### `x-transition`
**例:**
```html
<div
    x-show="open"
    x-transition:enter="transition ease-out duration-300"
    x-transition:enter-start="opacity-0 transform scale-90"
    x-transition:enter-end="opacity-100 transform scale-100"
    x-transition:leave="transition ease-in duration-300"
    x-transition:leave-start="opacity-100 transform scale-100"
    x-transition:leave-end="opacity-0 transform scale-90"
>...</div>
```

```html
<template x-if="open">
    <div
        x-transition:enter="transition ease-out duration-300"
        x-transition:enter-start="opacity-0 transform scale-90"
        x-transition:enter-end="opacity-100 transform scale-100"
        x-transition:leave="transition ease-in duration-300"
        x-transition:leave-start="opacity-100 transform scale-100"
        x-transition:leave-end="opacity-0 transform scale-90"
    >...</div>
</template>
```

Alpine は、「非表示」と「表示」の遷移間のさまざまな段階にクラスを要素に適用するための6つの異なるトランジションディレクティブを提供します。これらのディレクティブは、`x-show` と `x-if` の両方で機能します。

これらは、VueJs のトランジションディレクティブとまったく同じように動作しますが、より理にかなった異なる名前を持っています。

| ディレクティブ | 説明 |
| --- | --- |
| `:enter` | エンターフェーズ全体に適用されます。 |
| `:enter-start` | 要素が挿入される前に追加され、要素が挿入の1フレーム後に削除されます。 |
| `:enter-end` | 要素が挿入後（`enter-start` が削除されると同時）の1フレームに追加され、トランジション/アニメーションが終了すると削除されます。
| `:leave` | リーブフェーズ全体に適用されます。 |
| `:leave-start` | リーブ遷移がトリガされるとすぐに追加され、1フレーム後に削除されます。 |
| `:leave-end` | リーブ遷移がトリガされた後（同時に `leave-start` が削除される）の1フレームに追加され、トランジション/アニメーションが終了すると削除されます。

---

### `x-spread`
**Example:**
```html
<div x-data="dropdown()">
    <button x-spread="trigger">Open Dropdown</button>

    <span x-spread="dialogue">Dropdown Contents</span>
</div>

<script>
    function dropdown() {
        return {
            open: false,
            trigger: {
                ['@click']() {
                    this.open = true
                },
            },
            dialogue: {
                ['x-show']() {
                    return this.open
                },
                ['@click.away']() {
                    this.open = false
                },
            }
        }
    }
</script>
```

`x-spread` は、Alpineのバインディングを要素から再利用可能なオブジェクトに抽出することができます。

オブジェクトのキーはディレクティブ(もちろん修飾子を含むことができます)として、値はコールバックとして評価されます。

> 注意: x-spreadにはいくつかの注意点があります。
> - 展開されるディレクティブが`x-for` の場合、コールバックから通常の式の文字列を返す必要があります。例えば `['x-for']() { return 'item in items' }` のようにです。
> - `x-data` と `x-init` は "spread" オブジェクトの中では使えません。

---

### `x-cloak`
**例:** `<div x-data="{}" x-cloak></div>`

Alpine の初期化時に、要素から `x-cloak` 属性が削除されます。これは、事前に初期化された DOM を隠すのに役立ちます。これが機能するためには、通常、次のグローバルスタイルを追加します。:

```html
<style>
    [x-cloak] { display: none; }
</style>
```

### マジックプロパティ

---

### `$el`
**例:**
```html
<div x-data>
    <button @click="$el.innerHTML = 'foo'">"foo"に置換します</button>
</div>
```

`$el` は、ルートコンポーネントの DOM ノードを取得するために使用できるマジックプロパティです。

### `$refs`
**例:**
```html
<span x-ref="foo"></span>

<button x-on:click="$refs.foo.innerText = 'bar'"></button>
```

`$refs` は、コンポーネント内で `x-ref` でマークされた DOM 要素を取得するために使用できるマジックプロパティです。これは、DOM 要素を手動で操作する必要がある場合に便利です。

---

### `$event`
**例:**
```html
<input x-on:input="alert($event.target.value)">
```

`$event` は、ネイティブブラウザの「Event」オブジェクトを取得するためにイベントリスナ内で使用できるマジックプロパティです。

---

### `$dispatch`
**例:**
```html
<div @custom-event="console.log($event.detail.foo)">
    <button @click="$dispatch('custom-event', { foo: 'bar' })">
    <!-- クリックされると、 console.log が「bar」を出力します -->
</div>
```

`$dispatch` は、`CustomEvent` を作成し、内部で `.dispatchEvent()` を使用してディスパッチするためのショートカットです。カスタムイベントを使用してコンポーネント間およびコンポーネント間でデータを渡すには、多くの適切なユースケースがあります。基礎となるブラウザの `CustomEvent` システムの詳細については、[こちらをご覧ください。](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events)

`$dispatch('some-event', { some: 'data' })` の2番目のパラメータとして渡されるデータは、新しいイベントの「detail」プロパティ: `$event.detail.some` です。カスタムイベントデータを `.detail` プロパティにアタッチすることは、ブラウザの `CustomEvent` の標準的な方法です。詳細は[こちらをご覧ください。](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/detail)

また、`$dispatch()` を使用して、`x-model` バインディングのデータ更新をトリガすることもできます。 例えば:

```html
<div x-data="{ foo: 'bar' }">
    <span x-model="foo">
        <button @click="$dispatch('input', 'baz')">
        <!-- ボタンがクリックされると、`x-model` は浮上する「input」イベントをキャッチし、foo を「baz」に更新します。 -->
    </span>
</div>
```

---

### `$nextTick`
**例:**
```html
<div x-data="{ fruit: 'apple' }">
    <button
        x-on:click="
            fruit = 'pear';
            $nextTick(() => { console.log($event.target.innerText) });
        "
        x-text="fruit"
    ></button>
</div>
```

`$nextTick` は、Alpine がリアクティブな DOM 更新を行った後、指定された式のみを実行できるマジックプロパティです。これは、データ更新が反映された後に DOM ステートとやり取りしたい場合に便利です。

---

### `$watch`
**例:**
```html
<div x-data="{ open: false }" x-init="$watch('open', value => console.log(value))">
    <button @click="open = ! open">開閉を切り替える</button>
</div>
```

マジックメソッド `$watch` を使用してコンポーネントのプロパティを「監視」することができます。上の例では、ボタンがクリックされて `open` が変更されるとコールバックが実行され、新しい値が `console.log` によって出力されます。

## セキュリティ
もしも脆弱性を発見した場合, [calebporzio@gmail.com]() 宛にメールを送ってください。

Alpineでは `Function` オブジェクトを使用したカスタム実装に依存しています。この関数は `eval()` より安全ですが、Google Chromeアプリのような [CSP](https://csp.withgoogle.com/docs/strict-csp.html) が制限されている環境では使用が禁止されています。

もしも機密データを扱う環境でAlpineを使用している場合かつ [CSP](https://csp.withgoogle.com/docs/strict-csp.html) を必要とする場合は、ポリシーに `unsafe-eval`を含める必要があります。堅牢なポリシーを設定することで、個人情報や財務情報を利用する際のユーザー保護に役立ちます。

ポリシーはページ内すべてのスクリプトに適用されるので、Webサイトに含まれる他の外部ライブラリを信用できるかどうか慎重に検討し、 `eval()` 関数を使用したり、DOMを操作して悪意あるコードをページに注入したりするクロスサイトスクリプティング脆弱性を防ぐことが重要です。

## V3 ロードマップ
* Vueと互換性を持たせるために `x-ref` から `ref` に変更する？
* `Alpine.directive()` を追加する
* `Alpine.component('foo', {...})` ( `__init()` マジックメソッドと一緒に) を追加する
* "loaded"や"transition-start"のようなイベントを追加する ([#299](https://github.com/alpinejs/alpine/pull/299)) ？
* オブジェクトや配列を `x-bind:class="{ 'foo': true }"` から削除する([#236](https://github.com/alpinejs/alpine/pull/236) では `style` 属性をサポートしようとしている)
* `x-for` の性能を改善する ([#165](https://github.com/alpinejs/alpine/pull/165))
* 「深い監視」をV3で追加する ([#294](https://github.com/alpinejs/alpine/pull/294))
* `$el` ショートカットを追加する
* `@click.away` から `@click.outside` に変更する？

## ライセンス

Copyright © 2019-2021 Caleb Porzio and contributors

MIT ライセンスの下でライセンスされています。詳細については、[LICENSE.md](LICENSE.md) を参照してください。