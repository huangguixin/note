# bootstrap-Table表格

1、首先需要引入如下css与js

```
<link rel="stylesheet" href="bootstrap.min.css">
<script src="jquery.min.js"></script>
<script src="bootstrap.min.js"></script>

<link rel="stylesheet" href="bootstrap-table.css">
<script src="bootstrap-table.js"></script>
<--汉化文件，放在 bootstrap-table.js 后面-->
<script src="bootstrap-table-zh-CN.js"></script>
```



2.写个table标签

```
<table id="table"></table>
```



3.初始化

```
$('#table').bootstrapTable({
    url: 'data1.json',
    columns: [{
        field: 'id',
        title: 'Item ID'
    }, {
        field: 'name',
        title: 'Item Name'
    }, {
        field: 'price',
        title: 'Item Price'
    }, ]
});
```



4.配置详解

```
$('#table').bootstrapTable({

    url: 'data1.json',   <!-- 请求后台的URL（*） -->
    method: 'get',       <!-- 请求方式（*） -->
    striped: true,   //是否显示行间隔色
    cache: false,   //是否使用缓存，默认为true，所以一般情况下需要设置一下这个属性（*）
    pagination: true, //是否显示分页（*）
    sortable: true,                     //是否启用排序
    sortOrder: "id desc",                   //排序方式
    pageNumber: 1,      //初始化加载第一页，默认第一页
    pageSize: 20,      //每页的记录行数（*）
    pageList: [20, 50, 100, 150],  //可供选择的每页的行数（*）
    queryParamsType: '', //默认值为 'limit' ,在默认情况下 传给服务端的参数为：offset,limit,sort
                          // 设置为 '' 在这种情况下传给服务器的参数为：pageSize,pageNumber
    queryParams: queryParams,//前端调用服务时，会默认传递上边提到的参数，如果需要添加自定义参数，可以                              //自定义一个函数返回请求参数
    sidePagination: "server",   //分页方式：client客户端分页，server服务端分页（*）
    showColumns: true, //是否显示所有的列
    showRefresh: true, //是否显示刷新按钮
    minimumCountColumns: 2,    //最少允许的列数
    clickToSelect: false,    //是否启用点击选中行
    search: false,                  //是否显示表格搜索
    //height: 500,                   //行高，如果没有设置height属性，表格自动根据记录条数觉得表格高度
   // uniqueId: "ID",                     //每一行的唯一标识，一般为主键列
    showToggle: true,                   //是否显示详细视图和列表视图的切换按钮
     cardView: false,                    //是否显示详细视图
      columns: [{
                     checkbox: true,  
                     visible: true                  //是否显示复选框  
                 }, {
                     field: 'Name',
                     title: '姓名',
                     sortable: true,
                     align: 'center',
                     valign: 'middle',
                     formatter: function (value, row, index) {//value表示当前字段的值，row
                     //通过formatter可以自定义列显示的内容
                        //value：当前field的值，即id
                        //row：当前行的数据
                     var button;
                    
                    return button;
                   }/*formatter end*/
                 },  
          ]，
           onLoadSuccess: function () {}，
           onLoadError: function () {}，
           onDblClickRow: function (row, $element) {}，
           strictSearch: true,
           searchOnEnterKey: true,
           checkboxHeader: true,
           toolbar: '#toolbar',    //工具按钮用哪个容器
                 
});
```

```
 function queryParams(params) {  //配置参数
        var temp = {
            pageNumber: params.pageNumber,   //页面大小
            pageSize: params.pageSize,   //页面大小
            pageList: params.pageList,   //页面大小

            filter_nickname: $("#filter_nickname").val(),
            filter_name: $("#filter_name").val(),
            filter_wechat: $("#filter_wechat").val(),
            filter_status: $("#filter_status").val(),
            filter_missionId: $("#filter_missionId").val(),

            sortName: params.sortName,
            sortOrder: params.sortOrder
        };
        return temp;
    }
```

如果发送的是post请求，因为bootstap table使用的是ajax方式获取数据，这时会将请求的content type默认设置为 text/plain，这样在服务端直接通过 @RequestParam参数映射是获取不到的。

这时就需要在bootstrap-table的参数列表中显式设置：

```
 contentType: "application/x-www-form-urlencoded"
```

设置成form表单的形式，tomcat内部就会自动将requset payload中的数据部分解析放到request.getParameter()中，之后就可以直接通过@RequestParam映射参数获取



```
  //搜索按钮
    $(".btn-primary-search").click(function () {
        $('#table').bootstrapTable('selectPage', 1);
        $('#table').bootstrapTable(
                "refresh",
                {
                    url: "listNew.json"
                }
        );
    });
```



```
  <div class="btn-group">
                                    <span> <button type="button"
                                                   class="btn btn-sm btn-success btn-primary-search"> 搜索 </button> </span>
                                            <span>  <div id="toolbar" class="btn-group"></div></span>
                                        </div>
```

