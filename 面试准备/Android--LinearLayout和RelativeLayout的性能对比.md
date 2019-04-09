# Android--LinearLayout和RelativeLayout的性能对比



LinearLayout和RelativeLayout的onLayout和OnMeasure的时间基本上一致，而它们的OnMeasure执行时间上有差异。



```jav
@Override  
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {  
//...
View[] views = mSortedHorizontalChildren;
int count = views.length;
for (int i = 0; i < count; i++) {
    View child = views[i];
    if (child.getVisibility() != GONE) {
         LayoutParams params = (LayoutParams) child.getLayoutParams();
         applyHorizontalSizeRules(params, myWidth);
         measureChildHorizontal(child, params, myWidth, myHeight);    //measureChild 水平方向
         if (positionChildHorizontal(child, params, myWidth, isWrapContentWidth)) {
               offsetHorizontalAxis = true;
         }
    }
}
 
views = mSortedVerticalChildren;
count = views.length;
for (int i = 0; i < count; i++) {
     View child = views[i];
     if (child.getVisibility() != GONE) {
           LayoutParams params = (LayoutParams) child.getLayoutParams();
           applyVerticalSizeRules(params, myHeight);
           measureChild(child, params, myWidth, myHeight);		//measureChild 垂直方向
           if (positionChildVertical(child, params, myHeight, isWrapContentHeight)) {
                 offsetVerticalAxis = true;
           }
           if (isWrapContentWidth) {
                 width = Math.max(width, params.mRight);
           }
           if (isWrapContentHeight) {
                 height = Math.max(height, params.mBottom);
           }
           if (child != ignore || verticalGravity) {
                 left = Math.min(left, params.mLeft - params.leftMargin);
                 top = Math.min(top, params.mTop - params.topMargin);
           }
           if (child != ignore || horizontalGravity) {
                 right = Math.max(right, params.mRight + params.rightMargin);
                 bottom = Math.max(bottom, params.mBottom + params.bottomMargin);
           }
       }
  }
  //...
}

```



查看源码我们发现RelativeLayout会对子View做两次measure。这是由于RelativeLayout是基于相对位置的，而且子View会在横向和纵向两个方向上分布，因此，需要在横向和纵向分别进行一次measure过程。



同时需要注意的是View.measure()方法存在以下优化：

```java
public final void measure(int widthMeasureSpec, int heightMeasureSpec) {
        if ((mPrivateFlags & FORCE_LAYOUT) == FORCE_LAYOUT ||
                widthMeasureSpec != mOldWidthMeasureSpec ||
                heightMeasureSpec != mOldHeightMeasureSpec) {
        ...
        mOldWidthMeasureSpec = widthMeasureSpec;
        mOldHeightMeasureSpec = heightMeasureSpec;
}
```



即如果我们或者我们的子View没有要求强制刷新，而父View给子View传入的值也没有变化（也就是说子View的位置没变化），就不会做无谓的测量。RelativeLayout在onMeasure中做横向测量时，纵向的测量结果尚未完成，只好暂时使用myHeight传入子View系统。这样会导致在子View的高度和RelativeLayout的高度不相同时（设置了Margin），上述优化会失效，在View系统足够复杂时，效率问题就会很明显。



```java
@Override  
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {  
  if (mOrientation == VERTICAL) {  
    measureVertical(widthMeasureSpec, heightMeasureSpec);  
  } else {  
    measureHorizontal(widthMeasureSpec, heightMeasureSpec);  
  }  
}  
//LinearLayout会先做一个简单横纵方向判断，我们选择纵向这种情况继续分析
void measureVertical(int widthMeasureSpec, int heightMeasureSpec) {
//...
for (int i = 0; i < count; ++i) {  
      final View child = getVirtualChildAt(i);  
      //... child为空、Gone以及分界线的情况略去
     //累计权重
      LinearLayout.LayoutParams lp = (LinearLayout.LayoutParams) child.getLayoutParams();  
      totalWeight += lp.weight;  
      //计算
      if (heightMode == MeasureSpec.EXACTLY && lp.height == 0 && lp.weight > 0) {  
            //精确模式的情况下，子控件layout_height=0dp且weight大于0无法计算子控件的高度
            //但是可以先把margin值合入到总值中，后面根据剩余空间及权值再重新计算对应的高度
            final int totalLength = mTotalLength;  
            mTotalLength = Math.max(totalLength, totalLength + lp.topMargin + lp.bottomMargin);  
      } else {  
           if (lp.height == 0 && lp.weight > 0) {  
            //如果这个条件成立，就代表 heightMode不是精确测量以及wrap_conent模式
            //也就是说布局是越小越好，你还想利用权值多分剩余空间是不可能的，只设为wrap_content模式
                 lp.height = LayoutParams.WRAP_CONTENT;  
           }  
  
          // 子控件测量
          measureChildBeforeLayout(child, i, widthMeasureSpec,0, heightMeasureSpec,totalWeight== 0 ? mTotalLength :0);         
          //获取该子视图最终的高度，并将这个高度添加到mTotalLength中
          final int childHeight = child.getMeasuredHeight();  
          final int totalLength = mTotalLength;  
          mTotalLength = Math.max(totalLength, totalLength + childHeight + lp.topMargin + lp.bottomMargin + getNextLocationOffset(child)); 
          } 
        //...
}
```

源码中已经标注了一些注释，需要注意的是在每次对child测量完毕后，都会调用child.getMeasuredHeight()获取该子视图最终的高度，并将这个高度添加到mTotalLength中。但是getMeasuredHeight暂时避开了lp.weight>0且高度为0子View，因为后面会将把剩余高度按weight分配给相应的子View。因此可以得出以下结论：

（1）如果我们在LinearLayout中不使用weight属性，将只进行一次measure的过程。

（2）如果使用了weight属性，LinearLayout在第一次测量时获取所有子View的高度，之后再将剩余高度根据weight加到weight>0的子View上。

**由此可见，weight属性对性能是有影响的。**



3.   总结论

（1）RelativeLayout慢于LinearLayout是因为它会让子View调用2次measure过程，而LinearLayout只需一次，但是有weight属性存在时，LinearLayout也需要两次measure。应尽量减少`weight`的使用。



（2）RelativeLayout的子View如果高度和RelativeLayout不同，会导致RelativeLayout在onMeasure()方法中做横向测量时，纵向的测量结果尚未完成，只好暂时使用自己的高度传入子View系统。而父View给子View传入的值也没有变化就不会做无谓的测量的优化会失效，解决办法就是可以使用padding代替margin以优化此问题。

（3）在不响应层级深度的情况下，使用Linearlayout而不是RelativeLayout。



结论中的第三条也解释了文章前言中的问题：DecorView的层级深度已知且固定的，上面一个标题栏，下面一个内容栏，采用RelativeLayout并不会降低层级深度，因此这种情况下使用LinearLayout效率更高。

而为开发者默认新建RelativeLayout是希望开发者能采用尽量少的View层级，很多效果是需要多层LinearLayout的嵌套，这必然不如一层的RelativeLayout性能更好。因此我们应该尽量减少布局嵌套，减少层级结构，使用比如viewStub，include等技巧。可以进行较大的布局优化。具体技巧的使用后面会继续写文总结。



