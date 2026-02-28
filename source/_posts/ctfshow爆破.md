---
title: ctfshow爆破
date: 2025-09-05 15:57:39
tags:
- CTF
- Web
- 爆破
- BurpSuite
categories:
- ctfshow-web入门wp
description: " "
---



# web21

**`Payload set` ----> `custom iterator`(自定义迭代器)    需要 base64 编码**

**ctfshow{91bd4cda-e6db-4b02-9c72-0b9ec12b06c6}**



<img src="\images\article_images\image-20250415135857606.png" alt="image-20250415135857606" style="zoom:33%;" />

<br>



<img src="\images\article_images\image-20250415135834992.png" alt="image-20250415135834992" style="zoom: 33%;" />

<br>



<img src="\images\article_images\image-20250415132207403.png" alt="image-20250415132207403" style="zoom:33%;" />

<br>



---

# web22

**子域名爆破**

<br>



---

# web23

**脚本寻找符合条件的值**

<img src="\images\article_images\image-20250415232134157.png" alt="image-20250415232134157" style="zoom:33%;" />

<br>

<img src="\images\article_images\image-20250415232153261.png" alt="image-20250415232153261" style="zoom:33%;" />

<br>



---

# web24

**php伪随机数漏洞    找到多次访问不变的随机数**

<img src="\images\article_images\image-20250415234223624.png" alt="image-20250415234223624" style="zoom: 50%;" />

<br>



<img src="\images\article_images\image-20250415234418611.png" alt="image-20250415234418611" style="zoom: 33%;" />

<br>



---

# web25

**逆推：**

**传`r=0`得到 -766445872(这个就是`-intval(mt_rand()`)**

**所以 `intval(mt_rand)=766445872`， 接下来使用kali反推出种子（会有几个种子，需要去试试是哪个），然后再根据种子用脚本得到的第一次的随机数给`r`,第二次和第三次的随机数的二者之和赋给cookie的token**

```php
<?php
error_reporting(0);
include("flag.php");
if(isset($_GET['r'])){
    $r = $_GET['r'];
    mt_srand(hexdec(substr(md5($flag), 0,8)));
    $rand = intval($r)-intval(mt_rand());//第一次调用
    if((!$rand)){
        if($_COOKIE['token']==(mt_rand()+mt_rand())){//第二次和第三次
            echo $flag;
        }
    }else{
        echo $rand;
    }
}else{
    highlight_file(__FILE__);
    echo system('cat /proc/version');
}
```

<img src="\images\article_images\image-20250416001946094.png" alt="image-20250416001946094" style="zoom:33%;" />

<br>

<img src="\images\article_images\image-20250416121106676.png" alt="image-20250416121106676" style="zoom:33%;" />

<br>

<img src="\images\article_images\image-20250416125247763.png" alt="image-20250416125247763" style="zoom:33%;" />

<br>

<img src="\images\article_images\image-20250416125329494.png" alt="image-20250416125329494" style="zoom:33%;" />

<br>

<img src="\images\article_images\image-20250416125112127.png" alt="image-20250416125112127" style="zoom: 33%;" />

<br>

---

# web26

**按提示填写后抓包，爆破密码**

**ctfshow{60fe9a09-2e71-4d50-9f97-2cbfe1e42310}**

<img src="\images\article_images\image-20250416132016529.png" alt="image-20250416132016529" style="zoom:33%;" />

<br>

<img src="\images\article_images\image-20250416131950853.png" alt="image-20250416131950853" style="zoom:33%;" />

<br>

---

# web27

**根据录取名单，爆破身份证号，注意日期格式 `yyyyMMdd` ，附加前缀和后缀**

<img src="\images\article_images\image-20250416133958163.png" alt="image-20250416133958163" style="zoom:90%;" />

<br>

<img src="\images\article_images\image-20250416133750995.png" alt="image-20250416133750995" style="zoom: 33%;" />

<br>

<img src="\images\article_images\image-20250416133717374.png" alt="image-20250416133717374" style="zoom:33%;" />

<br>

---

# web28

**爆破目录（注意要删去 `/2.txt`）**

<img src="\images\article_images\image-20250416135330728.png" alt="image-20250416135330728" style="zoom: 25%;" />

<br>

<img src="\images\article_images\image-20250416135304794.png" alt="image-20250416135304794" style="zoom:33%;" />

<br>

<img src="\images\article_images\image-20250416135250311.png" alt="image-20250416135250311" style="zoom:33%;" />

<br>
