### 自定义View-Canvas

----画布，是一种绘制时的规则（Android平台2D图形绘制的基础）

> 规定绘制内容时的规则 & 内容

**1. Paint类**

----画笔，确定绘制内容的具体效果，如：颜色、大小

使用步骤：
- 创建画笔对象
- 设置画笔
- 初始化画笔（尽量在View的构造函数初始化）

**1.1 创建画笔对象**
```
private Paint mPaint = new Paint();
```

**1.2 设置画笔**
```
private void initPaint() {

    // 设置画笔颜色
    // 可直接引入Color类，如Color.red等
    mPaint.setColor(int color); 
    
    // 设置画笔模式
    mPaint.setStyle(Style style); 
    
    // Style有3种类型：
    // Paint.Style.FILLANDSTROKE（描边+填充）
    // Paint.Style.FILL（只填充不描边）
    // Paint.Style.STROKE（只描边不填充）
        
    //设置画笔的粗细
    mPaint.setStrokeWidth(float width)      
    // 如设置画笔宽度为10px
    mPaint.setStrokeWidth(10f);    

    // 得到画笔的颜色     
    mPaint.getColor()      
    
    // 设置Shader
    // 即着色器，定义了图形的着色、外观
    // 可以绘制出多彩的图形
    // 具体请参考文章：http://blog.csdn.net/iispring/article/details/50500106
    Paint.setShader(Shader shader)  

    // 设置画笔的a,r,p,g值
    mPaint.setARGB(int a, int r, int g, int b) 
    
    // 设置透明度
    mPaint.setAlpha(int a)   
    
    // 得到画笔的Alpha值
    mPaint.getAlpha()        

    // 设置字体大小
    mPaint.setTextSize(float textSize)       

    // 设置对齐方式   
    setTextAlign（）
    // LEFT：左对齐
    // CENTER：居中对齐
    // RIGHT：右对齐

    // 设置文本的下划线
    setUnderlineText(boolean underlineText)      
        
    // 设置文本的删除线
    setStrikeThruText(boolean strikeThruText)    

    // 设置文本粗体
    setFakeBoldText(boolean fakeBoldText)  
        
    // 设置斜体
    Paint.setTextSkewX(-0.5f);

    // 设置文字阴影
    Paint.setShadowLayer(5,5,5,Color.YELLOW);
}
```

**1.3 初始化画笔**
```
public CustomView(Context context, AttributeSet attrs) {
    super(context, attrs);
    initPaint();
}
```

**2. Canvas类**

**2.1 创建Canvas对象**

- 方法1：使用构造方法创建Canvas对象
```
Canvas canvas = new Canvas()；
```

- 方法2：通过传入装载画布Bitmap对象创建Canvas对象 (Bitmap上存储所有绘制在Canvas的信息)
```
Canvas canvas = new Canvas(bitmap)
```

- 方法3：重写View.onDraw()创建Canvas对象
```
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    // 在这里获取Canvas对象
}
```

- 方法4：在SurfaceView里画图时创建Canvas对象、
```
SurfaceView surfaceView = new SurfaceView(this);

// 从SurfaceView的surfaceHolder里锁定获取Canvas
SurfaceHolder surfaceHolder = surfaceView.getHolder();

// 获取Canvas
Canvas canvas = surfaceHolder.lockCanvas();
        
// 进行Canvas操作...

// Canvas操作结束之后解锁并执行Canvas
surfaceHolder.unlockCanvasAndPost(canvas);
```

**2.2 Canvas的绘制**
- 绘制颜色
- 绘制文本
- 绘制基本形状
- 绘制图片
- 绘制路径
- Canvas操作

**刷新View：**
- requestLayout()：view会执行onMeasure（先）及 onLayout（后）方法
- invalidate()：view 会调用 onDraw()方法

**注意：**
- 先调用requestLayout()方法，再调用invalidate()方法
- 若只改变宽高调用 requestLayout() 方法即可
- 若只更新内容调用 invalidate()方法

**2.2.1 绘制颜色**
- drawColor()
- drawRGB()
- drawARGB()
```
canvas.drawColor(Color.BLUE);

canvas.drawRGB(0, 255, 0);

canvas.drawARGB(255, 255, 0, 0);
```

**2.2.2 绘制文本**
- 绘制文字
- 绘制文字时指定每个文字的位置
- 根据路径绘制文字

**2.2.2.1 绘制文字**

- drawText()
```
// drawText (String text, float x, float y, Paint paint)
canvas.drawText("zhaoqy", 300, 400, paint);

// drawText (CharSequence text, int start, int end, float x, float y, Paint paint)
canvas.drawText("zhaoqy", 1, 4, 300, 400, paint);
```

**2.2.2.2 绘制文字时指定每个文字的位置**

