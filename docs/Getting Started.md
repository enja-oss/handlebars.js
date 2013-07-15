 +  元文書: [wycats/handlebars-site/index.haml · wycats/handlebars-site · GitHub]
(https://github.com/wycats/handlebars-site/blob/master/src/pages/index.haml 
"wycats/handlebars-site/index.haml · wycats/handlebars-site · GitHub")

Handlebarsは、効果的にセマンティックなテンプレートを構築するために必要な機能をフラストレーションなく提供します。

MustacheテンプレートとHandlebarsは互換性があります。MustacheテンプレートからHandlebarsにインポートして、Handlebarsの特別な機能を活用し始めることができます。

## はじめに

Handlebarsのテンプレートは通常のHTMLの内部にHandlebarsの評価式が組み込まれたものです。

```` html
<div class="entry">
  <h1>{{title}}</h1>
  <div class="body">
    {{body}}
  </div>
</div>
````

ひとつのHandlebars式は `{{`、なにかのコンテンツ、最後に `}}` で構成されます。

テンプレートを&lt;script&gt;タグの中にインクルードする方法で、ブラウザへ届けることもできます。

```` html
<script id="entry-template" type="text/x-handlebars-template">
  template content
</script>
````

`Handlebars.compile` を使うことでテンプレートをJavaScriptへコンパイルします。

```` javascript
var source   = $(" 
#entry
-template ").html();
var template = Handlebars.compile(source);
````

テンプレートをプリコンパイルすることも可能です。
そうすることで、必要なランタイムライブラリがより小さくなり、ブラウザ上でテンプレートをコンパイルする必要がないので大幅なコスト削減になります。
これは、モバイルデバイス上で動作させる場合に特に重要になります。

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

Handlebarsでは `{{expression}}` を使うことでHTMLエスケープした結果を返します。
Handlebarsでエスケープして欲しくない場合、"トリプルスタッシュ"を使用してください。`{{{`


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

Handlebars は `Handlebars.SafeString` をエスケープしません。
もし、独自のHTMLを生成するヘルパーを作成する場合、通常 `new Handlebars.SafeString(result)` した結果が必要でしょう。
このような状況では手動でエスケープできるパラメータあればいいと思うでしょう。

````
Handlebars.registerHelper('link', function(text, url) {
  text = Handlebars.Utils.escapeExpression(text);
  url  = Handlebars.Utils.escapeExpression(url);

  var result = '<a href="' + url + '">' + text + '</a>';

  return new Handlebars.SafeString(result);
});
````

こうすることで、パラメータとして渡されたものをエスケープしながらも、結果には安全というマークを付与します。
これにより、Handlebarsでは"トリプルスタッシュ"を使用しなかったとしても、エスケープしようと試みません。 
