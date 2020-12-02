---
title: OverTheWire —— 一个 Linux 真娱乐网站
date: 2020-12-2
categories: [Linux]
tags: [linux]
---



一个类似 CTF 的练习 Linux 命令的网站

[overthewire.org](https://overthewire.org/wargames)

网站说不让剧透，包括写题解。我接下来说的东西我也不知道我在说什么。

对不上，对不上。

# Level 8 -> Level 9

## 目标

下一关的密码存放在 `data.txt` 文件中，并且是不重复的唯一一行。

## 解决这一关也许需要的命令

`grep`, `sort`, `uniq`, `strings`, `base64`, `tr`, `tar`, `gzip`, `bzip2`, `xxd`

## 有帮助的阅读资料

[Piping and Redirection](https://ryanstutorials.net/linuxtutorial/piping.php)

## 我不知道我在说什么

这一关需要用到管道，将文件内容排序后去重即可

```bash
$ cat data.txt | sort | uniq -u
```

其中 `uniq -u` 输出唯一的不重复的一行。

得到 flag 

```
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```



# Level 9 -> Level 10

## 目标

下一关的密码存储在文件 `data.txt` 中，是为数不多的可读字符串之一，前面有几个'='字符。

## 解决这一关也许需要的命令

`grep`, `sort`, `uniq`, `strings`, `base64`, `tr`, `tar`, `gzip`, `bzip2`, `xxd`

## 我不知道我在说什么

这一关用 `strings` 命令，可将能阅读的字符串输出至标准输出。

```bash
$ strings data.txt
```

之后很容易找到特征。

```
...
========== the*2i"4
...
========== password
...
Z)========== is
...
&========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
...
```

所以，毫无疑问，答案就是这一串了。

```
truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```



# Level 10 -> Level 11

## 目标

下一关的密码存放在 `data.txt` 中，并且用 `base64` 方式加密。

## 解决这一关也许需要的命令

`grep`, `sort`, `uniq`, `strings`, `base64`, `tr`, `tar`, `gzip`, `bzip2`, `xxd`

## 有帮助的阅读资料

[Base64 on Wikipedia](https://en.wikipedia.org/wiki/Base64)

## 我不知道我在说什么

这关只要解密 `base64` 即可

```bash
$ base64 -d data.txt
```

用 `-d` 参数进行解密，否则默认是加密。

输出

```
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

显而易见，密码是

```
IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

# Level 11 -> Level 12

## 目标

下一关的密码存储在文件data.txt中，其中所有小写(a-z)和大写(A-Z)字母已经旋转了13个位置。

## 解决这一关也许需要的命令

`grep`, `sort`, `uniq`, `strings`, `base64`, `tr`, `tar`, `gzip`, `bzip2`, `xxd`

## 有帮助的阅读资料

[Rot13 on Wikipedia](https://en.wikipedia.org/wiki/Rot13)

## 我不知道我在说什么

虽然这个题其实写个 `C` 或者用 `Python` 可能更方便那么一点点。。。

这个旋转13个位置实际上是一种简单的加密方法，指将前13个字母和后13个字母调换。

![](https://en.wikipedia.org/wiki/File:ROT13_table_with_example.svg)

WikiPedia 甚至给出了解法，在 Linux 可以使用 `tr` 命令。

在此题中，将字母互相 map 出对应关系即可。

```bash
# Map upper case A-Z to N-ZA-M and lower case a-z to n-za-m
$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

输出 

```bash
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

By the way，Wikipedia 中也给出了 Vim/Emacs 和 Python 的示例代码，将 Python 代码一并贴出

```python
import codecs
a = "Gur cnffjbeq vf 5Gr8L4qetPEsPk8htqjhRK8XSP6x2RHh"
print(codecs.decode(a, 'rot13'))
```

（我是 API Caller 我自豪）

Or

```c
#include <stdio.h>

char* decodeRot13(const char* ori, char* ans)
{
    int i = 0;
    while(ori[i] != '\0')
    {
        if(ori[i] >= 65 && ori[i] <= 77) // if character between A-M 
            // map to N-Z
            ans[i] = ori[i] + 13;
        else if(ori[i] >= 78 && ori[i] <= 90) //if between N-Z
            // map to A-M
            ans[i] = ori[i] - 13;
        else if(ori[i] >= 97 && ori[i] <= 109) // if character between a-m 
            // map to n-z
            ans[i] = ori[i] + 13;
        else if(ori[i] >= 110 && ori[i] <= 122) //if between n-z
            // map to a-m
            ans[i] = ori[i] - 13;
        else
        {
            ans[i] = ori[i];
        }
        
        
        i++;
        
    }
    ans[i] = '\0';
    return ans;
}

int main()
{
    char ori[] = "Gur cnffjbeq vf 5Gr8L4qetPEsPk8htqjhRK8XSP6x2RHh";
    char ans[100];
    decodeRot13(ori,ans);

    printf("%s\n",ans);
    return 0;
}
```

其实我不当 API Caller 也是可以的（

Anyway，密码是

```
5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```



# Level 12 -> Level 13

## 目标

下一关的密码存储在文件data.txt中，这是一个经过反复压缩的文件的十六进制转储。对于这一关，在/tmp下创建一个目录可能是有用的，你可以使用`mkdir`在其中工作。例如： `mkdir /tmp/myname123` 。然后用`cp`复制数据文件，用`mv`重命名它（请阅读手册！）

## 解决这一关也许需要的命令

`grep`, `sort`, `uniq`, `strings`, `base64`, `tr`, `tar`, `gzip`, `bzip2`, `xxd`, `mkdir`, `cp`, `mv`, `file`

## 有帮助的阅读资料

[Hex dump on Wikipedia](https://en.wikipedia.org/wiki/Hex_dump)

## 我不知道我在说什么

按照说明，我们需要创建一个文件夹，Banner 中已经给出，用`mktemp -d`

```bash
$ mktemp -d
/tmp/tmp.0CC45KW14Z
```

首先给出的是一个 Hexdump 文本，用 `xxd` 命令将其重新打包为二进制文件

```sh
$ xxd -r < data.txt > /tmp/tmp.0CC45KW14Z/output
```

然后我们看一下文件类型

```bash
$ cd /tmp/tmp.0CC45KW14Z
$ file output
```

显示为 `gzip` 压缩后的文件

```
output: gzip compressed data, was "data2.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
```

再解压看看

```bash
$ mv output data2.bin.gz
$ gunzip data2.bin
```

还就那个套娃

```bash
$ file data2.bin 
data2.bin: bzip2 compressed data, block size = 900k
$ mv data2.bin data2.bin.bz2
$ bunzip2 data2.bin.bz2
$ file data2.bin 
data2.bin: bzip2 compressed data, block size = 900k
$ mv data2.bin data2.bin.gz
$ gunzip data2.bin.gz
$ file data2.bin 
data2.bin: POSIX tar archive (GNU)
$ mv data2.bin data2.tar
$ tar -xvf data2.tar
$ file data5.bin 
data5.bin: POSIX tar archive (GNU)
$ tar -xvf data5.tar
$ file data6.bin 
data6.bin: bzip2 compressed data, block size = 900k
$ mv data6.bin data6.bin.bz2
$ bunzip2 data6.bin.bz2
$ file data6.bin 
data6.bin: POSIX tar archive (GNU)
$ mv data6.bin data6.tar
$ tar -xvf data6.tar
$ file data8.bin 
data8.bin: gzip compressed data, was "data9.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
$ mv data8.bin data8.bin.gz
$ gunzip data8.bin.gz
$ file data8.bin 
data8.bin: ASCII text
```

WOW! Finally!

```sh
$ cat data8.bin
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```

单纯为这道题其实可以用脚本搞定，不过我懒，就不写了。

所以密码就是

```
8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```

# Level 13 -> Level 14

## 目标

下一层的密码存储在`/etc/bandit_pass/bandit14`中，只有用户`bandit14`才能读取。对于这一层，你不会得到下一层的密码，但你会得到一个私有的 SSH 密钥，可以用来登录下一层。注意：localhost是指你正在工作的机器的主机名。

## 解决这一关也许需要的命令

`ssh`, `telnet`, `nc`, `openssl`, `s_client`, `nmap`

## 有帮助的阅读资料

[SSH/OpenSSH/Keys](https://help.ubuntu.com/community/SSH/OpenSSH/Keys)

## 我不知道我在说什么

总算来点不一样的东西了！

他直接把私钥给我们了。直接登录好了

把给的私钥复制到本机，然后登录

```bash
$ vim sshkey.private
$ chmod 600 sshkey.private
$ ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

登进来直接 `cat` 就好了

拿到 flag

```
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
```

# Level 14 -> Level 15

## 目标

通过向localhost上的30000端口提交当前级别的密码，可以找回下一级别的密码。

## 解决这一关也许需要的命令

`ssh`, `telnet`, `nc`, `openssl`, `s_client`, `nmap`

## 有帮助的阅读资料

- [How the Internet works in 5 minutes (YouTube)](https://www.youtube.com/watch?v=7_LPdttKXPc) (Not completely accurate, but good enough for beginners)
- [IP Addresses](http://computer.howstuffworks.com/web-server5.htm)
- [IP Address on Wikipedia](https://en.wikipedia.org/wiki/IP_address)
- [Localhost on Wikipedia](https://en.wikipedia.org/wiki/Localhost)
- [Ports](http://computer.howstuffworks.com/web-server8.htm)
- [Port (computer networking) on Wikipedia](https://en.wikipedia.org/wiki/Port_(computer_networking))

## 我不知道我在说什么

说到底，这一关要向上发一个数据包

单纯掌握 `nc` 命令罢了，对 CTF 但凡有一点点了解应该也知道这个

```sh
$ echo 4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e | nc localhost 30000
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

就这么简单，flag为

```
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

# Level 15 -> Level 16

## 目标

通过SSL加密向localhost上的30001端口提交当前级别的密码，可以找回下一级别的密码。

帮助说明：获得 `HEARTBEATING`和 `Read R BLOCK`？使用`-ign_eof`并阅读`man` 手册中的 `CONNECTED COMMANDS`部分。在 `R `和 `Q `旁边，`B `命令在这个版本的命令中也可以使用......

## 解决这一关也许需要的命令

`ssh`, `telnet`, `nc`, `openssl`, `s_client`, `nmap`

## 有帮助的阅读资料

- [Secure Socket Layer/Transport Layer Security on Wikipedia](https://en.wikipedia.org/wiki/Secure_Socket_Layer)
- [OpenSSL Cookbook - Testing with OpenSSL](https://www.feistyduck.com/library/openssl-cookbook/online/ch-testing-with-openssl.html)

## 我不知道我在说什么

其实提示已经给的太多了。

要用`openssl` 连接远端

```sh
$ echo BfMYroe26WYalil77FoDi9qh59eK5xNr | openssl s_client -connect localhost:30001 -ign_eof
```

`-ign_eof` 指当远端发送 `EOF` 信号后自动断开连接

忽略一大堆输出之后，给了我们密码

```
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd

closed
```

所以密码就是

```
cluFn7wTiGryunymYOu4RcffSxQluehd
```

# Level 16 -> Level 17

## 目标

通过向 `localhost` 上 31000 到 32000 范围内的端口提交当前级别的密码，就可以获取下一级别的凭证。首先找出这些端口中哪些有服务器在监听。然后找出其中哪些使用SSL，哪些不使用。只有 1 台服务器会给出下一个凭证，其他服务器只会把你发给它的任何东西回传给你。

## 解决这一关也许需要的命令

`ssh`, `telnet`, `nc`, `openssl`, `s_client`, `nmap`

## 有帮助的阅读资料

- [Port scanner on Wikipedia](https://en.wikipedia.org/wiki/Port_scanner)

## 我不知道我在说什么

毫无疑问，这关要扫描端口了。终于该 `nmap` 出场了

```sh
$ nmap -sV -p31000-32000 localhost

Starting Nmap 7.40 ( https://nmap.org ) at 2020-12-02 16:11 CET
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00032s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.40%T=SSL%I=7%D=12/2%Time=5FC7AEBB%P=x86_64-pc-linux-g
SF:nu%r(GenericLines,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cu
SF:rrent\x20password\n")%r(GetRequest,31,"Wrong!\x20Please\x20enter\x20the
SF:\x20correct\x20current\x20password\n")%r(HTTPOptions,31,"Wrong!\x20Plea
SF:se\x20enter\x20the\x20correct\x20current\x20password\n")%r(RTSPRequest,
SF:31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\
SF:n")%r(Help,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x
SF:20password\n")%r(SSLSessionReq,31,"Wrong!\x20Please\x20enter\x20the\x20
SF:correct\x20current\x20password\n")%r(TLSSessionReq,31,"Wrong!\x20Please
SF:\x20enter\x20the\x20correct\x20current\x20password\n")%r(Kerberos,31,"W
SF:rong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\n")%r
SF:(FourOhFourRequest,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20c
SF:urrent\x20password\n")%r(LPDString,31,"Wrong!\x20Please\x20enter\x20the
SF:\x20correct\x20current\x20password\n")%r(LDAPSearchReq,31,"Wrong!\x20Pl
SF:ease\x20enter\x20the\x20correct\x20current\x20password\n")%r(SIPOptions
SF:,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password
SF:\n");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 90.07 seconds

```

扫了好一会才结束

这样可以看出只有 31790 端口比较可疑

直接连接

```sh
$ echo cluFn7wTiGryunymYOu4RcffSxQluehd | openssl s_client -connect localhost:31790 -ign_eof
```



```
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

closed

```

拿到了一个 SSH 私钥

嘛，也就相当于我们拿到密码了吧。

贴下面

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

# Level 17 -> Level 18

## 目标

家目录中有2个文件：passwords.old和passwords.new。下一级的密码在 passwords.new 中，是 passwords.old 和 passwords.new 之间唯一被修改的一行。

注意：如果你解决了这一关，在尝试登录 bandit18 时看到'Byebye！'，这与下一关 bandit19 有关。

## 解决这一关也许需要的命令

`cat`, `grep`, `ls`, `diff`

## 我不知道我在说什么

一个 `diff` 解决战斗

```sh
$ diff passwords.new passwords.old 
42c42
< kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
---
> w0Yfolrc5bwjS4qw5mq1nnQi6mF03bii
```

得到 Key 为

```
kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
```

# Level 18 -> Level 19

## 目标

下一级的密码存储在家目录里的readme文件中。不幸的是，有人修改了`.bashrc`，当你用 SSH 登录时，会将你注销。

## 解决这一关也许需要的命令

`ssh`, `ls`, `cat`

## 我不知道我在说什么

SSH连接的时候是可以带上命令的，所以就很简单了

```sh
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password: 
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

得到 Key 为

```
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

# Level 19 -> Level 20

## 目标

为了获得下一级别的访问权限，你应该使用家目录中的`setuid`二进制文件。在没有参数的情况下执行它以了解如何使用它。在使用了`setuid`二进制文件之后，可以在通常的地方 (/etc/bandit_pass) 找到这一层的密码。

## 有帮助的阅读资料

[setuid on Wikipedia](https://en.wikipedia.org/wiki/Setuid)

## 我不知道我在说什么

我们先执行一下

```sh
$ ./bandit20-do 
Run a command as another user.
  Example: ./bandit20-do id
```

很有意思，可以以其他用户身份执行命令

这里有一个知识点，`setuid`

`setuid` 和 `setgid` 是一种特殊权限，可以使用拥有者的权限去执行。

因此，`./bandit20-do id` 输出

```sh
./bandit20-do id
uid=11019(bandit19) gid=11019(bandit19) euid=11020(bandit20) groups=11019(bandit19)
```

因此可以轻松得到密码

```sh
$ ./bandit20-do cat /etc/bandit_pass/bandit20 
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```

密码为

```
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```