- drawPosText()
```
// drawPosText (String text, float[] pos, Paint paint)
canvas.drawPosText("zhaoq", new float[]{
        100, 100,    // 第一个字符位置
        200, 200,    // 第二个字符位置
        300, 300,    // ...
        400, 400,
        500, 500
    }, paint);
    
    
// drawPosText (char[] text, int index, int count, float[] pos, Paint paint)
char[] chars = "zhaoq".toCharArray();
canvas.drawPosText(chars, 1, 3, new float[]{
        300, 300,    // 指定的第一个字符位置
        400, 400,    // 指定的第二个字符位置
        500, 500,    // 指定的第三个字符位置
    }, paint);
```

**2.2.2.3 根据路径绘制文字**

- drawTextOnPath()
```
// 1.创建路径对象
Path path = new Path();
// 2. 设置路径轨迹
path.cubicTo(540, 750, 640, 450, 840, 600);
// 3. 画路径
paint.setColor(Color.YELLOW);
canvas.drawPath(path, paint);
// 4. 画出在路径上的字
paint.setColor(Color.RED);
canvas.drawTextOnPath("在Path上写的字: zhaoqy", path, 50, 0, paint);
```

**2.2.3 绘制基本形状**
- 点
- 线
- 矩形
- 圆角矩形
- 圆弧
- 圆
- 椭圆
 
**2.2.3.1 点** 

----在某个坐标处绘制点

- drawPoint()
- drawPoints()
```
drawPoint(float x, float y, Paint paint);

Paint paint = new Paint();
// 在绘制点之前，需要设置画笔的宽度，否则不能画出来
paint.setStrokeWidth(10);
canvas.drawPoint(200, 200, paint);

canvas.drawPoints(new float[]{ 400, 400, 400, 500, 400, 600 }, paint);
```

**2.2.3.2 线**

----两点（初始点 & 结束点）确定一条直线

- drawLine()
- drawLines()
```
drawLine(float startX, float startY, float stopX, float stopY, Paint paint) 
canvas.drawLine(100, 200, 700, 200, paint);

drawLines(float[] pts, Paint paint)
canvas.drawLines(new float[]{ 400, 500, 500, 500, 400, 600, 500, 600 }, paint);
```

**2.2.3.3 矩形**

----矩形的对角线顶点确定一个矩形（一般是采用左上角和右下角的两个点的坐标）

- drawRect()
```
// 方法1：直接传入两个顶点的坐标
// 两个顶点坐标分别是：（100,100），（800,800）
canvas.drawRect(100, 100, 800, 800, paint);

// 方法2：将两个顶点坐标封装为Rect
Rect rect = new Rect(100, 100, 800, 800);
canvas.drawRect(rect, paint);

// 方法3：将两个顶点坐标封装为RectF
RectF rectF = new RectF(100, 100, 800, 800);
canvas.drawRect(rectF, paint);
```

**2.2.3.4 圆角矩形**

----矩形的对角线顶点确定一个矩形

- drawRoundRect()
```
RectF rectF = new RectF(100, 100, 800, 400);
canvas.drawRoundRect(rectF, 30, 30, paint);
```

**2.2.3.5 圆弧**

----通过圆弧角度的起始位置和扫过的角度确定圆弧

- drawArc()
```
// 绘制圆弧1(无使用中心)
RectF rectF = new RectF(100, 100, 800,400);
// 绘制背景矩形
canvas.drawRect(rectF, paint);
// 绘制圆弧
paint.setColor(Color.RED);
canvas.drawArc(rectF, 0, 90, false, paint);

// 绘制圆弧2(使用中心)
RectF rectF2 = new RectF(100, 600, 800, 900);
// 绘制背景矩形
paint.setColor(Color.BLUE);
canvas.drawRect(rectF2, paint);
// 绘制圆弧
paint.setColor(Color.RED);
canvas.drawArc(rectF2, 0, 90, true, paint);
```

**2.2.3.6 圆**

----圆心坐标+半径决定圆

- drawCircle()
```
canvas.drawCircle(500, 500, 400, paint);
```

**2.2.3.7 椭圆**

----矩形的对角线顶点确定矩形，根据传入矩形的长宽作为长轴和短轴画椭圆

- drawOval()
```
RectF rectF = new RectF(100, 100, 800, 400);
paint.setColor(Color.BLUE);
canvas.drawOval(rectF, paint);
```

**2.2.4 绘制图片**
- 矢量图
- 位图

**2.2.4.1 矢量图**

----绘制矢量图的内容

- drawPicture()

**2.2.4.2 位图**

- drawBitmap()



**2.2.5 绘制路径**
- drawPath()

**2.2.6 Canvas操作**
- 剪裁
- 变换
- 快照


