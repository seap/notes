##koa vs. express
### HTTP Request
```
// Express
// 获取QueryString参数
// GET /shoes?order=desc&shoe[color]=blue
req.query.order
// => "desc"

req.query.shoe.color
// => "blue"

// 通过路由获取Restful风格的URL参数
app.get('/user/:id?', function userIdHandler(req, res) {
    console.log(req.params.id);
    res.send('GET');
})

//获取POST数据:需要body-parser中间件
var bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: true }));
app.post('/', function (req, res) {
    console.log(req.body);
    res.json(req.body);
})
```
```
// 获取QueryString参数
// GET /?action=delete&id=1234
this.request.query
// => { action: 'delete', id: '1234' }

// 通过路由获取Restful风格的URL参数
var route = require('koa-route');
app.use(route.get('/post/:id', function *(id){
    console.log(id);
    // => 1234
}));

// 获取POST数据:需要co-body中间件
// Content-Type: application/x-www-form-urlencoded
// title=Test&content=This+is+a+test+post
var parse = require('co-body');
app.use(route.post('/post/new', function *(){
    var post = yield parse(this.request);//this
    console.log(post);
    // => { title: 'Test', content: 'This is a test post' }
}));
```

### HTTP Response
```
// Express
// 输出普通的html
res.render('tplName', {data});

// 输出JSON
res.jsonp({ user: 'Samoay' });
// => { "user": "Samoay" }

//输出JSONP   ?callback=foo
res.jsonp({ user: 'Samoay' });
// => foo({ "user": "Samoay" });

//res.send([body]);
res.send(new Buffer('whoop'));
res.send({ some: 'json' });
res.send('<p>some html</p>');

//设定HTTP Status状态码
res.status(200);
```
```
// koa直接set ctx的status和body
app.use(route.get('/post/update/:id', function *(id){
    this.status = 404;
    this.body = 'Page Not Found';
}));

var views = require('co-views');
var render = views('tpls', {
    default: "jade"//render不提供后缀名时
});
app.use(route.get('/post/:id', function *(id){
    var post = getPost(id);
    this.status = 200;//by default, optional
    this.body = yield render('user', post);
}));

//JSON
var json = require('koa-json');
app.use(route.get('/post/:id', function *(id){
    this.body = {id:1234, title:"Test post", content:"..."};
}));
```
### Route
```
// Express
// app.all表示对所有的路径和请求方式都要经过这些回调方法的处理，可以逗号方式传入多个
app.all('*', authentication, loadUser);
// 也可以多次调用
app.all('*', requireAuthentication)
app.all('*', loadUser);
// 也可以针对某具体路径下面的所有请求
app.all('/api/*', requireAuthentication);

// app.get GET方式的请求
app.get('/user/:id', function(req, res) {
    res.send('user ' + req.params.id);
});

// app.post  POST方式的请求
app.post('/user/create', function(req, res) {
    res.send('create new user');
});
```
```
// Koa
// 和Express不同，koa需要先引入route中间件
var route = require('koa-route');

//引入中间件之后支持的写法差不多，只是路径传入route，然后把route作为中间件挂载到app
app.use(route.get('/', list));
app.use(route.get('/post/new', add));
app.use(route.get('/post/:id', show));
app.use(route.post('/post', create));

// 链式写法
var router = require('koa-router')();

router.get('/', list)
      .get('/post/new', add)
      .get('/post/:id', show)
      .post('/post', create);

app.use(router.routes())
   .use(router.allowedMethods());
```

### Views
```
// Express
// 这只模板路径和默认的模板后缀
app.set('views', __dirname + '/tpls');
app.set('view engine', 'html');

//默认，express根据template的后缀自动选择模板
//引擎渲染，支持jade和ejs。如果不使用默认扩展名
app.engine(ext, callback)

app.engine('html', require('ejs').renderFile);

//如果模板引擎不支持(path, options, callback)
var engines = require('consolidate');
app.engine('html', engines.handlebars);
app.engine('tpl', engines.underscore);

app.get('list', function(res, req){
    res.render('list', {data});
});
```
```
// Koa
// 需要引入co-views中间件
var views = require('co-views');

var render = views('tpls', {
    map: { html: 'swig' },//html后缀使用引擎
    default: "jade"//render不提供后缀名时
});

var userInfo = {
    name: 'tobi',
    species: 'ferret'
};

var html;
html = render('user', { user: userInfo });
html = render('user.jade', { user: userInfo });
html = render('user.ejs', { user: userInfo });
```
### Middleware
```
// Express
// req 用于获取请求信息， ServerRequest 的实例
// res 用于响应处理结果， ServerResponse 的实例
// next() 函数用于将当前控制权转交给下一步处理，
//        如果给 next() 传递一个参数时，表示出错信息
var x = function (req, res, next) {

    // 对req和res进行必要的处理

    // 进入下一个中间件
    return next();

    // 传递错误信息到下一个中间件
    return next(err);

    // 直接输出，不再进入后面的中间件
    return res.send('show page');
};
```

```
// koa 一切都在ctx对象上+generator
app.use(function *(){
    this; // is the Context

    this.request; // is a koa Request
    this.response; // is a koa Response

    this.req;// is node js request
    this.res;// is node js response

    //不再进入后面的中间件, 回溯upstream
    return;
});
```
