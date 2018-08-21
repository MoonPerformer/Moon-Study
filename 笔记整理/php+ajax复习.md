# PHP+AJAX 复习

（2，5，6，,9, 10, 11, 12, 13)

### 1-表单提交

+   前端页面：
    +  action :     提交地址
    +  method :  提交方式 post  get  
    +  name：     必须有name属性，后台根据name属性取值
    +  enctype:   multipart/form-data   上传文件  


+   后面处理表单常用的超全局变量
    + $_GET
    + $_POST 
    + $_FILES
    + $_SESSION
    + $_COOKIE  可以获取cookie中数据 

### 2-HTTP协议 

1.请求 request 

```javascript
构成：
请求行： 请求方式  请求地址 协议版本 
请求头： 把浏览器相关信息发送给服务器
请求主体： 传递给服务器核心数据
```

2.响应 response 
```javascript
构成：
状态行： 协议版本 状态码
响应头： 把服务器的相关信息传递浏览器
响应主体： 把 核心数据传递给浏览器 
```


     常见状态码：
     200  成功
     302  页面重定向  header('location:./index.php');
     404  页面未找到
     500  服务器错误
 3.get请求和post请求的区别
```javascript
1- get请求会在url后面拼接数据  01.php?name=zs&age=18
 1-get 不安全
 2-大小会受到限制 4kb左右 
 3-get速度快 
 4- get 没有请求主体
2-post 
  1- 相对安全
  2- 对上传文件大小没有限制  上传文件 和 图片 
  3-post有请求主体   
  4-请求头设置  content-type: application/x-www-form-urlencoded;
```

​	http协议的请求方式： get  post   put  delete 

### 3-sql 语法

**增删改查**
+ 插入    
```sql
insert into 表名 （字段。。。） values (值， 值)
```

+ 删除    
```sql
delete from 表名  where  id = $id;
```

+ 修改     
```sql
update 表名  set 字段 = 值 where  id = $id ；
```

+ 查询    
```sql
select 字段列表 from  表名 where 条件  
```



```sql
and  or  !  与或非     
=  != 
id  in (3,5,9)  一对多匹配
模糊匹配
where name like '%王%';
求和
select count(*) as total  from 表名；
```

+ 表的截取(分页)
```sql
limit  起始索引， 截取长度 
起始索引 = (当前页面 - 1）* 每页数据条数
```
+ 排序   
```sql
order by  id  desc  默认升序， desc 降序 
```

+ 联合查询
```sql
select * from 表A  join  表B   on 联合条件（表A.外键 = 表B.主键） 
```

### 4 - PHP操作mySql

1. 连接数据库  mysqli_connect();

2. 准备 sql(外双内单)

    php中双引号可以解析变量 

   php中 单引号效率高！  双引号 

3. 执行sql  mysqli_query()  增删改  true  false  查询 结果集  false 

4. 显示最近的错误信息 mysqli_error();

5. 获取结果集的行数  mysqli_num_rows($res);

6. 读取结果集中数据 
   while ( row = mysqli_fetch_assoc($res) ) {

   ​	$data[] = row

   }

7. 关闭数据库连接  mysqli_close();



### 5 - cookie 和Session
+ cookie 浏览器端存储数据的容器
```;javascript
操作cookie的常用方法，及cookie的特点：
	$.cookie(k,v); 设置
	$.cookie(k)； 获取
	$.cookie(k, v , {expires: 1})  设置有效期   设置有效期为负数 就会自动删除

php 操作cookie:
	setcookie(k, v, 有效期)  设置cookie 
	$_COOKIE超全局变量 只能获取cookie 

cookie特点：
	1- cookie大小只有4kb
	2- cookie中的数据可以被同一个网站的多个页面共享
 	3- cookie中的数据会自动添加请求报文中，传递给服务器，供服务器使用 
 	4- 服务器可以设置响应报文，在响应报文中进行设置，浏览器获取响应报文后，会根据响应报文要求去向cookie中添加数据；
```



