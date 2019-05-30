# 真保蟹管理系统前端文档

## 1.开始使用

&emsp;当前版本：`iframe v3.1.1`，&emsp;更新于2019/03/24。

### 1.1.项目结构

```
|-assets
|     |-css               // 样式
|     |-images            // 图片
|     |-js                // javascript
|         |-common.js     // 公共js，每个页面都需要引入
|     |-libs              // 第三方库，echarts(图表)、layui(ui框架)
|     |-module            // layui模块，版本更新只用替换此目录
|         |-theme             // 主题资源
|         |-admin.css         // admin样式
|         |-admin.js          // admin模块
|         |-index.js          // index模块
|         |-contextMenu.js    // 鼠标右键模块
|         |-printer.js        // 打印模块
|         |-xxxxxxxx          // 其他模块
|-page              // html页面
|-json              // 模拟数据
|-index.html        // 主页面
```


### 1.2.导入项目

1. 在`我的订单`中下载项目源码；
2. 使用IDEA、WebStorm、HBuilder等工具打开；
3. 运行index.html启动：

![运行教程](http://zhenbaoxie.profacty.cn/data/20190530/1559186000681919286483.jpg)

> 注意：使用`localhost`的形式访问，而不是使用`file://`的形式访问。


### 1.3.添加一个菜单

1. 打开`index.html`，找到侧导航栏，添加一个菜单：
    ```html
   <dd><a lay-href="xxx.html">XX管理</a></dd>
    ```

1. 新建一个`xxx.html`页面；
3. 运行项目，查看效果。

> 注意：a标签里面是 `lay-href`而不是`href`，lay-href点击会打开一个标签页。


### 1.4.index.html结构说明

```html
<html>
<head>
    <link rel="stylesheet" href="assets/libs/layui/css/layui.css"/>
    <link rel="stylesheet" href="assets/module/admin.css"/>
</head>
<body class="layui-layout-body">
    <div class="layui-layout layui-layout-admin">
        <!-- 头部 -->
        <div class="layui-header">...</div>
        <!-- 侧边栏 -->
        <div class="layui-side">...</div>
        <!-- 主体部分 -->
        <div class="layui-body">...</div>
        <!-- 底部 -->
        <div class="layui-footer">...</div>
    </div>
    
    <script type="text/javascript" src="assets/libs/layui/layui.js"></script>
    <script type="text/javascript" src="assets/js/common.js"></script>
    <script>
        layui.use(['index'], function () {
            var index = layui.index;
             // 加载主页
            index.loadHome({
                menuPath: 'page/console/console.html',
                menuName: '<i class="layui-icon layui-icon-home"></i>'
            });
        });
    </script>
</body>
</html>
```

> index.html结构就是固定这样写法，在整合到后台时，你可以把头部、侧边栏抽离出来，再include进去。


### 1.5.common.js说明

```javascript
layui.config({
    base: getProjectUrl() + 'assets/module/'  // 配置layui扩展模块目录
}).extend({
    notice: 'notice/notice',
    step: 'step-lay/step'
}).use(['admin'], function () {
    var admin = layui.admin;
    // 移除loading动画
    setTimeout(function () {
        admin.removeLoading();
    }, window == top ? 600 : 100);
});

// 获取项目根路径
function getProjectUrl() {
    return '...省略';
}
```

- layui.config是告诉layui扩展模块所在目录；
- layui.extend是配置每个模块具体js位置；
- admin.removeLoading()是移除页面加载动画；

> 像`admin.js`、`index.js`这些没有用子目录存放的模块就不需要写layui.extend，
> 延时移除加载动画是给加载上次切换的主题预留时间，因为切换主题也需要一定时间。

&emsp;每个页面都需要引入common.js，不引入直接使用扩展模块layui会无法正确加载模块，admin模块负责加载主题，
引入common.js主题才会跟随记忆，因为common.js里面加载了admin模块。


---

## 2.index模块

使用方法：
```javascript
layui.use(['index'], function () {
    var index = layui.index;
});
```

### 2.1.选项卡配置

配置名 | 默认 | 说明
:---|:--- | :---
pageTabs | true | 是否开启多标签
cacheTab | true | 是否记忆打开的选项卡
openTabCtxMenu | true | 是否开启Tab右键菜单
maxTabNum | 20 | 最多打开多少个tab

修改默认配置直接打开index.js修改这几个参数即可：

```javascript
var index = {
    pageTabs: true,  // 是否开启多标签
    cacheTab: true,  // 是否记忆打开的选项卡
    openTabCtxMenu: true,  // 是否开启Tab右键菜单
    maxTabNum: 20  // 最多打开多少个tab
    // ... 省略
}
```

> 在设置界面修改了配置会高于index中的默认配置。


### 2.2.loadHome加载主页

```javascript
layui.use(['index'], function () {
    var index = layui.index;
    
    index.loadHome({
        menuPath: 'page/console/console.html',
        menuName: '<i class="layui-icon layui-icon-home"></i>'
    });
});
```

- menuPath：&emsp;必填&emsp;主页路径
- menuName：&emsp;必填&emsp;Tab标题
- loadSetting：&emsp;选填&emsp;是否加载本地配置，默认加载

> 增加`loadSetting: false`参数会不恢复上次打开的Tab，不加载底部开关，Tab自动刷新开关配置项。

loadHome这个方法还可以告诉index模块主页的url，从而在记忆打开的Tab时不记忆主页的url，因为不同角色对应的主页不一样，
所以如果记忆会导致主页出现多个。


### 2.3.openTab打开选项卡

```javascript
top.layui.index.openTab({
    title: '百度', 
    url: 'https://www.baidu.com',
    end: function() {
        // table.reload('userTable'); 
    }
});
```

- title： 选项卡的标题
- url： 打开的页面地址
- end`： Tab关闭的回调事件

也可以直接使用`<a ew-href="xxx.html">XXX</a>`，注意是`ew-href`。


### 2.4.closeTab关闭选项卡

```javascript
top.layui.index.closeTab('https://www.baidu.com');
```


### 2.5.清除记忆Tab

```javascript
index.clearTabCache();
```

&emsp;Tab记忆功能是在刷新页面的时候可以恢复上次打开的所有Tab，在实际项目中，应该在登录成功后去清除当前的Tab记忆，
因为不同用户登录看见的菜单时不一样的。


### 2.6.setTabTitle设置标题

```javascript
index.setTabTitle('Hello');  // 仅修改标题文字

index.setTabTitleHtml('<span>Hello</span>');  // 修改整个标题栏的html
```

&emsp;关闭多标签系统会自动生成一个标题栏，生成的标题是侧边栏的菜单名称，如果要动态修改，
可使用这个方法，目前这个方法只在关闭多标签模式下生效。


### 2.7.切换Tab自动刷新

默认是关闭的，在设置界面可开启，如果要直接开启在index.html中添加如下代码：
```javascript
$('.layui-body>.layui-tab[lay-filter="admin-pagetabs"]').attr('lay-autoRefresh', 'true');
```
给layui-tab增加`lay-autoRefresh`属性为true即可。


### 2.8.侧边栏手风琴折叠

默认是开启的，如要关闭配置如下：
```html
<!-- 侧边栏 -->
<div class="layui-side">
    <div class="layui-side-scroll">
        <ul class="layui-nav layui-nav-tree" lay-accordion="false">
            ......省略其他部分
        </ul>
    </div>
</div>
```
修改`lay-accordion`属性为false或者直接去掉即可。


---

## 3.admin模块

### 3.1.默认主题、存储表名

修改这两个默认的配置打开admin.js修改如下参数即可：

```javascript
var admin = {
    defaultTheme: 'theme-admin',  // 默认主题
    tableName: 'easyweb'  // 存储表名
    // ...省略
}
```

- `defaultTheme`是默认的主题，可修改成`theme-red`等你配置的主题。
- `tableName`是本地缓存的表名，如果一个域名下有多个项目，建议修改成不同的名字。


### 3.2.常用方法

方法 | 参数 | 描述
:---|:--- |:--- 
flexible(expand) | true和false | 折叠/展开侧导航 
activeNav(url) | a标签里面的lay-href值 | 设置侧导航栏选中
refresh(url) | url，可为空 | 刷新指定Tab或当前Tab
closeAllTabs() | 无 | 关闭所有选项卡
closeOtherTabs(url) | url | 关闭出url外所有选项卡
closeThisTabs(url) | url，可为空 | 关闭url或当前选项卡
rollPage(d) | 方向(left、right、auto) | 滚动选项卡tab
 | | 
iframeAuto() | 无 | 让当前的ifram弹层自适应高度
closeThisDialog() | 无 | 关闭当前iframe弹窗
closeDialog(elem) | dom选择器 | 关闭elem元素所在的弹窗(页面层弹窗)
 | | 
putTempData(key, value) | key,value | 缓存临时数据
getTempData(key,) | key | 获取缓存的临时数据
parseJSON(string) | 字符串 | 解析json，解析失败返回undefined
getPageHeight() | 无 | 获取浏览器高度
getPageWidth() | 无 | 获取浏览器宽度

- 前几个方法是操作index.html中的元素，使用要加top：`top.layui.admin.xxx();`；
- 中间几个方法是跟弹窗有关的方法；
- 最后几个方法是通用的，直接用：`admin.xxx();`。

使用示例：
```javascript
layui.use(['admin'], function () {
    var admin = layui.admin;

    admin.getPageHeight();    // 获取浏览器高度
});
```

### 3.3.弹窗popupRight和open

```javascript
admin.open({
    type: 2,
    content: 'tpl-theme.html'
});
```

`admin.open`只是对layer.open进行了一层封装，使得弹窗可以有更好的样式，可以跟随主题变颜色。

> open和popupRight参数都和layer的用法是一样的，请查看[layer文档](https://www.layui.com/doc/modules/layer.html)。

**`popupRight`是在右侧弹出面板：**

```javascript
admin.popupRight({
    type: 2,
    content: 'tpl-theme.html'
});
```

![右侧弹出示例](http://zhenbaoxie.profacty.cn/data/20190530/1559186057575720167884.jpg)


### 3.4.显示加载动画

```javascript
admin.showLoading('#xxx', 1, '.8');

admin.removeLoading('#xxx', true, true);
```

`showLoading`显示加载动画：

- 参数一&emsp;非必填&emsp;元素选择器，默认为body；
- 参数二&emsp;非必填&emsp;动画类型(1小球，2魔方)；
- 参数三&emsp;非必填&emsp;透明度(0-1)；

`removeLoading`移除加载动画：

- 参数一&emsp;非必填&emsp;元素选择器，默认为body；
- 参数二&emsp;非必填&emsp;true是淡出效果，false直接隐藏；
- 参数三&emsp;非必填&emsp;true是删除，默认是隐藏不删除；

<br>

**也可以直接写在body下面作为整个页面的加载动画：**

```html
<!-- 小球样式 -->
<div class="page-loading">
    <div class="ball-loader">
        <span></span><span></span><span></span><span></span>
    </div>
</div>

<!-- 魔方样式 -->
<div class="page-loading">
    <div class="rubik-loader">
    </div>
</div>
```

在`common.js`中直接写`admin.removeLoading()`就会在页面加载完成移除body中的加载动画。


### 3.5.ajax封装

**`admin.req`是固定的写法，用法如下：**

```javascript
admin.req('url',{
    参数一: 'xxx',
    参数二: 'xxx'
}, function(res){
    alert(res.code + '-' + res.msg);
}, 'get');
```

- 参数一&emsp;请求的url
- 参数二&emsp;请求参数
- 参数三&emsp;请求回调（失败也进此回调，404等）
- 参数四&emsp;请求方式

**`admin.ajax`参数跟$.ajax参数一致，用法如下：**

```javascript
admin.ajax({
    url: 'url',
    data: {},
    type: 'post',
    dataType: 'json',
    success: function(res){
        alert(res.code + '-' + res.msg);
    }
});
```

> admin.req和admin.ajax都实现了自动传递header、预处理、系统错误依然回调到success等功能。

**自动传递header：**

重写admin的`getAjaxHeaders`方法：
```javascript
admin.getAjaxHeaders = function (requestUrl) {
    var headers = new Array();
    headers.push({name: 'token', value: 'xxxxx'});
    return headers;
}
```

**请求回调预处理：**

重写admin的`ajaxSuccessBefore`方法：
```javascript
admin.ajaxSuccessBefore = function (res, requestUrl) {
    if(res.code==401){
        alert('登录超时，请重新登录');
        return false;  // 返回false阻止代码执行
    }
    return true;
}
```

> 重写的操作可以放在common.js里面，这样就不用每个页面都去重写了。


### 3.6.ew-event事件绑定

使用示例：
```html
<a ew-event="fullScreen">全屏</a>
<a ew-event="flexible">折叠导航</a>
```

事件 | 描述
:---|:--- 
flexible | 折叠侧导航 
refresh | 刷新主体部分 
closeThisTabs | 关闭当前选项卡
closeOtherTabs | 关闭其他选项卡
closeAllTabs | 关闭全部选项卡
leftPage | 左滚动选项卡
rightPage | 右滚动选项卡
  |  
closeDialog | 关闭当前iframe弹窗
closePageDialog | 关闭当前页面层弹窗
  |  
theme | 打开主题设置弹窗 
note | 打开便签弹窗
message | 打开消息弹窗
psw | 打开修改密码弹窗
logout | 退出登录
  |  
fullScreen | 全屏切换
back | 浏览器后退

> 前几个只能在index.html中使用，`theme`、`note`等可以通过`data-url`属性配置对应的url。

```html
<a ew-event="theme" data-url="xxx.html">主题</a>
```

`ew-event`属性可用于任何元素。


### 3.7.提示框

鼠标滑过弹出tips，使用示例：

```html
<button lay-tips="大家好！">按钮</button>

<button lay-tips="大家好！" lay-direction="2" lay-bg="#009788">按钮</button>
```

- `lay-direction`： 设置位置，1上面（默认）；2右边；3下面；4 左边；
- `lay-bg`： 设置背景颜色；

![](http://zhenbaoxie.profacty.cn/data/20190530/1559186112427624209964.jpg)


---

## 4.admin.css介绍

### 4.1.公共类

类名（class） | 说明
:---|:--- 
pull-left | 左浮动
pull-right | 右浮动
text-left | 内容居左
text-center | 内容居中
text-right | 内容居右
inline-block | 设置display为inline-block
bg-white | 设置背景为白色
layui-link | 设置a标签颜色为主题色，不加为蓝色 

```html
<a href="xxx" class="layui-link">帐号注册</a>
```

![](http://zhenbaoxie.profacty.cn/data/20190530/1559186156246231643040.jpg)


### 4.2.组件样式

类名（class） | 说明
:---|:--- 
icon-btn | 带图标的按钮，会缩小边距
date-icon | 在元素的右边加入日期的图标 
search-icon | 在元素的右边加入搜索的图标 
btn-circle | 圆形按钮，参见便签界面
arrow2 | 设置侧边栏小三角为箭头图标，加在layui-nav上
close-footer | 关闭页脚，加`<body class="layui-layout-body close-footer">`上

```html
<!-- 图标按钮 -->
<button class="layui-btn icon-btn"><i class="layui-icon">&#xe615;</i>搜索</button>

<!-- 日期图标 -->
<input class="layui-input date-icon" type="text"/>
```

![](http://zhenbaoxie.profacty.cn/data/20190530/1559186195925960889594.jpg)
![](http://zhenbaoxie.profacty.cn/data/20190530/1559186237120375388504.jpg)


### 4.3.表单弹窗

类名（class） | 说明
:---|:--- 
model-form | 调整弹窗内的表单的间距使之更好看


### 4.4.表格工具栏

类名（class） | 说明
:---|:--- 
toolbar | 调整表格上面的表单间距使之更好看
w-auto | 设置width:auto，用于重置一些有固定宽度表单元素
mr0 | 设置margin-right:0，用于重置一些表单元素的样式

---

## 5.主题功能

1. 使用 [主题生成器](https://demo.easyweb.vip/theme/) 生成个性化主题；
2. 将生成的css放在`assets/moudel/theme`目录下；
3. 打开`page/tpl/tpl-theme.html`添加生成的主题：
    ```javascript
    var themes = [
        {title: '黑白主题', theme: 'admin'},
        {title: '黑色主题', theme: 'black'},
        {title: '你的主题', theme: 'xxx'}
    ];
    ```

> 主题css以`theme-xxx.css`的形式命名，主题预览图跟主题css名字保持一致，预览图可在主题生成器中获取。

---

## 6.扩展插件

### 6.1.鼠标右键

快速使用：

```javascript
layui.use(['contextMenu'], function () {
    var contextMenu = layui.contextMenu;
    
    // 重写整个页面右键菜单
    contextMenu.bind('html', [{
        icon: 'layui-icon layui-icon-snowflake',
        name: '菜单一',
        click: function () {
            alert('点击了菜单一');
        }
    }, {
        name: '菜单二',
        click: function () {
            alert('点击了菜单二');
        }
    }]);
});
```

- 参数一： 绑定元素；
- 参数二：菜单数组；

**菜单数组结构：**
```javascript
[{
    icon: 'xxxxx',
    name: '菜单三',
    subs: [{
        icon: 'xxxxx',
        name: '子菜单一',
        click: function () {
            alert('点击了子菜单一');
        }
    }]
}]
```

> `icon`：图标，`name `：菜单名，`click`：点击事件，`subs`：子菜单（支持无限极）。

**用于点击事件：**(使用`show`方法)

```javascript
$('#btn').click(function (e) {
    var x = $(this).offset().left;
    var y = $(this).offset().top + $(this).outerHeight();
    
    contextMenu.show([{
        name: '按钮菜单一',
        click: function () {
        }
    }, {
        name: '按钮菜单二',
        click: function () {
        }
    }], x, y);
    
    e.preventDefault();
    e.stopPropagation();
});
```

- 参数一： 菜单数组；
- 参数二三：xy坐标；


### 6.2.打印插件

**打印当前页面：**

```javascript
printer.print();

printer.print({
    hide: ['.layui-btn', '#btn01'],  // 打印时隐藏的元素
    horizontal: true,  // 是否横向打印
    blank: true,  // 是否打开新页面打印
    close: true,  // 如果是打开新页面，打印完是否关闭
    iePreview: true  // 是否兼容ie打印预览
});
```

> 参数都是可以选参数，默认值已经符合大多数需求。


**设置不打印的元素：**
```html
<div class="hide-print">非打印内容</div>
```

> 加`hide-print`这个class即可，可以跟`hide`参数叠加使用。


**打印自定义内容：**

```javascript
printer.printHtml({
    html: '<span>xxxx</span>',  // 要打印的内容
    horizontal: true,  // 是否横向打印
    blank: true,  // 是否打开新页面打印
    close: true,  // 如果是打开新页面，打印完是否关闭
    iePreview: true,  // 是否兼容ie打印预览
    print: true  // 如果是打开新窗口是否自动打印
});
```

**分页打印：**

```javascript
printer.printPage({
    htmls: [
        '<span>xxxx</span>',   // 要打印的内容
        '<span>xxxx</span>'
    ],
    style: '<style> span { color: red; } </style>',  // 页面样式
    horizontal: true,  // 是否横向打印
    padding: undefined,  // 页面间距，例如写'10px'
    debug: false,  // 调试模式
    blank: true,  // 是否打开新页面打印
    close: true,  // 如果是打开新页面，打印完是否关闭
    iePreview: true,  // 是否兼容ie打印预览
    print: true  // 如果是打开新窗口是否自动打印
});
```

参数都是可选参数：
- htmls&emsp;数组，代表每一页；
- style&emsp;样式，也可以写`<link rel="stylesheet">`的形式；
- padding&emsp;间距，undefined表示使用打印机默认间距，如果自定义间距，打印机间距要选择无；
- debug&emsp;调试模式，每一页会加红色边框，便于调试大小；

> `padding`为undefined表示使用打印机默认间距，插件内部会根据不同浏览器做兼容处理，因为不同浏览器的默认间距不同，
> 目前对谷歌、IE、Edge、火狐做了兼容处理，safari、欧朋后续会考虑。


**makeHtml()拼接html：**

```javascript
var htmlStr = printer.makeHtml({
    title: '网页标题',
    style: '<link rel="stylesheet" href="layui.css"><script type="text/javascript" src="layui.js"><\/script>',
    body: $('#xxx').html()
});
```

**其他方法：**

```javascript
printer.isIE();
printer.isEdge();
printer.isFirefox();
```

**打印任意url：**

目前没有提供直接的方法，可以使用ajax请求到url的html内容，在使用printHtml方法打印：
```javascript
$.ajax({
    url: 'xxx.html',
    type: 'get',
    dataType: 'html',
    success: function (res) {
        printer.printHtml({
            html: res,  // 要打印的内容
            horizontal: true  // 是否横向打印
        });
    }
});
```

> 另外js直接控制横向打印只支持谷歌内核的浏览器，火狐不支持js调用打印预览，
> 火狐打印预览可以通过设置`blank:true,print:false`然后手动选择打印预览。


### 6.3.tableX表格扩展

后续推出...


### 6.4.formX表单扩展

后续推出...


### 6.5.树形表格

[前往码云查看](https://gitee.com/whvse/treetable-lay)


### 6.6.下拉菜单

快速使用：

```html
<!-- data-dropdown是绑定下拉菜单，data-anchor是指定弹出位置（小三角相对于下拉菜单的位置） -->
<button data-dropdown="#dropdown1" data-anchor="top-left" class="layui-btn">更多操作</button>

<div class="dropdown-menu dark" id="dropdown1">
    <ul>
        <li><a class="edit">修改</a></li>
        <li><a class="del">删除</a></li>
        <li><a class="print">打印</a></li>
    </ul>
</div>

<script>
layui.use(['dropdown'], function () {
    var $ = layui.jquery;
    var dropdown = layui.dropdown;

    $('.edit').click(function () {
        alert('点击了修改');
    });
});
</script>
```

**dropdown-menu的可选class配置：**

 参数 | 描述
 :--- | :---
 dark |  夜间模式
 fixed | 使用固定定位，否则使用绝对定位
 dropdown-has-shade | 增加遮罩层，使用fixed定位才有效
 dropdown-disabled | 禁用下拉列表
 
**弹出位置配置：**

> 触发元素增加`data-anchor`属性，此属性是配置小三角相对于下拉菜单的位置

 参数 | 弹出位置
 :--- | :---
 top-left | 下左弹出（默认）
 top-center | 下中弹出
 top-right | 下右弹出
     |   
 right-top | 左上弹出
 right-center | 左中弹出
 right-bottom | 左下弹出
 ... | 上边弹出和右边弹出以此类推


### 6.7.消息通知

快速使用：

```javascript
layui.use(['notice'], function(){
    var notice = layui.notice;
    
    notice.success({
        title: '消息通知',
        message: '你有新的消息，请注意查收!'
    });
    
    // 提示框样式，1成功、2失败、3警告、4加载
    notice.msg('Hello', {icon: 1});
    
});
```

**全部方法：**

 方法名 | 作用
 :---:|:---:
 success | 成功消息（绿色背景）
 warning | 警告消息（黄色背景）
 error | 错误消息（红色背景）
 info | 通知消息（蓝色背景）
 msg | 提示框
 show | 自定义样式
 destroy | 关闭全部
 hide | 关闭指定的通知
 settings | 统一设置默认值
 
 **全部参数：**
 
 参数名 | 作用 | 默认值 | 可选值 
:---:|:---:|:---:|:---:
 title | 标题 |  | 
 message | 内容 | | 
 position | 位置 | bottomRight | 见下方单独说明
 transitionIn | 进入动画 | fadeInUp | 见下方单独说明
 transitionOut | 退出动画 | fadeOut | 
 timeout | 消失时间 | 5000 | 单位毫秒
 progressBar | 是否显示进度条 | true | true、false
 balloon | 是否使用气泡效果 | false | true、false
 close | 是否显示关闭按钮 | true | 
 pauseOnHover | 鼠标滑过暂停消失时间 | true | 
 resetOnHover | 鼠标滑过重置消失时间 | false |
 animateInside | 是否允许动画 | | 
 class | 加一个class | |
 | | | 
 color | 背景颜色 | | 十六进制颜色，英文颜色等
 progressBarColor | 进度条颜色 | |
 icon | 图标 | | 可以是Icomoon, Fontawesome等
 iconText | 图标文字 | | 
 iconColor | 图标颜色 | | 
 image | 图像 | |
 imageWidth | 图像宽度 | |
 layout | 布局类型 | 1 | 1或者2
 | | | 
 buttons | 按钮 | {} | 指定一组按钮
 onOpen | 打开消息框时的回调函数 | | 
 onClose | 关闭消息框时的回调函数 | | 

**显示位置可选属性：**<br>
`bottomRight`、`bottomLeft`、`topRight`、 `topLeft`、 `topCenter`、 `bottomCenter` 、 `center`。

**进入动画可选属性：**<br>
`bounceInLeft`, `bounceInRight`, `bounceInUp`, `bounceInDown`, `fadeIn`, `fadeInDown`, `fadeInUp`, 
`fadeInLeft`, `fadeInRight` , `flipInX`。

**退出动画可选属性：**<br>
`fadeOut`, `fadeOutUp`, `fadeOutDown`, `fadeOutLeft`, `fadeOutRight`, `flipOutX`。


**统一设置默认值：**
 
```javascript
notice.settings({
    timeout: 10000,
    resetOnHover: true,
    icon: 'material-icons',
    transitionIn: 'flipInX',
    transitionOut: 'flipOutX',
    onOpen: function(){
        console.log('callback abriu!');
    },
    onClose: function(){
        console.log("callback fechou!");
    }
});   
```

**关闭指定的通知**

根据参数指定的class筛选：
```javascript
notice.hide({
    transitionOut: 'fadeOutUp'
}, document.querySelector('.toast')); 
```

**自定义样式通知:**

```javascript
notice.show({
    color: 'dark',
    icon: 'icon-person',
    title: 'Hey',
    message: 'Welcome!',
    position: 'center', 
    progressBarColor: 'rgb(0, 255, 184)',
    buttons: [
        ['<button>Ok</button>', function (instance, toast) {
            alert("Hello world!");
        }],
        ['<button>Close</button>', function (instance, toast) {
            instance.hide({ transitionOut: 'fadeOutUp' }, toast);
        }]
    ]
});   
```


---

## 7.常见问题

### 7.1 后台生成侧边栏
这里用java的一个模板引擎beetl的语法示例：
```html
<ul class="layui-nav layui-nav-tree" lay-filter="admin-side-nav">
    
    <% for(menu in menus) { %>
    <li class="layui-nav-item">
        <a lay-href="${menu.menuUrl}"><i class="${menu.menuIcon}"></i>&emsp;<cite>${menu.menuName}</cite></a>
        <% if(menu.subMenus.~size>0) { %>
        <dl class="layui-nav-child">
            <% for(subMenu in menu.subMenus) { %>
            <dd>
                <a lay-href="${subMenu.menuUrl}">${subMenu.menuName}</a>
                <% if(subMenu.subMenus.~size>0) { %>
                <dl class="layui-nav-child">
                    <% for(temp in subMenu.subMenus) { %>
                    <dd><a lay-href="${temp.menuUrl}">${temp.menuName}</a></dd>
                    <% } %>
                </dl>
                <% } %>
            </dd>
            <% } %>
        </dl>
        <% } %>
    </li>
    <% } %>

</ul>
```


### 7.2 ajax加载侧边栏

演示地址：[side-ajax.html](https://demo.easyweb.vip/iframe/page/template/side-ajax.html)

```html
<div class="layui-side">
    <div class="layui-side-scroll">
        <ul class="layui-nav layui-nav-tree" lay-filter="admin-side-nav"></ul>
    </div>
</div>
<script id="sideNav" type="text/html">
    {{#  layui.each(d, function(index, item){ }}
    <li class="layui-nav-item">
        <a lay-href="{{item.url}}"><i class="{{item.icon}}"></i>&emsp;<cite>{{item.name }}</cite></a>
        {{# if(item.subMenus&&item.subMenus.length>0){ }}
        <dl class="layui-nav-child">
            {{# layui.each(item.subMenus, function(index, subItem){ }}
            <dd>
                <a lay-href="{{ subItem.url }}">{{ subItem.name }}</a>
                {{# if(subItem.subMenus&&subItem.subMenus.length>0){ }}
                <dl class="layui-nav-child">
                    {{# layui.each(subItem.subMenus, function(index, thrItem){ }}
                    <dd><a lay-href="{{ thrItem.url }}">{{ thrItem.name }}</a></dd>
                    {{# }); }}
                </dl>
                {{# } }}
            </dd>
            {{# }); }}
        </dl>
        {{# } }}
    </li>
    {{#  }); }}
</script>
<script>
layui.use([ 'element', 'admin','laytpl'], function () {
    var $ = layui.jquery;
    var admin = layui.admin;
    var laytpl = layui.laytpl;
    var element = layui.element;
        
    $.get('json/side.json', function (res) {
        laytpl(sideNav.innerHTML).render(res.data, function (html) {
            $('*[lay-filter=admin-side-nav]').html(html);
            element.render('nav');  // 这里非常重要
        });
    }, 'json');
});
</script>
```

json数据格式：

```json
{
  "code": 200,
  "msg": "",
  "data": [{
      "name": "系统管理",
      "icon": "layui-icon layui-icon-set",
      "url": "javascript:;",
      "subMenus": [
        {
          "name": "用户管理",
          "url": "system/user.html"
        }
      ]
    }
  ]
}
```


### 7.3 多系统模式

&emsp;&emsp;在header中有几个选项：“xx系统”、“xx系统”，点击不同的系统切换不同的侧边菜单，在线演示：[side-more.html](https://demo.easyweb.vip/iframe/page/template/side-more.html)

侧边栏代码，使用`nav-id`标识不同的菜单：
```html
<div class="layui-side">
    <div class="layui-side-scroll">
        <!-- 系统一的菜单，每个ul都加nav-id -->
        <ul nav-id="xt1" class="layui-nav layui-nav-tree" lay-filter="admin-side-nav">
            <!-- ...省略代码... -->
        </ul>
        <!-- 系统二的菜单，加layui-hide隐藏 -->
        <ul nav-id="xt2" class="layui-nav layui-nav-tree layui-hide" lay-filter="admin-side-nav">
            <!-- ...省略代码... -->
        </ul>
    </div>
</div>
```

header.html代码，使用`nav-bind`绑定侧边栏菜单：

```html
<div class="layui-header">
    <ul class="layui-nav layui-layout-left">
        <li class="layui-nav-item"><a nav-bind="xt1">系统一</a></li>
        <li class="layui-nav-item"<a nav-bind="xt2">系统二</a></li>
    </ul>
</div>
```


### 7.4 弹窗下拉框出现滚动条

非iframe类型的弹窗才能解决，解决办法如下：

```javascript
 admin.open({
        title: '添加用户',
        content: $('#model').html(),
        success: function (layero, index) {
            // 禁止出现滚动条
            $(layero).children('.layui-layer-content').css('overflow', 'visible'); 
        }
    });
```

> 关键代码就是在success回调中写上面那句话。


## 8.扩展图标

### 8.1 引入扩展图标
1. 成为iconfont真保蟹项目成员；
2. 搜索并添加想要的图标到真保蟹项目，重新生成在线链接；
3. 复制生成的链接到`insurance-zbx\src\main\resources\static\assets\js\common.js`文件中修改即可：

![iconfont](http://zhenbaoxie.profacty.cn/data/20190530/1559196589853977699484.png)

![引入图标](http://zhenbaoxie.profacty.cn/data/20190530/1559196420767445534839.png)

### 8.2 使用扩展图标
1. Font class图标使用(<font color=red>推荐</font>)
```html
	<i class="zbxico zbx-xxx"></i>
```
2. Symbol图标使用(`common.js`文件中JS一定要引入),使用页面加入通用css代码(支持多色图标了，不再受单色限制)
```css
	<style type="text/css">
        .svgicon {
            width: 1.8em; height: 1.8em;
            vertical-align: -0.15em;
            fill: currentColor;
            overflow: hidden;
        }
    </style>
```
```html
<svg class="svgicon" aria-hidden="true">
    <use xlink:href="#zbx-xxx"></use>
</svg>
```
![Symbol图标](http://zhenbaoxie.profacty.cn/data/20190530/1559198111477629928470.png)

