reGeorgX
=========

https://github.com/L-codes/Neo-reGeorg

**reGeorgX** 是一个旨在积极重构 [reGeorg](https://github.com/sensepost/reGeorg) 的项目，目的是：

* 提高 tunnel 连接安全性
* 提高可用性，避免特征检测
* 提高传输内容保密性
* 解决 reGeorg 现存在的问题，修复部分小BUG



## Features

* 传输内容经过变形base64加密，伪装成base64编码
* GET 请求响应可定制化 (如伪装的404页面)
* HTTP Headers 的指令随机生成，避免特征检测
* HTTP Headers 可定制化
* 兼容python2 / python3



Version
----

1.0.0



Dependencies
-----------

* [**requests**] - https://github.com/kennethreitz/requests




Basic Usage
--------------

* **Step 1.**
设置密码生成 tunnel.(aspx|ashx|jsp|php) 并上传到WEB服务器
```ruby
$ python reGeorgX.py generate -k password

    [+] Create reGeorgX server files:
       => reGeorgX_server/tunnel.nosocket.php
       => reGeorgX_server/tunnel.js
       => reGeorgX_server/tunnel.php
       => reGeorgX_server/tunnel.ashx
       => reGeorgX_server/tunnel.aspx
       => reGeorgX_server/tunnel.tomcat.5.jsp
       => reGeorgX_server/tunnel.jsp

```

* **Step 2.**
使用 reGeorgX.py 连接WEB服务器，在本地建立 socks 代理
```ruby
$ python3 reGeorgX.py -k password -u http://xx/tunnel.php
+------------------------------------------------------------------------+
  Log Level set to [ERROR]
  Starting socks server [127.0.0.1:1080], tunnel at [http://k/tunnel.php]
+------------------------------------------------------------------------+
```

   注意，如果你的工具，如NMap不支持socks代理，请使用 [proxychains](https://github.com/rofl0r/proxychains-ng) 




Advanced Usage
--------------

1. 支持生成服务端，默认 GET 请求响应指定的页面内容 (如伪装的404页面)
```ruby
$ python reGeorgX.py generate -k <you_password> --file 404.html
$ pytohn reGeorgX.py -k <you_password> -u <server_url> --skip
```

2. 如服务端WEB，需要设置代理才能访问
```ruby
$ pytohn reGeorgX.py -k <you_password> -u <server_url> --proxy socks5://10.1.1.1:8080
```

3. 如需Authorization认证，或者定制的Header或Cookie
```ruby
$ pytohn reGeorgX.py -k <you_password> -u <server_url> -H 'Authorization: cm9vdDppcyB0d2VsdmU=' --cookie "key=value"
```

* 更多关于性能和稳定性的参数设置参考 -h 帮助信息
```ruby
# 生成服务端脚本
$ python reGeorgX.py generate -h
    usage: reGeorgX.py [-h] -k KEY [-o DIR] [-f FILE] [--read-buff Bytes]

    Generate reGeorgX webshell

    optional arguments:
      -h, --help            show this help message and exit
      -k KEY, --key KEY     Specify connection key.
      -o DIR, --outdir DIR  Output directory.
      -f FILE, --file FILE  Camouflage html page file
      --read-buff Bytes     Remote read buffer.(default: 513)

# 连接服务端
$ python reGeorgX.py -h
    usage: reGeorgX.py [-h] -u URI -k KEY [-l IP] [-p PORT] [-s] [-H LINE] [-c LINE]
                     [-x LINE] [--read-buff Bytes] [--read-interval MS]
                     [--max-threads N] [-v]

    Socks server for reGeorgX HTTP(s) tunneller

    optional arguments:
      -h, --help            show this help message and exit
      -u URI, --url URI     The url containing the tunnel script
      -k KEY, --key KEY     Specify connection key
      -l IP, --listen-on IP
                            The default listening address.(default: 127.0.0.1)
      -p PORT, --listen-port PORT
                            The default listening port.(default: 1080)
      -s, --skip            Skip usability testing
      -H LINE, --header LINE
                            Pass custom header LINE to server
      -c LINE, --cookie LINE
                            Custom cookies to server
      -x LINE, --proxy LINE
                            proto://host[:port] Use proxy on given port
      --read-buff Bytes     Local read buffer, max data to be sent per
                            POST.(default: 1024)
      --read-interval MS    Read data interval in milliseconds.(default: 100)
      --max-threads N       Proxy max threads.(default: 1000)
      -v                    Increase verbosity level (use -vv or more for greater
                            effect)
```



## TODO

 * 解决 tennel.js 无法持续 TCP 连接问题

 * HTTP body 隐写

 * 传输 Target 隐写

 * 混淆/免杀/压缩 server 端

 * Single session mode ([@imbeee](https://www.imbeee.com/2016/01/06/reGeorgSocksProxy-with-single-session-mode/))

   指定Cookie：
   
   ```python
       def cookiesFilter(self,cookie):
           newcookies = []
           if cookie:
               for x in cookie.split(','):
                   if ';' in x:
                       match = re.findall('((.+?)=(.+?));(\sdomain=(.+);)?',x.strip())
                       if match:
                           if match[0][4] == self.httpHost or match[0][4] == '':
                               self.cookieDic[match[0][1]] = match[0][2]
                   else:
                       self.cookieDic[x.split('=')[0]] = x.split('=')[1]
           for k in self.cookieDic.keys():
               newcookies.append(k + '=' + self.cookieDic[k])
           return '; '.join(newcookies)
   
       def reqRand(self):
           return ''.join(random.sample('zyxwvutsrqponmlkjihgfedcba',10))
   ```

- Custom headers.

- Optional buffer size, the transfer speed many times faster.

- Optional url check.

- Proxy headers payloads can be selected between URL parameters and Request headers.

  [https://github.com/gorgiaxx/reGeorg](https://github.com/gorgiaxx/reGeorg)

## License

GPL 3.0
