### 估值器 TypeEvaluator

----设置动画如何从初始值过渡到结束值的逻辑

    - 插值器（Interpolator）：决定值的变化模式（匀速、加速等）
    
    - 估值器（TypeEvaluator）：决定值的具体变化数值


- ValueAnimator.ofInt（） & ValueAnimator.ofFloat（）都具备系统内置的估值器，   即FloatEvaluator & IntEvaluator。

- ValueAnimator.ofObject（），需自定义估值器（TypeEvaluator）来告知系统如何进行从初始对象过渡到结束对象的逻辑。

自定义实现的逻辑如下：
```
// 实现TypeEvaluator接口 
public class ObjectEvaluator implements TypeEvaluator { 

    // 复写evaluate（） 
    // 在evaluate（）里写入对象动画过渡的逻辑
    @Override 
    public Object evaluate(float fraction, Object startValue, Object endValue) { 
        // 参数说明 
        // fraction：表示动画完成度（根据它来计算当前动画的值）
        // startValue、endValue：动画的初始值和结束值 
        
        ...
        
        // 写入对象动画过渡的逻辑 
        
        // 返回对象动画过渡的逻辑计算后的值 
        return value;
    }
}
```
