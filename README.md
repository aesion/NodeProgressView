---
title: NodeProgressView 物流节点进度
date: 2016-06-26 12:42:09
tags: 
	- View
	- Android
categories: Android
---

## 关于 ##

# NodeProgressView #




>用来显示物节点进度的自定义View，仿淘宝
#### 版本尚未发布到Jcenter，后续将会逐一发布，让你使用更简洁 ####


# 使用 #

先拷贝View包下面的文件到你的项目，记得样式等Attr文件也要复制，不然会报错

在你的XMl文件中：

    <com.nodeprogress.nodeprogress.view.NodeProgressView
        android:id="@+id/npv_NodeProgressView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:width="2dp"
        app:nodeRadius="5dp"
        />

然后第一步，你的物流数据，我们先假设几条，一般要从服务器获取

```Java
        List<LogisticsData> logisticsDatas;
        logisticsDatas = new ArrayList<>();
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("快件在【相城中转仓】装车,正发往【无锡分拨中心】已签收,签收人是【王漾】,签收网点是【忻州原平】"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("快件在【相城中转仓】装车,正发往【无锡分拨中心】"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("【北京鸿运良乡站】的【010058.269】正在派件"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("快件到达【潍坊市中转部】,上一站是【】"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("快件在【潍坊市中转部】装车,正发往【潍坊奎文代派】"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("快件到达【潍坊】,上一站是【潍坊市中转部】"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("快件在【武汉分拨中心】装车,正发往【晋江分拨中心】"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("【北京鸿运良乡站】的【010058.269】正在派件"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("【北京鸿运良乡站】的【010058.269】正在派件"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("【北京鸿运良乡站】的【010058.269】正在派件"));
        logisticsDatas.add(new LogisticsData().setTime("2016-6-28 15:13:02").setContext("【北京鸿运良乡站】的【010058.269】正在派件"));
```

第二，找到你的控件并为其设置NodeProgressAdapter适配器


```Java
        NodeProgressView nodeProgressView = (NodeProgressView) findViewById(R.id.npv_NodeProgressView);
        nodeProgressView.setNodeProgressAdapter(new NodeProgressAdapter() {

            @Override
            public int getCount() {
                return logisticsDatas.size();
            }

            @Override
            public List<LogisticsData> getData() {
                return logisticsDatas;
            }
        });
```


OK，大功告成，剩下的就是交给控件了，有兴趣的童鞋可以看看源码，写的不是很好，多多指教，是不是很简单呢；

---


下面将源码贴出，大家可以看一看
# NodeProgressView.class #



