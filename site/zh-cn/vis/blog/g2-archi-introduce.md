<!--
index: 1
title: 可视化框架设计
authors:
  - name: 萧庆
    avatar: ${assets}/image/members/xiaoqing.jpg
date: "2017-11-22"
landscape: ${assets}/image/vis/blog/g2-archi-introduce.jpg
-->

## 目录

* 可视化框架的构成
* G2 的框架结构
* 数据图形映射的流程

## 可视化框架的构成

数据可视化的本质是：将数据映射到图形，同时增加一些辅助信息，让用户读懂数据。

一个可视化框架需要四部分：
* 数据处理模块，对数据进行加工的模块，包括一些数据处理方法。例如：合并、分组、排序、过滤、计算统计信息等
* 图形映射模块，将数据映射到图形视觉通道的过程。例如：将数据映射成颜色、位置、大小等
* 图形展示模块，决定使用何种图形来展示数据，点、线、面等图形标记
* 辅助信息模块，用于说明视觉通道跟数据的映射关系，例如：坐标轴、图例、辅助文本等

## G2 的框架结构

G2 实现了上面的四个模块，并且对着四个模块做了更进一步的细分：

![e0b4b884-c9eb-4c30-a1f8-f69d52168f45.png](https://gw.alipayobjects.com/zos/rmsportal/tpLLgZWOaMPqQKyxLsGN.png) 

这些细分的模块在后面的章节中各自介绍

### 数据处理模块

数据处理模块已经从 G2 中独立出来，作为 DataSet 模块单独提供，主要包含下面几个模块：

* DataSet 包含多个 DataView, 管理多个数据源
* DataView 每个 DataView 对应一个数据源，可以进行布局（Layout)、数据加工（Transform)
* Layout 对数据进行布局
* Transform 对数据进行加工、统计等处理，包括对地图数据的处理

### 图形映射模块

图形映射模块完成了将数据映射到数学空间 [0-1]，再从数学空间映射到画布空间的整个过程，主要包括三个模块：

* [Scale](/zh-cn/g2/3.x/tutorial/scale.html) 将数据映射到 0-1 之间，方便映射到画布上
* [Attr](/zh-cn/g2/3.x/tutorial/attr.html) 图形属性，数据映射到图形的属性（视觉通道），包括位置、颜色、大小、形状等
* [Coord](/zh-cn/g2/3.x/tutorial/coord.html) 展示图形需用到的坐标系，将数据映射到位置的属性（视觉通道）

### 辅助信息

辅助信息用于标示数据在各种图形属性上的映射，使得用户更容易的理解数据，主要包括下面几个模块：
* [Axis](/zh-cn/g2/3.x/tutorial/axes.html) 坐标轴，辅助用户识别图形所在的位置，判断数据大小的辅助元素
* [Tooltip](/zh-cn/g2/3.x/tutorial/tooltip.html) 提示信息，用户鼠标在画布上移动时，实时出现的鼠标附近的数据信息
* [Guide](/zh-cn/g2/3.x/tutorial/guide.html) 其他辅助元素，可以在图上添加辅助的文本、辅助图片、辅助线等，增强用户对图形的认知。
* [Legend](/zh-cn/g2/3.x/tutorial/legend.html) 图例，辅助用户识别图形的大小、颜色、形状，通常用于判断数据对应图形的分类

### 图形展示

* [Chart](/zh-cn/g2/3.x/tutorial/create-chart.html) 图表，展示图形的入口，会生成一个画布，同时管理数据到图形映射的所有周期
* [View](/zh-cn/g2/3.x/tutorial/how-to-create-view.html) 视图，一个画布上可以显示多张图表，每个图表有各自的绘图区域、数据源、坐标系等信息
* [Geom](/zh-cn/g2/3.x/tutorial/geom.html) 数据映射到的图形标识，G2 中的图形标识有：点、线、路径、面积、多边形等
* [Shape](/zh-cn/g2/3.x/tutorial/geom.html#_几何标记和图形形状) 更加细分的图形标识，例如，点可以分为圆点、正方形、十字等，线可以分为点线、折线、曲线等


## 数据图形映射的流程

可视化从数据映射到图形需要以下流程：

![image](https://gw.alipayobjects.com/zos/rmsportal/ftaXEHuBzyHUAlMGwnos.png)

* 原始数据：加载到页面上的 JSON 数组
* 统计分析：统计函数加工数据
* 预处理数据：每个视图接收到的数据
* 过滤：行过滤，列过滤（是否保存整条数据，后面讨论）
* 关注数据： 对数据进行行列的过滤，当前图表关注的数据
* 映射：将数据从数值域转换几何属性
* 绘制：调用绘图库，绘制出图形
* 图像数据：最终形成的图表

### g2 的数据转换流程

G2 的数据映射到图形的过程整体上遵循这个流程，但是细节上有所增加：

![3cdb7f65-bae9-405e-b5cd-87daff1a3aa5.png](https://gw.alipayobjects.com/zos/rmsportal/PJhhvRtPjpEspyWwVnBQ.png) 

几个大的流程：

* 对数据进行预处理，这部分工作是在图表的外部进行的，都包含在 DataSet 包中的数据处理模块中
* 图表对数据进行初步的处理，对数据进行格式化成数字、调整数据等操作
* 将数据映射到数学空间 [0-1] 中，然后再映射到各个图形属性，并绘制图形

小流程的简介：

* transform，DataSet 模块中可以使用多个 transform 对数据进行转换，包括过滤、补零、排序、统计、地图数据处理、地理映射等
* 过滤，对数据进行过滤，可以设置一些过滤条件，控制数据的展示
* 数字化，将分类类型，时间类型的数据转换成数字, 为了可以执行 “数据调整”, 调整图形在画布上的位置
* 数据调整，为了更清晰的展示数据，图形有时候需要层叠、分组、散开等，此时需要对数据进行调整

  ![image](https://gw.alipayobjects.com/zos/rmsportal/pnWwcbPSMGOpxBNaIuaH.png)
  ![image](https://gw.alipayobjects.com/zos/rmsportal/RMFKTiAVVlwVTgyAwHck.png)

* 统一度量，度量包含了数据字段的信息，例如连续字段的最大值、最小值等信息，分类字段包含的分类, 数据一旦进行调整，那么度量中的信息不再准确，所以需要进行度量的调整，同时存在多层图表的情况，例如在地图上显示点图时，这是需要多个数据源，但是用于表示位置的经纬度信息的范围必须进行统一。
* 归一化，将数据的值映射到数学空间 0-1 空间内，方便数据到图形属性（视觉通道）的映射
* 计算图形需要的点，绘制图形时需要多个点，例如绘制一个柱状图，需要 4 个点，如果将坐标系转换，仍然是这 4 个点，仅仅是连接点的方式不同，就会生成不同的图表

  ![image](https://gw.alipayobjects.com/zos/rmsportal/bKibtAIWKvDfahfpFhuk.png)

* 映射，将数据映射到图形空间的视觉通道
* 绘制，绘制完成所有的图形

更详细的数据流程介绍和示例在后面的数据处理模块 DataSet 中以及各类图表中详细介绍

## 更多

我们可以看到，在数据进行图形映射的流程中，数据类型非常重要，不同的数据类型影响不同的映射方式，后面我们会继续介绍数据分类和度量，敬请期待。
