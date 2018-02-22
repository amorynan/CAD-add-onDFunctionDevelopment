#### base block object

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace JDS_CAD.VerticalViewBlock
{
    interface BlockObject
    {
       
    }
}

```

#### subclass block object



180 ° 无角度

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Autodesk.AutoCAD.Runtime;
using Autodesk.AutoCAD.DatabaseServices;
using Autodesk.AutoCAD.ApplicationServices;
using  Autodesk.AutoCAD.EditorInput;
using Autodesk.AutoCAD.Geometry;
using JDS_CAD.Chair;

namespace JDS_CAD.VerticalViewBlock.blockSubClass
{
    class NoAngle : BlockObject
    {
        int flag;
        double Length;
        string name;
        Database db;
        List<Entity> ents = new List<Entity>();
        Point3d pt1, pt2;
        Line l1;
        // Double[] PyList = new Double[10];
        public NoAngle(string name, Database db)
        {
            this.name = name;
            this.db = db;
        }
        public void add()
        {
            string[] wname = new string[] { "坐", "背顶", "下背", "上背", "坐前上", "坐前下", "坐侧", "背侧", "背后", "扶手前" };
            Editor ed = Application.DocumentManager.MdiActiveDocument.Editor;
            PromptDoubleOptions pl = new PromptDoubleOptions("输入基线的长度");
            PromptDoubleResult length = ed.GetDouble(pl);
            Length = length.Value;
            pt1 = Point3d.Origin;
            pt2 = new Point3d(pt1.X+Length,pt1.Y,pt1.Z);
            l1 = new Line(pt1,pt2);
            l1.ColorIndex = 1;
            ents.Add(l1); 
            /*int i = 0;
            double Py;
            while (i < wname.Length)
            {
                PromptDoubleOptions ps = new PromptDoubleOptions("输入" + wname[i] + "的偏移量(如果没有请输入0)");//如果输入为零则代表没有这一部分
                PromptDoubleResult py = ed.GetDouble(ps);
                Py = py.Value;
                PyList[i] = Py;
                i++;
            }*/
            AddNewBlock ab = new AddNewBlock(db, name, ents);
            flag=ab.getNewBlockId();
        }
        //获取该块的各个属性加入到泡沫类中
        public sponge getSponge()
        {
            if (flag == 1)
            {
                sponge s = new sponge();
                s.Name = name;//该块的泡沫属性
                Block180Method bm = new Block180Method();
                bm.setLength(Length ,s);
                return s;
            }
            else
                return null;
        }
    }
}

```

直角\(后改\)

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace JDS_CAD.VerticalViewBlock.blockSubClass
{
    class Right_Angle : BlockObject
    {

    }
}

```

配置文件

```
<?xml version="1.0" encoding="utf-8" ?> 

<configuration>

	<appSettings>

		<add key="NoAngle" value="JDS_CAD.VerticalViewBlock.blockSubClass, JDS_CAD.VerticalViewBlock.blockSubClass.NoAngle" />
		<add key="Right_Angle" value="JDS_CAD.VerticalViewBlock.blockSubClass, JDS_CAD.VerticalViewBlock.blockSubClass.Right_Angle" />

	</appSettings>

</configuration>
```



