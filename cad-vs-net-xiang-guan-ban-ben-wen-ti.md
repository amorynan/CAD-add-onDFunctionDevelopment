### AutoCAD二次开发——AutoCAD.NET API开发环境搭建

| AutoCAD | VS | .NetFramework | 备注 |
| :--- | :--- | :--- | :--- |
| 2015 | 2012 | 4.5 |  |
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

_**注意 :**_ 新建类库项目，添加AcDbMgd和AcMgd 等引用，将引用的"复制本地"属性设置为False

综合一下，我的开发环境就用 ： vs 2013 ，AutoCAD 2012， office 2010

### Hello CAD :

1. 上面的四个动态链接库是需要引入的，这里面是autocad 关于 .net的托管类库
2. autocad 在加载程序集的时候，先会查找这里面有没有ExtensionApplication 类型的自定义属性，如果找到后会把与该属性有联系的类作为程序的入口点，如果没有，就会查找实现了IExtensionApplication接口的类，如果还没有，则跳过程序初始化工作，在找IExtensionApplication接口类同时还会去在程序集中查找所有的CommandClass属性，找到了，则在执行cad 命令的时候，就只会去搜索相关commandclass属性的类，否则要搜索所有的类。

   ExtensionApplication 和 CommandClass属于程序级别的属性，告诉了cad去哪里获取不同的对象，否则则需要查找才能区分对象的区别ExtensionApplication 属性只能被附加到一个类中，这个类还必须实现IExtensionApplication 接口，而CommandClass可以被添加到任何一个定义了cad命令的类，

3. 调试，异常处理 : autocad 在runtime类库中定义了Exception类，这个类最重要的两个属性 Message 和 Source



