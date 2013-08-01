 +  元文書: [wycats/handlebars-site/index.haml · wycats/handlebars-site · GitHub]
(https://github.com/wycats/handlebars-site/blob/master/src/pages/index.haml 
"wycats/handlebars-site/index.haml · wycats/handlebars-site · GitHub")

## ブロック式

ブロック式は、テンプレートの中で部分的に現在とは異なるコンテキストを呼び出すためのヘルパーを定義することができます。
では、HTMLリストを生成するヘルパーについて考えてみましょう

````
{{#list people}}{{firstName}} {{lastName}}{{/list}}
````

次のようなコンテキストを持つ場合:

````
{
  people: [
    {firstName: "Yehuda", lastName: "Katz"},
    {firstName: "Carl", lastName: "Lerche"},
    {firstName: "Alan", lastName: "Johnson"}
  ]
}
````

HTMLリストを生成するために `list` という名前のヘルパーを作成します。
ヘルパーは最初のパラメータで `people` を受け取り、2番目のパラメータでオプションhashを受け取ります。
オプションhashでは、通常のHandlebarsテンプレートを呼び出す場合と同じように、
コンテキストを伴って呼び出すことができる `fn` という名前のプロパティが含まれています。

````
Handlebars.registerHelper('list', function(items, options) {
  var out = "<ul>";

  for(var i=0, l=items.length; i<l; i++) {
    out = out + "<li>" + options.fn(items[i]) + "</li>";
  }

  return out + "</ul>";
});
````

実行すると、テンプレートがレンダリングされます。

````
<ul>
  <li>Yehuda Katz</li>
  <li>Carl Lerche</li>
  <li>Alan Johnson</li>
</ul>
````

ブロックヘルパーは `else` セクションを作成する機能など、他にも多くの機能を持っています。
（例えば、ビルトインされているヘルパーで使用されています。）

`options.fn(context)` を呼び出した場合、ブロックヘルパーの内容はすでにエスケープされているため、Handlebarsはブロックヘルパーの結果をエスケープしません。
エスケープしてしまうと、中のコンテンツが2回エスケープされることになります!
