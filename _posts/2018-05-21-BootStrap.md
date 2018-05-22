---
layout: post
title: 'BootStrap'
date: 2018-05-21
author: HenryHX
cover: '/assets/img/2018-05-21-BootStrap/BootStrap.png'
tags: BootStrap Excel
---

> 主要介绍BootStrap中遇到的问题。

### BootStrap 模态框初始化位置和可拖动

```js
$(modalSelect).on('show.bs.modal', function(){
    //设置模态框的水平垂直方向的位置；
    var $clone = $(this).clone().css('display','block').appendTo('body');
    var top = Math.round(($clone.height() - $clone.find('.modal-content').height()) / 3);
    top = top > 0 ? top : 0;
    var left = Math.round(($clone.width() - $clone.find('.modal-content').width()) / 2);
    left = left > 0 ? left : 0;
    $clone.remove();
    $(this).find('.modal-content').css("margin-top", top);
    $(this).find('.modal-content').css("margin-left", left);
    $(this).draggable({
        handle: ".modal-header"   // 只能点击头部拖动
    });
    $(this).css("overflow-y", "scroll");
    // 防止出现滚动条，出现的话，你会把滚动条一起拖着走的
})
```

### BootStrap上传文件控件样式 

> `bootstrap-filestyle`是一款可以简单实用的表单文件上传域美化jQuery插件。该插件可以将表单的文件上传域转换为类似Bootstrap按钮组的样式。它提供了大量的data属性来控制文件上传域的样式，可以自定义按钮文本和图标等。

详细使用请参考 ["bootstrap-filestyle"](http://markusslima.github.io/bootstrap-filestyle/)

```js
<div class="form-group">
        <label for="xlf">导入文件名:</label>
    <input type="file" id="xlf" class="filestyle" data-buttonText="浏览" data-icon="false" />
</div>

$("#xlf").filestyle('clear');
```

### BootstrapTable 读取Excel控件
> 纯前端利用 js-xlsx 实现 Excel 文件导入导出功能

下载["js-xlsx"](https://github.com/SheetJS/js-xlsx)复制出`xlsx.full.min.js`引入到页面中
然后通过`FileReader`对象读取文件利用`js-xlsx`转成json数据

```js
<script src="../js/exceljs/shim.min.js"></script>
<script src="../js/exceljs/xlsx.full.min.js"></script>

var X = XLSX;
var importData;
var previewData;
    
function add_eventListener() {
    var xlf = document.getElementById('xlf');
    if(!xlf.addEventListener)
        return;
    function handleFile(e) {
        do_file(e.target.files);
        if(xlf.value != '')
        {
            $("#importModal" + " " + ".next-page").removeClass("disabled");
        }
        else
        {
            $("#importModal" + " " + ".next-page").addClass("disabled");
        }
    }
    xlf.addEventListener('change', handleFile, false);
}
  
var do_file =  function do_file(files) {
    // var rABS = typeof FileReader !== "undefined" && (FileReader.prototype||{}).readAsBinaryString;

    var f = files[0];
    var reader = new FileReader();
    reader.onload = function(e) {
        if(typeof console !== 'undefined') console.log("onload", new Date());
        var data = e.target.result;
        process_wb(X.read(data, {type:'binary'}));
    };
    reader.readAsBinaryString(f);
};

var process_wb = function process_wb(wb) {
    /* get data */
    var ws = wb.Sheets[wb.SheetNames[0]];
    importData = XLSX.utils.sheet_to_json(ws, {header:["comboId","exchangeId","instrumentId","direction","value", "selected"]});
    importData.shift();


    while(importData.length > 3999)
    {
        importData.pop();
    }

    for (var i=0;i<importData.length;i++)
    {
        var dir =  parseInt(importData[i]["direction"]);
        if(dir == 1)
        {
            importData[i]["directionStr"] = "增加";
        }
        else if(dir == 2)
        {
            importData[i]["directionStr"] = "减少";
        }
        else
        {
            importData[i]["directionStr"] = "未定义";
        }

        if(isNaN(importData[i]["value"]))
        {
            importData[i]["value"] = "未定义";
        }
    }

    $("#AdjustPreviewTable").bootstrapTable('load', importData);
    // if(typeof console !== 'undefined') console.log("output", new Date(), importData);
};
  
<table data-toggle="table" id="AdjustPreviewTable" class="table table-bordered" data-pagination="false">
	<thead>
		<tr>
			<th data-field="comboId" data-align="center">组合</th>
			<th data-field="exchangeId" data-align="center">市场</th>
			<th data-field="instrumentId" data-align="center">代码</th>
			<th data-field="directionStr" data-align="center" data-formatter="preDisplayColor">调整方向</th>
			<th data-field="value" data-align="center" data-formatter="preDisplayColor">调整值</th>
			<th data-field="selected" data-checkbox="true"></th>
		</tr>
	</thead>
</table>

```

### 总结
**今天一小步 未来一大步**
![summary.jpg](/assets/img/summary.jpg "总结")
