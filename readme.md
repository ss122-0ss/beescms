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

![image-20240325184643765](https://github.com/ss122-0ss/beescms/assets/131983607/6466b9bc-c84b-4405-ad14-0b63a67e3610)


复现过程：

位置：后台模板页管理中的模板修改

![图片](https://github.com/ss122-0ss/beescms/assets/131983607/86483bb1-59ba-4954-967b-41753badf5f3)


点击修改，并且拦截数据包
![图片](https://github.com/ss122-0ss/beescms/assets/131983607/c9b92ce8-a045-469f-b695-9a35e73f485b)

![图片](https://github.com/ss122-0ss/beescms/assets/131983607/44961e76-0e0c-401d-8a30-0bb5ea2830a2)


将上述：模板内容修改成php代码，file——name修改成该网站自带php文件位置，如下图：

![图片](https://github.com/ss122-0ss/beescms/assets/131983607/fe100213-dab4-449b-ba3b-ba672c631dc6)


访问index.php

![图片](https://github.com/ss122-0ss/beescms/assets/131983607/8193120b-894c-4930-885c-a16fadf17fdf)
