选择集用法-和开发相关API

用法一 ： 简单创建三个同心圆，然后对选择的范围进行实体数量的计算

```
[CommandMethod("TestGetSelect")]
        public static void TestGetSelect()
        {
            Document doc = Application.DocumentManager.MdiActiveDocument;
            Database db = doc.Database;
            Editor ed = doc.Editor;

            Circle cir1 = new Circle(Point3d.Origin, Vector3d.ZAxis, 10);
            Circle cir2 = new Circle(Point3d.Origin, Vector3d.ZAxis, 20);
            Circle cir3 = new Circle(Point3d.Origin, Vector3d.ZAxis, 30);

            using (Transaction tran = db.TransactionManager.StartTransaction())
            {
                BlockTable bt = (BlockTable)tran.GetObject(db.BlockTableId, OpenMode.ForRead);
                BlockTableRecord btr =
                    (BlockTableRecord) tran.GetObject(bt[BlockTableRecord.ModelSpace], OpenMode.ForWrite);
                btr.AppendEntity(cir1);
                btr.AppendEntity(cir2);
                btr.AppendEntity(cir3);
                tran.AddNewlyCreatedDBObject(cir1, true);
                tran.AddNewlyCreatedDBObject(cir2, true);
                tran.AddNewlyCreatedDBObject(cir3, true);

                tran.Commit();
            }

            PromptSelectionResult psr = ed.GetSelection();
            if (psr.Status != PromptStatus.OK) return;
            SelectionSet set = psr.Value;

            Application.ShowAlertDialog("the number of entity in select set is :"+set.Count.ToString() );
        }
```

用法二 ： 合并选择集\(扩大你选择的范围\) -- 用到了LINQ 的 union 函数，其特点是，返回时IEnumberable&lt;T&gt; 泛型变量，用var 弱类型遍历装； 其次是union函数忽略相同相，也就是并集部分

```
 [CommandMethod("MergeSelection")]
        public void mergeSelection()
        {
            Document doc = Application.DocumentManager.MdiActiveDocument;
            Database db = doc.Database;
            Editor ed = doc.Editor;

            PromptSelectionResult psr = ed.GetSelection(); // get the first selection
            if (psr.Status != PromptStatus.OK) return; // if selection failed , return 

            Application.ShowAlertDialog("there are "+psr.Value.Count.ToString()+" objects here");

            PromptSelectionResult psr_ = ed.GetSelection(); // the second selection
            if (psr_.Status != PromptStatus.OK) return;

            Application.ShowAlertDialog("the second selection "+psr_.Value.Count.ToString()+" objects here");

            // merge -- linq union function
            var ss = psr.Value.GetObjectIds().Union(psr_.Value.GetObjectIds()); // 这里用的是var 弱类型，是由编译器在初始化语句时根据右边的表达式来推断的类型，结果返回的依然是强类型变量,或者说泛型变量

            Application.ShowAlertDialog("there are merge selection : "+ss.Count().ToString());

        }
```

用法三 ： 删除选择集中的另外的选择集（缩小选择的范围）

```
[CommandMethod("deleteSelection")]
        public void deleteSelection()
        {
            Document doc = Application.DocumentManager.MdiActiveDocument;
            Database db = doc.Database;
            Editor ed = doc.Editor;

            PromptSelectionResult psr = ed.GetSelection(); // get the first selection
            if (psr.Status != PromptStatus.OK) return; // if selection failed , return 

            Application.ShowAlertDialog("there are " + psr.Value.Count.ToString() + " objects here");

            PromptSelectionResult psr_ = ed.GetSelection(); // the second selection
            if (psr_.Status != PromptStatus.OK) return;

            Application.ShowAlertDialog("the second selection " + psr_.Value.Count.ToString() + " objects here");

            // merge -- linq Except function
            var ss = psr.Value.GetObjectIds().Except(psr_.Value.GetObjectIds()); // 这里用的是var 弱类型，是由编译器在初始化语句时根据右边的表达式来推断的类型，结果返回的依然是强类型变量,或者说泛型变量

            Application.ShowAlertDialog("there are delete selection : " + ss.Count().ToString());
        }
```

当然还有选择集求交集的函数 ,跟上面的union和delete的一样只是改了LINQ的函数为Intersect

用法四 ： 测试 先选择，后操作

```
[CommandMethod("PickFirst",CommandFlags.UsePickSet  )]
        public void pickFirst()
        {
            Document doc = Application.DocumentManager.MdiActiveDocument;
            Database db = doc.Database;
            Editor ed = doc.Editor;

            PromptSelectionResult psr = ed.SelectImplied(); // get the selection before the command
            if (psr.Status == PromptStatus.OK)
            {
                // if there are selection here
                SelectionSet ss = psr.Value;

                Application.ShowAlertDialog("there are "+ss.Count.ToString() + " objects before command");

                // then clean the editor's selection
                ed.SetImpliedSelection(new ObjectId[0]);

                // then new selection 
                psr = ed.GetSelection();
                if (psr.Status == PromptStatus.OK)
                {
                    ed.SetImpliedSelection(psr.Value.GetObjectIds());
                    Application.ShowAlertDialog("there are " + psr.Value.Count.ToString() + " objects after command");
                }
                else
                {
                    Application.ShowAlertDialog("there are none objects new here");
                }
            }

        }
```

LINQ 的使用简化选择集操作 -- 主要是对选择的集合对象进行排序，筛选，分组等等操作，要理解这一系列操作其实不难，因为这些实体对象都是储存在块表中的，也就是我们在对数据库中的表进行操作，所以其实数据库中的操作，这里都能做，LINQ的功能也就是封装提供直接的数据库表的功能的API

_**注意一点 ： LINQ的对象需要实现IEnumerable&lt;T&gt;接口。并且LINQ的查询表达式是在最近一次创建对 象时才被编译的，关于LINQ的运用，我们可以进行自定义特定条件的类实现此接口，然后进行LINQ选择集的遍历,也就是自定义LINQ适配器**_

官方参考文档，里面主要是LINQ目前实现的方法

[https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/index](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/index)



具体思路做法 ： 

step 1 ：get database source - 获取数据源

```
eg List<Circles> circles = new List<Circle>();
```

  再次注意，这里的数据源一定是_**实现IEnumerable&lt;T&gt;接口的对象**_

step 2 ：create select statements -- 创建查询语句，这一步其实就是对关系型数据库查询语句的自我封装后提供的API的调用，详细的上面官方文档有，请自行根据自己目前的需求查找

```
var cir = from c in circles
                where c.colorIndex == 1
                select c; // 转变查询 : select new{Area = c.Area} 选择集的结果就变成了c的面积
```



step 3 : carry on the selection  -- 执行查询， 也就是要对查询的结果集进行一个foreach的遍历操作，去输出我们的东西，也就是客户端和数据源之间的数据互显示

```
foreach(var c in cir){
    // do something with the res cir
    ed.writeMessage(c.Area.ToString()); // 有这一句才会执行上面的查询语句，这也正好体现了LINQ的上下文编译的特性
}
```

特别强调的一点是LINQ操作是属于数据延迟查询操作，也就是你没有要用你查询的结果集，他是不会执行查询操作的，更直白的就是，你没有第三步，第二步他不会给你做，但也有不做第三步，强制让他立即查询的方法，就是，在LINQ语句中调用ToList 或者 ToArray将查询的结果集进行转变成列表和数组

```
var cir = (from c in circles
                where c.colorIndex == 1
                select c).ToList();
```



自定义对象的捕捉 （对于三视图中侧面图的文字对象的捕捉）

