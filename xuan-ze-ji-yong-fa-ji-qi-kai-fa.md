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

用法三 ： 删除选择集中的对象

```

```

用法四 ： 测试 先选择，后操作

LINQ 的使用简化选择集操作

自定义对象的捕捉 （对于三视图中侧面图的文字对象的捕捉）

