### Bitmap

----为程序员提供了对图像文件的操作支持

**1. Bitmap基本概念**
- Bitmap是Android系统中的图像处理重要类
- 通过Bitmap，可以获取图片信息
- 获取到信息后，可以对其进行缩放、剪裁等操作

**2. Bitmap的加载方式**
- BitmapFactory.decodeByteArray() --字节数组
- BitmapFactory.decodeFile() --文件路径
- BitmapFactory.decodeResource() --资源ID
- BitmapFactory.decodeStream() --流

**3. 为什么要高效的加载Bitmap**
- 防止内存溢出
- 尽可能的节省内存开销
- 使app运行的更加流畅

理论依据：BitmapFactory.Options

**4. BitmapFactory.Options类**

----是BitmapFactory的内部类，为Bitmap的优化提供了可能

BitmapFactory.Options的重要属性：
- inJustDecodeBounds
- outWidth & outHeight
- inSampleSize