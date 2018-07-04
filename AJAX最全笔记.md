# AJAX最全笔记

## 处理二进制数据

XMLHttpRequest一般用来发送和接收文本数据，但也可以发送和接收二进制内容。

处理二进制数据的两种方式：

XMLHttpRequest.prototype.overrideMimeType虽然不是标准方法，但的确是一个解决方案。

responseType属性（IE12+）。

实例：

```JavaScript
//使用overrideMimeType，注意，此例中是同步的XHR请求。
function load_binary_resource(url) {
  var req = new XMLHttpRequest();
  req.open('GET', url, false);
  //XHR binary charset opt by Marcus Granado 2006 [http://mgran.blogspot.com]
  req.overrideMimeType('text/plain; charset=x-user-defined');
  req.send(null);
  if (req.status != 200) return '';
  return req.responseText;
}
var filestream = load_binary_resource(url);
var abyte = filestream.charCodeAt(x) & 0xff; // 扔掉的高位字节(f7)
//但是在老旧浏览器中，如何消费这个数据，是个问题。
```

