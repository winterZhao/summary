##子进程定时爬虫

#### 场景
自己弄了一个[个人站点](http://www.zhaoleilei.cn)，平时文章更多的是在博客园写就，也懒得将博文搬走，于是想到做一个爬虫将博文目录结构以及分类爬取到自己的个人站点上。
#### 流程
思路是开一个子进程，每隔30分钟爬取博客园自己个人的博文目录和结构，保存到数据库。再在跳到对应路由的时候从数据库读取对应数据。
#### 用到的工具/包;
* promise系列:request-promise,cheerio,child_process,process；
* 回调系列:request，cheerio,async,(这次没有走完，走完后会有代码贴出来。)

以上二选一。

#### 碰到的问题与总结
* **request爬取utf8页面与gbk页面**;注意:使用一下代码时注意引入对应的包,具体参见源码，源码地址在页底:

```javascript
const request = require('request');
const cheerio = require('cheerio');

//utf8页面;需要用到request,cherrio两个包;
request({url:'https://www.baidu.com'},function(err,response,body){
    if(err) throw err;
    var $ = cheerio.load(body,{decodeEntities:false});
//这里必须加上{decodeEntities:false},cheerio默认将汉字转换为unicode编码，加上这句，不转换html实体；
    var imgUrl = $('#lg').find('img').attr('src');
    console.log(imgUrl)
});
----------
//gbk页面;
const request = require('request');
const cheerio = require('cheerio');
const iconv = require('iconv-lite');
request({url:'http://top.baidu.com/',encoding:null},function(err,response,body){
    if(err) throw err;
    body = iconv.decode(body,'gbk');
    var $ = cheerio.load(body);
    var imgUrl = $('.lg').find('img').attr('src');
    console.log(imgUrl);
});
```
不得不说，这种简单的爬虫只是入门，更高深的爬虫比比可见，就比如我本来想拿网页作为gbk的例子，结果报了ISP供应商并没有遵循标准的互联网协议，我们对您的访问作出限制，敬请谅解。错误,原因是没有设置请求头信息,需要在请求中设置请求体的userAgent。
```js
var options = {
    url: 'http://www.163.com/',
    headers: {
        'User-Agent' : 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36',
     },
    encoding : null
};
request(options);
```

* **流程控制问题**
我需要爬取文章目录和分类两个不同的内容，可以是并行，也可以是串行；每个内容都要经历爬取——>找到目标内容——>处理数据——>插入数据库这个过程。这里有两种方案，一种是promise，一种是async;这里选择的是promise;选择promise就会遇到一个大问题，我们常用的第三方包有些是没有promise的(当然现在有了)，所以最好的办法是我们可以将一个异步的回调方法封装成一个promise对象，详见3;

```javascriptgairequest
let getRequestData =
        rp(themeUrl)
        .then(function(body){
            let $ = cheerio.load(body,{decodeEntities:false});
            let theme = $('#sidebar_postcategory').find('a');
            let themeArr = [];
            theme.each(function(index,item){
                let obj ={};
                let $item = $(item);
                obj.title = $item.html();
                obj.link = $item.attr('href');
                themeArr.push(obj);
            });
            return themeArr;
        })
        .then(function (themeArr){
            p.query('delete from theme');
            for (let i=0,r=themeArr.length;i<r;i++){
                let sql = 'insert into theme(title,link) values (?,?)';
                let title = themeArr[i].title;
                let link = themeArr[i].link;
                p.query(sql,[title,link]);
            }
        })
        .then(function(){
            return rp(articalUrl)
        })
```

**注一**:这里request包改为request-promise;
**注二**: 每一个then里的回调的return都是下一个then方法里的参数;

* **封装一个promise对象**;
　　这里头es6有详细的介绍[promise](http://es6.ruanyifeng.com/#docs/promise);
```javascript
  const fs = require('fs');
const promise = new Promise(function(resolve,reject){
    fs.readFile('./index.html',function(err,data){
        if(err){
            reject(err);
        } else {
            resolve(data);
        }
    })
});
promise.then(function(data){
    console.log(data.toString());
}).catch(function(err){
    console.log(err);
})
```
　　事实上，我们在平时使用的时候只需要执行var promise = new Promise(function(resolve,reject){……})，就已经相当于封装了一个promise对象，可以直接的使用.then.catch的链式写法了。具体情况阮一峰的教程;封装promise对象是很有用的技巧。此外promise.all()方法也请留意一下，类似于async.auto；能起到流程先后顺序控制的问题;
* **开一个子进程，让爬虫在子进程里跑**;

　　nodeJs的子进程child_progress建立方式有四种:spawn，exec，execFile，fork;这里开启子进程的目的是为了让爬虫所在的node应用跑起来，故选择fork;
```javascript
let fork = require('child_process').fork;
fork ('./task/crawl.js');
```
　　spawn，exec，execFile，fork具体四种方式的差异和作用、格式等请上网查找，或参考[这里](http://www.cnblogs.com/zhaowinter/p/5661048.html)；

* **启动定时功能，cron**

网上定时功能很多推荐later，但没看太懂，选择了linux下的cron在nodejs里的实现cron包;将子进程套入;

```javascript
let fork = require('child_process').fork;
let cronJob = require('cron').CronJob;
let job = new cronJob('* */30 * * * *',function(){
   fork ('./task/crawl.js');

},null,true);
```
　　事实上，我所写的爬虫到这里已经算爬完了，但还有两个问题


* 每次爬取到的内容和已经保存到数据库内容的关系;我处理的办法简单粗暴，每次在将爬取结果往数据库中插入的时候，会首先将数据库里的数据删除。因为只有实时的最新的数据是我所需要的。

* nodeJS子进程与定时功能相结合会导致产生众多子进程。这一点可以在电脑的进程管理器中查看到，我的解决办法是在fork运行的nodejs文件末尾加上`process.exit();`

[demo](https://github.com/winterZhao/server/tree/koa)文件里的`app.js`以及`task`文件夹为此次项目的demo;

　　以上是这次爬虫学习经历的总结。