# nodejs_captchapng
`nodejs captcha examples`
##验证码需要将url输出头设置为image/png的mime类型，其次要保存显示码字符串到session中。
##首先用npm安装一些相关的包:
`npm install captchapng`
`npm install express`
`npm install express-session`
###编写如下执行文件代码：
```javascript
const express = require('express');
const captchapng = require('captchapng');
const session = require('express-session');

var app = express();
app.use(session({
  secret: 'hes',
  resave: false,
  saveUninitialized: true,
  cookie: { maxAge: 60000 }
}));

app.get('/', function (req, res) {
  randomcodePngController(req, res);
});

app.get('/session', function (req, res) {
  res.send(req.session.loginStatus);
});
app.listen(2345);

function randomcodePngController(req , res){
    var code = '0123456789';
    var length = 4;
    var randomcode = '';
    for (var i = 0; i < length; i++) {
        randomcode += code[parseInt(Math.random() * 1000) % code.length];
    }
    // 保存到session
    if (null == req.session['loginStatus']) {
        req.session["loginStatus"] = {};
    }
    req.session.loginStatus.randomcode= randomcode;

   // 输出图片
     var p = new captchapng(100,36,parseInt(randomcode)); // width,height,numeric captcha
    p.color(0, 0, 0, 255);  // First color: background (red, green, blue, alpha)
    p.color(255, 255, 255, 255); // Second color: paint (red, green, blue, alpha)
    var img = p.getBase64();
    var imgbase64 = new Buffer(img,'base64');
    res.writeHead(200, {
        'Content-Type': 'image/png'
    });
    res.end(imgbase64);
}
```
访问文档地址：[capchapng的详细说明地址](http://mousebird.cn/blog/2016/11/09/8)
