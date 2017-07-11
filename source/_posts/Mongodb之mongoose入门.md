---
title: Mongodb之mongoose入门
date: 2017-07-11 16:51:46
tags: mongodb
---
> 最近一直在看node，先从简单的express学起，其他的还好说，主要是和数据库的连接。既然是js，那就先从mongodb开始吧，我们使用mongoose中间件连接mongodb。

### 基本操作

关于mongoose的基本操作其实网上很多教程，按照mongoose官网的[quick start guide](http://mongoosejs.com/docs/index.html)，很快就能有个大概的了解。
```js
$ npm install mongoose --save
var mongoose = require('mongoose')
mongoose.connect('mongodb://ip:port/database')
```
<!--more-->
这里注意，很多教程都没有详细说明对于有权限的数据库的连接方式，也让我在刚接触时郁闷了很久，数据库加密是防止数据库发生损失的第一道锁，如下(在数据库中操作)
```js
$ use database
$ db.createUser({
    user: user,
    pwd: pwd,
    roles:[
        {
            role: "readWrite ...", // 具体的权限
            db: "" //datebase
        }
    ]
})
$ db.auth("userName","password")
```
注意，在服务器上，如果要直接操作数据库，可以先连接，再通过db.auth()来验证权限，可是如果在node中该怎么做呢？

example：我的数据库账号是admin,密码为password,需要连接的database为test，数据库地址是111.222.333.444:1024那么再连接的时候就可以直接指定连接的地址为
```js
mongoose.connect('mongodb://admin:password@111.222.333.444:1024/test')
```
这样就不会出现由于权限的问题而无法连接了，但是要注意，在mongodb的admin下建的账户，在子数据库中是不能直接验证的，同时也为了安全，应该在每个数据库下都建立不同权限的账户
```js
$ use admin //切换到admin
$ db.system.users.find().pretty()   //查看系统下所有的账户
$ use test  //切换到test
$ show users  //查询test下的账户
```
### 连接数据库

在成功连接了数据库并通过正好验证以后，就该对数据库进行增删改查了，很多关于mongoose的入门的教程都是寥寥几句，尤其对于最核心的Schema和Model，并没有过多的深入，而官方文档对于刚接触不深的我来说，也是比较吃力，下面是个人在学习过程中的一些小小的总结

Schema是mongoose的一种模式，这是比较抽象的一个东西，它对应的是数据库中的collection,比如，db.collection.find({})的数据是这样的
```js
{
    name: 'Jack',
    age: '25',
    date: '2017-05-25'
}
```
那么对应的这个Schema就是这样的：
```js
{
    name: {type: String},
    age: {type: Number},
    date: {type: Date}
}
```
Schema定义了一个集合中数据的基本格式，但控制数据的，其实是model,关于model稍后再细谈。

好了，现在就可以进行下一步了：
```js
var Schema = mongoose.Schema;
var newSchema = new Schema(
    {
        name: {type: String},
        age: {type: Number},
        date: {type: Date}
    },
    {
        collection: "" // 这个是什么？？？
    }
)

module.exports = mongoose.model("User", newSchema)
```
在这一步里，我们成功的创造了一个Schema的实例，也就是一个新的模式，并通过mongoose.model()生成了一个model，而具体操作数据库的，就是这个model,比如这里的User，下面就插入一条数据：
```js
// 先require()到刚才的文件
var user = new User({
    name: 'jesse',
    age: 25,
    date: new Date()
})
user.save(function(err, res){...}
我们可以查看一下这个数据

User.find(function(err, res){
    console.log(res)
})
```
可以成功打印出刚插入数据的数据库里已经有了这条数据

看一下服务器：
```sql
$ show collections

users

$ db.users.find()
......
......  //具体的数据
```
也没有问题，可是，这个users是怎么来的呢？还有刚刚在定义Schema时，那个collection是干什么的？我们怎么从已有的数据库中取数据呢？
带着这三个问题，我们继续探索！

### 继续探索

刚才，我们定义了数据的模式，生成了model，并成功的插入了一条数据，可是我们并没有定义这个collection的名字呀，怎么多出来了一个users，经过网上的搜索，应该是mongoose在生成数据时，自动在User等Schema的后面加上了s，并转换为小写，作为collection的默认名字，但如果想要自定义这个名字该怎么做呢?

**可以这样**
```js
mongoose.model("User", newSchema,"userseseseses...")
```
也就是model()的第三个参数，也可以这样
```js
var newSchema = new Schema({
    ...
},{
    collection: "collectionName"
})
```
即Schema({},{})的第二个参数.

