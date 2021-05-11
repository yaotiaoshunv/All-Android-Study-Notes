> 知识路径：Android > View
>
> version：2021/4/13
>
> review：2021/4/13
>
> 掌握程度：初学



前言：（可选）

## 一、预备知识

可选

## 二、View

### 2.1 MeasureSpec

![image-20210412161840582](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412161840582.png)

![image-20210412161856195](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412161856195.png)

![image-20210412161936489](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412161936489.png)

![image-20210412162037749](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412162037749.png)

![image-20210412162617322](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412162617322.png)

![image-20210412162626273](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412162626273.png)



### 2.2 MotionEvent

![image-20210412162926281](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412162926281.png)

![image-20210412162938433](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412162938433.png)

### 2.3 VelocityTracker

![image-20210412163040123](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412163040123.png)

### 2.4 GestureDetector

```java
final ImageView imageView = findViewById(R.id.iv_1);

final GestureDetector gestureDetector = new GestureDetector(this, new GestureDetector.OnGestureListener() {
    @Override
    public boolean onDown(MotionEvent e) {
        return false;
    }

    @Override
    public void onShowPress(MotionEvent e) {

    }

    @Override
    public boolean onSingleTapUp(MotionEvent e) {
        return false;
    }

    @Override
    public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
        Toast.makeText(MainActivity.this, "onScroll", Toast.LENGTH_SHORT).show();
        return true;
    }

    @Override
    public void onLongPress(MotionEvent e) {
        Toast.makeText(MainActivity.this, "onLongPress", Toast.LENGTH_SHORT).show();
    }

    @Override
    public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
        return false;
    }
});
gestureDetector.setOnDoubleTapListener(new GestureDetector.OnDoubleTapListener() {
    @Override
    public boolean onSingleTapConfirmed(MotionEvent e) {
        return false;
    }

    @Override
    public boolean onDoubleTap(MotionEvent e) {
        Toast.makeText(MainActivity.this, "onDoubleTap", Toast.LENGTH_SHORT).show();
        return true;
    }

    @Override
    public boolean onDoubleTapEvent(MotionEvent e) {
        return false;
    }
});
// 解决长按屏幕后无法拖动的问题
gestureDetector.setIsLongpressEnabled(false);

imageView.setOnTouchListener(new View.OnTouchListener() {
    @Override
    public boolean onTouch(View v, MotionEvent event) {
        gestureDetector.onTouchEvent(event);
        gestureDetector.setIsLongpressEnabled(true);
        return true;
    }
});
```

![image-20210412170737725](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412170737725.png)

### 2.5 Scroller

![image-20210412171442616](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412171442616.png)

![image-20210412171859942](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412171859942.png)

![image-20210412171907645](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412171907645.png)

### 2.6 View 的滑动

![image-20210413093600963](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413093600963.png)

```java
public void scrollTo(int x, int y) {
    if (mScrollX != x || mScrollY != y) {
        int oldX = mScrollX;
        int oldY = mScrollY;
        mScrollX = x;
        mScrollY = y;
        invalidateParentCaches();
        onScrollChanged(mScrollX, mScrollY, oldX, oldY);
        if (!awakenScrollBars()) {
            postInvalidateOnAnimation();
        }
    }
}

    public void scrollBy(int x, int y) {
        scrollTo(mScrollX + x, mScrollY + y);
    }
```

![image-20210413093711208](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413093711208.png)

![image-20210413094609743](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413094609743.png)

### 2.7 View 的事件分发

![image-20210413094729102](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413094729102.png)

![image-20210413100635319](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413100635319.png)

![image-20210413100649421](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413100649421.png)

![image-20210413100220742](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413100220742.png)

![image-20210413100227103](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413100227103.png)

### 2.8 在 Activity 中获取某个 View 的宽高

![image-20210413104348966](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413104348966.png)

![image-20210413104709544](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413104709544.png)

![image-20210413104731950](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413104731950.png)

![image-20210413104748149](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413104748149.png)

### 2.9 Draw 的基本流程

