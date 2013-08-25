**Handlebars expressions are the basic unit of a Handlebars template. You can use them alone in a `{{mustache}}`, pass them to a Handlebars helper, or use them as values in hash arguments.**

**Handlebars式はHandlebarsテンプレートの基本ユニットです。`{{mustache}}` の中で単独で使用して、Handlebars ヘルパーに渡したり、ハッシュ引数の中で使用することができます。**

## Basic Usage

## 基本的な使い方

The simplest Handlebars expression is a simple identifier:

最もシンプルなHandlebars式は単純な識別子です。

```
<h1>{{title}}</h1>
```

This expression means "look up the `title` property in the current context". 
Block helpers may manipulate the current context, but they do not change the basic meaning of an expression.

この式は、「カレントコンテキストから`title`プロパティを探す」という意味です。
ブロックヘルパーはカレントコンテキストを操作することができますが、式の基本的な意味合いを変更することは避けてください。

Actually, it means "look for a helper named `title`, then do the above", but we'll get to that soon enough.

事実、この意味は「`title` という名前のヘルパーを探し、それを実行する」というものですが、我々にとってはこれで十分満足することでしょう。

Handlebars expressions can also be dot-separated paths.

Handlebars式はドットで区切られたものも渡すことができます。

```
<h1>{{article.title}}</h1>
```

This expression means "look up the `article` property in the current context. Then look up the `title` property in the result".

この式は、「カレントコンテキストから`article`プロパティを探し、その中から`title`プロパティを探す」という意味です。

Handlebars also supports a deprecated `/` syntax, so you could write the above template as:

Handlebarsは非推奨の `/` 構文もサポートしているため、上のテンプレートのように記述することができます。

```
<h1>{{article/title}}</h1>
```

Identifiers may be any unicode character except for the following:

識別子は以下を除く任意のUnicode文字で構成されるでしょう。

Whitespace `!` `"` `#` `%` `&` `'` `(` `)` `*` `+` `,` `.` `/` `;` `<` `=` `>` `@` `[` `\` `]` `^` `` ` `` `{` `|` `}` `~`

To reference a property that is not a valid identifier, you can use segment-literal notation, `[`:

識別子として有効ではない `[` をセグメントリテラルとして使うことで、プロパティを参照することができます。

```
{{#each articles.[10].[#comments]}}
  <h1>{{subject}}</h1>
  <div>
    {{body}}
  </div>
{{/each}}
```

In the example above, the template will treat the `each` parameter roughly equivalent to this javascript: `articles[10]['#comments']`

上の例では、テンプレートは `each` のパラメータをjavascript: `articles[10]['#comments']` とほぼ同じものとして扱います。

You may not include a closing `]` in a path-literal, but all other characters are fair game.

クローズの `]` をパスリテラルに含ない場合があるかもしれませんが、他の全ての文字は正しく評価されます。

Handlebars HTML-escapes values returned by a `{{expression}}`. If you don't want Handlebars to escape a value, use the "triple-stash", `{{{`

Handlebarsの `{{expression}}` はHTMLエスケープされた値を返します。Handlebarsが値をエスケープして欲しくない場合、"トリプルスタッシュ" `{{{` を使用してください。

```
{{{foo}}}
```

## Helpers

## ヘルパー

A Handlebars helper call is a simple identifier, followed by zero or more parameters (separated by space). Each parameter is a Handlebars expression.

Handlebarsのヘルパー呼び出しは0個以上のパラメータ（スペースで区切られている）を伴った単純な識別子です。
各パラメータはHandlebars式です。

```
{{{link story}}}
```

In this case, `link` is the name of a Handlebars helper, and story is a parameter to the helper. Handlebars evaluates parameters in exactly the same way described above in "Basic Usage".

このケースでは、`link` はHandlebarsヘルパーの名前であり、`story` はヘルパーのパラメータです。
Handlebarsでのパラメータの評価方法は、上の"基本的な使い方"とまったく同じです。

```
Handlebars.registerHelper('link', function(object) {
  return new Handlebars.SafeString(
    "<a href='" + object.url + "'>" + object.text + "</a>"
  );
});
```

When returning HTML from a helper, you should return a Handlebars SafeString if you don't want it to be escaped by default.

ヘルパーからHTMLを返す際に、デフォルトでエスケープして欲しくない場合は、 `Handlebars SafeString` を返してください。


You can also pass a simple String as a parameter to Handlebars helpers.

さらにHandlebarsヘルパーには、単純な文字列をパラメータのように渡すこともできます。

```
{{{link "See more..." story.url}}}
```

In this case, Handlebars will pass the link helper two parameters: the String `"See more..."` and the result of evaluating `story.url` in the current context.

このケースでは、Handlebarsは `link` ヘルパーに文字列 `"See more..."` と現在のコンテキストの中で `story.url` の評価結果の2つのパラメータを渡しています。

```
Handlebars.registerHelper('link', function(text, url) {
  return new Handlebars.SafeString(
    "<a href='" + url + "'>" + text + "</a>"
  );
});
```

You could use the exact same helper with dynamic text based on the value of `story.text`:

`story.text` の値のように動的文字列を渡しても、まったく同じようにヘルパーを使うことができます。

```
{{{link story.text story.url}}}
```

Handlebars helpers can also receive an optional sequence of key-value pairs as their final parameter (referred to as hash arguments in the documentation):

Handlebarsヘルパーは、最後のパラメータにkey-valueペアの任意のシーケンスを渡すことが出来ます。（本ドキュメント中でハッシュ引数と呼ばれる）

```
{{{link "See more..." href=story.url class="story"}}}
```

The keys in hash arguments must each be simple identifiers, and the values are Handlebars expressions. 
This means that values can be simple identifiers, paths, or Strings.

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

Handlebars provides additional metadata, such as Hash arguments, to helpers as a final parameter.

Handlebarsはハッシュ引数のように最後のパラメータを介してヘルパーに追加のメタデータを提供します。

Handlebars also offers a mechanism for invoking a helper with a block of the template. 
Block helpers can then invoke that block zero or more times with any context it chooses.

Handlebarsではテンプレートの1ブロックに対してヘルパーを呼び出すメカニズムを提供しています（訳注：文中でブロックヘルパーと呼ばれるもののこと）。
ブロックヘルパーは任意のコンテキストにてブロックを0回以上呼び出すことができます。

[Learn More: Block Helpers](Learn More: Block Helpers.md)

[ブロックヘルパーについて詳しくはこちら](ブロックヘルパーについて詳しくはこちら: Block Helpers.md)
