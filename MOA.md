# 开发流程
1. components/v7/router/allmodules.js中添加路由

2. page中引入对应的二级页面（index引入对应的js，对应js中继承我们的SF.page.AbstractPage);同时在这里进行判断，是否是营销版等；在对应js文件中进行引入我们business下的对应的js，继承了SF.page.AbstractPage的js相当于总页面，而business下对应的js则是这个页面里面需要展示的组件。

3. business下的js继承了我们的card布局（想用啥用啥），进行我们页面的布局，然后对应上我们导入的组件进行展现出页面。

4. 开发过程中，如果想保持在原来的界面，可以在控制台调用SF.totest()，即在我们的url后面标记上我们当前的界面

5. SF 与SFX 与B等命名空间有俗称约定的规则，对于B即business下的文件，SFX则是对于components下的framework下的文件（SFX.framework.xxx） SF则是对应上components/framework/core

> 原理：Ext.ns()，命名空间，类似于Java的包一样，可以把一些东西隐藏起来,不用暴露在原型链上。

6. 如果是一些自定义的组件，可放置于独立的文件夹中进行开发，不需要放在components中，因为components在一开始是全部加载的，而不同的页面，在显示的时候才需要加载。

7. 在开发的时候如果需要测试到PC端的功能,在终端 Ext.isPC=true. 判断我们当前环境为pc端即可.

8. 开发过程中,对于EXT的组件继承,我们需要在initComponent中执行xxx.superclass.initComponent.call(this),从而调用我们父类的初始化函数,同理,onLayout,onDestory如若有继承我们的父类,也需要调用父类方法进行调用.(用处: 调用我们的父类initComponent等,是因为我们在父类的构造函数中,初始化了一些自定义的方法,比如我们的mainpanel的hideRighitem等,我们需要去初始化父类从而在原型链上加上对应的方法或者属性)


# 基础常用API小技巧

1. SFX.getBuyInfo// 获取购买产品的信息 SFX.PRODUCT_ID.MARKETING_ID

2. SFX.getBuyVersion// 获取购买产品的版本

```js
//现有的版本(（电销版、进销存版）专业版，专业版+，电销版，电销版+，进销存版，进销存版+)
//return PRO(专业版) PROPLUS(专业版+) PHONESALE(电销版) PHONESALEPLUS(电销版+) JXC(进销存版)
// JXCPLUS(进销存版+)  NORMAL(普通)
SFX.version = {
    pro: 'PRO',
    proPlus: 'PROPLUS',
    phoneSale: 'PHONESALE',
    phoneSalePlus: 'PHONESALEPLUS',
    jxc: 'JXC',
    jxcPlus: 'JXCPLUS',
    normal: 'NORMAL',
    phoneSaleJxc: 'PHONESALE-JXC', //电销版、进销存版
    marketing: 'MARKETING', // 营销版
    marketingJxcPlus: 'MARKETING-JXCPLUS', // 营销版、进销存版+
    marketingPhoneSaleJxc: 'MARKETING-PHONESALE-JXC', // 营销版、电销版、进销存版
    marketingJxc: 'MARKETING-JXC', // 营销版、进销存
    marketingPhoneSalePlus: 'MARKETING-PHONESALEPLUS', // 营销版、电销版+
    marketingPhoneSale: 'MARKETING-PHONESALE', // 营销版、电销版
};
//商品ID
SFX.PRODUCT_ID = {
    PROFESSIONAL_ID: '144705271616603025848', //专业版id
    PHONESALE_ID: '144705271616603025874', //电销版id
    JXC_ID: '144705271616603025879', //进销存版id
    SMART_PHONE_ID: '144705271616603025876', // 智能话机的id
    CLOUDPHONE_CHARGE: '144705271616603025888', // 云电话充值
    NUMBERCHECK_ID: '144705271616603025893', // 号码智检额度
    MARKETING_ID: '144705271616603025895', // 营销版id
};
```

3. 独立封装出来card布局的前进后退：/business/HistoryCard.js  前进后退通过 forwardCard & backToCard 来完成

4. this.findCmp():获取当前的组件（获取的组件需要用name去标识，将name传入findCmp为参数），然后setActiveItem，可以对card布局进行替换

5. 对于SFX.framework封装出来的比较常用的功能有 showRightItem 和 hideRightItem两个方法,目的是隐藏右边弹窗,只有继承了SFX.framework.MainPanel才有.

6. supposeVue 配置支持vue组件.

7. $action标志,是我们对于html模板引擎中的字段进行判断,并转为对应的方法,比如我们的`<span action="loadMore">加载更多...</span>`,对于这个action中的话我们可以在对应的类组件中补充loadMore方法,并开启$action=true,即可.

