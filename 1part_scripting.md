# Part scripting零件脚本

**Introduction**

The main data structure used in the Part module is the [BRep](http://en.wikipedia.org/wiki/Boundary_representation) data type from [OpenCASCADE](https://wiki.freecad.org/OpenCASCADE). Almost all contents and object types of the Part module are available in [Python](https://wiki.freecad.org/Python) scripting. This includes geometric primitives, such as Lines, Circles and Arcs, and the whole range of TopoShapes, like Vertexes, Edges, Wires, Faces, Solids and Compounds. For each of those objects, several creation methods exist, and for some of them, especially the TopoShapes, advanced operations like boolean union/difference/intersection are also available. Explore the contents of the Part module, as described in the [FreeCAD Scripting Basics](https://wiki.freecad.org/FreeCAD_Scripting_Basics) page, to know more.

在零件模块中，主要用到的数据格式是BRep类型来自OpenCASCADE。零件的几乎所有内容和器件类型，在python脚本中都是可以用的。包括基本几何形状，如，直线，圆，弧和整个ToPoShapes，如：顶点，边缘，线条，面，实体，复合体。每个器件，有多重创建方法，其中一些，特别是TopoShapes，高级操作像布尔、并集、差集、交集都是可用的。在FreeCAD Scripting Basics页面，浏览零件模块，了解更多。

The most basic object that can be created is a [Part Feature](https://wiki.freecad.org/Part_Feature), which has a simple Data **Placement** property, and basic properties to define its color and appearance.

最简单的器件，被创建的是Part Feature，它有基本的Placement属性，和基本属性，定义它的颜色和外观

Another simple object used in 2D geometrical objects is the [Part Part2DObject](https://wiki.freecad.org/Part_Part2DObject), which is the base of the [Sketcher SketchObject](https://wiki.freecad.org/Sketcher_SketchObject) and most [Draft elements](https://wiki.freecad.org/Draft_Workbench).

另一个简单的器件，运用在2D几何器件是Part Part2DObject，它是Sketcher SketchObject和大多数Draft elements的基础

**Test script 测试脚本**

Test the creation of [Part Primitives](https://wiki.freecad.org/Part_Primitives) with a script. [introduced in version 0.19](https://wiki.freecad.org/Release_notes_0.19)

用脚本，创建基本的器件

```
import parttests.part_test_objects as pto
pto.create_test_file("example_file")
```

This script is located in the installation directory of the program, and can be examined to see how the basic primitives are built.

这个脚本位于安装目录文件夹中，可以查看，基本器件是如何创建的

```
$INSTALL_DIR/Mod/Part/parttests/part_test_objects.py
```

**Examples**

### Line

To create a line element switch to the [Python console](https://wiki.freecad.org/Python_console) and enter:

创建一个元件，转换成python console

```
import FreeCAD as App  # 导入FreeCAD
import Part  # 导入Part模块

doc = App.newDocument()  # 创建一个新的文档

line = Part.LineSegment()
line.StartPoint = (0.0, 0.0, 0.0)
line.EndPoint = (1.0, 1.0, 1.0)
obj = doc.addObject("Part::Feature", "Line")
obj.Shape= line.toShape()

doc.recompute()  # 重新绘制
```

```
line = Part.LineSegment()
line.StartPoint = (0.0, 0.0, 0.0)
line.EndPoint = (1.0, 1.0, 1.0)
```

Line is actually a line segment, hence the start and endpoint.

直线其实就是线段，包含起点和重点

```
obj = doc.addObject("Part::Feature", "Line")
obj.Shape= line.toShape()
```

This adds a Part object type to the document and assigns the shape representation of the line segment to the `Shape` property of the added object. It is important to understand here that we use a geometric primitive (the `Part.LineSegment`) to create a TopoShape out of it (with the `toShape()` method). Only shapes can be added to the document. In FreeCAD geometric primitives are used as "building structures" for shapes.

这里在文件中添加了器件类型，声明线段的外观，Shape属性。理解这里很重要：我们用原始的几何模型(Part.LineSegment器件线段)来创建一个TopoShape(用toShape方法)。只有模型(shapes)能添加到文档。在FreeCAD中，几何图形中被用来给模型创建器件结构

Note that a line segment can also be created by specifying its start and endpoint directly in the constructor, for example `Part.LineSegment(point1, point2)`, or we can create a default line and set its properties afterwards, as we did here.

注意，线段也可在构造函数中直接指定它的起点和终点，例如Part.LineSegment(point1, point2)，或者，创建一个默认直线，稍后再设置它的属性

A Line can also be created using:  直线也可以这样创建

```
import FreeCAD as App
import Part

def my_create_line(pt1, pt2, obj_name):
    obj = App.ActiveDocument.addObject("Part::Line", obj_name)
    obj.X1 = pt1[0]
    obj.Y1 = pt1[1]
    obj.Z1 = pt1[2]

    obj.X2 = pt2[0]
    obj.Y2 = pt2[1]
    obj.Z2 = pt2[2]

    App.ActiveDocument.recompute()
    return obj

line = my_create_line((0, 0, 0), (0, 10, 0), "LineName")
```

### Circle

A circle can be created in a similar way: 创建圆的方法类似

```
import FreeCAD as App
import Part

doc = App.activeDocument()

circle = Part.Circle() 
circle.Radius = 10.0  
obj = doc.addObject("Part::Feature", "Circle")
obj.Shape = circle.toShape()

doc.recompute()
```

Or using:

```
import FreeCAD as App
import Part

def my_create_circle(rad, obj_name):
    obj = App.ActiveDocument.addObject("Part::Circle", obj_name)
    obj.Radius = rad

    App.ActiveDocument.recompute()
    return obj

circle = my_create_circle(5.0, "CircleName")
```

Alternatively we can create a circle by defining its center, axis and radius:

```
import FreeCAD as App
import Part

doc = App.activeDocument()

center = App.Vector(1, 2, 3)
axis = App.Vector(1, 1, 1)
radius = 10
circle = Part.Circle(center, axis, radius)
obj = doc.addObject("Part::Feature", "Circle")
obj.Shape = circle.toShape()

doc.recompute()
```

Or by defining three points on its circumference(圆周):

```
import FreeCAD as App
import Part

doc = App.activeDocument()

p1 = App.Vector(10, 0, 0)
p2 = App.Vector(0, 10, 0)
p3 = App.Vector(0, 0, 10)
circle = Part.Circle(p1, p2, p3)
obj = doc.addObject("Part::Feature", "Circle")
obj.Shape = circle.toShape()

doc.recompute()
```

Note again, we used the circle (geometric primitive) to construct a shape. We can of course still access our construction geometry afterwards, by doing:

可以获取几何属性

```
shape = obj.Shape
edge = shape.Edges[0]
curve = edge.Curve  # 曲线？
```

Here we take the `Shape` of our object `obj` and then its list of `Edges`. In this case there will be only one edge because we made the shape out of a single circle. So we take only the first item in the `Edges` list, and then take its curve. Every edge has a `Curve`, which is the geometric primitive it is based on.

获取器件的Shape属性和Edges列表，这个例子，只有一个edge，因为我们用一个圆创建了shape，所以取Edges列表中的第一个，然后获取它的曲线，每个edge(边缘)都有一个以曲线作为基本单元

### Arc(圆弧)

An arc can be created like this:

```
import FreeCAD as App
import Part

doc = App.activeDocument()

p1 = App.Vector(10, 0, 0)
p2 = App.Vector(0, 10, 0)
p3 = App.Vector(-10, 0, 0)
arc = Part.Arc(p1, p2, p3)
obj = doc.addObject("Part::Feature", "Arc")
obj.Shape = arc.toShape()

doc.recompute()
```

This draws a half circle. The center is at (0, 0, 0). The radius is 10. P1 is the start point on +X axis. P2 is the middle point on +Y axis and P3 is the end point on -X axis.

这里画了一个半圆，中心是(0,0,0)，半径是10。p1是x轴上的起点，p2是y轴上的中间点，p3是x轴负方向的终点

We can also create an arc from a circle:

```
import FreeCAD as App
import Part

doc = App.activeDocument()

p1 = App.Vector(10, 0, 0)
p2 = App.Vector(0, 10, 0)
p3 = App.Vector(-10, 0, 0)
circle = Part.Circle(p1, p2, p3)
arc = Part.ArcOfCircle(circle, 0.0, 0.7854)
obj = doc.addObject("Part::Feature", "Arc")
obj.Shape = arc.toShape()

doc.recompute()
```

It needs a circle, and a start angle and end angle in radians.(弧度)

