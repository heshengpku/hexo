---
title: 在Excel中使用VB提取超链接
date: 2024-10-11 17:26:50
category: Tools
tags:
 - Excel
---

### 打开Excel文档

1. 按“Alt+F11”进入VBA环境；

2. 右击“Microsoft Excel 对象”；

3. 从其扩展菜单中选择“插入”-“模块”项；

4. 接着在打开的“模块1”编辑界面中，输入代码：

```VB
Function GetActAddress(HlinkCell)
Application.Volatile True
With HlinkCell.Hyperlinks(1)
GetActAddress = IIf(.Address = "", .SubAddress, .Address)
End With
End Function
```

返回Excel编辑界面后，在超链接右侧的单元格，输入“=GetActAddress”的完整公式，即可提取超链接信息。
