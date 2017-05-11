---
title: 'Mathematica技巧[2]——修改输入和输出字体'
date: 2017-05-09 20:55:31
tags: Mathematica
description: "在Mathematica中永久性修改输入和输出字体."
---

`Mathematica`自带的修改字体功能似乎并没有什么用……如果想修改的话，需要手动运行代码修改。代码分为两部分

## 函数定义

```
setFont[fontFamily_, fontSize_] := With[
    {
        styleNB = Notebook[
            {
                Cell[StyleData[StyleDefinitions -> "Default.nb"]],
                Cell[StyleData["StandardForm"],
                FontFamily -> ToString[fontFamily],
                FontSize -> ToExpression[fontSize]]
            }
        ],
        styleSheetName = FileNameJoin[
            {
                $UserBaseDirectory, 
                "SystemFiles", 
                "FrontEnd", 
                "StyleSheets", 
                "myStyle.nb"
            }
        ]
    },
    If[
    	FileExistsQ[styleSheetName], 
    	SetOptions[$FrontEnd,
    	DefaultStyleDefinitions -> "Default.nb"]
    ];
    Export[styleSheetName, styleNB];
    SetOptions[$FrontEnd, DefaultStyleDefinitions -> styleSheetName]
];
```

## 函数运行

```
setFont["字体名称", 字体大小]
比如：
setFont["Consolas", 12]
或者
setFont["Monaco", 13]
```

## 用法

将以上函数和运行的代码粘贴进一个`.nb`文档，运行即可修改字体。