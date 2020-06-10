# NeDB, Node.js嵌入式数据库

*NeDB* 是使用 *Node.js* 实现的一个 *NoSQL* 嵌入式数据库操作模块， 可以充当内存数据库，也可以用来实现本地存储，甚至可以在浏览器中使用。 查询方式比较灵活，支持使用正则、比较运算符、逻辑运算符、索引以及 *JSON* 深度查询等，适用于不需要大量数据处理的应用系统。

本节将以一个 *demo* 开始，一步步讲述 *NeDB* 的创建及使用。

## 安装NeDB

使用 *npm* 安装 *NeDB* ，执行如下命令：

```sh
$ npm install nedb --save
```

## 创建NeDB数据库

### 引入依赖

首先，使用 *require* 引入 *nedb* ：

```javascript
var NeDB = require('nedb')
```

### 数据库初始化

接下来，我们需要初始化一个 *NeDB* 对象：

```javascript
var db = new NeDB({
    filename: './user.db',
    autoload: true,
})
```

初始化数据库时，我们传入两个参数：*filename* 和 *autoload* 。

第 *2* 行，*filename* 用于指定数据存储的文件位置，在本示例中，*filename* 指定为同个目录下的 *user.db*； 第 *3* 行，设置 *autoload* 为 *true* ，用于自动加载数据库。

至此，我们得到了一个数据库对象 *db* 。 接下来，对数据库进行常规操作：插入、查询、更新、删除。

## 数据操作

### 插入

往数据库插入一条数据：

```javascript
db.insert({
    name: 'Alice',
    age: 20,
    rank: 1,
}, function(err, doc) {
    console.log('inserted:', doc)
})
```

上述代码中，*insert* 方法接收两个参数，一个是插入的数据 *doc*，一个是插入后的回调函数 *callback*； 第 *2-4* 行，*doc* 在本例中是一个 *Object* 对象。实际上，*doc* 也可以是 *Number* 、*Boolean* 、*String* 、*Date* 、*Null* 等基本类型，或者是 *Array* 类型； 第 *5-7* 行，*callback* 接收两个参数，*err* 和 *doc* 。*err* 表示插入错误，*doc* 表示插入后的数据。

### 查询

查询数据，我们使用 *find* 方法：

```javascript
db.find({
    name: 'Alice',
}, function(err, docs) {
    console.log('Alice found:', docs)
})
```

同样，*find* 方法也接收两个参数：查询条件 *query* 和回调函数 *callback*。

第 *2* 行，*query* 参数除了接受 *Object* 类型，也支持其他更高级的查询形式； 第 *3-5* 行，*callback* 接收两个参数： *err* 和 *docs*。*err* 表示查询错误，*docs* 表示查询到的数据。

### 更新

使用 *update* 方法，将 *Alice* 的年龄更改为 *21* ：

```javascript
db.update({
    name: 'Alice',
}, {
    $set: {
        age: 21,
    },
}, function(err, n) {
    console.log('docs updated:', n)
})
```

与前面两个方法不同，*update* 方法传入的参数更多，在本例中，接收了三个参数，分别是，匹配条件 *query*、更新的数据 *updateDoc* 和 回调函数 *callback*。

*query* 与 *find* 方法的相同，我们重点来看看 *updateDoc* 和 *callback*。

第 *4-6* 行，毫无疑问 *{age: 21}* 是需要更新的数据，那 *$set* 又是什么作用呢？我们更新数据时，可能有时需要全量更新，但是更多时候只需替换指定的字段； 这个 *$set* 就是表示，只更新指定的字段。第 *7-9* 行，同样，*callback* 第一个参数 *err* 表示更新错误，第二个参数 *n* 表示更新的个数。

现在，我们查询下数据，看是否真的如愿更新了 *Alice* 的年龄：

```javascript
db.find({
    name: 'Alice',
}, function(err, docs) {
    console.log('Alice changed:', docs)
})
```

### 删除

最后，使用 *remove* 方法删除有关 *Alice* 的数据：

```javascript
db.remove({
    name: 'Alice',
}, function(err, n) {
    console.log('docs deleted:', n)
})
```

*remove* 方法接收的参数与 *find* 方法类似，此处不再赘述。 需要注意的是，*callback* 函数接受的第二个参数 *n* 是表示被删除的个数，而不是删除的数据。

同样，我们也通过 *find* 方法，验证数据是否已经被删除了：

```javascript
db.find({
    name: 'Alice',
}, function (err, docs) {
    console.log('Alice removed:', docs)
})
```

## 运行demo

最后，我们运行 *demo.js*，体会下数据从创建到删除的过程。打开终端，执行：

```sh
$ node demo.js
```

在终端可看到以下输出：

```sh
inserted: { name: 'Alice', age: 20, rank: 1, _id: '6YB3yV31XIvknXMm' }
Alice found: [ { name: 'Alice', age: 20, rank: 1, _id: '6YB3yV31XIvknXMm' } ]
docs updated: 1
Alice changed: [ { name: 'Alice', age: 21, rank: 1, _id: '6YB3yV31XIvknXMm' } ]
docs deleted: 1
Alice removed: []
```

这样，我们成功地运行了一个完整的 *nedb* 例子。

## 附录

更多 *Node.js* 技术文章请访问：[Node.js小册](https://nodejs.fasionchan.com)，转至 [原文](https://nodejs.fasionchan.com/zh_CN/latest/database/nedb/index.html) 可获得最佳阅读体验

订阅更新，获取更多学习资料，请关注我们的 [微信公众号](https://nodejs.fasionchan.com/zh_CN/latest/about/contact.html#wechat-mp) ：

![小菜学编程](https://cdn.fasionchan.com/coding-fan-wechat-soso-qrcode.png?x-oss-process=image/resize,w_640)