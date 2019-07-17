
文档名称 | 真保蟹管理系统前端文档 
--- | --- 
当前版本 | `iframe v3.1.3` 
更新时间 | 2019-07-14
基于框架 | layui-v2.5.4
作者 | 曹灼祥（搜集整理）
说明 | 真保蟹管理平台前端框架文档（`内部使用，禁止外泄`）&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;

## 1.开始使用
### 1.1.项目结构

```
|-assets
|     |-css                     // 样式
|     |-images                  // 图片
|     |-js                      // javascript
|         |-common.js           // 公共js，主要配置layui扩展模块位置，公共方法，扩展图标
|     |-libs                    // 第三方库，echarts(图表)、layui(ui框架)、zTree(树)
|     |-module                  // layui扩展模块
|         |-city-picker         // 城市选择器
|         |-dropdown            // 下拉菜单
|         |-dropDownTree        // 整合layui下拉树插件(https://fly.layui.com/jie/30382/)
|         |-dtree               // 树形组件
|         |-echarts             // 图表插件
|         |-formSelects         // 多选框组件
|         |-img                 // 图片资源
|         |-introJs             // 引导插件
|         |-notice              // 消息通知
|         |-numinput            // 表单数字输入组件
|         |-sliderVerify        // 滑块验证
|         |-step-lay            // 分步表单
|         |-tableSelect         // 下拉表格选择器
|         |-tagsInput           // tag输入框
|         |-theme               // 主题资源
|         |-treetable-lay       // 树形表格
|         |-admin.css           // admin样式
|         |-admin.js            // admin模块
|         |-ClipboardJS.js      // 剪贴板组件
|         |-contextMenu.js      // 鼠标右键模块
|         |-index.js            // index模块
|         |-mousewheel.js       // 鼠标滚轮监听
|         |-printer.js          // 打印模块
|         |-QRCode.js           // 二维码组件
|         |-tableX.js           // tableX表格扩展
|         |-********            // 后续扩展模块
|-HandyEditor                   // 富文本编辑器
```

> assets整个目录都是静态资源。

### 1.2.index.html结构说明

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

> index.html就是这样固定的结构，在整合到后台时，你可以把头部、侧边栏抽离出来，再include进去。


### 1.3.添加一个菜单

1. 打开`index.html`，找到侧导航栏，添加一个菜单：
    ```html
   <dd><a lay-href="xxx.html">XX管理</a></dd>
   <!-- 还可以增加ew-title属性自定义打开的Tab标题
   <a lay-href="xxx.html" ew-title="XXXXX">XX管理</a> -->
    ```

2. 新建一个`xxx.html`页面；
3. 运行项目，查看效果。

> 注意：a标签里面是 `lay-href`而不是`href`，lay-href点击会打开一个Tab标签页。


### 1.4.common.js说明

```javascript
layui.config({
    base: getProjectUrl() + 'assets/module/'  // 配置layui扩展模块目录
}).extend({  // 配置每个模块分别所在的目录
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

&emsp;像`admin.js`、`index.js`这些没有用子目录存放的模块不需要配置layui.extend，延时移除加载动画是给切换主题预留时间。

> 每个页面都需要引入common.js


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

> 在设置界面修改的配置会高于index中的默认配置，设置界面的配置信息是放在缓存中，清除缓存就会以index中配置为主。


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

> index.loadHome()方法在index.html中调用即可。


### 2.3.openTab打开选项卡

```javascript
index.openTab({
    title: '百度', 
    url: 'https://www.baidu.com',
    end: function() {
        // table.reload('userTable'); 
    }
});
```

- title： 选项卡的标题
- url： 打开的页面地址
- end： Tab关闭的回调事件

&emsp;也可以直接使用`<a ew-href="xxx.html">XXX</a>`，注意是`ew-href`，还可以增加`ew-title`属性设置Tab标题：`<a ew-href="xxx.html" ew-title="XXX管理">XXX</a>`。

> 注意：单标签模式下end回调无效。

### 2.4.closeTab关闭选项卡

```javascript
index.closeTab('https://www.baidu.com');
```

关闭已经打开的Tab选项卡，参数是Tab的url，单标签模式下此方法无效。


### 2.5.clearTabCache清除Tab记忆

```javascript
index.clearTabCache();
```

&emsp;Tab记忆功能是刷新页面的时候可以恢复上次打开的所有Tab，此方法用于清除已经缓存的Tab。

> 注意：在实际项目中，登录成功后应该调用此方法清除Tab记忆。


### 2.6.setTabTitle修改Tab标题

```javascript
index.setTabTitle('Hello');  // 修改当前Tab标题文字，也支持单标签模式

index.setTabTitle(title, tabId);  // 修改指定Tab标题文字

index.setTabTitleHtml('<span>Hello</span>');  // 修改整个标题栏的html，此方法只在单标签模式有效
```

&emsp;关闭多标签时框架会自动生成一个标题栏，可使用此方法修改标题栏内容，参数为undefined时为隐藏标题栏。


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
    tableName: 'zbxweb'  // 存储表名
    // ...省略
}
```

- `defaultTheme`是默认的主题，可修改成`theme-red`等你配置的主题。
- `tableName`是本地缓存的表名，如果一个域名下有多个项目，建议修改成不同的名字。


### 3.2.全部方法

方法 | 参数 | 描述
:---|:--- |:--- 
flexible(expand) | true和false | 折叠/展开侧导航 
activeNav(url) | a标签里面的lay-href值 | 设置侧导航栏选中
refresh(url) | url，可为空 | 刷新指定Tab或当前Tab
closeAllTabs() | 无 | 关闭所有选项卡
closeOtherTabs(url) | url | 关闭除url外所有选项卡
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
 | | 
 btnLoading(elem) | dom选择器 | 设置按钮为加载状态
 openSideAutoExpand() | 无 | 开启鼠标移入侧边栏自动展开
 openCellAutoExpand() | 无 | 开启鼠标移入表格单元格超出内容自动展开
 modelForm(layero, btnFilter, formFilter) |  | 把弹窗自带的按钮跟弹窗内的表单绑定一起

使用示例：
```javascript
layui.use(['admin'], function () {
    var admin = layui.admin;

    var pageHeight = admin.getPageHeight();    // 获取浏览器高度
});
```

**按钮loading：**
```javascript
admin.btnLoading('#btn1');   // 设置按钮为loading状态
admin.btnLoading('#btnLoading', false);  // 移除按钮的loading状态

admin.btnLoading('#btn1', '加载中');   // 设置按钮为loading状态，同时修改按钮文字
admin.btnLoading('#btnLoading', '保存', false);  // 移除按钮的loading状态，同时修改按钮文字
```

**admin.iframeAuto()方法：**
&emsp;针对type:2的弹窗自适应弹窗高度，写在你的弹窗的子页面中，此方法是调用一次做一次高度自适应，如果你用js动态修改了弹窗子页面的高度，需要再调用一次。

```javascript
layui.use(['admin'], function(){
	var admin = layui.admin;

	admin.iframeAuto();
});
```

**admin.closeThisDialog()关闭当前iframe类型弹窗：**
&emsp;针对type:2的弹窗，在弹窗的子页面调用即可关闭当前的iframe弹窗。

**admin.closeDialog(elem)关闭非iframe类型的弹窗：**
&emsp;调用传递任意一个弹窗页面里面的元素即可，比如弹窗里面有一个`<button id="xxx">`：

```
admin.closeDialog('#xxx');  // 即可关闭xxx元素所在的页面层的弹窗
```

关闭弹窗还可以使用ew-event操作：
```html
<!-- 关闭iframe类型的弹窗 -->
<button ew-event="closeDialog"></button>
<!-- 关闭页面层的弹窗 -->
<button ew-event="closePageDialog"></button>
```


> admin.openSideAutoExpand()方法在index.html中调用，admin.openCellAutoExpand()可在任何页面调用



### 3.3.弹窗popupRight和open

```javascript
// 打开弹窗
admin.open({
    type: 2,
    content: 'tpl-theme.html'
});

// 右侧打开面板
admin.popupRight({
    type: 2,
    content: 'tpl-theme.html'
});
```

&emsp;这两个方法只是对layer.open进行了一层封装，使得弹窗可以有更好的样式，可以跟随主题改变颜色。

