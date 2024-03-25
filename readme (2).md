# BEESCMS任意文件写入

 代码漏洞位于admin_template.php的54行

对文件是否存在进行了验证，但未对文件进行路径的隔离和后缀的校验导致了任意文件写入漏洞

```php
elseif($action=='save_template'){
	if(!check_purview('tpl_manage')){msg('<span style="color:red">操作失败,你的权限不足!</span>');}
	$template = $_POST['template'];
	$file = $_POST['file'];
	$template=stripslashes($template);
	$path=CMS_PATH.$file;
	//判断文件是否存在
	if(!file_exists($path)){msg('不存在该文件，请重新操作');}
	if(!$fp=@fopen($path,'w+')){err('<span style="color:red">模板打开失败,请确定【'.$file.'】模板是否存在</span>');}
	flock($fp,LOCK_EX);
	fwrite($fp,$template);
	flock($fp,LOCK_UN);
	fclose($fp);
	msg('【'.$file.'】模板修改完成','?nav='.$admin_nav.'&admin_p_nav='.$admin_p_nav);
}
```

![image-20240325184643765](C:\Users\28162\AppData\Roaming\Typora\typora-user-images\image-20240325184643765.png)

复现过程：

位置：后台模板页管理中的模板修改

![image-20240325184031122](C:\Users\28162\AppData\Roaming\Typora\typora-user-images\image-20240325184031122.png)

点击修改，并且拦截数据包

![image-20240325184124630](C:\Users\28162\AppData\Roaming\Typora\typora-user-images\image-20240325184124630.png)

![image-20240325184227271](C:\Users\28162\AppData\Roaming\Typora\typora-user-images\image-20240325184227271.png)

将上述：模板内容修改成php代码，file——name修改成该网站自带php文件位置，如下图：

![image-20240325184513590](C:\Users\28162\AppData\Roaming\Typora\typora-user-images\image-20240325184513590.png)

访问index.php

![image-20240325184546620](C:\Users\28162\AppData\Roaming\Typora\typora-user-images\image-20240325184546620.png)