8. JsonStore读取本地mock数据
```js
    var dataJson = {
      'root': [{ 'pid': 1, 'name': '张三',"landing":"K12",'phone': 18657322233, 'channel': '百度', "create_time": new Date() },
      { 'pid': 2, 'name': "李四","landing":"成人培训", 'phone': 18657322233, 'channel': '头条', "create_time": new Date() },
      { 'pid': 3,"landing":"成人培训", 'name': "李四", 'phone': 18657322233, 'channel': '头条', "create_time": new Date() }, 
      { 'pid': 4,"landing":"K12", 'name': "李四", 'phone': 18657322233, 'channel': '头条', "create_time": new Date() }]
    }
    this.store = new Ext.data.JsonStore({
      root: 'root',
      data: dataJson,
      idProperty: "pid",
      fields: ['name', 'channel', 'create_time', 'phone','landing'],
    })
```

9. SFX.viewport.openPage:打开某个页面

10. PC端进行调试的时候,需要改package.json中的chromium-args参数,最后加个s,同时,把ip改成4433端口 (nwjs的功劳:把chrome搞成pc端)
    

# 自定义封装的一些grid、store、form等详细API

1. MoaGrid：表格基于Ext.grid.GridPanel自定义封装

> 最基础的border cls name等是继承于我们的container 

| 属性               | 类型            | 含义                           |
| ------------------ | --------------- | ------------------------------ |
| **pagingButtons**  | []              | 底部分页控件的按钮             |
| saveScrollState    | boolean         | 保存滚动条所在的位置           |
| stripeRows         | boolean         | 斑马线显示                     |
| **storeFields**    | []              | 定义表格列字段的数组           |
| **paging**         | boolean         | 启用分页工具条                 |
| supportedPageSizes | boolean         | false or "20/50/100"           |
| **tbar**           | []              | 顶部工具条的配置               |
| enableSM           | boolean         | 启用复选框                     |
| idProperty         | string          | 每一行的唯一标识               |
| selectAll          | boolean         | 选择所有                       |
| **bindScope**      | false or string | 绑定当前的this指向             |
| enableHdMenu       | boolean         | 使用头部菜单栏（项目一般不用） |

```js
viewConfig: {
      onRowOut: function (e) {
        var row = e.getTarget('.x-grid3-row');
        if (row && !e.within(row, true)) {
          row = Ext.fly(row);
          row.removeClass('x-grid3-row-over');
        }
      },
      autoFill: true,
      forceFit: true
    },
```

方法：需要重写的有：createStore、createCols、onRefresh

* createStore: 创建JSONStore

* createBBar：创建底部导航栏，继承我们的MoaGrid后可引入pageConfig对象的配置,格式如下：
```js
  pageConfig: {
    supportedPageSizes: '20/50/100/200',
    paramNames: {
      dir: "dir",
      filter: "filter",
      limit: "count",
      sort: "sort",
      start: "skip"
    },
    doLoad(skip) {
      const o = {
        param: {}
      }, pn = this.getParams();
      const filter = this.store.baseParams.param.filter || undefined
      const sort_type = this.store.baseParams.param.sort_type

      o.param[pn.start] = skip;
      o.param[pn.limit] = this.pageSize;
      o.param.filter = filter
      o.param.sort_type = sort_type

      if (skip) {
        if ((skip - this.cursor) > 0) {
          o.param.reverse = false;
        } else {
          o.param.reverse = true;
        }
      }
      if (this.fireEvent('beforechange', this, o) !== false) {
        this.store.load({ params: o });
      }
    },
    onLoad(store, r, o) {
      if (this.usePaging) {
        if (!this.rendered) {
          this.dsLoaded = [store, r, o];
          return;
        }
        var p = this.getParams();
        this.cursor = (o.params && o.params.param && o.params.param[p.start]) ? o.params.param[p.start] : 0;
        var d = this.getPageData();
        this.updateInfo(d);
        this.fireEvent('change', this, d);
      }
    }
  }
```
同时对于我们的底部导航栏，又抽离独立开封装成SFX.PagingToolbar(pageConfig);

* createCols: 创建行样式，对于表格的每一行数据进行自定义
  - searchable 与searchCmp 的作用就是 自定义鼠标hover弹出的窗口
  - 通用组件(均继承我们的HdSearchCtn):
    - SFX.hdsearch.Textfield(单个文本框+按钮) 
    - UserdeptTree(成员部门选择树) 
    - SDatePicker(日期季度选择器)
    - RangeField(区间筛选) 
    - Provider(筛选排序) 
    - ProductSearchPanel(商品搜索界面) 
    - DDatePicker(col时间选择器)
    - Customer(客户选择器)
    - Config(可以复用的一些升序降序)
    - ClassifyTree(商品分类选择树)
    - CheckRadioGroup(多选框)   问题：无法默认选中，即使代码中包含着checked=checked，但仍然无法默认选中
    - AllProductionSearchPanel(全部商品)
  