> open和popupRight参数和layer用法一样，查看[layer文档](https://www.layui.com/doc/modules/layer.html)。

popupRight右侧面板示例：

![右侧弹出示例](https://s2.ax1x.com/2019/07/14/Z5rneK.png)


&emsp;`type:2, content:xxx`这种是iframe类型的弹窗，也可以使用`url`，这个参数是额外增加的，使用`url`会通过ajax方式加载页面到弹窗中，而不是iframe，看到很多人喜欢把弹窗页面独立，然而父子页面传值又比较麻烦，所以增加了url方式。


```javascript
admin.open({ 
    title: 'Hello',
    url: 'tpl-theme.html' 
});


admin.popupRight({ 
    url: 'tpl-theme.html' 
});
```

&emsp;当你使用url方式加载的时候，你的弹窗页面应该时代码片段，而不是完整的html，如下所示：
```html
<form id="modelRoleForm" lay-filter="modelRoleForm" class="layui-form model-form">
	<input name="roleId" type="hidden"/>
	<div class="layui-form-item">
		<label class="layui-form-label">角色名</label>
		<div class="layui-input-block">
			<input name="roleName" placeholder="请输入角色名" type="text" class="layui-input" maxlength="20"
				   lay-verType="tips" lay-verify="required" required/>
		</div>
	</div>
	<div class="layui-form-item text-right">
		<button class="layui-btn layui-btn-primary" type="button" ew-event="closePageDialog">取消</button>
		<button class="layui-btn" lay-filter="modelSubmitRole" lay-submit>保存</button>
	</div>
</form>
<script>
    layui.use(['layer', 'form'], function () {
        var $ = layui.jquery;
        var layer = layui.layer;
        var form = layui.form;

	// 表单提交事件
	form.on('submit(modelSubmitRole)', function (data) {
		console.log(data.field);
		return false;
	});

    });
</script>
```


&emsp;页面不需要html、body这些东西，并且可以直接用`<script>`标签来写事件，至于参数传递，请查看下面的弹窗专题。

<br>

### 3.4.显示加载动画

**`showLoading`显示加载动画：**

```javascript
admin.showLoading('#xxx');  // 在id为xxx的元素中显示加载层

admin.showLoading('#xxx', 1, '.8');  // 显示type为1透明度为0.8的遮罩层
```

- 参数一`elem`&emsp;非必填&emsp;元素选择器，不填为body；
- 参数二`type`&emsp;非必填&emsp;动画类型(1 小球，2 魔方，3信号)，默认是1；
- 参数三`opacity`&emsp;非必填&emsp;透明度(0 - 1)，默认不透明；

&emsp;目前增加了尺寸的控制，提供有两种尺寸，用法：
```javascript
admin.showLoading({
    elem: '#xxx',
    type: 1,
    size: 'sm'
});
```
&emsp;默认是sm小型尺寸，还可以选md大型尺寸


**removeLoading`移除加载动画：**

```javascript
admin.removeLoading('#xxx');

admin.removeLoading('#xxx', true, true);
```

- 参数一&emsp;非必填&emsp;元素选择器，不填为body；
- 参数二&emsp;非必填&emsp;true是淡出效果，false直接隐藏，默认是true；
- 参数三&emsp;非必填&emsp;true是删除，false是隐藏不删除，默认是false；

<br>

**也可以写在body中作为页面载入的加载动画：**

```html
<body>
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
    
    <!-- 信号样式 -->
    <div class="page-loading">
        <div class="signal-loader">
            <span></span><span></span><span></span><span></span>
        </div>
    </div>
    
    <!-- 加sm是小型尺寸 -->
    <div class="page-loading">
        <div class="signal-loader sm">
            <span></span><span></span><span></span><span></span>
        </div>
    </div>
</body>
```

&emsp;写在页面中需要在js中调用`admin.removeLoading()`移除加载动画，不过common.js中已经写了。


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
- 参数三&emsp;请求回调（失败也进此回调，404、403等）
- 参数四&emsp;请求方式，get、post、put、delete等

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

&emsp;admin.req和admin.ajax都实现了自动传递header、预处理、系统错误依然回调到success等功能。

**自动传递header：**

&emsp;重写admin的`getAjaxHeaders`方法：
```javascript
admin.getAjaxHeaders = function (requestUrl) {
    var headers = new Array();
    headers.push({name: 'token', value: 'xxxxx'});
    return headers;
}
```

**请求回调预处理：**

&emsp;重写admin的`ajaxSuccessBefore`方法：
```javascript
admin.ajaxSuccessBefore = function (res, requestUrl) {
    if(res.code==401){
        alert('登录超时，请重新登录');
        return false;  // 返回false阻止代码执行
    }
    return true;
}
```

> 重写的操作建议放在common.js里面，这样就不用每个页面都去重写了，只要保证重写是在调用req、ajax之前即可。


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
  |  
open | 打开弹窗
popupRight | 打开右侧弹窗

`theme`、`note`等可以通过`data-url`属性配置对应的url，还可以通过`data-window="top"`属性在父页面处理事件。

```html
<a ew-event="theme" data-url="xxx.html">主题</a>
```

`ew-event`属性可用于任何元素，不仅仅是a标签。

<br/>

#### 3.6.1.open事件和popupRight
&emsp;这两个事件是用来支持非js方式打开弹窗：
```html
<button ew-event="open" data-type="2" data-content="http://baidu.com">iframe弹窗</button>

<button ew-event="open" data-type="1" data-url="form.html">页面弹窗</button>

<button ew-event="open" data-type="1" data-content="#userForm">页面弹窗</button>
<form id="userForm">......省略</form>

<!-- 设置area和offset -->
<button ew-event="open" data-type="1" data-content="Hello" data-area="80px,60px" data-offset="10px,10px">页面弹窗</button>

<!-- popupRight一样的用法 -->
<button ew-event="popupRight" data-type="2" data-url="http://baidu.com" data-title="百度一下，你就知道">右侧弹窗</button>
```
&emsp;layer支持的参数大部分都可以通过data属性来设置，数组类型用逗号分隔，function类型不支持。

<br/>


### 3.7.提示框

鼠标滑过弹出tips，使用示例：

```html
<button lay-tips="大家好！">按钮</button>

<button lay-tips="大家好！" lay-direction="2" lay-bg="#009788" lay-width="500px">按钮</button>

<button lay-tips="大家好！" lay-offset="10px">按钮</button>

<button lay-tips="大家好！" lay-offset="10px,10px">按钮</button>
```

- `lay-direction`： 设置位置，1上面（默认）；2右边；3下面；4 左边；
- `lay-bg`： 设置背景颜色；
- `lay-width`：设置宽度；
- `lay-offset`： 设置偏移距离，(上下,左右)

![](https://s2.ax1x.com/2019/07/11/Z2BGmd.png)

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
layui-link | 设置a标签颜色为主题色 
 | 
text-muted | 文字颜色为灰色
text-success | 文字颜色为绿色，成功色
text-warning | 文字颜色为黄色警告色
text-danger | 文字颜色为红色危险色
text-info | 文字颜色为蓝色信息色
text-primary | 文字颜色为主题色
 | 
layui-text | .layui-text下面的a标签为蓝色

```html
<a href="xxx" class="layui-link">帐号注册</a>
```

![](https://s2.ax1x.com/2019/07/06/ZwTuqJ.jpg)

```html
<div class="layui-text">
    <a href="xxx">xxx</a>
    <a href="xxx">xxx</a>
</div>
```

![](https://s2.ax1x.com/2019/06/05/VUIQit.png)

### 4.2.组件样式

类名（class） | 说明
:---|:--- 
icon-btn | 带图标的按钮，会缩小边距
date-icon | 在元素的右边加入日期的图标 
search-icon | 在元素的右边加入搜索的图标 
btn-circle | 圆形按钮，参见便签界面
 | 
layui-form-select-top | 控制下拉框上弹出，加载select父元素上
xm-select-nri | 多选下拉框去掉最后那个没用的图标，加在父元素上
 | 
mini-bar | 如果有滚动条，使用细的风格
 | 
arrow2 | 设置侧边栏小三角为箭头图标，加在layui-nav上
arrow3 | 设置侧边栏小三角为加减号图标，加在layui-nav上
 | 
close-footer | 关闭页脚，加`<body class="layui-layout-body close-footer">`上
 | 
table-tool-mini | 数据表格工具栏mini样式，加在table父元素上
 | 
hide-body-title | 全局隐藏单标签模式标题栏，加在body上

```html
<!-- 图标按钮 -->
<button class="layui-btn icon-btn"><i class="layui-icon">&#xe615;</i>搜索</button>

<!-- 日期图标 -->
<input class="layui-input date-icon" type="text"/>

<!-- 圆形按钮 -->
<div class="btn-circle">
    <i class="layui-icon layui-icon-add-1"></i>
</div>

<!-- 下拉框上弹出 -->
<div class="layui-form-select-top">
    <select>....</select>
</div>

<!-- 多选下拉框去掉最后那个没用的图标 -->
<div class="xm-select-nri">
    <select xm-select="xx">....</select>
</div>
```

![](https://s2.ax1x.com/2019/07/06/ZwHpAs.jpg)
![](https://s2.ax1x.com/2019/07/06/ZwTtMD.jpg)
![](https://s2.ax1x.com/2019/06/05/VUTm4I.png)
![](https://s2.ax1x.com/2019/07/11/Z2UdEV.png)

### 4.3.表单弹窗

类名（class） | 说明
:---|:--- 
model-form | 调整弹窗内的表单的间距使之更好看
model-form-body | 表单内容部分，高度自适应，超过屏幕高度显示滚动条
model-form-footer | 表单底部按钮部分，用于固定底部按钮

表单弹窗示例，添加用户：
```html
<script type="text/html" id="modelUser">
    <form id="modelUserForm" lay-filter="modelUserForm" class="layui-form model-form">
        <input name="userId" type="hidden"/>
        <div class="layui-form-item">
            <label class="layui-form-label">账号</label>
            <div class="layui-input-block">
                <input name="username" placeholder="请输入账号" type="text" class="layui-input" maxlength="20"
                       lay-verType="tips" lay-verify="required" required/>
            </div>
        </div>
        <div class="layui-form-item">
            <label class="layui-form-label">用户名</label>
            <div class="layui-input-block">
                <input name="nickName" placeholder="请输入用户名" type="text" class="layui-input" maxlength="20"
                       lay-verType="tips" lay-verify="required" required/>
            </div>
        </div>
        <div class="layui-form-item">
            <label class="layui-form-label">性别</label>
            <div class="layui-input-block">
                <input type="radio" name="sex" value="男" title="男" checked/>
                <input type="radio" name="sex" value="女" title="女"/>
            </div>
        </div>
        <div class="layui-form-item">
            <label class="layui-form-label">角色</label>
            <div class="layui-input-block">
                <select name="roleId" lay-verType="tips" lay-verify="required">
                    <option value="">请选择角色</option>
                    <option value="1">管理员</option>
                    <option value="2">普通用户</option>
                    <option value="3">游客</option>
                </select>
            </div>
        </div>
        <div class="layui-form-item text-right">
            <button class="layui-btn layui-btn-primary" type="button" ew-event="closePageDialog">取消</button>
            <button class="layui-btn" lay-filter="modelSubmitUser" lay-submit>保存</button>
        </div>
    </form>
</script>
<script>
admin.open({
    type: 1,
    title: '添加用户',
    content: $('#modelUser').html(),
    success: function (layero, dIndex) {
    }
});
</script>
```
![](https://s2.ax1x.com/2019/06/05/VU7UdH.png)

固定底部操作按钮：

```html
<script type="text/html" id="modelPtInfo">
    <form id="modelFormPtInfo" lay-filter="modelFormPtInfo" class="layui-form model-form no-padding">
        <div class="model-form-body" style="max-height: 320px;"> <!-- 如果要超出屏幕才固定底部，不要写max-height -->
            <div class="layui-form-item">
                <label class="layui-form-label">实习公司</label>
                <div class="layui-input-block">
                    <input name="companyName" placeholder="请输入实习公司" type="text" class="layui-input"
                           lay-verType="tips" lay-verify="required" required/>
                </div>
            </div>
            <!-- ......省略 -->
        </div>
        <div class="layui-form-item text-right model-form-footer">
            <button class="layui-btn layui-btn-primary" type="button" ew-event="closePageDialog">取消</button>
            <button class="layui-btn" lay-filter="modelSubmitPtInfo" lay-submit>保存</button>
        </div>
    </form>
</script>
```

![](https://s2.ax1x.com/2019/06/05/VU7olV.png)


### 4.4.表格工具栏

类名（class） | 说明
:---|:--- 
toolbar | 调整表格上面的表单间距使之更好看
w-auto | 设置width:auto，用于重置一些有固定宽度表单元素
mr0 | 设置margin-right:0，用于重置一些表单元素的样式

```html
<div class="layui-form toolbar">
    <div class="layui-form-item">
        <div class="layui-inline">
            <label class="layui-form-label w-auto">账&emsp;号：</label>
            <div class="layui-input-inline mr0">
                <input name="username" class="layui-input" type="text" placeholder="输入账号"/>
            </div>
        </div>
        <div class="layui-inline">
            <label class="layui-form-label w-auto">用户名：</label>
            <div class="layui-input-inline mr0">
                <input name="nickName" class="layui-input" type="text" placeholder="输入用户名"/>
            </div>
        </div>
        <div class="layui-inline">
            <button class="layui-btn icon-btn" lay-filter="formSubSearchUser" lay-submit>
                <i class="layui-icon">&#xe615;</i>搜索
            </button>
            <button id="btnAddUser" class="layui-btn icon-btn"><i class="layui-icon">&#xe654;</i>添加</button>
        </div>
    </div>
</div>

<table class="layui-table" id="tableUser" lay-filter="tableUser"></table>
```

![](https://s2.ax1x.com/2019/06/05/VU7qw4.png)

移动端下自动适配样式：

![](https://s2.ax1x.com/2019/06/05/VUHAkd.png)

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

&emsp;打印表格时可以给table加`print-table`这个class来设置表格的边框样式：<table class="print-table"></table>。


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

 方法 | 参数 | 描述
:--- | :--- | :---
merges(tableId, indexs, fields) | 见单独说明 | 合并单元格
bindCtxMenu(tableId, items) | 见单独说明 | 给表格行绑定鼠标右键
render(object) | 同layui表格 | 渲染表格，带后端排序功能
renderFront(object) | 同layui表格 | 渲染表格，带前端分页、排序、模糊搜索、刷新功能
exportData(object) | 见单独说明 | 导出任意数据为excel
 | 
loadUrl(object, callback) | 见单独说明 | 请求表格数据
parseTbData(cols, dataList, overwrite) | 见单独说明 | 获取解析templet后数据
filterData(dataList, searchName, searchValue) | 见单独说明 | 前端搜索数据
deepClone(object) | 任意对象 | 深度克隆对象


#### 6.3.1.merges合并单元格

```javascript
layui.use(['tableX'], function () {
    var tableX = layui.tableX;
    
    table.render({
        elem: '#xTable2',
        url: '../../json/tablex1.json',
        cols: [[
            {type: 'numbers'},
            {field: 'parentName', title: '模块名称', sort: true},
            {field: 'authorityName', title: '菜单名称', sort: true}
        ]],
        done: function () {
            tableX.merges('xTable2', [1]);  // 在done回调里面调用
        }
    });
});
```

使用方法：
```javascript
tableX.merges('xTable2', [1]);  // 合并第2列相同的单元格
tableX.merges('xTable2', [1], ['parentName']);  // 合并第2列相同的单元格
tableX.merges('xTable2', [1, 2]);   // 合并第2、3列相同的单元格
tableX.merges('xTable2', [1, 2], ['parentName', 'authorityName']);   // 合并第2、3列相同的单元格

tableX.merges('xTable2', [1, 2], false);   // 在后面加一个false可解决排序冲突的问题
```
- 参数一 &emsp; 必填 &emsp;&emsp; 表格的lay-filter
- 参数二 &emsp; 必填 &emsp;&emsp; 要合并列的索引，数组类型
- 参数三 &emsp; 非必填 &emsp; 根据数据字段判断是否相同，为空时根据单元格的html内容判断


#### 6.3.2.bindCtxMenu行绑定鼠标右键

```javascript
table.render({
    elem: '#xTable3',
    url: '../../json/user.json',
    cols: [[
        {field: 'nickName', title: '用户名', sort: true},
        {field: 'sex', title: '性别', sort: true}
    ]],
    done: function () {
        // 在done回调里面绑定
        tableX.bindCtxMenu('xTable3', [{
            icon: 'layui-icon layui-icon-edit',
            name: '修改此用户',
            click: function (d) {
                layer.msg('点击了修改，userId：' + d.userId);
            }
        }, {
            icon: 'layui-icon layui-icon-close text-danger',
            name: '<span class="text-danger">删除此用户</span>',
            click: function (d) {
                layer.msg('点击了删除，userId：' + d.userId);
            }
        }]);
    }
});
```

- 参数一 &emsp; 表格的lay-filter
- 参数二 &emsp; 右键菜单
    - icon &emsp;&nbsp;&nbsp; 图标
    - name &emsp; 标题
    - click &emsp;&nbsp;&nbsp; 点击事件，d是当前行的数据


#### 6.3.3.render后端排序

```javascript
tableX.render({
    elem: '#xTable3',
    url: '../../json/user.json',
    cols: [[
        {type: 'numbers'},
        {field: 'nickName', title: '用户名', sort: true},
        {field: 'sex', title: '性别', sort: true}
    ]]
});
```
&emsp;仅仅是把`table.render`换成`tableX.render`就会在点击排序时自动传递`sort`和`order`参数，例如：user?page=1&limit=10&sort=sex&order=asc。

- sort &emsp;&nbsp;&nbsp; 排序字段，值是点击排序列的field
- order &emsp; 排序方式，升序是asc，降序是desc

> 注意：如果写了sort: true开启排序，一定要写field: 'xxx'。


#### 6.3.4.renderFront前端分页排序

```javascript
tableX.renderFront({
    elem: '#xTable1',
    url: '../../json/userAll.json',
    page: { groups: 6 },
    cols: [[
        {type: 'checkbox'},
        {field: 'nickName', title: '用户名', sort: true},
        {field: 'sex', title: '性别', sort: true}
    ]]
});
```
&emsp;仅仅是把`table.render`换成`tableX.renderFront`就可以有前端分页和排序功能了，参数跟layui表格的参数一摸一样，url方式你的接口可以返回全部数据，前端来分页，也支持data方式。

**前端模糊搜索：**
```html
<input tb-search="xTable1" class="layui-input icon-search" type="text"/>
```
&emsp;页面中加入上面的输入框，通过`tb-search`关联表格，就实现了对表格的模糊搜索功能，你可以对该input加任意样式，放在任意位置。

&emsp;还可以增加name属性来设置搜索时只搜索某些字段，多个字段通过逗号分隔：
```html
<input tb-search="xTable1" name="sex,phone" class="layui-input icon-search" type="text"/>
```

**刷新功能：**
```html
<!-- 通过tb-refresh绑定刷新按钮 -->
<button tb-refresh="xTable1" class="layui-btn">刷新</button>
```
&emsp;也可以通过js刷新：
```javascript
var insTb = tableX.renderFront('.....省略');

insTb.reloadUrl();  // url方式刷新

insTb.reloadData({data: dataList, page: {curr: 1}});  // data方式的刷新
```

> 注意：url方式的前端分页使用reloadUrl()方法，reloadUrl()方法暂时不支持加参数，reloadData方法跟table.reload()方法参数一致。

**前端排序：**

&emsp;前端排序如果有field字段，会根据field字段的值来排序，如果没有field有templet会根据templet转换后的值排序，
templet可能会返回表单元素，比如switch开关等，这些元素根本无法用来排序，那么可以通过`export-show`和`export-hide`这两个东西来控制。

```html
<!-- 开关，state=0开关打开,state=1开关关闭 -->
<script type="text/html" id="tableState">
    <input type="checkbox" lay-skin="switch" lay-text="正常|锁定" lay-filter="ckState" value="{{d.userId}}" {{d.state==0?'checked':''}}/>
    <div class="export-show">{{d.state==0?'正常':'锁定'}}</div>
</script>
<!-- 图标，state=0显示ok的图标，state=1显示叉叉的图标 -->
<script type="text/html" id="tableState">
    <div class="export-hide">{{d.state==0?'<i class="layui-icon layui-icon-ok"></i>':'<i class="layui-icon layui-icon-close"></i>'}}</div>
    <div class="export-show">{{d.state==0?'正常':'锁定'}}</div>
</script>
<!-- 图标和开关没法用于排序，可以写两份，一份用于表格展示，一份用于排序和导出功能 -->
```

- `export-hide`用于表格展示，但是对排序和导出时屏蔽
- `export-show`用于排序和导出功能获取单元格数据，但是对表格展示时屏蔽

> 排序建议最好是通过field来根据数据的字段排序，field不支持d.xxx.xxx这种嵌套的对象，就需要用上面的方法

#### 6.3.5.exportData导出任意数据

```javascript
tableX.exportData({
    cols: insTb.config.cols,   // 表头配置
    data: table.cache.xTable3,  // 数据，支持url方式
    fileName: '用户表'          // 文件名称
});
```

参数 | 必填 | 说明 | 默认
:--- | :--- | :--- | :---
cols | 是 | 表头配置 | 
data | 是 | 导出的数据，支持数组和string的url | 
fileName | 否 | 导出的文件名称 | table
expType | 否 | 导出的文件类型 | xls
option | 否 | url方式的配置 | 

&emsp;如果data是string类型会把data当url请求数据，option是请求的配置，跟表格的配置一样，配置method、where、headers等，接口返回的格式也要跟表格一样包含code、count、data等信息。

&emsp;cols的配置也跟表格一样，是一个多维数组，可以通过`insTb3.config.cols`来获取表格的cols，也可以重写。

**导出的数据会包含templet转换：**
```javascript
tableX.renderFront({
    elem: '#xTable1',
    url: '../../json/userAll.json',
    page: { groups: 6 },
    cols: [[
        {type: 'checkbox'},
        {field: 'nickName', title: '用户名', sort: true},
        {field: 'sex', title: '性别', sort: true},
        {
            title: '状态', templet: function (d) {
                 var stateStr = ['正常', '冻结', '欠费'];
                 return stateStr[d.state];
            }
        }
    ]]
});
```
&emsp;像上面这种state数据存的是0和1，但是显示是文字，导出数据会自动转成文字(layui2.5后自带的带出也包含此功能)。

&emsp;如果列是表单元素，比如switch开关，导出的方法：

```html
<!-- 状态列 -->
<script type="text/html" id="tableState">
    <input type="checkbox" lay-skin="switch" lay-text="正常|锁定" lay-filter="ckState" value="{{d.userId}}" {{d.state==0?'checked':''}}/>
    <div class="export-show">{{d.state==0?'正常':'锁定'}}</div>
</script>
<script>
layui.use(['tableX'], function () {
    var tableX = layui.tableX;
    
    tableX.render({
        elem: '#xTable3',
        url: '../../json/user.json',
        cols: [[
            {field: 'nickName', title: '用户名', sort: true},
            {field: 'phone', title: '手机号', sort: true},
            {field: 'state', templet: '#tableState', title: '状态', sort: true}
        ]]
    });
});
</script>
```

&emsp;通过加一个`export-show`类可以保证里面的内容只作为导出的时候用，不作为表格展示，还可以加`export-hide`来设置导出的时候屏蔽的内容。

```html
<!-- 图标，state=0显示ok的图标，state=1显示叉叉的图标 -->
<script type="text/html" id="tableState">
    <div class="export-hide">{{d.state==0?'<i class="layui-icon">&#xe605;</i>':'<i class="layui-icon">&#x1006;</i>'}}</div>
    <div class="export-show">{{d.state==0?'正常':'锁定'}}</div>
</script>
<!-- 不可能把图标的unicode字符导出吧，那就写两份，一份用于表格展示，一份用于排序和导出功能 -->
```

- `export-hide`用于表格展示，但是对排序和导出时屏蔽
- `export-show`用于排序和导出功能获取单元格数据，但是对表格展示时屏蔽


### 6.4.formX表单扩展

后续推出...


### 6.5.树形表格

[前往码云查看](https://gitee.com/whvse/treetable-lay)


### 6.6.下拉菜单

快速使用：

```html
<!-- click模式触发 -->
<div class="dropdown-menu">
    <button class="layui-btn icon-btn">
        &nbsp;Click me <i class="layui-icon layui-icon-drop"></i>
    </button>
    <ul class="dropdown-menu-nav">
        <li><a>1st menu item</a></li>
        <li><a>2nd menu item</a></li>
        <li><a>3rd menu item</a></li>
    </ul>
</div>

<!-- hover模式触发，增加dropdown-hover即可 -->
<div class="dropdown-menu dropdown-hover">
    <button class="layui-btn icon-btn">
        &nbsp;Hover me <i class="layui-icon layui-icon-drop"></i></button>
    <ul class="dropdown-menu-nav">
        <li><a>1st menu item</a></li>
        <li><a>2nd menu item</a></li>
        <li><a>3rd menu item</a></li>
    </ul>
</div>

<script>
layui.use(['dropdown'], function () {
    var dropdown = layui.dropdown;  // 加载模块
});
</script>
```
![](https://s2.ax1x.com/2019/06/06/VUL0HS.png)

更多样式：

```html
<!-- 标题及禁用样式 -->
<div class="dropdown-menu dropdown-hover">
    <button class="layui-btn icon-btn">
        &nbsp;更多样式 <i class="layui-icon layui-icon-drop"></i></button>
    <ul class="dropdown-menu-nav">
        <li class="title">HEADER</li>
        <li><a><i class="layui-icon layui-icon-star-fill"></i>1st menu item</a></li>
        <li class="disabled">
            <a><i class="layui-icon layui-icon-template-1"></i>2nd menu item</a></li>
        <hr>
        <li class="title">HEADER</li>
        <li><a><i class="layui-icon layui-icon-set-fill"></i>3rd menu item</a></li>
    </ul>
</div>

<!-- 带小三角样式 -->
<div class="dropdown-menu dropdown-hover">
    <button class="layui-btn icon-btn">
        &nbsp;带小三角 <i class="layui-icon layui-icon-drop"></i>
    </button>
    <ul class="dropdown-menu-nav">
        <div class="dropdown-anchor"></div>
        <li><a>1st menu item</a></li>
        <li><a>2nd menu item</a></li>
        <li><a>3rd menu item</a></li>
    </ul>
</div>

<!-- 暗色主题 -->
<div class="dropdown-menu">
    <button class="layui-btn layui-btn-normal icon-btn">
        &nbsp;暗色主题 <i class="layui-icon layui-icon-drop"></i></button>
    <ul class="dropdown-menu-nav dark">
        <div class="dropdown-anchor"></div>
        <li class="title">HEADER</li>
        <li><a><i class="layui-icon layui-icon-star-fill"></i>1st menu item</a></li>
        <li class="disabled">
            <a><i class="layui-icon layui-icon-template-1"></i>2nd menu item</a></li>
        <hr>
        <li class="title">HEADER</li>
        <li><a><i class="layui-icon layui-icon-set-fill"></i>3rd menu item</a></li>
    </ul>
</div>
```
![](https://s2.ax1x.com/2019/06/06/VULshj.png)
![](https://s2.ax1x.com/2019/06/06/VUL69s.png)
![](https://s2.ax1x.com/2019/06/06/VULc3n.png)

对任意元素都可以绑定，不一定是按钮：
```html
<div class="dropdown-menu dropdown-hover">
    <input type="text" placeholder="一个会跳舞的输入框" class="layui-input"/>
    <ul class="dropdown-menu-nav">
        <li class="title">是不是在找</li>
        <li><a>另一个会跳舞的下拉框</a></li>
        <li><a>一杯忧郁的可乐</a></li>
    </ul>
</div>
```
![](https://s2.ax1x.com/2019/06/06/VULWuV.png)

带遮罩层，遮罩层是分离式的绑定，通过data-dropdown绑定：
```html
<button class="layui-btn layui-btn-normal icon-btn" data-dropdown="#dropdown1">
   带遮罩层 <i class="layui-icon layui-icon-drop"></i>
</button>

<!-- 下拉菜单 -->
<ul class="dropdown-menu-nav dropdown-bottom-right layui-hide" id="dropdown1">
    <div class="dropdown-anchor"></div>
    <li class="title">HEADER</li>
    <li><a><i class="layui-icon layui-icon-star-fill"></i>1st menu item</a></li>
    <li class="disabled">
        <a><i class="layui-icon layui-icon-template-1"></i>2nd menu item</a></li>
    <hr>
    <li class="title">HEADER</li>
    <li><a><i class="layui-icon layui-icon-set-fill"></i>3rd menu item</a></li>
</ul>
```
![](https://s2.ax1x.com/2019/06/06/VULfBT.png)

自定义下拉里面的内容：
```html
<div class="dropdown-menu">
    <button class="layui-btn layui-btn-normal icon-btn">
        &nbsp;克隆/下载 <i class="layui-icon layui-icon-drop"></i></button>
    <div class="dropdown-menu-nav dropdown-bottom-right"
         style="width: 280px;padding: 0 10px 10px 10px;">
        <div class="dropdown-anchor"></div>
        <!-- 下面是自定义内容 -->
        <div class="layui-tab layui-tab-brief">
            <ul class="layui-tab-title">
                <li class="layui-this">HTTPS</li>
                <li>SSH</li>
            </ul>
            <div class="layui-tab-content" style="padding: 10px 0 10px 0;">
                <div class="layui-tab-item layui-show">
                    <input class="layui-input" value="https://gitee.com/whvse/easyweb-jwt.git"/>
                </div>
                <div class="layui-tab-item">
                    <input class="layui-input" value="git@gitee.com:whvse/easyweb-jwt.git"/>
                </div>
            </div>
        </div>
        <button class="layui-btn layui-btn-sm layui-btn-fluid" style="margin-bottom: 10px;">
            Download ZIP
        </button>
        <img src="http://p1.music.126.net/voV3yPduAhNATICMRJza1A==/109951164017919367.jpg"
             width="100%">
        <!-- //end.自定义内容结束 -->
    </div>
</div>
```
![](https://s2.ax1x.com/2019/06/06/VUL5EF.png)

控制显示方向，在dropdown-menu-nav上加dropdown-bottom-center等位置：

 类名 | 位置
:--- | :---
 dropdown-bottom-left | 下左弹出
 dropdown-bottom-center| 下中弹出
 dropdown-bottom-right | 下右弹出
 | 
 dropdown-top-left | 上左弹出
 dropdown-top-center | 上中弹出
 dropdown-top-right | 上右弹出
 | 
 dropdown-left-top | 左上弹出
 dropdown-left-center | 左中弹出
 dropdown-left-bottom | 左下弹出
 | 
 dropdown-right-top | 右上弹出
 dropdown-right-center | 右中弹出
 dropdown-right-bottom | 右下弹出
   
```html
<!-- Bottom Center -->
<div class="dropdown-menu dropdown-hover">
    <button class="layui-btn layui-btn-primary icon-btn">
        Bottom <i class="layui-icon layui-icon-drop"></i> Center
    </button>
    <ul class="dropdown-menu-nav dropdown-bottom-center"><!-- 这里加控制方向的类 -->
        <div class="dropdown-anchor"></div>
        <li><a>1st menu item</a></li>
        <li><a>2nd menu item</a></li>
        <li><a>3rd menu item</a></li>
    </ul>
</div>

<!-- Bottom Right -->
<div class="dropdown-menu dropdown-hover">
    <button class="layui-btn layui-btn-primary icon-btn">
        Bottom Right <i class="layui-icon layui-icon-drop"></i>
    </button>
    <ul class="dropdown-menu-nav dropdown-bottom-right"><!-- 这里加控制方向的类 -->
        <div class="dropdown-anchor"></div>
        <li><a>1st menu item</a></li>
        <li><a>2nd menu item</a></li>
        <li><a>3rd menu item</a></li>
    </ul>
</div>
```
![](https://s2.ax1x.com/2019/06/06/VULo4J.png)


### 6.7.消息通知

```javascript
layui.use(['notice'], function(){
    var notice = layui.notice;
    
    // 通知样式
    notice.success({
        title: '消息通知',
        message: '你有新的消息，请注意查收!'
    });
    
    // 提示框样式，1成功、2失败、3警告、4加载、5信息(蓝色图标)
    notice.msg('Hello', {icon: 1});
    
});
```

**方法：**

 方法 | 参数 | 说明
 :--- | :--- | :---
 success(object) | 见下方 | 成功消息（绿色）
 warning(object) | 见下方 |  警告消息（黄色）
 error(object) | 见下方 |  错误消息（红色）
 info(object) |  见下方 | 通知消息（蓝色）
 show(object) | 见下方 |  自定义样式
  | 
 msg(string, object) | 内容，其他配置 |  提示框
  | 
 destroy() |  无 | 关闭全部
 hide(object, toast, closedBy) |  见下方 | 关闭指定的通知
 settings(object) | 见下方 |  统一设置默认值
 
 **参数：**
 
 参数 | 说明 | 默认值 | 可选值 
:---|:---|:---|:---
 title | 标题 | 无 | string类型
 message | 内容 | 无 |  string类型
 position | 显示位置 | topRight | 见下方
 transitionIn | 进入动画 | fadeInLeft | 见下方
 transitionOut | 退出动画 | fadeOutRight | 见下方
 timeout | 消失时间 | 5000 | 单位毫秒，false永不消失
 progressBar | 进度条 | true | true显示、false不显示
 balloon | 气泡效果 | false | true开启、false关闭
 close | 关闭按钮 | true | true显示，false不显示
 pauseOnHover | 鼠标滑过暂停消失时间 | true | true、false
 resetOnHover | 鼠标滑过重置消失时间 | false | true、false
 animateInside | 文字动画效果 | false | true开启、false关闭
 className | 自定义class | 无 | 多个用空格分隔
 theme | 主题 | light | light、dark
 audio | 音效 | 无 | 1，2，3，4，5，6
 | | | 
 image | 显示图片 | 无 | 图片地址
 imageWidth | 图片宽度 | 60 | 数字 
 buttons | 显示按钮 | [] | [ [ 'btn1', function(){} ], ['btn2', function(){} ] ] 
 overlay | 遮罩层 | false | true显示,false不显示
 drag | 滑动关闭 | true | true开启，false关闭
 layout | 布局类型 | 2 | 1标题和内容并排，2两排显示
 rtl | 布局方向 | false | false内容居左，true居右
 displayMode | 显示模式 | 0 | 0无限制，1同类型存在不显示，2同类型存在先移除
 targetFirst | 插入方式 | 自动 | true从上插入，false下插入，null自动
 onOpened | 打开后回调函数 | 无 | function
 onClosed | 关闭后回调函数 | 无 | function
 | | | 
 titleColor | 标题颜色 | 默认| 颜色单位
 titleSize | 标题大小 | 默认| 尺寸单位
 messageColor | 文字颜色 | 默认| 颜色单位
 messageSize | 文字大小 | 默认| 尺寸单位
 backgroundColor | 背景颜色 | 默认 | 颜色单位
 progressBarColor | 进度条颜色 | 默认 | 颜色单位
 maxWidth | 最大宽度 | null| 尺寸单位

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
    transitionIn: 'flipInX',
    onOpen: function(){
        console.log('callback abriu!');
    }
});   
```

**关闭指定的通知**

```javascript
notice.hide({}, document.querySelector('.toast')); 
```

- 参数一 &emsp; 重写一些参数，比如关闭动画等
- 参数二 &emsp; 根据自定义的className选择关闭的对象

<br>

### 6.8.标签输入框
模块名：tagsInput，使用方法：
```html
<input id="demoTagsInput" value="辣妹子,大长腿" class="layui-hide"/>

<script>
    layui.use(['jquery', 'tagsInput'], function () {
        var $ = layui.jquery;

        // 输入框样式
        $('#demoTagsInput').tagsInput();

        // 无边框样式
        $('#demoTagsInput').tagsInput({skin: 'tagsinput-default'});

        // BackSpace键可删除标签
        $('#demoTagsInput').tagsInput({removeWithBackspace: true});

        // 输入列表提示
        $('#demoTagsInput').tagsInput({
            skin: 'tagsinput-default',
            autocomplete_url: '../../json/tagsInput.json'
        });

    });
</script>
```
&emsp;只需要写一个input框，调用tagsInput方法渲染即可，回显数据写在value中用逗号分隔。

方法说明：

方法 | 说明 | 示例
--- | --- | ---
tagsInput() | 输入标签调用tagsInput()函数 | $('#tags').tagsInput();
tagExist() | 是否存在 | if ($('#tags').tagExist('xxx')){...}
addTag() | 增加标签 | $('#tags').addTag('xxx');
removeTag() | 删除标签 | $('#tags').removeTag('xxx');
importTags() | 导进一组tag列表，需要注意的是这样会将value里设置的默认tag替换掉(传空串为重置) | $('#tags').importTags('aaa,bbb,ccc');

参数说明：

参数 | 说明 | 默认值/示例
--- | --- | ---
defaultText | 提示文字 | +请输入
skin | 样式风格 | 'tagsinput-default'
removeWithBackspace | 回退键可删除已添加的标签 | false
focusWithClick | 点击已添加标签输入框获取焦点 | true
autocomplete_url | 自动提示接口url | 见示例
autocomplete | 接口配置 | 见示例
height | 设置高度 | '40px'
width | 设置宽度 | '100px'
interactive | 是否允许添加标签，false为阻止 | true
onAddTag | 增加标签的回调函数 | callback_function
onRemoveTag | 删除标签的回调函数 | callback_function
onChange | 改变一个标签时的回调函数 | callback_function
minChars | 标签的小最字符 | 0
maxChars | 最大字符，如果不设置或者为0，就是无限大 | 0

autocomplete参数：
```javascript
$('#demoTagsInput').tagsInput({
    autocomplete_url: '../../json/tagsInput.json',
    autocomplete: {
        type: 'post',
        data: {
            access_token: 'xxxxx'
        }
    }
});
```

回调函数：
```javascript
$('#demoTagsInput').tagsInput({
    skin: 'tagsinput-default',
    minChars : 2,
    height:'40px',
    onAddTag:function(tag){
        console.log('增加了'+tag)
    },
    onRemoveTag:function(tag){
        console.log('删除了'+tag)
    }
});
```

&emsp;type是请求方式，默认是get请求，data是额外参数，请求autocomplete_url会传递`name`参数(输入框的值)。

![](https://s2.ax1x.com/2019/07/11/Z26OyT.png)

> 此插件基于 [jQuery-Tags-Input](https://github.com/xoxco/jQuery-Tags-Input) 二次修改。

<br>

### 6.9.鼠标滚轮监听
模块名：mousewheel，使用方式：
```javascript
layui.use(['mousewheel'], function () {
    var $ = layui.jquery;

    // 滚动监听
    $('#xxx').on('mousewheel', function (event) {
        console.log(event.deltaX, event.deltaY, event.deltaFactor);
        event.stopPropagation();  // 阻止事件冒泡
        event.preventDefault();  // 阻止默认事件
    });

});
```

event对象中可以获取如下三个属性值：

- deltaX：值为负的（-1），则表示滚轮向左滚动。值为正的（1），则表示滚轮向右滚动。
- deltaY：值为负的（-1），则表示滚轮向下滚动。值为正的（1），则表示滚轮向上滚动。
- deltaFactor：增量因子。通过 deltaFactor * deltaX 或者 deltaFactor * deltaY 可以得到浏览器实际的滚动距离。

> 此插件来源于 [jquery-mousewheel](https://github.com/jquery/jquery-mousewheel) 。

<br>

### 6.10.二维码模块
模块名：QRCode，使用方式：
```html
<div id="xxx"></div>
<script>
    layui.use(['QRCode'], function () {
        var $ = layui.jquery;
        var QRCode = layui.QRCode;
    
        // 二维码
        var demoQrCode = new QRCode(document.getElementById("xxx"), {
            text: "Hello Word!",
            width: 101,  // 宽度
            height: 101,  // 高度
            colorDark: "#000000",  // 颜色
            colorLight: "#ffffff",  // 背景颜色
            correctLevel: QRCode.CorrectLevel.H
        });
        
        // 更换内容
        demoQrCode.makeCode("xxxxxxx");
    
    });
</script>
```

> 此插件来源于 [qrcodejs](https://github.com/davidshimjs/qrcodejs) 。

<br>

### 6.11.引导插件
模块名：introJs，使用方式：
```html
<p data-step="1" data-intro="这是步骤一">步骤一</p>
<p data-step="2" data-intro="这是步骤二">步骤二</p>
<script>
    layui.use(['introJs'], function () {
        var introJs = layui.introJs;
    
        // 初始化
        introJs().start();
    
    });
</script>
```

> 此插件基于 [intro.js](https://github.com/usablica/intro.js) 二次修改。

<br>

### 6.12.剪贴板
模块名：ClipboardJS，使用方式：
```html
<input id="foo" value="https://github.com/zenorocha/clipboard.js.git">
<button class="btn" data-clipboard-target="#foo">复制</button>
<script>
    layui.use(['ClipboardJS'], function () {
        var ClipboardJS = layui.ClipboardJS;
    
        var clipboard = new ClipboardJS('.btn');
        clipboard.on('success', function(e) {
            e.clearSelection();
        });
        
        clipboard.on('error', function(e) {
            console.error('Action:', e.action);
        });
    
    });
</script>
```

> 此插件来源于 [clipboard.js](https://zenorocha.github.io/clipboard.js) 。


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

> 关键代码就是在success回调中写上面那句话


### 7.5 弹窗宽度不能超出屏幕

&emsp;这个是针对手机屏幕下做了让弹窗宽度自适应，如果你需要让弹窗宽度超出屏幕如下代码即可：


```javascript
admin.open({
        title: '添加用户',
        content: $('#model').html(),
        success: function (layero, index) {
            $(layero).css('max-width', 'unset');   // 去掉max-width属性
        }
});
```

> success回调中去掉max-width属性


### 7.6 表单文字出现换行

&emsp;layui的表单左边的标题文字最多显示5个字，超出会换行，通过添加css修改宽度：

```css
#userForm .layui-form-label {
    width: 100px;  // 这里修改标题宽度
}

#userForm .layui-input-block {
    margin-left: 130px;  // 这里要比上面始终大30px
}
```

&emsp;`#userForm`是表单的id，加id避免影响其他表单样式：
```html
<form id="userForm" class="layui-form">
    <div class="layui-form-item">
        <label class="layui-form-label">活动起止时间</label>
        <div class="layui-input-block">
            <input type="text" class="layui-input"/>
        </div>
    </div>
    <div class="layui-form-item">
        <label class="layui-form-label">活动详细介绍</label>
        <div class="layui-input-block">
            <textarea class="layui-textarea" maxlength="200"></textarea>
        </div>
    </div>
</form>
```

### 7.7 侧边栏折叠图标放大
&emsp;侧边栏折叠后图标会进行放大，如果要修改大小，添加如下css：
```css
@media screen and (min-width: 750px) {
    .layui-layout-admin.admin-nav-mini .layui-side .layui-nav .layui-nav-item > a > .layui-icon {
        font-size: 18px;
    }
}
```
&emsp;修改font-size即可，如果不想放大，改成14px即可。

<br/>

## 8.表单弹窗专题

&emsp;&emsp;layui没有像bootstrap那样的直接写在页面中的模态弹窗，layui的弹窗是通过layer模块用js去弹出窗口，
这就意味着layui的弹窗功能更丰富、操作更灵活，当然灵活也就意味着对于新手来说使用更为复杂，下面来介绍使用弹窗的几种方式。

### 8.1.第一种 页面层弹窗
&emsp;页面层弹窗就是弹窗页面和列表页面写在一起，弹窗类型type=1：
```html
<button id="btnAddUser" class="layui-btn">添加</button>
<table id="tableUser" lay-filter="tableUser"></table>

<!-- 表单弹窗 -->
<script type="text/html" id="modelUser">
    <form id="modelUserForm" lay-filter="modelUserForm" class="layui-form model-form">
        <input name="userId" type="hidden"/>
        <div class="layui-form-item">
            <label class="layui-form-label">用户名</label>
            <div class="layui-input-block">
                <input name="nickName" placeholder="请输入用户名" type="text" class="layui-input" maxlength="20"
                       lay-verType="tips" lay-verify="required" required/>
            </div>
        </div>
        <div class="layui-form-item">
            <label class="layui-form-label">性别</label>
            <div class="layui-input-block">
                <input type="radio" name="sex" value="男" title="男" checked/>
                <input type="radio" name="sex" value="女" title="女"/>
            </div>
        </div>
        <div class="layui-form-item text-right">
            <button class="layui-btn layui-btn-primary" type="button" ew-event="closePageDialog">取消</button>
            <button class="layui-btn" lay-filter="modelSubmitUser" lay-submit>保存</button>
        </div>
    </form>
</script>

<!-- 表格操作列 -->
<script type="text/html" id="tableBarUser">
    <a class="layui-btn layui-btn-primary layui-btn-xs" lay-event="edit">修改</a>
    <a class="layui-btn layui-btn-danger layui-btn-xs" lay-event="del">删除</a>
</script>

<!-- js部分 -->
<script>
    layui.use(['layer', 'form', 'table', 'admin'], function () {
        var $ = layui.jquery;
        var layer = layui.layer;
        var form = layui.form;
        var table = layui.table;
        var admin = layui.admin;

        // 渲染表格
        var insTb = table.render({
            elem: '#tableUser',
            url: '../../json/user.json',
            cols: [[
                {type: 'numbers', title: '#'},
                {field: 'nickName', sort: true, title: '用户名'},
                {field: 'sex', sort: true, title: '性别'},
                {align: 'center', toolbar: '#tableBarUser', title: '操作', minWidth: 200}
            ]]
        });

        // 添加
        $('#btnAddUser').click(function () {
            showEditModel();
        });

        // 工具条点击事件
        table.on('tool(tableUser)', function (obj) {
            var data = obj.data;
            var layEvent = obj.event;
            if (layEvent === 'edit') { // 修改
                showEditModel(data);
            } else if (layEvent === 'del') { // 删除
                layer.msg('点击了删除', {icon: 2});
            }
        });

        // 显示表单弹窗
        function showEditModel(mUser) {
            admin.open({
                type: 1,
                title: (mUser ? '修改' : '添加') + '用户',
                content: $('#modelUser').html(),
                success: function (layero, dIndex) {
                    var url = mUser ? '/updateUser' : '/addUser';
                    // 回显数据
                    form.val('modelUserForm', mUser);
                    // 表单提交事件
                    form.on('submit(modelSubmitUser)', function (data) {
                        layer.load(2);
                        $.post(url, data.field, function (res) {
                            layer.closeAll('loading');
                            if (res.code == 200) {
                                layer.close(dIndex);
                                layer.msg(res.msg, {icon: 1});
                                insTb.reload();
                            } else {
                                layer.msg(res.msg, {icon: 2});
                            }
                        }, 'json');
                        return false;
                    });
                }
            });
        }

    });
</script>
```

&emsp;弹窗的参数`type: 1`表示弹窗类型是页面层类型而不是iframe层类型，`content: $("#modelUser").html()`是指定弹窗的内容，
表单使用`<script type="text/html">`而不是使用div，这样表单页面不会直接显示在列表下面，需要注意的是，操作表单的代码，
包括表单的事件监听都要写在弹窗的`success`回调里面，因为layer弹窗是把表单的html**复制一份**动态插入到页面上，而不是把那一段代码变成模态窗，
所以事件绑定必须写在success里面。

> 这种方式表单和表格在一个页面上面，数据传递、页面相互操作比较方便，不涉及到iframe父子传值的问题，推荐新手使用这种方式

<br/>


### 8.2.第二种 iframe层弹窗

&emsp;使用iframe类型的弹窗可以让表单页面和表格页面分开，逻辑更清晰。

弹窗页面，userForm.html：
```html
<html>
<head>
    <link rel="stylesheet" href="../../assets/libs/layui/css/layui.css"/>
    <link rel="stylesheet" href="../../assets/module/admin.css"/>
</head>
<body>
<form id="modelUserForm" lay-filter="modelUserForm" class="layui-form model-form">
    <input name="userId" type="hidden"/>
    <div class="layui-form-item">
        <label class="layui-form-label">用户名</label>
        <div class="layui-input-block">
            <input name="nickName" placeholder="请输入用户名" type="text" class="layui-input" maxlength="20"
                   lay-verType="tips" lay-verify="required" required/>
        </div>
    </div>
    <div class="layui-form-item">
        <label class="layui-form-label">性别</label>
        <div class="layui-input-block">
            <input type="radio" name="sex" value="男" title="男" checked/>
            <input type="radio" name="sex" value="女" title="女"/>
        </div>
    </div>
    <div class="layui-form-item text-right">
        <button class="layui-btn layui-btn-primary" type="button" ew-event="closePageDialog">取消</button>
        <button class="layui-btn" lay-filter="modelSubmitUser" lay-submit>保存</button>
    </div>
</form>

<!-- js部分 -->
<script type="text/javascript" src="../../assets/libs/layui/layui.js"></script>
<script type="text/javascript" src="../../assets/js/common.js"></script>
<script>
    layui.use(['layer', 'form', 'admin'], function () {
        var $ = layui.jquery;
        var layer = layui.layer;
        var form = layui.form;
        var admin = layui.admin;

        var mUser = parent.mSelUser;  // 获取列表页面传递的数据，也可以使用top.xxx
        // 回显数据
        form.val('modelUserForm', mUser);
        // 表单提交事件
        form.on('submit(modelSubmitUser)', function (data) {
            layer.load(2);
            var url = mUser ? '/updateUser' : '/addUser';
            $.post(url, data.field, function (res) {
                layer.closeAll('loading');
                if (res.code == 200) {
                    layer.msg(res.msg, {icon: 1});
                    parent.userFormIsOk = true;  // 设置操作成功的标识，也可以使用top.xxx
                    admin.closeThisDialog();  // 关闭当前iframe弹窗
                } else {
                    layer.msg(res.msg, {icon: 2});
                }
            }, 'json');
            return false;
        });
        
    });
</script>
</body>
</html>

```

表格页面，list.html：
```html
<button id="btnAddUser" class="layui-btn">添加</button>
<table id="tableUser" lay-filter="tableUser"></table>

<!-- 表格操作列 -->
<script type="text/html" id="tableBarUser">
    <a class="layui-btn layui-btn-primary layui-btn-xs" lay-event="edit">修改</a>
    <a class="layui-btn layui-btn-danger layui-btn-xs" lay-event="del">删除</a>
</script>

<!-- js部分 -->
<script>
    layui.use(['layer', 'table', 'admin'], function () {
        var $ = layui.jquery;
        var layer = layui.layer;
        var table = layui.table;
        var admin = layui.admin;

        // 渲染表格
        var insTb = table.render({
            elem: '#tableUser',
            url: '../../json/user.json',
            cols: [[
                {type: 'numbers', title: '#'},
                {field: 'nickName', sort: true, title: '用户名'},
                {field: 'sex', sort: true, title: '性别'},
                {align: 'center', toolbar: '#tableBarUser', title: '操作', minWidth: 200}
            ]]
        });

        // 添加
        $('#btnAddUser').click(function () {
            showEditModel();
        });

        // 工具条点击事件
        table.on('tool(tableUser)', function (obj) {
            var data = obj.data;
            var layEvent = obj.event;
            if (layEvent === 'edit') { // 修改
                showEditModel(data);
            } else if (layEvent === 'del') { // 删除
                layer.msg('点击了删除', {icon: 2});
            }
        });

        // 显示表单弹窗
        function showEditModel(mUser) {
            window.mSelUser = mUser;  // 传递数据到表单页面，也可以使用top.xxx
            window.userFormIsOk = false;  // 重置表单操作成功标识，也可以使用top.xxx
            admin.open({
                type: 2,
                title: (mUser ? '修改' : '添加') + '用户',
                content: 'userForm.html',
                end: function () {
                    if (window.userFormIsOk) {  // 判断表单操作成功标识，也可以使用top.xxx
                        insTb.reload();  // 成功刷新表格
                    } 
                }
            });
        }

    });
</script>
```

&emsp;弹窗参数`type: 2`就表示是一个iframe类型的弹窗，content参数写表单的页面url，
然后在end回调里面判断操作成功的标识然后刷新表格，注意是end而不是success，这样做的好处就是，
弹窗页面跟表格独立开了，大大减少了每个页面的代码量，将业务逻辑分开，坏处就是需要借助window和parent来做父子页面的参数传递，
还有一个坏处就是，如果页面有下拉框、日期选择控件等元素，它们的范围不能超出弹窗的范围，会导致弹窗出现滚动条，甚至不显示出来。

&emsp;那么有没有办法既能让表单页面独立，又能让表单里面的下拉框、日期等组件能够超出弹窗的范围呢，请看第三种方式。

&emsp;这里演示的是使用window和parent来传递参数，也可以使用admin.putTempData(key, value)
和admin.getTempData(key)来操作sessionStorage实现参数传递，如果你被window、parent、top这三个绕晕了，那就使用这个方法。

> 注意：如果你用top.layui.admin.open打开弹窗，那就不要用window和parent传递参数，全部使用top

<br/>

### 8.3.第三种 url方式弹窗
&emsp;url方式弹窗就是使用url参数将弹窗页面独立出来：

弹窗页面，userForm.html：
```html
<form id="modelUserForm" lay-filter="modelUserForm" class="layui-form model-form">
    <input name="userId" type="hidden"/>
    <div class="layui-form-item">
        <label class="layui-form-label">用户名</label>
        <div class="layui-input-block">
            <input name="nickName" placeholder="请输入用户名" type="text" class="layui-input" maxlength="20"
                   lay-verType="tips" lay-verify="required" required/>
        </div>
    </div>
    <div class="layui-form-item">
        <label class="layui-form-label">性别</label>
        <div class="layui-input-block">
            <input type="radio" name="sex" value="男" title="男" checked/>
            <input type="radio" name="sex" value="女" title="女"/>
        </div>
    </div>
    <div class="layui-form-item text-right">
        <button class="layui-btn layui-btn-primary" type="button" ew-event="closePageDialog">取消</button>
        <button class="layui-btn" lay-filter="modelSubmitUser" lay-submit>保存</button>
    </div>
</form>

<script>
    layui.use(['layer', 'form', admin'], function () {
        var $ = layui.jquery;
        var layer = layui.layer;
        var form = layui.form;
        var admin = layui.admin;

        var mUser = window.mSelUser;  // 列表页面传递的数据，也可以使用top.xxx
        // 回显数据
        form.val('modelUserForm', mUser);
        // 表单提交事件
        form.on('submit(modelSubmitUser)', function (data) {
            layer.load(2);
        var url = mUser ? '/updateUser' : '/addUser';
            $.post(url, data.field, function (res) {
                layer.closeAll('loading');
                if (res.code == 200) {
                    layer.msg(res.msg, {icon: 1});
                    window.userFormIsOk = true;  // 设置操作成功的标识，也可以使用top.xxx
                    admin.closeDialog('#modelUserForm');  // 关闭页面层弹窗
                } else {
                    layer.msg(res.msg, {icon: 2});
                }
            }, 'json');
            return false;
        });
        
    });
</script>
```
&emsp;注意这里，页面不需要写`<html><body>`这些东西，它是一个html片段，不是完整的html页面。

表格页面，list.html：
```html
<button id="btnAddUser" class="layui-btn">添加</button>
<table id="tableUser" lay-filter="tableUser"></table>

<!-- 表格操作列 -->
<script type="text/html" id="tableBarUser">
    <a class="layui-btn layui-btn-primary layui-btn-xs" lay-event="edit">修改</a>
    <a class="layui-btn layui-btn-danger layui-btn-xs" lay-event="del">删除</a>
</script>

<!-- js部分 -->
<script>
    layui.use(['layer', table', 'admin'], function () {
        var $ = layui.jquery;
        var layer = layui.layer;
        var table = layui.table;
        var admin = layui.admin;

        // 渲染表格
        var insTb = table.render({
            elem: '#tableUser',
            url: '../../json/user.json',
            cols: [[
                {type: 'numbers', title: '#'},
                {field: 'nickName', sort: true, title: '用户名'},
                {field: 'sex', sort: true, title: '性别'},
                {align: 'center', toolbar: '#tableBarUser', title: '操作', minWidth: 200}
            ]]
        });

        // 添加
        $('#btnAddUser').click(function () {
            showEditModel();
        });

        // 工具条点击事件
        table.on('tool(tableUser)', function (obj) {
            var data = obj.data;
            var layEvent = obj.event;
            if (layEvent === 'edit') { // 修改
                showEditModel(data);
            } else if (layEvent === 'del') { // 删除
                layer.msg('点击了删除', {icon: 2});
            }
        });

        // 显示表单弹窗
        function showEditModel(mUser) {
            window.mSelUser = mUser;  // 传递数据到表单页面，也可以使用top.xxx
            window.userFormIsOk = false;  // 重置表单操作成功标识，也可以使用top.xxx
            admin.open({
                title: (mUser ? '修改' : '添加') + '用户',
                url: 'userForm.html',
                end: function () {
                    if (window.userFormIsOk) {  // 判断表单操作成功标识，也可以使用top.xxx
                        insTb.reload();
                    } 
                },
                success: function (layero, dIndex) {
                    // 弹窗超出范围不出现滚动条
                    $(layero).children('.layui-layer-content').css('overflow', 'visible');
                }
            });
        }

    });
</script>
```

&emsp;这里与iframe弹窗的区别是，使用`url`参数指定表单的页面链接，而不是使用content，url这个参数是admin.open扩展的，layer不具备，
这样做会把表单页面的html内容使用ajax加载到弹窗中，而不是iframe嵌入，这样表单里面的下拉框、日期等组件就可以超出弹窗的范围，不会导致弹窗出现滚动条。

&emsp;需要注意的是，参数传递不是widnow和parent，而是全部是window，因为表单页面和列表页面虽然是两个页面，但是是通过ajax加载在一个页面上的
，如果使用top.layui.admin.open打开弹窗那就全部使用top。

> 注意：表单页面是html片段，不是完整的html，这点不要忘记了，另外表单页面和表格页面不要出现重复的id，因为最终是在一个页面上

### 8.4.三种方式选择指南

方式 | 推荐 | 理由
:--- | :--- | :---
第一种 | 建议新手使用这种 | 不涉及两个页面传值问题
第二种 | 推荐表单跟表格无交互使用，比如详情 | iframe弹窗不建议做表单
第三种 | 表单弹窗、页面有交互建议使用 | 页面传值方便，不存在问题

&emsp;父子页面参数传递除了使用window、parent、top之外，还可以使用admin.putTempData(key, value)
和admin.getTempData(key)来操作sessionStorage实现参数传递，如果你被window、parent、top这三个绕晕了，
那就使用这个方法，这个方法是操作本地存储sessionStorage，从效率上来讲没有window和parent高效。


<br/>

### 8.5.admin.modelForm方法
&emsp;此方法是把layer弹窗自带的确定按钮绑定成表单的提交按钮。

```html

<!-- 表单弹窗 -->
<script type="text/html" id="modelUser">
    <div class="layui-form-item">
        <label class="layui-form-label">用户名</label>
        <div class="layui-input-block">
            <input name="nickName" placeholder="请输入用户名" type="text" class="layui-input" maxlength="20"
                   lay-verType="tips" lay-verify="required" required/>
        </div>
    </div>
    <div class="layui-form-item">
        <label class="layui-form-label">性别</label>
        <div class="layui-input-block">
            <input type="radio" name="sex" value="男" title="男" checked/>
            <input type="radio" name="sex" value="女" title="女"/>
        </div>
    </div>
</script>

<!-- js部分 -->
<script>
    layui.use(['layer', 'form', admin'], function () {
        var $ = layui.jquery;
        var layer = layui.layer;
        var form = layui.form;
        var admin = layui.admin;
        
        admin.open({
            type: 1,
            title: '添加用户',
            btn: ['确定', '取消'],
            content: $('#modelUser').html(),
            success: function (layero, dIndex) {
                // 把确定按钮绑定表单提交，参数二是给按钮起一个lay-filter，参数三是给表单起一个lay-filter
                admin.modelForm(layero, 'demoFormSubmit', 'demoForm');
                
                // 给表单赋值
                form.val('demoForm', {nickName: '张三', sex: '男'}); 
                
                // 监听表单提交
                form.on('submit(demoFormSubmit)', function (data) {
                    layer.msg(JSON.stringify(data.field));
                    return false;
                });
                
            },
            yes: function () {
                // 确定按钮方法什么都不要操作
            }
        });

    });
</script>
```

&emsp;admin.modelForm()这个方法会把弹窗外面包一个form，然后把确定按钮加lay-submit，所以你的表单页面不需要写form和确定、关闭按钮，只需要写表单项。

&emsp;这个方法的使用场景为你想要表单的按钮固定，只滚动表单内容部分，可以用这个操作，
当然以前那种表单弹窗的写法(就是自己写确定和取消按钮那种写法)也是支持固定按钮的，
前面css组件样式中有介绍。


## 9.扩展图标

### 9.1 引入扩展图标
1. 成为iconfont真保蟹项目成员；
2. 搜索并添加想要的图标到真保蟹项目，重新生成在线链接；
3. 复制生成的链接到`insurance-zbx\src\main\resources\static\assets\js\common.js`文件中修改即可：

![iconfont](http://zhenbaoxie.profacty.cn/data/20190530/1559196589853977699484.png)

![引入图标](http://zhenbaoxie.profacty.cn/data/20190530/1559196420767445534839.png)

### 9.2 使用扩展图标
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