```java
// 绘制基本上分为6个步骤
public void draw(Canvas canvas) {
...
    /*
     * Draw traversal performs several drawing steps which must be executed
     * in the appropriate order:
     *
     *      1. Draw the background
     *      2. If necessary, save the canvas' layers to prepare for fading
     *      3. Draw view's content
     *      4. Draw children
     *      5. If necessary, draw the fading edges and restore layers
     *      6. Draw decorations (scrollbars for instance)
     *      7. If necessary, draw the default focus highlight
     */
	// 步骤一：绘制 View 的背景
    // Step 1, draw the background, if needed
    int saveCount;

    drawBackground(canvas);

    // skip step 2 & 5 if possible (common case)
    final int viewFlags = mViewFlags;
    boolean horizontalEdges = (viewFlags & FADING_EDGE_HORIZONTAL) != 0;
    boolean verticalEdges = (viewFlags & FADING_EDGE_VERTICAL) != 0;
    if (!verticalEdges && !horizontalEdges) {
        // 步骤三：绘制 View 的内容
        // Step 3, draw the content
        onDraw(canvas);

        // 步骤四：绘制 View 的子 View
        // Step 4, draw the children
        dispatchDraw(canvas);

        drawAutofilledHighlight(canvas);

        // Overlay is part of the content and draws beneath Foreground
        if (mOverlay != null && !mOverlay.isEmpty()) {
            mOverlay.getOverlayView().dispatchDraw(canvas);
        }

        // 步骤六：绘制 View 的装饰（例如滚动条等）
        // Step 6, draw decorations (foreground, scrollbars)
        onDrawForeground(canvas);

        // 步骤七：
        // Step 7, draw the default focus highlight
        drawDefaultFocusHighlight(canvas);

        if (isShowingLayoutBounds()) {
            debugDrawFocus(canvas);
        }

        // we're done...
        return;
    }

    /*
     * Here we do the full fledged routine...
     * (this is an uncommon case where speed matters less,
     * this is why we repeat some of the tests that have been
     * done above)
     */

    boolean drawTop = false;
    boolean drawBottom = false;
    boolean drawLeft = false;
    boolean drawRight = false;

    float topFadeStrength = 0.0f;
    float bottomFadeStrength = 0.0f;
    float leftFadeStrength = 0.0f;
    float rightFadeStrength = 0.0f;

    // Step 2, save the canvas' layers
    int paddingLeft = mPaddingLeft;

    final boolean offsetRequired = isPaddingOffsetRequired();
    if (offsetRequired) {
        paddingLeft += getLeftPaddingOffset();
    }

    int left = mScrollX + paddingLeft;
    int right = left + mRight - mLeft - mPaddingRight - paddingLeft;
    int top = mScrollY + getFadeTop(offsetRequired);
    int bottom = top + getFadeHeight(offsetRequired);

    if (offsetRequired) {
        right += getRightPaddingOffset();
        bottom += getBottomPaddingOffset();
    }

    final ScrollabilityCache scrollabilityCache = mScrollCache;
    final float fadeHeight = scrollabilityCache.fadingEdgeLength;
    int length = (int) fadeHeight;

    // clip the fade length if top and bottom fades overlap
    // overlapping fades produce odd-looking artifacts
    if (verticalEdges && (top + length > bottom - length)) {
        length = (bottom - top) / 2;
    }

    // also clip horizontal fades if necessary
    if (horizontalEdges && (left + length > right - length)) {
        length = (right - left) / 2;
    }

    if (verticalEdges) {
        topFadeStrength = Math.max(0.0f, Math.min(1.0f, getTopFadingEdgeStrength()));
        drawTop = topFadeStrength * fadeHeight > 1.0f;
        bottomFadeStrength = Math.max(0.0f, Math.min(1.0f, getBottomFadingEdgeStrength()));
        drawBottom = bottomFadeStrength * fadeHeight > 1.0f;
    }

    if (horizontalEdges) {
        leftFadeStrength = Math.max(0.0f, Math.min(1.0f, getLeftFadingEdgeStrength()));
        drawLeft = leftFadeStrength * fadeHeight > 1.0f;
        rightFadeStrength = Math.max(0.0f, Math.min(1.0f, getRightFadingEdgeStrength()));
        drawRight = rightFadeStrength * fadeHeight > 1.0f;
    }

    saveCount = canvas.getSaveCount();
    int topSaveCount = -1;
    int bottomSaveCount = -1;
    int leftSaveCount = -1;
    int rightSaveCount = -1;

    int solidColor = getSolidColor();
    if (solidColor == 0) {
        if (drawTop) {
            topSaveCount = canvas.saveUnclippedLayer(left, top, right, top + length);
        }

        if (drawBottom) {
            bottomSaveCount = canvas.saveUnclippedLayer(left, bottom - length, right, bottom);
        }

        if (drawLeft) {
            leftSaveCount = canvas.saveUnclippedLayer(left, top, left + length, bottom);
        }

        if (drawRight) {
            rightSaveCount = canvas.saveUnclippedLayer(right - length, top, right, bottom);
        }
    } else {
        scrollabilityCache.setFadeColor(solidColor);
    }

    // Step 3, draw the content
    onDraw(canvas);

    // Step 4, draw the children
    dispatchDraw(canvas);

    // Step 5, draw the fade effect and restore layers
    final Paint p = scrollabilityCache.paint;
    final Matrix matrix = scrollabilityCache.matrix;
    final Shader fade = scrollabilityCache.shader;

    // must be restored in the reverse order that they were saved
    if (drawRight) {
        matrix.setScale(1, fadeHeight * rightFadeStrength);
        matrix.postRotate(90);
        matrix.postTranslate(right, top);
        fade.setLocalMatrix(matrix);
        p.setShader(fade);
        if (solidColor == 0) {
            canvas.restoreUnclippedLayer(rightSaveCount, p);

        } else {
            canvas.drawRect(right - length, top, right, bottom, p);
        }
    }

    if (drawLeft) {
        matrix.setScale(1, fadeHeight * leftFadeStrength);
        matrix.postRotate(-90);
        matrix.postTranslate(left, top);
        fade.setLocalMatrix(matrix);
        p.setShader(fade);
        if (solidColor == 0) {
            canvas.restoreUnclippedLayer(leftSaveCount, p);
        } else {
            canvas.drawRect(left, top, left + length, bottom, p);
        }
    }

    if (drawBottom) {
        matrix.setScale(1, fadeHeight * bottomFadeStrength);
        matrix.postRotate(180);
        matrix.postTranslate(left, bottom);
        fade.setLocalMatrix(matrix);
        p.setShader(fade);
        if (solidColor == 0) {
            canvas.restoreUnclippedLayer(bottomSaveCount, p);
        } else {
            canvas.drawRect(left, bottom - length, right, bottom, p);
        }
    }

    if (drawTop) {
        matrix.setScale(1, fadeHeight * topFadeStrength);
        matrix.postTranslate(left, top);
        fade.setLocalMatrix(matrix);
        p.setShader(fade);
        if (solidColor == 0) {
            canvas.restoreUnclippedLayer(topSaveCount, p);
        } else {
            canvas.drawRect(left, top, right, top + length, p);
        }
    }

    canvas.restoreToCount(saveCount);

    drawAutofilledHighlight(canvas);

    // Overlay is part of the content and draws beneath Foreground
    if (mOverlay != null && !mOverlay.isEmpty()) {
        mOverlay.getOverlayView().dispatchDraw(canvas);
    }

    // Step 6, draw decorations (foreground, scrollbars)
    onDrawForeground(canvas);

    // Step 7, draw the default focus highlight
    drawDefaultFocusHighlight(canvas);

    if (isShowingLayoutBounds()) {
        debugDrawFocus(canvas);
    }
}
```



### 2.10 自定义 View

![image-20210413105648794](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210413105648794.png)



使用

原理

## 三、总结

本文总结：核心知识点

## 四、思维导图

总结本文；把本文知识融入整体知识体系。

## 五、拓展

​	相关知识，横向、纵向对比；使用场景（示例）、适用范围、注意事项；

​	头脑风暴：联想相关知识，准备对比，为进一步深入学习做准备。



**参考：**

1、《Android核心知识点笔记V2020.03.30》