---
title: 自建随机图片API
date: 2023-03-20 20:17:14.278
updated: 2023-03-21 20:15:08.089
url: /archives/zi-jian-sui-ji-tu-pian-api
categories: 
- 编译与配置
tags: 
- php
---

# 新建站点

![image-20230320201905510](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202303202019583.png)

填写好域名，并选择`php`版本

# 编写`php`

`api.php`文件如下：

```php
<?php
// 默认是一个404图片
$rst = "https://一张默认的404图片.png";
if(isset($_GET['type'])){
	$type = htmlspecialchars($_GET['type']);
	$file_path = "data.json";
	if (file_exists($file_path)) {
		$file_data = file_get_contents($file_path);
		if($file_data != null){
			$data = json_decode($file_data, true);
			if ($data != null) {
				if (isset($data[$type])) {
					$num = count($data[$type]);
					$index = mt_rand(0, $num-1);
					$rst = $data[$type][$index];
				}
			}
		}	
	}
}
header("Location:".$rst);
exit();
?>
```

`data.json`文件格式如下：

```json
{
    "类别1": ["https://图片1.jpg", "https://图片2.png"],
    "类别2": []
}
```

# 上传

&emsp;将上述两个文件上传到网站目录

![image-20230320202218401](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202303202022459.png)

# 访问

`https://你的域名/api.php?type=类别`

# 测试

&emsp;比如下面这张图片就是随机`api`的图片

![随机图片](https://image.api.lmzyoyo.top/api.php?type=erciyuan)