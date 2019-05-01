# viewer

```
 <link href="${base}/resources/js/plugins/viewer/viewer.css" rel="stylesheet">
```



```
<script src="${base}/resources/js/plugins/viewer/viewer.js"></script>
```



```
 <style>
        ul {
            list-style-type: none;
        }
    </style>
```



```
 <ul class="viewer">
     <li><img src="${imgUrl}" onerror="javascript:this.style.display='none'"
      style="max-width: 300px;max-height: 300px;margin: 10px;" width="80px" height="80px"></li> </ul>
```



```
<script>
    /*图片放大初始化*/
    $(function () {
        viewer();
    });
    
    function viewer() {
        $(".viewer").each(function (i,value) {
            $(this).viewer({
                navbar: false
            });
        });
    }
</script>
```



```
class="viewer" onerror="javascript:this.style.display='none'"
```



```
 onLoadSuccess: function () {
                    $(".viewer").each(function (i,value) {
                        $(this).viewer({
                            navbar: false
                        });
                    });
                }
```



## ajax图片上传

```html
  <div class="form-group">
    <div class="row">
      <div class="col-sm-6">
        <label class="col-sm-3 control-label">任务图片</label>
        <div class="col-sm-9">
          <div id="divImg">
            <img src="" style="max-width:250px;"
                 onerror="javascript:this.style.display='none'" class="viewer"/>
            <input type="file" name="file">
            <input type="hidden" name="img" id="img" value="">
          </div>
        </div>
      </div>
    </div>
</div>
 <!--/row-->
```



```html
 /*任务图片上传*/
    $('#divImg').on('change', ':file', function () {
        var formData = new FormData();
        formData.append("file", $(this)[0].files[0]);
        formData.append("fileType", "image");
        $.ajax({
            type: 'post',
            url: "${base}/admin/file/upload.jhtml",
            data: formData,
            cache: false,
            processData: false,
            contentType: false,
            success: function (result) {
                var data = JSON.parse(result);
                $(this).parent().children("[name=img]").attr("value", data.url);
                $(this).parent().children("img").attr("src", data.url);
                $(this).parent().children("img").css("display", "");
            }.bind(this),
            error: function (data) {
                alert(data + "上传失败");
            }
        });
    });
    /*end*/
```