+ session  服务器端存储数据的容器

```javascript
 操作session的常用方法，及session的特点：
 	使用之前先开启 session_start();
	$_SESSION['name'] = 'zs';
	unset($_SESSION['name']);
	$_SESSION= []; 


	session_start做的三件事：
    1-会随机生成一个sessionID
    2-后台会创建一个session文件，文件名就是sessionID
    3-会将创建sessionID 添加给响应报文， 浏览器接受到响应报文后，会将sessionID存放cookie中
 
```



+ cookie 和 session 配合 实现登录状态保持 的过程
```javascript
1-用户登录成功时，记录用户登录标记
	session_start();
	$_SESSION['user_id'] = id;
2-用户下次登录个人中心
	1- 先判断用户是否携带sessionID 
    2- 判断sessionID是否和后台一致
    if (!empty($_COOKIE['PHPSESSID'])) {
  		//判断是否和后台对应
		if (empty($_SESSION['user_id'])) {
  			//去登录
		}
     } else  {
  		//去登录
	 }

```






### 6 - 原生Ajax(XMLhttpRequest对象)

```javascript
var  xhr = new XMLHttpRequest();
xhr.open('get', '01.php');
xhr.setRequestHeader('content-type', 'text/html');
xhr.send(null);
//监听服务器响应
xhr.onreadystatechange = function () {
  if (xhr.readyState == 4 && xhr.status == 200) {
     var   r = xhr.responseText;
  }
}
```

### 7 - $.ajax()

```javascript
//1-语法：
$.ajax({
  type: 'get',
  url: '0.php',
  dataType: 'json',  // text  xml jsonp 
  data: {name: 'zs'},
  timeout: 2000, //超时 
  beforeSend:function () {},
  success: function () {},
  error: function () {},
  complete: function () {}
});
 
//2-如何终止请求  return false;
```

### 8 - 模板引擎 (template-web.js)

```javascript
使用步骤：
    1- 引入插件
    2- 准备模板
    3- 准备数据（对象）
    4- 把数据和模板进行组合，生成结构
    
    注意： 模板的数据必须是 对象 
    	   在模板中直接使用对应属性名，不写对象名
语法：
     1 js代码 放在 {{ }}
	 2 遍历
     {{ each list v  i }}
	 {{ /each }}
     
     3 判断
     {{ if name == 'zs'}}
      
     {{ /if }}
     template(模板id， 对象);
```

### 9 - XMLHttpRequest2.0 

```javascript
//1-超时
xhr.timeout = 2000;
xhr.ontimeout = function (){ //超时处理事件}
//2-formData
//FormData对象 对表单数据进行管理
  var formData = new FormData(DOM表单);
  $.ajax({
    ....
  	data: formData,
    contentType:false,
    processData: false
  })
  
  表单序列化： $('form').serialize();   可以快速将表单name 和 value 属性拼接成字符串，无法获取上传文件 
//3-上传文件的进度
//所有的事件都有事件对象，事件对象中会包含该事件相关的信息 
  xhr.upload.onprogress = function (e) {
  	//进度 = 已经上传大小/总大小
	var value = e.loaded/e.total;
  }
```


### 10 - JSONP的原理
```javascript
1- $.ajax()   
dataType: 'json'
	$.ajax方法中 使用 XMLHttpRequest对象请求的服务器， dataType为json 返回的数据 通过JSON.parse()进行解析；
    
dataType:'jsonp' 解决跨域问题：
	1- 在跨域情况下，XMLHttpRequest不能发送请求，
    2- 当将dataType:'jsonp' ，$.ajax() 内部使用script的src属性  跨域请求服务器
    
    //实现细节需要前端配合 
    //jsonp  json with padding  用json数据进行填充
    // function say(obj) { console.log(obj)}
    <script src="01.php?callback=say"></script>
    
    //后台
	// echo $_GET['callback'].'({"name":"zs", "age":18})';              
    // echo say({"name":"zs", "age":18});
    1-前端先定义好函数， 通过script的src属性将 函数名传递给后台
    2-后台获取传递函数名， 在函数名后面拼接括号，在括号中填充json字符串，填充完成后，进行输出
    3-前端获取到 后台传递方法调用，会立即执行，就可以获取后台填充的参数 
    
    //注意： jsonp需要前端配合  jsonp只能以get方式发送请求   
	  
	

```