```Java
/**
 * User: Daidingkang(ddk19941017@Gmail.com)
 * Date: 2016-06-28
 * Time: 09:43
 * FIXME
 */
public class NodeProgressView extends View {

    float width;
    float nodeRadius;

    Paint paint;

    Context context;

    /**
     * 节点间隔
     */
    int nodeInterval;

    /**
     * 边距
     */
    int left = 20;
    int top = 30;

    int dWidth;
    int dHeight;


    public NodeProgressView(Context context) {
        super(context);
        this.context = context;
        init();
    }

    public NodeProgressView(Context context, AttributeSet attrs) {
        super(context, attrs);
        this.context = context;
        TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.NodeProgressView);
        width = typedArray.getDimension(R.styleable.NodeProgressView_width, 5);
        nodeRadius = typedArray.getDimension(R.styleable.NodeProgressView_nodeRadius, 10);
        init();
    }

    public NodeProgressView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init();
    }

    private void init() {
        paint = new Paint();
        paint.setColor(getResources().getColor(R.color.nodeColor));
        paint.setAntiAlias(true);

        nodeInterval = dip2px(context, 80);

        WindowManager wm = (WindowManager) getContext()
                .getSystemService(Context.WINDOW_SERVICE);

        dWidth = wm.getDefaultDisplay().getWidth();
        dHeight = wm.getDefaultDisplay().getHeight();
    }

    NodeProgressAdapter nodeProgressAdapter;

    /**
     * 设置适配数据
     */
    public void setNodeProgressAdapter(NodeProgressAdapter nodeProgressAdapter) {
        this.nodeProgressAdapter = nodeProgressAdapter;
    }


    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        if (nodeProgressAdapter == null || nodeProgressAdapter.getCount() == 0)
            return;
        List data = nodeProgressAdapter.getData();

//        canvas.translate(20,30);偏移位置，防止遮挡
        canvas.drawRect(left, top, width + left, nodeProgressAdapter.getCount() * nodeInterval + top, paint);
        for (int i = 0; i < nodeProgressAdapter.getCount(); i++) {

            if (i == 0) {
                Paint mPaint = new Paint();
                mPaint.setAntiAlias(true);
                mPaint.setColor(getResources().getColor(R.color.nodeTextColor));
                //画文字
                mPaint.setTextSize(30);
                canvas.drawText(((LogisticsData)data.get(i)).getTime()+"", left * 2 + nodeRadius * 2 + 10, (i + 1) * nodeInterval + top - 20, mPaint);
//                canvas.drawText("不换行", left * 2 + nodeRadius * 2 + 10, i * nodeInterval + top + (nodeInterval / 3), mPaint);
                //文字换行
                TextPaint textPaint = new TextPaint();
                textPaint.setColor(getResources().getColor(R.color.nodeTextColor));
                textPaint.setTextSize(35.0F);
                textPaint.setAntiAlias(true);
                StaticLayout layout = new StaticLayout(((LogisticsData)data.get(i)).getContext()+"", textPaint, (int) (dWidth * 0.8), Layout.Alignment.ALIGN_NORMAL, 1.0F, 0.0F, true);
                canvas.save();
                canvas.translate(left * 2 + nodeRadius * 2 + 10, i * nodeInterval + top + (nodeInterval / 4));
                layout.draw(canvas);
                canvas.restore();//重置

                //画圆
                canvas.drawCircle(width / 2 + left, i * nodeInterval + top, nodeRadius + 2, mPaint);
                mPaint.setStyle(Paint.Style.STROKE);//设置为空心
                mPaint.setStrokeWidth(8);//空心宽度
                mPaint.setAlpha(88);
                canvas.drawCircle(width / 2 + left, i * nodeInterval + top, nodeRadius + 4, mPaint);


            } else {
                paint.setColor(getResources().getColor(R.color.nodeColor));
                canvas.drawCircle(width / 2 + left, i * nodeInterval + top, nodeRadius, paint);
                canvas.drawLine(left * 2 + nodeRadius * 2, i * nodeInterval + top, dWidth, i * nodeInterval + top, paint); //画线


                //画文字
                paint.setTextSize(30);
                canvas.drawText(((LogisticsData)data.get(i)).getTime()+"", left * 2 + nodeRadius * 2 + 10, (i + 1) * nodeInterval + top - 20, paint);
                //文字换行
                TextPaint textPaint = new TextPaint();
                textPaint.setColor(getResources().getColor(R.color.nodeColor));
                textPaint.setTextSize(35.0F);
                textPaint.setAntiAlias(true);
                StaticLayout layout = new StaticLayout(((LogisticsData)data.get(i)).getContext()+"", textPaint, (int) (dWidth * 0.8), Layout.Alignment.ALIGN_NORMAL, 1.0F, 0.0F, true);
                canvas.save();//很重要，不然会样式出错
                canvas.translate(left * 2 + nodeRadius * 2 + 10, i * nodeInterval + top + (nodeInterval / 4));
                layout.draw(canvas);
                canvas.restore();//重置
            }

        }
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        if (nodeProgressAdapter == null || nodeProgressAdapter.getCount() == 0)
            return;
        setMeasuredDimension(widthMeasureSpec, nodeProgressAdapter.getCount() * nodeInterval + top);
    }


    /**
     * 根据手机的分辨率从 dp 的单位 转成为 px(像素)
     */
    public static int dip2px(Context context, float dpValue) {
        final float scale = context.getResources().getDisplayMetrics().density;
        return (int) (dpValue * scale + 0.5f);
    }

}
```



值得注意的是使用此控件的童鞋，里面有一个JavaBean需要实现，你也可以继承自此Bean

下载的童鞋希望多多star and fork；


# 作者 #
---

戴定康

>博客：[个人博客](http://daidingkang.cc/ "个人博客")

>Csdn:[戴定康的博客](http://blog.csdn.net/ddk837239693)

---

实例：

![](https://github.com/aesion/NodeProgressView/blob/master/image/S60628-152145.jpg)
