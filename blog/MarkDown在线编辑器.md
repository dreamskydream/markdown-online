MarkDown在线编辑器
==
使用开源项目[editor.md](https://github.com/pandao/editor.md.git "editor.md")我的gitee仓库下也有。

安装
--
`# npm install editor.md`

使用
--
```
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8">
        <link rel="stylesheet" href="editor.md/css/editormd.min.css" />
        <title>Markdown 编辑在线器</title>
    </head>
    <body>
    <div id="text" style="width: 100%;">
        <div id="editor" style="height: 100%;">
            <!-- Tips: Editor.md can auto append a `<textarea>` tag -->
            <textarea style="display:none;" placeholder="欢迎使用"></textarea>
        </div>
    </div>
    <script src="jquery.min.js"></script>
    <script src="editor.md/editormd.min.js"></script>
    <script type="text/javascript">
        // 设置div与窗口高度一致
        window.onload = function () {
            var h = window.innerHeight - 25 ; //获取当前窗口的高度; 
            document.getElementById("text").style.height = h + "px"; //设置div的高度
        }
        // 设置textarea参数
        $(function() {
            var h = window.innerHeight - 25
            var editor = editormd("editor", {
                // width: "100%",
                height: h,
                saveHTMLToTextarea   : true,
                // markdown: "xxxx",     // dynamic set Markdown text
                path : "editor.md/lib/"  // Autoload modules mode, codemirror, marked... dependents libs path
            });
        });
    </script>
    </body>
    </html>
```