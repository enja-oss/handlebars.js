 +  元文書: [wycats/handlebars-site/index.haml · wycats/handlebars-site · GitHub]
(https://github.com/wycats/handlebars-site/blob/master/src/pages/index.haml 
"wycats/handlebars-site/index.haml · wycats/handlebars-site · GitHub")

## Block Expressions

## ブロック式

Block expressions allow you to define helpers that will invoke a section of your template with a different context than the current. 
Let's consider a helper that will generate an HTML list:

ブロック式は、テンプレートの中で部分的に現在とは異なるコンテキストを呼び出すためのヘルパーを定義することができます。
では、HTMLリストを生成するヘルパーについて考えてみましょう

````
{{#list people}}{{firstName}} {{lastName}}{{/list}}
````

If we have the following context:

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

we would create a helper named `list` to generate our HTML list. 
The helper receives the `people` as its first parameter, and an options hash as its second parameter. 
The options hash contains a property named `fn`, which you can invoke with a context just as you would invoke a normal Handlebars template.

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

When executed, the template will render:

実行すると、テンプレートがレンダリングされます。

````
<ul>
  <li>Yehuda Katz</li>
  <li>Carl Lerche</li>
  <li>Alan Johnson</li>
</ul>
````

Block helpers have more features, such as the ability to create an `else` section (used, for instance, by the built-in if helper).

ブロックヘルパーは `else` セクションを作成する機能など、他にも多くの機能を持っています。
（例えば、ビルトインされているヘルパーで使用されています。）

Since the contents of a block helper are escaped when you call `options.fn(context)`, Handlebars does not escape the results of a block helper. If it did, inner content would be double-escaped!

`options.fn(context)` を呼び出した場合、ブロックヘルパーの内容がエスケープされるため、Handlebarsはブロックヘルパーの結果をエスケープしません。
もしそれがなかった場合、中のコンテンツが2回エスケープされてしまいます！
