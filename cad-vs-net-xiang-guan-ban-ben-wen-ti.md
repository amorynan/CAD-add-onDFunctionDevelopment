### AutoCAD二次开发——AutoCAD.NET API开发环境搭建

| AutoCAD | VS | .NetFramework | 备注 |
| :--- | :--- | :--- | :--- |
| 2015 | 2012 | 4.5 | |
| 2012-2014 | 2010/2012 | 4.0 | 也有用vs10开发在08cad运行        .net4.0有DLR |
| 2010-2011 | 2008 | 3.5 | cad10-12用户28%第二多 推荐 也有用vs08在cad08运行   .net3.0之后有LINQ,wpf,wf,wcf |

选择版本对应最关键的是VS引用的DLL必须要和目标环境AutoCAD一致。其次是FrameWork版本。上面只是最佳组合，但不是硬性要求。

而vs 引用的dll 是用CAD平台已经封装好了很多类，常用的AutoCAD .NET API的四个主要DLL文件是

| **名称** | **作用** | **备注** |
| :--- | :--- | :--- |
| **AcDbMgd.dll** | 处理图形文件中存储的对象 |  |
| **AcMgd.dll** | 处理AutoCAD应用程序和用户接口 |  |
| **AcCui.dll** | 处理自定义文件 |  |
| **AcCoreMgd.dll** | 处理编辑器、发布与打印、定义AutoLISP命令和函数 | AutoCAD 2014版 |

这四个引用 的位置可以在CAD的安装路径下找到，（&lt;盘符&gt;:\program Files\AutoCAD 20xx），