### 11- 浏览器向服务器发送请求的过程
```javascript
1- 在浏览器中输入网址，会先通过DNS将域名解析成IP,通过IP去查找服务器
2- 如果请求服务器的是静态资源（js，html, css, 图片）， 服务器会直接原样返回，交给浏览器解析渲染
3- 如果请求的是php程序， 在服务器会先执行php标签中php代码，php代码执行完成后， 会将php的执行结果和非php代码 一起返回给浏览器，由浏览器进行解析
```
### 12 -常用的各种插件
```javascript
	1-分页：jquery.pagination.js 
	2-时间格式化： moment.js
    	moment(时间).format('YYYY-MM-DD HH:mm:ss');
	3-富文本编辑器： wangEditor.js
```
### 13 - 其他知识
```javascript
	1-当用户输入的事件：
    	box.oninput = function () {}
	2-控制文件上传的类型：
    	accpet = "image/png, image/gif";
	3-图片本地预览：
    	input.onchange = function () {
        	//获取选中的文件
			var file = this.files[0]
            //获取文件url
            var url = URL.createObjectURL(file);
            //赋值给img的src
            img.src = url;
        }
	4-表单重置： reset();
    	$('form')[0].reset();
    5-常见的jq表单状态选择器（可用，禁用，被选中）：
    	//被选中
    	$('input:checked') 
        $('select option:selected')
        // 
		$('input:disabled')  禁用
        $('input:enabled') 可用 
    6-jq对象和DOM对象的区别：
        //$('div') -->结果 获取了页面中所有div --> [div, div, div, div ];
    	//$('form').reset();
       $('div')[2]  将jq对象转成DOM对象 
    7-事件委托的使用场景：
    	对应动态生成元素用事件委托 绑定事件 
        $('已经存在的父盒子').on('事件类型', '触发事件子元素', function () {})
 
```
### 14- 增删改查的思路

```javascript
    1-添加数据思路：
		1- 先写好后台接口
		2- 前端请求接口
		3- 请求完成后重新渲染页面

		1- 设计表单
		2- 获取数据： $('form').serialize();   var formData=new FormData();
		3- 通过ajax把数据发送给后台
		4- 后台获取数据，存到数据中
		5- 添加完成，重新渲染页面  (渲染使用模板引擎)

	2- 删除数据思路：
		1- 获取对应数据id
		2- 通过ajax把id传给后台
		3- 后台根据id进行删除
		4- 删除成功后，重新渲染当前页

	3- 更新数据思路：
		1- 获取对应数据id
		2- 通过ajax把id传给后台
		3- 后台根据id返回对应的数据
		4- 把返回的数据渲染在页面中，供修改
		5- 修改完成，把数据根据id更新回数据库
		6- 修改成功后，重新渲染当前页

	4- 查找数据思路：
		1- 根据查找条件，向后台发送ajax请求
		2- 后台根据条件返回对应的数据
		3- 前端根据返回数据格式解析数据 xml  json
		4- 准备模板引擎，把数据渲染到页面中
        
    5-分页的思路实现过程
    	1- 获取后台数据中数据总数
        2- 根据总数生成分页标签
    
    6-实际工作中前后端分工：
    	后台 给前端提供数据和接口 （对数据进行增删改查操作）
        前端 对数据进行渲染
    	
```



