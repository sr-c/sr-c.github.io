---
title: testpasswd
date: 2018-04-30 15:43:33
tags: blog
---
测试密码文章(＾Ｕ＾)ノ~ＹＯ

还要再做一些工作！
<!-- more -->
尝试在文本中加入
retry

小把戏😘

<script> 
	if("un233"==prompt("请输入密码")){
	alert("正确"); 
	} else {
	alert("错误"); 
	location="https://sr-c.github.io"; 
	} 
</script> 

或者使用
```bash
<script>
	(function(){
		if('{{ page.password }}'){
			if (prompt('请输入文章密码','') !== '{{ page.password }}'){
				alert('密码错误！');
				history.back();
			}
		}
	})();
</script>
```
现作者[博客](https://blog.paddings.cn/2016/12/01/blog/hexo-password/)网页源码中的代码
```bash
<script>
	(function(){
		if('password'){
			if(sessionStorage.getItem(location.pathname) !== 'password'){
				var password = prompt('请输入文章密码');
				if (password === 'password'){
					sessionStorage.setItem(location.pathname, password);
				}else{
					alert('密码错误！');
					history.back();
				}
			}
		}
	})();
</script>
```

参考链接：
https://www.jianshu.com/p/a2330937de6c
https://www.jianshu.com/p/5479bf2d57a0