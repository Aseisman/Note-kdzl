# 基本定义
* 命名约定
  + 类：大驼峰
  + 方法：小驼峰
  + 变量：小驼峰
  + 静态全局变量：只有大写+下划线
  + 属性名称：小驼峰

* 架构：mvc架构（1-4）mvvm架构（5～）

  + 基本方法：
    - Ext.onready(function,作用域,延迟时间等):在DOM构建完后使用，在window.onload前使用
    - Ext.define 创建类，可以继承，也可以被继承。
    - Ext.apply(this,o)//复制o的所有属性到自身
    - Ext.applyIf(this,o)//复制o的所有属性，如果有同名的则用原来的，抛弃o的
    - Ext.require([路径数组])//动态加载js文件

  + 属性  
    - mixins：多继承（在define中，继承其他的类）
    - statics：定义静态变量
    - config：属性包装器，提供set和get方法
    - constructor:function(){} //构造函数
    

  + EXT的对象选择
    - 选择html控件：Ext.getDom(对象的ID)
    - 选择EXT元素：Ext.get(对象的ID)
    - 选择EXT组件：Ext.getCmp(对象的ID) 
  
  + 生命周期
    - initComponent
    - render
    - destroy

* 可定义class类
  + Ext.define()定义类
  + Ext.create()创建类
  + 可new 可继承（define的时候）

* 组件的继承关系
  + 使用Ext.extend进行继承扩展，最好不设置单独的xtype
  + 后用Ext.reg注册

* 容器概念：Ext.container. Container是Ext.js中所有容器的基类
  + 可以嵌套，有很多的组建样式：panel, form, tab, container. Viewport
  + Ext.Viewport 创建一个可视区域 一个页面上只允许存在一个viewport，把子元素放在items里面。viewport会占用一整个body，随着浏览器高度宽度变化而变化。
  + Ext.Panel布局提供一定的宽度高度，不会随浏览器变而变
  ```js
      var panel = ExtPanel();
      window.onresize = function() {
          panel.setHeight(document.documentElement.clientHeight);
      };
  ```

* 布局
  + absolute布局：Anchor 布局方式，并增加了X/Y配置选项对子组件进行定位，Absolute布局的目的是为了扩展布局的属性，使得布局更容易使用。
  + accordion布局：手风琴布局
  + anchor布局：使组件固定于父容器的某一个位置，子元素尺寸相对于容器的尺寸，按比例缩放。每个自组件都有一个anchor属性，用来配置此子组件在父容器中所处的位置。
     ```js
      anchor: "75%,25%", //宽度为父的75，高度为父的25
      anchor: "-295,-300". //相对于父右边距295，底部300
      anchor: "-200,10%" //混合模式
    ```
  + border布局：border布局也称边界布局，他将页面分隔为west, east, south, north, center这五个部分，我们需要在其items中指定使用region参数为其子元素指定具体位置。
  + card布局：用来管理多个子组件。任何时刻只能显示一个自组件。`layout:'card'`
    - 使用getNext()或者getPrev()来下一个或者上一个
    - setActiveItem进行设置到哪个界面
  + fit布局：子元素自动填满整个父元素。设置宽度无效，在fit布局中放置多个组件，只会显示第一个子元素。
  + column列布局：每列的宽度可以指定一个百分比或者是一个固定的宽度。
  + vbox 垂直布局
  + hbox 横向布局


* Grid必须配置的两个
  + store(数据格式组织，存储)
  + grid.columns 展示数据格式

