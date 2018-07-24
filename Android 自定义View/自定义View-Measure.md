### 自定义View-Measure

**1. 作用**

----测量View的宽 & 高

测量过程用到的2个类：

- ViewGroup.LayoutParams
- MeasureSpecs（父视图对子视图的测量要求）

**1.1 ViewGroup.LayoutParams**

----指定视图View 的高度（height）& 宽度（width）等布局参数

ViewGroup的子类（RelativeLayout、LinearLayout）有其对应的ViewGroup.LayoutParams子类，如：RelativeLayoutParams

**1.2 MeasureSpec**

----测量规格（MeasureSpec）= 测量模式（mode）+ 测量大小（size）

- 测量规格（MeasureSpec）：32位，int型
- 测量模式（mode）：占MeasureSpec高2位
- 测量大小（size）：占MeasureSpec底30位

测量模式：

- UNSPECIFIED
    - 父视图不约束子视图View：View可取任意尺寸
    - 应用场景：内部系统，如：ListView、ScrollView
- EXACTLY 
    - 父视图为子视图指定一个确切的尺寸
    - 子视图必须在该指定尺寸内
    - 应用场景：子View与父视图大小相等（match_parent）或具体数值（10dp）
- AT_MOST
    - 父视图为子视图指定一个最大的尺寸
    - 子视图必须在该可适应范围内
    - 应用场景：自适应大小（wrap_content）

MeasureSpec的具体使用:
```
// 1. 获取测量模式（Mode）
int specMode = MeasureSpec.getMode(measureSpec)

// 2. 获取测量大小（Size）
int specSize = MeasureSpec.getSize(measureSpec)

// 3. 通过Mode & Size 生成新的SpecMode
int measureSpec=MeasureSpec.makeMeasureSpec(size, mode);
```
----

**2. measure过程**

View类型 | measure过程
---|---
单一View | 只测量自身一个View
ViewGroup | 对ViewGroup视图中所有的View都进行测量

**2.1 单一View的measure过程**

开始 --> measure() --> onMeasure() --> setMeasureDimension() --> getDefaultSize() --> 完成

- measure()
    - 基本测量逻辑的判断
    - 调用onMeasure()进行下一步测量
- onMeasure()
    - 根据View宽 & 高的测量规格，计算View的宽 & 高值：getDefaultSize()
    - 存储测量后的View宽 & 高：setMeasureDimension()
- setMeasureDimension()
    - 存储测量后的View宽 & 高
- getDefaultSize()
    - 根据View宽 & 高的测量规格，计算View的宽 & 高值

----

**2.2 ViewGroup的measure过程**

**原理：**

- 遍历测量所有子View的尺寸
- 合并所有子View的尺行，得到ViewGroup父视图的测量值 

> 自上而下、一层一层地传递下去，直到完成整个View树的measure（）过程

**流程：**

开始 --> measure() --> onMeasure() --> measureChildren() --> measureChild() --> getChildMeasureSpec() --> setMeasureDimension() --> 完成

- measure()
    - 基本测量逻辑的判断
    - 调用onMeasure()进行下一步测量
- onMeasure()（需复写）
    - 遍历所有子View & 测量：measureChildren()
    - 合并所有子View尺寸，计算出ViewGroup的尺寸
    - 存储测量后的子View宽 & 高：setMeasureDimension()
- measureChildren()
    - 遍历子View
    - 调用measureChild()进行子View的下一步测量
- measureChild()
    - 只算单个子View的MeasureSpec参数：getChildMeasureSpec()
    - 调用每个子View的measure进行下一步测量
- getChildMeasureSpec()
    - 只算子View的MeasureSpec参数
- setMeasureDimension()
    - 存储测量后的子View宽 & 高