```js
            createCols() {
                const cols = [{
                    header: '渠道',
                    dataIndex: "channel",
                    searchable:true,
                    searchCmp:Ext.extend(SFX.hdsearch.Textfield,{
                        name:"seatchChannel",
                        hideTextfield:true,
                        vacancyBtn:true,
                        vacancyBtnText:"从高到低排序",
                        vacancyFilterText: '从高到低排序',
                    }),
                    renderer: function (v, meta, record) {
                        return v ? v : '-';
                    }
                }];
                this.columns = cols;
            },
```

* onRefresh: 刷新方法

2. SFX.PagingToolbar：底部导航栏，传入pageConfig配置

| 属性               | 类型         | 含义                                             |
| ------------------ | ------------ | ------------------------------------------------ |
| alias              | string       | 起别名                                           |
| pageSize           | number       | 无baseParam的limit时，这个值充当默认的页数量大小 |
| pagingAlign        | "center" ... | 布局                                             |
| enableElAction     | boolean      |                                                  |
| selectAll          | boolean      | 选择所有                                         |
| **pagingButtons**  | []           | 底部分页控件的按钮                               |
| supportedPageSizes | boolean      | false or "20/50/100"                             |

pagConfig配置是为了给PagingToolbar的ToolBar的属性一般有：
| 属性               | 类型                                                                                                          | 含义                                            |
| ------------------ | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| paramNames         | `{start : 'start',limit : 'limit',sort : 'sort',dir : 'direction',filterCol : 'filterCol',filter : 'filter'}` | HttpProxy请求数据时携带的参数                   |
| supportedPageSizes | '20/50/100/200'                                                                                               | 页的大小                                        |
| doload             | function                                                                                                      | 传入数字，表示跳过多少页，然后HttpProxy发起请求 |
|                    |                                                                                                               |                                                 |


3. SFX.franework.Panel:
   - 每个页面都会有主Panel,带着rightItem,showRightItem,会浮动在右边的组件
   - 在原型链上添加showRightItem 和hideRightItem,对右边组件进行显示或隐藏
| 属性            | 类型     | 含义                           |
| --------------- | -------- | ------------------------------ |
| rightCls        | string   | 给右侧弹窗添加的class          |
| dragTitle       | Boolean  | 是否让标题栏具有拖拽窗口的功能 |
| applyPermission | boolean  |                                |
| updateTitle     | function | 更新标题                       |

4. B.product.SettingsList 设置列表(CRM里面一些管理面板的列表)
   

5. SFX.FormWindow 继承SFX.Window 继承 Ext.Window (有啥用,目前不清楚,,,,)


# 使用nwjs进行构建桌面端应用
* nwjs
  - 定义: nwjs(原名node-webkit [https://github.com/nwjs/nw.js]), 结合Chromium和node.js的应用, 跨平台轻量级桌面应用开发.
  - 原理: NW.js的范式更面向浏览器。它基本上加载指定的HTML页面(chromium的功能,实际上就是提供个UI浏览器)，并且该页面可以访问 Node.js 上下文。如果打开了多个窗口，那么它们都可以访问共享的Node.js上下文。这意味着可以从所有打开的窗口的 DOM 透明地直接访问Nodejs上下文。
  - 在Chromium代码库中插入一些钩子,以便DOM可以访问Nodejs上下文.(需要NWjs开发人员做更多的工作,因为浏览器和Node环境交互复杂).

* electron
  - 定义: 提供一个能通过JS和HTMl创建桌面应用的平台,同时集成Node来授予网页访问底层系统的权限
  - 原理: 启动一个主进程,主进程可以打开具有单独渲染器进程的窗口(两个进程间的通信). 所以两进程间通信有点困难. 为此采用IPC通信进行数据编组.
  - 与nwjs不同,electron不会关闭最后一个窗口时自动退出.需要开发人员监听并退出.


- electron & nwjs
  - 不同点:
    - 入口不同:
      - electron主入口是一个js ,工作方式更像Nodejs运行时,api更加底层.
      - nwjs的应用主入口是一个html or js , package.json指定一个主页面.
    - 构建系统:
      - electron通过`libchromiumcontent`来访问Chromium和Content API
        - `libchromiumcontent`是一个独立的,引入了Chromium Content模块及其所有依赖的共享库.
      - nwjs: 直接将Chromium拉过来,效率低.
    - Node集成:
      - electron 通过各个平台的消息循环和libuv的循环集成
      - nwjs:直接给chromium打补丁
    - 多上下文语境
      - nwjs多上下文语境,通常以模块化开发
      - electron不需要引入js上下文,独立开来的上下文.

# sasd