```js
<script type = "text/javascript">
    Ext.onReady(function() {
        var store = Ext.create('Ext.data.Store', {
            fields: ["cataId", "cataNo", "cataRemark", "cataTitle", "cataObjectName", "cataeditstatusName",
                "cataPublishName", "cataEndDate", "holyUpdateTime", "catapushtime"
            ],
            pageSize: 20, //页容量5条数据
            //是否在服务端排序 （true的话，在客户端就不能排序）
            remoteSort: false,
            remoteFilter: true,
            proxy: {
                type: 'ajax',
                url: '/Tools/106.ashx?method=getAllCatalog',
                reader: { //这里的reader为数据存储组织的地方，下面的配置是为json格式的数据，例如：[{"total":50,"rows":[{"a":"3","b":"4"}]}]
                    type: 'json', //返回数据类型为json格式
                    root: 'rows', //数据
                    totalProperty: 'total' //数据总条数
                }
            },
            sorters: [{
                //排序字段。
                property: 'ordeId',
                //排序类型，默认为 ASC 
                direction: 'desc'
            }],
            autoLoad: true //即时加载数据
        });

        var grid = Ext.create('Ext.grid.Panel', {
            renderTo: Ext.getBody(),
            store: store,
            height: 400,
            width: 800,
            selModel: {
                selType: 'checkboxmodel'
            }, //选择框
            columns: [{
                    text: '型录ID',
                    dataIndex: 'cataId',
                    align: 'left',
                    maxWidth: 80
                },
                {
                    text: '型录编号',
                    dataIndex: 'cataNo',
                    maxWidth: 120
                },
                {
                    text: '助记标题',
                    dataIndex: 'cataTitle',
                    align: 'left',
                    minWidth: 80
                },
                {
                    text: '应用对象',
                    dataIndex: 'cataObjectName',
                    maxWidth: 80,
                    align: 'left'
                },
                {
                    text: '发布状态',
                    dataIndex: 'cataPublishName',
                    maxWidth: 80
                },
                {
                    text: '活动截止日期',
                    dataIndex: 'cataEndDate',
                    xtype: 'datecolumn',
                    dateFormat: 'Y-m-d H:i:s'
                },
                {
                    text: '更新时间',
                    dataIndex: 'holyUpdateTime',
                    xtype: 'datecolumn',
                    dateFormat: 'Y-m-d H:i:s'
                },
                {
                    text: '发布时间',
                    dataIndex: 'catapushtime',
                    xtype: 'datecolumn',
                    dateFormat: 'Y-m-d H:i:s'
                }
            ],
            bbar: [{
                xtype: 'pagingtoolbar',
                store: store,
                displayMsg: '显示 {0} - {1} 条，共计 {2} 条',
                emptyMsg: "没有数据",
                beforePageText: "当前页",
                afterPageText: "共{0}页",
                displayInfo: true
            }],
            listeners: {
                'itemclick': function(view, record, item, index, e) {
                    Ext.MessageBox.alert("标题", record.data.cataId);
                }
            },
            tbar: [{
                    text: '新增',
                    iconCls: 'a_add',
                    handler: showAlert
                }, "-",
                {
                    text: '编辑',
                    iconCls: 'a_edit2',
                    handler: showAlert
                }, "-",
                {
                    text: '停用/启用',
                    iconCls: 'a_lock'
                }, "-",
                {
                    text: '发布',
                    iconCls: 'a_upload',
                    handler: showAlert
                }, "-",
                {
                    text: '组织型录',
                    iconCls: 'a_edit',
                    handler: showAlert
                }, "-",
                {
                    text: '管理商品',
                    iconCls: 'a_edit',
                    handler: showAlert
                }, "-",
                "->", {
                    iconCls: "a_search",
                    text: "搜索",
                    handler: showAlert
                }
            ],
        });

        function showAlert() {
            var selectedData = grid.getSelectionModel().getSelection()[0].data;

            Ext.MessageBox.alert("标题", selectedData.cataId);
        }
    }); 
</script>
```

* 事件
  - listeners：自定义监听函数（底层就是addeventlistener）
  - fireEvent：当前对象调用fireEvent可以由继承当前对象的父元素进行调用，相当于emit，调用父元素的方法。


* Template语法:
  - `<tpl for=".">{name}</tpl>`遍历store中的数据时,直接表明属性即可
  - `<tpl for="."><tpl for="urls">{#} {.}</tpl></tpl>`遍历数组时,采用{.}来表示当前的值
  - `<tpl for="."><tpl for="user">{username}</tpl></tpl>`遍历对象时,直接表明属性即可
  - `<tpl for=".">{[this.formatTime(values)]}</tpl>` & `formatTime(values){return new Date(values.time).format('Y-m-d H:i:s');}`调整显示格式时,调用方法



* 生命周期
  - 初始化: 
    - 注册事件(所有继承于Ext.Component的组件都会默认拥有的基本事件enable/disable,show,hide,render,destory,state restore,state save)
    - 组件注册实例
    - initcomponent初始化函数: 会在构造函数中被调用,子类到父类的这么一个顺序,因此我们在写子类的时候,必须加上一句xxx.superClass.initComponent.call(this)来调用父类的initcomponent进行执行.**扩展组件的入口**
  - 渲染阶段
    - beforeRender函数:渲染之前调用,
    - onRender函数:渲染期间调用,这个与initComponent一致,会从子类到父类这么一个顺序,开始渲染,所以记得xxx.superClass.onRender.call(this)来调用父类的onRender
    - 不隐藏组件:大多数组件都会通过设置像 x-hidden 这个样式来使它隐藏。当 autoShow 设置为true 时，这个隐藏功能的样式会被移除。
    - afterRender函数:渲染过后
  - 销毁阶段
    - beforeDestory
    - onDestory

# API
* Ext.create（“Ext.panel”, {参数}）： 创建对象

* Ext. Viewport 创建一个可视区域 一个页面上只允许存在一个viewport，把子元素放在items里面。viewport会占用一整个body，随着浏览器高度宽度变化而变化。
* Ext. Panel布局提供一定的宽度高度，不会随浏览器变而变

```js
var panel = ExtPanel();
window.onresize = function() {
    panel.setHeight(document.documentElement.clientHeight);
};
```

* Ext.addEvents方法:注册监听函数,与listeners中函数功能相同

* 先viewport、panel再布局，后组件
