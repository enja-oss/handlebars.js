
Handlebars provides the power necessary to let you build semantic templates effectively with no frustration.

Handlebarsは、効果的にセマンティックなテンプレートを構築するために必要な機能をフラストレーションなく提供します。

Mustache templates are compatible with Handlebars, so you can take a Mustache template, import it into Handlebars, and start taking advantage of the extra Handlebars features.

MustacheテンプレートとHandlebarsは互換性があります。MustacheテンプレートからHandlebarsにインポートして、Handlebarsの特別な機能を活用して始めることができます。

## はじめに

Handlebars templates look like regular HTML, with embedded handlebars expressions.

Handlebarsのテンプレートは普通のHTMLのように見ることができ、内部にHandlebarsの評価式が組み込まれています。

```` html
<div class="entry">
  <h1>{{title}}</h1>
  <div class="body">
    {{body}}
  </div>
</div>
````

A handlebars expression is a `{{`, some contents, followed by a `}}`

ひとつのHandlebars式は `{{`、なにかのコンテンツ、最後に `}}` で構成されます。

You can deliver a template to the browser by including it in a &lt;script&gt; tag.

テンプレートを&lt;script&gt;タグの中にインクルードする方法で、ブラウザへ届けることもできます。

```` html
<script id="entry-template" type="text/x-handlebars-template">
  template content
</script>
````

Compile a template in JavaScript by using `Handlebars.compile`

`Handlebars.compile` を使うことでテンプレートをJavascriptへコンパイルします。

```` javascript
var source   = $(" 
#entry
-template ").html();
var template = Handlebars.compile(source);
````

It is also possible to precompile your templates. 
This will result in a smaller required runtime library and significant savings from not having to compile the template in the browser. 
This can be especially important when working with mobile devices.

テンプレートをプリコンパイルすることも可能です。
結果的に、必要なランタイムライブラリがより小さくなり、ブラウザ上でテンプレートをコンパイルする必要がないので大幅なコスト削減になります。
これは、モバイルデバイス上で動作する場合、特に重要です。

Get the HTML result of evaluating a Handlebars template by executing the template with a context.

コンテキストとテンプレートを同時に実行することで、Handlebarsテンプレートを評価してHTMLを得ます。

```` javascript
var context = {title: "My New Post", body: "This is my first post!"}
var html    = template(context);
````

結果:


```` html
<div class="entry">
  <h1>My New Post</h1>
  <div class="body">
    This is my first post!
  </div>
</div>
````

Handlebars HTML-escapes values returned by a `{{expression}}`. 
If you don't want Handlebars to escape a value, use the "triple-stash".

Handlebarsでは `{{expression}}` を使うことでHTMLエスケープした結果を返します。
Handlebarsでエスケープして欲しくない場合、"トリプルスラッシュ"を使用してください。`{{{`


```` html
<div class="entry">
  <h1>{{title}}</h1>
  <div class="body">
    {{{body}}}
  </div>
</div>
````

このコンテキストを利用した場合:


````
{
  title: "All about <p> Tags",
  body: "<p>This is a post about &lt;p&gt; tags</p>"
}
````

結果:


````
<div class="entry">
  <h1>All About &lt;p&gt; Tags</h1>
  <div class="body">
    <p>This is a post about &lt;p&gt; tags</p>
  </div>
</div>
````

Handlebars will not escape a `Handlebars.SafeString`. 
If you write a helper that generates its own HTML, you will usually want to return a `new Handlebars.SafeString(result)`. In such a circumstance, you will want to manually escape parameters.

Handlebars は `Handlebars.SafeString` をエスケープしません。
もし、独自のHTMLを生成するヘルパーを作成する場合、通常 `new Handlebars.SafeString(result)` した結果を欲するでしょう。
このような状況では、手動でのエスケープパラメータが欲しくなるはずです。

````
Handlebars.registerHelper('link', function(text, url) {
  text = Handlebars.Utils.escapeExpression(text);
  url  = Handlebars.Utils.escapeExpression(url);

  var result = '<a href="' + url + '">' + text + '</a>';

  return new Handlebars.SafeString(result);
});
````

This will escape the passed in parameters, but mark the response as safe, 
so Handlebars will not try to escape it even if the "triple-stash" is not used.

パラメータで渡されたものをエスケープして、安全というマークを返します。
これにより、Handlebarsでは"トリプルスラッシュ"を使用しなかったとしても、エスケープしようと試みません。 
