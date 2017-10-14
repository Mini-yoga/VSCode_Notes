模块
==

```coffee
module.exports = greet;
```

函数`greet()`是我们在`hello`模块中定义的，你可能注意到最后一行是一个奇怪的赋值语句，它的意思是，把函数`greet`作为模块的输出暴露出去，这样其他模块就可以使用`greet`函数了。

```coffee
var greet = require('./hello');//相对路径
var s = 'Michael';
greet(s); // Hello, Michael!
```

其他模块怎么使用`hello`模块的这个`greet`函数



CommonJS规范
==========

### 一个模块想要对外暴露变量（函数也是变量），可以用`module.exports = variable;`，一个模块要引用其他模块暴露的变量，用`var ref = require('module_name');`就拿到了引用模块的变量。



基本模块
====

因为Node.js是运行在服务区端的JavaScript环境，服务器程序和浏览器程序相比，最大的特点是没有浏览器的安全限制了，而且，服务器程序必须能接收网络请求，读写文件，处理二进制内容，所以，Node.js内置的常用模块就是为了实现基本的服务器功能。这些模块在浏览器环境中是无法被执行的，因为它们的底层代码是用C/C++在Node.js运行环境中实现的。

### global

在Node.js环境中，也有唯一的全局对象

### process

`process`也是Node.js提供的一个对象，它代表当前Node.js进程。

### 判断JavaScript执行环境

有很多JavaScript代码既能在浏览器中执行，也能在Node环境执行，但有些时候，程序本身需要判断自己到底是在什么环境下执行的，常用的方式就是根据浏览器和Node环境提供的全局变量名称来判断：

```coffee
if (typeof(window) === 'undefined') {
    console.log('node.js');
} else {
    console.log('browser');
}
```



fs
==

Node.js内置的`fs`模块就是文件系统模块，负责读写文件。

和所有其它JavaScript模块不同的是，`fs`模块同时提供了异步和同步的方法。

### 异步读文件

```coffee
var fs = require('fs');

fs.readFile('sample.txt', 'utf-8', function (err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
    }
});
```

读二进制文件，当读取二进制文件时，不传入文件编码时，回调函数的`data`参数将返回一个`Buffer`对象。在Node.js中，`Buffer`对象就是一个包含零个或任意个字节的数组（注意和Array不同）。

```coffee
var fs = require('fs');

fs.readFile('sample.png', function (err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
        console.log(data.length + ' bytes');
    }
});
```

### 同步读文件

除了标准的异步读取模式外，`fs`也提供相应的同步读取函数。同步读取的函数和异步函数相比，多了一个`Sync`后缀，并且不接收回调函数，函数直接返回结果。

```coffee
var fs = require('fs');

var data = fs.readFileSync('sample.txt', 'utf-8');
console.log(data);
```

如果同步读取文件发生错误，则需要用`try...catch`捕获该错误：

```coffee
try {
    var data = fs.readFileSync('sample.txt', 'utf-8');
    console.log(data);
} catch (err) {
    // 出错了
}
```

### 写文件

```coffee
var fs = require('fs');

var data = 'Hello, Node.js';
fs.writeFile('output.txt', data, function (err) {
    if (err) {
        console.log(err);
    } else {
        console.log('ok.');
    }
});
```

和`readFile()`类似，`writeFile()`也有一个同步方法，叫`writeFileSync()`

### stat

如果我们要获取文件大小，创建时间等信息，可以使用`fs.stat()`，它返回一个`Stat`对象，能告诉我们文件或目录的详细信息：

`stat()`也有一个对应的同步函数`statSync()`

#### 

stream
======

`stream`是Node.js提供的又一个仅在服务区端可用的模块，目的是支持“流”这种数据结构。流的特点是数据是有序的，而且必须依次读取，或者依次写入，不能像Array那样随机定位。在Node.js中，流也是一个对象，我们只需要响应流的事件就可以了：`data`事件表示流的数据已经可以读取了，`end`事件表示这个流已经到末尾了，没有数据可以读取了，`error`事件表示出错了。

```coffee

var fs = require('fs');

// 打开一个流:
var rs = fs.createReadStream('sample.txt', 'utf-8');

rs.on('data', function (chunk) {
    console.log('DATA:')
    console.log(chunk);
});

rs.on('end', function () {
    console.log('END');
});

rs.on('error', function (err) {
    console.log('ERROR: ' + err);
});
```

所有可以读取数据的流都继承自`stream.Readable`，所有可以写入的流都继承自`stream.Writable`。

### pipe

### 就像可以把两个水管串成一个更长的水管一样，两个流也可以串起来。一个`Readable`流和一个`Writable`流串起来后，所有的数据自动从`Readable`流进入`Writable`流，这种操作叫`pipe`。

在Node.js中，`Readable`流有一个`pipe()`方法，就是用来干这件事的。

让我们用`pipe()`把一个文件流和另一个文件流串起来，这样源文件的所有数据就自动写入到目标文件里了，所以，这实际上是一个复制文件的程序：

```coffee
var fs = require('fs');

var rs = fs.createReadStream('sample.txt');
var ws = fs.createWriteStream('copied.txt');

rs.pipe(ws);

```

默认情况下，当`Readable`流的数据读取完毕，`end`事件触发后，将自动关闭`Writable`流。如果我们不希望自动关闭`Writable`流，需要传入参数：

```coffee
readable.pipe(writable, { end: false });
```



http
====

