### 创建和编辑基本图形对象

我们针对的autoCAD 图形对象，而在autocad 的图形， 是以图形数据库的形式存在，数据库的基本就是表和记录

而一个数据库包含了块表，层表，文字样式表，线性表。视图表等等至少9种表

记录就是表的基本组成单位

所以我们在创建一个基本的图形对象的基本步骤：

1. 得到创建对象的图形数据库

```
Database db = HostApplicationServices.WorkingDatabase
```

  2. 在内存中创建一个实体类的对象

```
Circle cirlce = new Circle(center, Vector3d.ZAxis, 10.0);
```

  3. 打开图形数据库的块表\(一般在打开块表的时候会用事件操作,主要是为了保证插入表中的数据的ACID特性\)

```
using (Transaction trans = db.TransactionManager.StartTransaction()){
    BlockTable bt = (BlockTable)(trans.GetObject(db.BlockTableId, OpenMode.ForRead)); // 此打开模式为只读
```

 4. 打开储存实体的一个块表记录（一般都在模型空间里绘图），所有模型空间里的实体都在块表的模型空间记录里面

```
// 此打开模式为只写
  BlockTableRecord btr = (BlockTableRecord)trans.GetObject(bt[BlockTableRecord.ModelSpace], OpenMode.ForWrite);
```

 5. 将该对象添加到块表中去

```
btr.AppendEntity(cirlce);
trans.AddNewlyCreatedDBObject(cirlce, true);
```

 6. 开启了事件的记得提交事件

```
    trans.Commit();         
}
```



##### _**注意一点 ： 在开发的时候我创建的圆在模型空间里看不到，但是打开布局里面又存在，我的情况是在模型空间里的比例不对，也就是创建的圆半径小，看不到，这时候解决方法，可以输入命令Z之后，在输入命令A 进行缩放，就可以看到了。**_



