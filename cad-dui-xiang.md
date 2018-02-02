#### CAD 对象（转）

**1、CAD对象**

一个CAD文件（DWG文件）即对应一个数据库，数据库中的所有组成部分，看的见（包括点、多段线、文字、圆等）和看不见（图层、线型、颜色等）的都是对象，数据库本身也是一个对象。

* 直线、圆弧、文字和标注等图形对象都是对象。
* 线型与标注样式等样式设置都是对象。
* 图层、编组和块等组织结构都是对象。
* 视图与视口等图形显示都是对象。
* 甚至图形和 AutoCAD 应用程序本身也是对象。

**2、CAD中的容器**

对于任何一个DWG文件，都会创建下列容器对像：九个固定的符号表（如下）和有名对像字典（包含Group字典和Mline样式字典）。CAD中的所有对象都放在这些容器对象中，符号表及有名对象字典都具有同样的功能，即组织和管理CAD数据库中的对象。即它们含有实体对象，可以通过文字字符串作为关键字对对象实体进行查询，并向这些容器添加记录（记录即对象），修改记录。

**3、符号表与字典的不同**

符号表如下所示有9种，每种符号表只能存储特定类型的对象\(如块表只能存储块表记录，层表只能存储层表记录\)，而字典则是一种通用容器，可存储任何类型的对象。（一般多是通过符号表操作对象）

**9种符号表：**

1\)块表\(BlockTabLe\)

2\)尺寸标注样式表\(DimStyleTable\)

3\)层表\(LayerTable\)

4\)线型表\(LinetypeTable\)

5\)应用程序注册表\(RegAppTable\)

6\)文字样式表\(TextStyleTable\)

7\)用户坐标系表\(UCSTable\)

8\)视口表\(ViewportTable\)

9\)视图表\(ViewTable\)

**4、AutoCAD .NET API中的对象层次**

**1）Application对象层次**

Application 对象是 AutoCAD .NET API 对象模型的根对象。通过 Application 对象，用户可以访问主窗口以及任何打开的图形。一旦用户获得了图形，就可以访问图形中的对象。

例如，Application 对象具有 DocumentManager 特性，该特性可以返回 DocumentManager 对象。该对象提供对AutoCAD中当前图形的访问并允许用户并允许用户创建、保存和打开图形文件。通过 Application 对象提供的其它特性可以专用数据，例如信息中心（InfoCenter）、主窗口（main window）和状态栏（status bar）。MainWindow 特性允许用户访问应用程序的名称、 大小、位置和可见性。

虽然 Application 对象的特性允许访问 AutoCAD .NET API 的大多数对象，也有一些 AutoCAD ActiveX® Automation 对象的引用。这些特性包括 application 对象的 COM 版本\(AcadApplication\)，菜单栏\(MenuBar\),加载的菜单组 \(MenuGroups\)和参数选项 \(Preferences\)。

![](http://images.cnitblog.com/blog/31097/201409/011709443758631.jpg)

**2\) Document的对象层次**

Document 对象实际上就是 AutoCAD 图形，它是 DocumentCollection 对象的一部分,并提供访问与 Document 对象相关的 Database 对象。 Database 对象包括所有的图形和大部分非图形的 AutoCAD 对象。

连同 Database 对象一起, Document 对象提供访问图形状态栏（StatusBar），document 对象被打开的窗口（Window），Editor （Editor） 和事务管理器（TransactionManager）的对象。Editor 提供用户以输入形式获取的点、输入字符串或数字值的功能。

事务管理器对象用于访问将多个 database 对象下的单个操作当作一个 _transaction_ 对象。事务可以被嵌套，当用户完成事务时，可以提交或放弃已做的改变。

![](http://images.cnitblog.com/blog/31097/201409/011708101253359.jpg)

**3\)DataBase对象层次**

Database 对象包括所有的图形和大部分非图形的 AutoCAD 对象。 被包含在 Database 中的一些对象有实体，符号表，命名字典。实体在 Database 中表示图纸内部的图形对象。直线、圆、圆弧、文本、填充和多段线都是实体的一个例子。用户可以在屏幕上看到一个实体并可以操作它。

用户通过 Document 对象的 Database 成员属性可以访问当前文档的 Database 对象。

Application.DocumentManager.MdiActiveDocument.Database

符号表和字典

符号表和字典对象提供对非图形对象的访问（块、图层、线型、布局等等）。在AutoCAD中的每一个图形都包含9个固定的符号表，然而图形中的字典的数量是可以根据功能和应用程序的使用类型来改变的。新的符号表不能被添加到 Database 中去。

例如，层表 \(_LayerTable_\)就是符号表，它包含层表记录，块表也是符号表，它包含块表记录。所有的图形实体 \(直线、圆、圆弧等等\) 都是属于一个块表记录。默认情况下，每一个图形都为模型空间和图纸空间预定义了块表记录。每一个图纸空间布局都有它自己的块表记录。

字典是一个可以包含任何 AutoCAD 对象或 Xrecord 对象的容器。

![](http://images.cnitblog.com/blog/31097/201409/011711399537488.jpg)

**4）图形对象**

也称为图元，是组成图形的可见对象（例如直线、圆、光栅图像等）。添加图形对象到图形中是通过正确的块表记录引用，然后使用 AppendEntity 方法将一个新对象添加到图形中来完成的。

要修改或查询这些对象，请从正确的块表记录获得对象引用，然后使用对象本身的方法或特性。每一个图形对象都拥有允许应用程序执行大部分 AutoCAD 编辑命令的方法，例如复制、删除、移动、镜像等。

这些对象还提供了一些方法，用来设置和检索对象的扩展数据\(xdata\)，亮显和更新对象，以及从另外的图元设置属性。大多数图形对象具有诸如 LayerId、LinetypeId、Color 和 Handle 之类的典型特性。每个对象也具有一些特有的特性，例如 Center、StartPoint、Radius 和 FitTolerance。

非图形对象是指属于图形的一部分但不可见的（提示性的）对象，例如 Layers、Linetypes、DimStyles、TableStyle 等。若要创建新的符号表记录，请使用表所有者的 Add 方法或使用 SetAt 方法添加一个字典到命令对象字典中。要修改或查询这些对象，请使用对象本身的方法或特性。每一个非图形对象都有用于特定目的的方法和特性，都有设置和检索扩展数据以及删除自己的方法。