### HTTP服务器

`request`对象封装了HTTP请求，我们调用`request`对象的属性和方法就可以拿到所有HTTP请求的信息；

`response`对象封装了HTTP响应，我们操作`response`对象的方法，就可以把HTTP响应返回给浏览器。

```coffee
// 导入http模块:
var http = require('http');

// 创建http server，并传入回调函数:
var server = http.createServer(function (request, response) {
    // 回调函数接收request和response对象,
    // 获得HTTP请求的method和url:
    console.log(request.method + ': ' + request.url);
    // 将HTTP响应200写入response, 同时设置Content-Type: text/html:
    response.writeHead(200, {'Content-Type': 'text/html'});
    // 将HTTP响应的HTML内容写入response:
    response.end('<h1>Hello world!</h1>');
});

// 让服务器监听8080端口:
server.listen(8080);

console.log('Server is running at http://127.0.0.1:8080/');
```

### 文件服务器

让我们继续扩展一下上面的Web程序。我们可以设定一个目录，然后让Web程序变成一个文件服务器。要实现这一点，我们只需要解析`request.url`中的路径，然后在本地找到对应的文件，把文件内容发送出去就可以了。

解析URL需要用到Node.js提供的`url`模块，它使用起来非常简单，通过`parse()`将一个字符串解析为一个`Url`对象：

```coffee
var url = require('url');

console.log(url.parse('http://user:pass@host.com:8080/path/to/file?query=string#hash'));

```

处理本地文件目录需要使用Node.js提供的`path`模块，它可以方便地构造目录：

```coffee
var path = require('path');

// 解析当前目录:
var workDir = path.resolve('.'); // '/Users/michael'

// 组合完整的文件路径:当前目录+'pub'+'index.html':
var filePath = path.join(workDir, 'pub', 'index.html');
// '/Users/michael/pub/index.html'
```

最后，我们实现一个文件服务器`file_server.js`：

```coffee
var
    fs = require('fs'),
    url = require('url'),
    path = require('path'),
    http = require('http');

// 从命令行参数获取root目录，默认是当前目录:
var root = path.resolve(process.argv[2] || '.');

console.log('Static root dir: ' + root);

// 创建服务器:
var server = http.createServer(function (request, response) {
    // 获得URL的path，类似 '/css/bootstrap.css':
    var pathname = url.parse(request.url).pathname;
    // 获得对应的本地文件路径，类似 '/srv/www/css/bootstrap.css':
    var filepath = path.join(root, pathname);
    // 获取文件状态:
    fs.stat(filepath, function (err, stats) {
        if (!err && stats.isFile()) {
            // 没有出错并且文件存在:
            console.log('200 ' + request.url);
            // 发送200响应:
            response.writeHead(200);
            // 将文件流导向response:
            fs.createReadStream(filepath).pipe(response);
        } else {
            // 出错了或者文件不存在:
            console.log('404 ' + request.url);
            // 发送404响应:
            response.writeHead(404);
            response.end('404 Not Found');
        }
    });
});

server.listen(8080);

console.log('Server is running at http://127.0.0.1:8080/');

```

#### 

crypto
======

crypto模块的目的是为了提供通用的加密和哈希算法。

### MD5和SHA1

MD5是一种常用的哈希算法，用于给任意数据一个“签名”。这个签名通常用一个十六进制的字符串表示：

```coffee
const crypto = require('crypto');

const hash = crypto.createHash('md5');

// 可任意多次调用update():
hash.update('Hello, world!');
hash.update('Hello, nodejs!');

console.log(hash.digest('hex')); // 7e1977739c748beac0c0fd14fd26a544
```

### Hmac

Hmac算法也是一种哈希算法，它可以利用MD5或SHA1等哈希算法。不同的是，Hmac还需要一个密钥，只要密钥发生了变化，那么同样的输入数据也会得到不同的签名，因此，可以把Hmac理解为用随机数“增强”的哈希算法。

```coffee
const crypto = require('crypto');

const hmac = crypto.createHmac('sha256', 'secret-key');

hmac.update('Hello, world!');
hmac.update('Hello, nodejs!');

console.log(hmac.digest('hex')); // 80f7e22570...
```

### AES

AES是一种常用的对称加密算法，加解密都用同一个密钥。crypto模块提供了AES支持，但是需要自己封装好函数，便于使用

### Diffie-Hellman

DH算法是一种密钥交换协议，它可以让双方在不泄漏密钥的情况下协商出一个密钥来。DH算法基于数学原理，比如小明和小红想要协商一个密钥，可以这么做：

1. 小明先选一个素数和一个底数，例如，素数`p=23`，底数`g=5`（底数可以任选），再选择一个秘密整数`a=6`，计算`A=g^a mod p=8`，然后大声告诉小红：`p=23，g=5，A=8`；
2. 小红收到小明发来的`p`，`g`，`A`后，也选一个秘密整数`b=15`，然后计算`B=g^b mod p=19`，并大声告诉小明：`B=19`；
3. 小明自己计算出`s=B^a mod p=2`，小红也自己计算出`s=A^b mod p=2`，因此，最终协商的密钥`s`为`2`。

在这个过程中，密钥`2`并不是小明告诉小红的，也不是小红告诉小明的，而是双方协商计算出来的。第三方只能知道`p=23`，`g=5`，`A=8`，`B=19`，由于不知道双方选的秘密整数`a=6`和`b=15`，因此无法计算出密钥`2`。