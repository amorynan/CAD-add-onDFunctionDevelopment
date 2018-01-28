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

用法二 ： 合并选择集



用法三 ： 删除选择集中的对象



用法四 ： 测试 先选择，后操作



LINQ 的使用简化选择集操作



自定义对象的捕捉 （对于三视图中侧面图的文字对象的捕捉）

