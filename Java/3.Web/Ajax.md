# JQuerry方式

## $.ajax方式

* **需要注意跨域问题，否则请求失败**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>标题</title>
</head>
<script src="js/jquery-3.4.1.min.js"></script>
<script>
    function fun() {
        $.ajax({
            url: "http://localhost:8080/test",
            type: "GET",
            data: "name=小明&age=20",
            dataType: "text",
            success: function (data) {
                alert(data)
            },
            error: function (error) {
                alert("错误")
            },
        });
    }

</script>
<body>
<form>
    <input id="btn" type="button" value="按钮" onclick="fun()">
</form>
</body>
</html>
```

## $.get方式

| 参数                      | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| URL                       | 必需，规定您需要请求的 URL                                   |
| data                      | 可选，规定连同请求发送到服务器的数据                         |
| function(data,status,xhr) | 可选，规定当请求成功时运行的函数，<br />data：包含来自请求的结果数据<br />status：包含请求的状态（"success"、"notmodified"、"error"、"timeout"、"parsererror"）<br />xhr：包含 XMLHttpRequest 对象 |
| dataType                  | 可选，规定预期的服务器响应的数据类型<br />text：文本内容<br />json：json串 |

```
$.get(URL,data,function(data,status,xhr),dataType)
```

```xml
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>标题</title>
</head>
<script src="js/jquery-3.4.1.min.js"></script>
<script>
    function fun() {
        $.get("http://localhost:8080/test", "name=小明&age=20", function (data) {
            alert(data)
        }, "text")
    }

</script>
<body>
<form>
    <input id="btn" type="button" value="按钮" onclick="fun()">
</form>
</body>
</html>
```

## $.post方式

```
$(selector).post(URL,data,function(data,status,xhr),dataType)
```

