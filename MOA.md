- 自定义封装的一些grid、store、form等

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
// 属性，对于我们的
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



3. SFX.hdSearch.CheckRadioGroup 多选框 继承SFX.hdseacrch.HdSeatchCtn
问题：无法默认选中，即使代码中包含着checked=checked，但仍然无法默认选中


# 技巧

1. JsonStore读取本地mock数据
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