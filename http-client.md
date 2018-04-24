# [译]不要在产线中使用Go's 默认HTTP client

编写通过http与其他服务通信的Go程序是一件轻松、惬意的的事情， 我曾经写过很多
API client包， 并且发现这是一件相当有意思的事情。然而，我掉进了坑，并且非常迅速地
搞垮我的系统： 这个就坑就是 ``the default HTTP client``.

TL;DR: 默认情况下，Go的http包没有设置请求的timeout， 这会允许服务劫持你的goroutines.
所以当你连接外部服务时，应该总是设置合适的timeout时间。

## 示例
例如： 你希望通过JSON REST API与 **spacely-sprockets.com** 交互，并获取可用
齿轮列表。在Go语言中， 也许你会这样做

```go
// error checking omitted for brevity
var sprockets SprocketsResponse
response, _ := http.Get("spacely-sprockets.com/api/sprockets")
buf, _ := ioutil.ReadAll(response.Body)
json.Unmarshal(buf, &sprockets)
```

编译、执行， 所有都运转正常。然后简单包装一下你的代码， 使其变成一个api， 并允许
你的app调用这个api， 来显示 **spacely-sprockets.com** 上的可以用齿轮。

一切正常， 直到有一天你的app停止响应结果。 你检查日志， 没有任何收获。
然后， 你检查你的监控工具， 除了CPU， 内存，I/O，其他一切都正常。然后你启动
一个测试的环境， 并且一切正常，然后就蒙圈了。

挫败的你， 打开Twitter， 你看到 **spacely-sprockets.com** 开发团队发布不久之前系统
崩溃的消息，但是现在已经恢复。 然后你检查系统崩溃时间，惊讶的发现也是在几分钟之前，
难道这是巧合吗。

## Go http包
Go的http包使用一个叫Client结构体来管理HTTP(s)通信的内部状态。 Client是并发安全的，
它负责配置， 管理TCP状态， 处理Cookie等。 当你使用```http.Get(url)``时， 其实你使用
的是```http.DefaultClient``,  一个包级别的变量，它使用了默认配置。声明如下：

```go
var DefaultClient = &Client{}
```

另外， http.Client 通过配置timeout来提前终止长时间运行的连接。 timeout的默认值为0，
意味着默认是没有timeout时间。 对于一个包， 0是一个不错的选择，但是这也让使用者
极易掉入这个坑中， 就像上面示例一样。结果就是， 当**spacely-sprockets.com**
的API挂起所有连接，并长时间无响应时，我们的goroutines也会被挂起，直到API恢复响应。
goroutines挂起越来越多，将会耗尽系统资源，最终导致崩溃。

下面有一个模拟这种情况的示例：

```go
package main
import (
  “fmt”
  “net/http”
  “net/http/httptest”
  “time”
)
func main() {
  svr := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    time.Sleep(time.Hour)
  }))
  defer svr.Close()
  fmt.Println(“making request”)
  http.Get(svr.URL)
  fmt.Println(“finished request”)
}

```
 
## 解决方案

可以通过设置一个合理的timeout时间来规避这个问题， 例如：

```go
var netClient = &http.Client{
  Timeout: time.Second * 10,
}
response, _ := netClient.Get(url)
```

为每个请求设置一个timeout时间， 当API service执行超过timeout时间， ``Get()``就会返回如下错误：

```text
&httpError{
  err:     err.Error() + " (Client.Timeout exceeded while awaiting headers)",
  timeout: true,
}

```

如果你希望细粒度的控制请求的生命周期， 你可以实例化一个```net.Transport`` 和 ``net.Dialer``，
Transport用于底层的TCP 连接，而Dailer 用于管理简历连接。 Go的net包提供了默认的Transport
和Dialer实现， 示例如下：

```go

var netTransport = &http.Transport{
  Dial: (&net.Dialer{
    Timeout: 5 * time.Second,
  }).Dial,
  TLSHandshakeTimeout: 5 * time.Second,
}
var netClient = &http.Client{
  Timeout: time.Second * 10,
  Transport: netTransport,
}
response, _ := netClient.Get(url)

```

示例中展示了针对TCP连接，TCP建立连接和TLS握手的timeout设置，同时还可以设置keep-alive
的过期时间。

## 结论
Go的net和http包针对HTTP请求， 是经过深思熟虑设计的。但是默认timeout缺失，
加上http.Get(url)这样的便捷方法，极易导致使用者踩坑。