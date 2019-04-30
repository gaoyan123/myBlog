<html lang="en"><head>
    <meta charset="UTF-8">
<body marginheight="0"><h3>for...in...</h3>
<ol>
<li>获取对象的键名，获取不到键值</li>
<li>所有可枚举的属性</li>
<li>可以中途跳出循环</li>
</ol>
<pre><code>var arr = ['a', 'b', 'c', 'd'];
    arr.foo = "a"
for (let a in arr) {  
    console.log(a); // 0 1 2 3 foo
}</code></pre>
<h3>for...of...</h3>
<ol>
<li>获取对象的键值</li>
<li>只能返回具有数字索引的属性,后面手动添加进去的属性不生效</li>
<li>可以中途跳出循环</li>
</ol>
<pre><code>var arr = ['a', 'b', 'c', 'd'];
arr.foo = "a"

for (let a of arr) {
  console.log(a); // a b c
}</code></pre>
<p>for...of...不能循环普通对象，但是可以改造一下

</p>
<pre><code>var obj = {
    a:'1',
    b:'2'
};

for (let key of Object.keys(obj) ) {
  console.log(key+":"+obj[key]); // a b c
}</code></pre>
<h3>其他遍历语法</h3>
<ol>
<li>for()</li>
<li>forEach() </li>
<li>无法中途跳出循环</li>
<li>for...in...</li>
<li>可以获取键名</li>
<li>如果键名为数字，这种方式遍历出来的键名为字符串</li>
<li>还会遍历手动添加的其他键。包括原型上的键</li>
<li>for...of... ：可以算作最优解决方案了 
Edit By <a href="http://mahua.jser.me">MaHua</a></li>
</ol>
</body></html>