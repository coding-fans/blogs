# 用 Python 生成 HTML 表格

在 **邮件报表** 之类的开发任务中，需要生成 *HTML* 表格。

使用 *Python* 生成 *HTML* 表格基本没啥难度， *for* 循环遍历一遍数据并输出标签即可。 如果需要实现合并单元格，或者按需调整表格样式，就比较麻烦了。


这时，可以试试本文的主角 —— [html-table](https://github.com/fasionchan/py-html-table) 包，借助它可生成各种样式的 *HTML* 表格。 接下来，以一个简单的例子演示 *html-table* 的常用用法：

![表格效果图](https://python.fasionchan.com/zh_CN/latest/_images/997ad67a7f305a39e5a77e3bf86c7798.png)

开始之前，须通过 *pip* 安装 [html-table](https://github.com/fasionchan/py-html-table) 包：

```sh
$ python -m pip install html-table
```

安装完毕后，即可导入 *HTMLTable* 类：

```python
from HTMLTable import (
    HTMLTable,
)
```
创建一个新表格，标题为 **果园收成表** ：

```python
# 标题
table = HTMLTable(caption='果园收成表')
```

附上表头：

```python
# 表头行
table.append_header_rows((
    ('名称',    '产量 (吨)',    '环比',             ''),
    ('',        '',             '增长量 (吨)',      '增长率 (%)'),
))
```

注意到，表头分为两行，有些单元格需要合并，被合并的单元格需要留空占位。

合并单元格设置：

```python
# 合并单元格
table[0][0].attr.rowspan = 2
table[0][1].attr.rowspan = 2
table[0][2].attr.colspan = 2
```

*table[0]* 取出第一行，即第一个 ``<tr>`` 标签； *table[0][0]* 取出第一个单元格，对应 **名称** ； *table[0][0].attr* 则是其标签 ``<th>`` 的属性。 该单元格合并下方一个单元格，需要将标签属性 *rowspan* 设置为 *2* 。

接着，加入数据，方法与表头类似，总共有 *3* 行：

```python
# 数据行
table.append_data_rows((
    ('荔枝', 11, 1, 10),
    ('芒果', 9, -1, -10),
    ('香蕉', 6, 1, 20),
))
```

至此，数据准备完毕，可以着手调整样式。先设置表格标题样式：

```python
# 标题样式
table.caption.set_style({
    'font-size': '15px',
})
```

设置 ``<table>`` 标签的样式：

```python
# 表格样式，即<table>标签样式
table.set_style({
    'border-collapse': 'collapse',
    'word-break': 'keep-all',
    'white-space': 'nowrap',
    'font-size': '14px',
})
```

以上 *CSS* 样式设置在 ``<table>`` 标签上，作用于整个表格，影响表格边框、字体大小等。 注意到，下面会覆盖部分单元格(如表头单元格)的字体大小。

接着，设置每个单元格的样式，主要是规定边框样式：

```python
# 统一设置所有单元格样式，<td>或<th>
table.set_cell_style({
    'border-color': '#000',
    'border-width': '1px',
    'border-style': 'solid',
    'padding': '5px',
})
```

接着，设置表头单元格样式，规定颜色、字体大小、以及填充大小：

```python
# 表头样式
table.set_header_row_style({
    'color': '#fff',
    'background-color': '#48a6fb',
    'font-size': '18px',
})

# 覆盖表头单元格字体样式
table.set_header_cell_style({
    'padding': '15px',
})
```

*set\_header\_row\_style* 将样式设置到表头两个 ``<tr>`` 标签上； *set\_header\_cell\_style* 则将样式设置到每个 ``<th>`` 标签上。 应该尽量将颜色等样式设置到 ``<tr>`` 标签上，而不是 ``<th>`` 标签上，以精简生成的 *HTML* 。

将次级表头字体大小调小，不再赘述：

```python
# 调小次表头字体大小
table[1].set_cell_style({
    'padding': '8px',
    'font-size': '15px',
})
```

遍历每个数据行，如果第 *2* 个单元格值小于 *0* ，设置样式标红背景颜色：

```python
# 遍历数据行，如果增长量为负，标红背景颜色
for row in table.iter_data_rows():
    if row[2].value < 0:
        row.set_style({
            'background-color': '#ffdddd',
        })
```

最后，生成 *HTML* 文本：

```python
html = table.to_html()
print(html)
```

## 附录

更多 *Python* 技术文章，请查看：[Python语言小册](https://python.fasionchan.com) ，转至 [原文](https://python.fasionchan.com/zh_CN/latest/libs/html-table.html) 可获得最佳阅读体验。

订阅更新，获取更多学习资料，请关注我们的 [微信公众号](https://python.fasionchan.com/zh_CN/latest/about/contact.html#wechat-mp) ：

![小菜学编程](https://cdn.fasionchan.com/qrcode/wechat-coding-fan-tiny.jpg)