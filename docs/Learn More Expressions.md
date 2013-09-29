 +  元文書: [wycats/handlebars-site/expressions.haml · wycats/handlebars-site · GitHub]
(https://github.com/wycats/handlebars-site/blob/master/src/pages/expressions.haml 
"wycats/handlebars-site/expressions.haml · wycats/handlebars-site · GitHub")

**Handlebars式はHandlebarsテンプレートの基本ユニットです。`{{mustache}}` の中で単独で使用して、Handlebars ヘルパーに渡したり、ハッシュ引数の中で使用することができます。**

## 基本的な使い方

最もシンプルなHandlebars式は単純な識別子です。

```
<h1>{{title}}</h1>
```

この式は、「カレントコンテキストから`title`プロパティを探す」という意味です。
ブロックヘルパーはカレントコンテキストを操作することもありますが、式の基本的な意味合いは変わりません。

この式は実際に「`title` という名前のヘルパーを探し、それを実行する」という意味になりますが、詳細は後述します。

Handlebars式はドットで区切られたものも渡すことができます。

```
<h1>{{article.title}}</h1>
```

この式は、「カレントコンテキストから`article`プロパティを探し、その結果から`title`プロパティを探す」という意味です。

Handlebarsは廃止予定となっている `/` 構文もサポートしているため、上記のテンプレートは以下のように記述することもできます。

```
<h1>{{article/title}}</h1>
```

識別子は以下を除く任意のUnicode文字で構成されるでしょう。

`スペース` `!` `"` `#` `%` `&` `'` `(` `)` `*` `+` `,` `.` `/` `;` `<` `=` `>` `@` `[` `\` `]` `^` `` ` `` `{` `|` `}` `~`

識別子として有効ではないプロパティを参照するために、 `[` をセグメントリテラルとして使うことができます。

```
{{#each articles.[10].[#comments]}}
  <h1>{{subject}}</h1>
  <div>
    {{body}}
  </div>
{{/each}}
```

上の例では、テンプレートは `each` のパラメータをjavascript: `articles[10]['#comments']` とほぼ同じものとして扱います。

クローズの `]` をパスリテラルに含ない場合があるかもしれませんが、他の全ての文字は正しく評価されます。

Handlebarsの `{{expression}}` はHTMLエスケープされた値を返します。Handlebarsが値をエスケープして欲しくない場合、"トリプルスタッシュ" `{{{` を使用してください。

```
{{{foo}}}
```

## ヘルパー

Handlebarsのヘルパー呼び出しは0個以上のパラメータ（スペースで区切られている）を伴った単純な識別子です。
各パラメータはHandlebars式です。

```
{{{link story}}}
```

このケースでは、`link` はHandlebarsヘルパーの名前であり、`story` はヘルパーのパラメータです。
Handlebarsでのパラメータの評価方法は、上の"基本的な使い方"とまったく同じです。

```
Handlebars.registerHelper('link', function(object) {
  return new Handlebars.SafeString(
    "<a href='" + object.url + "'>" + object.text + "</a>"
  );
});
```

ヘルパーからHTMLを返す際に、デフォルトでエスケープして欲しくない場合は、 `Handlebars SafeString` を返してください。

さらにHandlebarsヘルパーには、単純な文字列をパラメータのように渡すこともできます。

```
{{{link "See more..." story.url}}}
```

このケースでは、Handlebarsは `link` ヘルパーに文字列 `"See more..."` と現在のコンテキストの中で `story.url` の評価結果の2つのパラメータを渡しています。

```
Handlebars.registerHelper('link', function(text, url) {
  return new Handlebars.SafeString(
    "<a href='" + url + "'>" + text + "</a>"
  );
});
```

`story.text` の値のように動的文字列を渡しても、まったく同じようにヘルパーを使うことができます。

```
{{{link story.text story.url}}}
```

Handlebarsヘルパーは、最後のパラメータにkey-valueペアの任意のシーケンスを渡すことが出来ます。（本ドキュメント中でハッシュ引数と呼ばれる）

```
{{{link "See more..." href=story.url class="story"}}}
```

ハッシュ引数の中のキーはそれぞれが単独の識別子でなければなりません。そして、値はHandlebars式です。
これは、値が単独の識別子、パス、または文字列であることを意味します。

```
Handlebars.registerHelper('link', function(text, options) {
  var attrs = [];

  for(var prop in options.hash) {
    attrs.push(prop + '="' + options.hash[prop] + '"');
  }

  return new Handlebars.SafeString(
    "<a " + attrs.join(" ") + ">" + text + "</a>"
  );
});
```

Handlebarsはハッシュ引数のように最後のパラメータを介してヘルパーに追加のメタデータを提供します。

Handlebarsではテンプレートの1ブロックに対してヘルパーを呼び出すメカニズムを提供しています（訳注：文中でブロックヘルパーと呼ばれるもののこと）。
ブロックヘルパーは任意のコンテキストにてブロックを0回以上呼び出すことができます。

[ブロックヘルパーについて詳しくはこちら](ブロックヘルパーについて詳しくはこちら: Block Helpers.md)
