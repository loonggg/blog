---
title: Android Material Design系列之RecyclerView和CardView
date: 2016-07-01 12:39:04
tags:
- Material Design
- RecyclerView
- CardView
categories: 
- 技术博客
- Material Design
---
>去年很早之前，我就讲解过RecyclerView的使用，今天我们就在讲解CardView的时候，顺便再把RecyclerView同时讲解一下。RecyclerView、CardView为用于显示复杂视图的新增Widget。接下来看看如何使用吧。

### RecyclerView

#### RecyclerView介绍

RecyclerView作为替代ListView使用，它更先进，更灵活，RecyclerView标准化了ViewHolder，ListView中convertView是复用的，在RecyclerView中，是把ViewHolder作为缓存的单位了，然后convertView作为ViewHolder的成员变量保持在ViewHolder中，也就是说，假设手机屏幕可显示10个条目，则会创建10个ViewHolder缓存起来，每次复用的是ViewHolder，所以他把getView这个方法变为了onCreateViewHolder。 ViewHolder更适合多种子布局的列表，尤其IM聊天对话框列表。

注意的是：RecyclerView不提供ListView中的setOnItemClickListener方法，我们可以在ViewHolder中添加类似的点击事件。 
<!--more-->
#### RecyclerView注意事项，如何用

虽然RecyclerView充分考虑了它的扩展性，更好用，更灵活，但是用起来也有些麻烦。所以要使用RecyclerView，要好好考虑以下几点：
* RecyclerView.Adapter：RecyclerView.Adapter包含了一种新型适配器，其实与以前我们使用的适配器基本类似，只是稍微有所不同，比如viewholder它帮我们封装好了，不用像以前使用listview的适配器一样自己去写viewholder了。所以它的性能比以前应该好了不少。
* LayoutManager：这个LayoutManager类决定视图被放在画面中哪个位置，但这只是它的众多职责之一。它可以管理滚动和循环利用。LayoutManager是一个抽象类，好在系统提供了3个实现类：
1、LinearLayoutManager 现行管理器，支持横向、纵向。
2、GridLayoutManager 网格布局管理器
3、StaggeredGridLayoutManager 瀑布就式布局管理器
* ItemAnimator：ItemAnimator简单来说是会根据适配器上收到的相关通知去动画的显示组件的修改，添加和删除等。它会自动添加和移除item的动画。自带的默认效果也不错，已经非常好了。

如何用呢？这里我就不过多介绍了，因为关于RecyclerView的使用，去年我很早时间就写过一篇文章。建议大家参考：
http://mp.weixin.qq.com/s?__biz=MjM5NDkxMTgyNw==&mid=208467006&idx=1&sn=c29971b395611008319222eaffad4f31#rd

#### RecyclerView上拉更多
RecyclerView具体使用不讲了，今天我们顺便讲一下如何在RecyclerView加上拉更多的效果吧，下拉刷新我们使用SwipeRefreshLayout的效果就行。因为我看市面上目前大部分的app都是这样做的，下拉刷新用SwipeRefreshLayout的效果，自己在RecyclerView上添加上拉更多。

很可惜的是，RecyclerView并没有像ListView那样提供给我们addFooterView()那样的方法，那该如何实现呢？前面我们介绍RecyclerView时，说过RecyclerView适合多种嵌套的布局效果， ViewHolder更适合多种子布局的列表。所以我们看 RecyclerView的Adapter中的一个方法如下：
```java
public ViewHolder onCreateViewHolder(ViewGroup viewGroup, int viewType)
```
看到viewType了吧，就是在这里处理多种布局效果，上拉更多的布局和其他item其实没有什么区别。所以处理方式大家都知道了吧。

* 第一步：添加布局状态标识，并增加一项FooterView
在adapter中声明布局状态标识，是普通布局还是foot布局
```java
private static final int TYPE_NORMAL_ITEM = 0;  //普通Item
private static final int TYPE_FOOTER_ITEM = 1;  //底部FooterView
```
在getItemCount()中加1
```java
@Override
public int getItemCount() {
     //+1是加入底部的加载布局项
    return list.size() + 1;
}
```

* 第二步：重写getItemViewType判断不同布局
```java
public int getItemViewType(int position) {
    // 如果position+1等于整个布局所有数总和就是底部布局
    if (position + 1 == getItemCount()) {
         return TYPE_FOOTER_ITEM;
    } else {
         return TYPE_NORMAL_ITEM;
    }
}
```

* 第三步：在onCreateViewHolder根据viewType返回不同的布局
```java
        //创建新View，被LayoutManager所调用
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup viewGroup, int viewType) {
        //如果viewType是普通item返回普通的布局，否则是底部布局并返回
        if (viewType == TYPE_NORMAL_ITEM) {
            View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout
                    .cardview_recyclerview_item, viewGroup, false);
            final NormalItmeViewHolder vh = new NormalItmeViewHolder(view);
            if (mClickListener != null) {
                vh.itemView.setOnClickListener(new View.OnClickListener() {
                    @Override
                    public void onClick(View view) {
                        mClickListener.onItemClick(vh.itemView, vh.getLayoutPosition());
                    }
                });
            }
            return vh;
        } else {
            View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout
                    .recyclerview_footer_view, viewGroup, false);
            FooterViewHolder vh = new FooterViewHolder(view);
            return vh;
        }
    }
```

* 第四步：根据holder类型判断数据
```java
    //将数据与界面进行绑定的操作
    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder viewHolder, int position) {
        if (viewHolder instanceof NormalItmeViewHolder) {
            ((NormalItmeViewHolder) viewHolder).titleTv.setText(list.get(position).getTitle());
            ((NormalItmeViewHolder) viewHolder).contentTv.setText(list.get(position).getContent());
        } else if (viewHolder instanceof FooterViewHolder) {
            FooterViewHolder footViewHolder = (FooterViewHolder) viewHolder;
            switch (load_more_status) {
                case PULLUP_LOAD_MORE:
                    footViewHolder.foot_view_item_tv.setVisibility(View.VISIBLE);
                    footViewHolder.foot_view_item_tv.setText("上拉加载更多");
                    footViewHolder.pb.setVisibility(View.GONE);
                    break;
                case LOADING_MORE:
                    footViewHolder.foot_view_item_tv.setVisibility(View.GONE);
                    footViewHolder.pb.setVisibility(View.VISIBLE);
                    break;
            }
        }
    }
```
到这里，关于上拉更多基本就配置完了。一会完整adapter我会贴出来的。先看效果图，如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/dsdfdsfdsfdfdse3455.gif)

Adapter的全部代码如下：
```java
public class PullMoreRecyclerAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    private static final int TYPE_NORMAL_ITEM = 0;  //普通Item
    private static final int TYPE_FOOTER_ITEM = 1;  //底部FooterView

    //上拉加载更多
    public static final int PULLUP_LOAD_MORE = 1;
    //正在加载中
    public static final int LOADING_MORE = 2;
    //默认为0
    private int load_more_status = 0;

    public List<CardInfo> list;
    private OnItemClickListener mClickListener;

    public PullMoreRecyclerAdapter(List<CardInfo> list) {
        this.list = list;
    }

    //创建新View，被LayoutManager所调用
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup viewGroup, int viewType) {
        //如果viewType是普通item返回普通的布局，否则是底部布局并返回
        if (viewType == TYPE_NORMAL_ITEM) {
            View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout
                    .cardview_recyclerview_item, viewGroup, false);
            final NormalItmeViewHolder vh = new NormalItmeViewHolder(view);
            if (mClickListener != null) {
                vh.itemView.setOnClickListener(new View.OnClickListener() {
                    @Override
                    public void onClick(View view) {
                        mClickListener.onItemClick(vh.itemView, vh.getLayoutPosition());
                    }
                });
            }
            return vh;
        } else {
            View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout
                    .recyclerview_footer_view, viewGroup, false);
            FooterViewHolder vh = new FooterViewHolder(view);
            return vh;
        }
    }

    //将数据与界面进行绑定的操作
    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder viewHolder, int position) {
        if (viewHolder instanceof NormalItmeViewHolder) {
            ((NormalItmeViewHolder) viewHolder).titleTv.setText(list.get(position).getTitle());
            ((NormalItmeViewHolder) viewHolder).contentTv.setText(list.get(position).getContent());
        } else if (viewHolder instanceof FooterViewHolder) {
            FooterViewHolder footViewHolder = (FooterViewHolder) viewHolder;
            switch (load_more_status) {
                case PULLUP_LOAD_MORE:
                    footViewHolder.foot_view_item_tv.setVisibility(View.VISIBLE);
                    footViewHolder.foot_view_item_tv.setText("上拉加载更多");
                    footViewHolder.pb.setVisibility(View.GONE);
                    break;
                case LOADING_MORE:
                    footViewHolder.foot_view_item_tv.setVisibility(View.GONE);
                    footViewHolder.pb.setVisibility(View.VISIBLE);
                    break;
            }
        }
    }

    @Override
    public int getItemCount() {
        //+1是加入底部的加载布局项
        return list.size() + 1;
    }

    public int getItemViewType(int position) {
        // 如果position+1等于整个布局所有数总和就是底部布局
        if (position + 1 == getItemCount()) {
            return TYPE_FOOTER_ITEM;
        } else {
            return TYPE_NORMAL_ITEM;
        }
    }

    //自定义的ViewHolder，持有每个Item的的所有界面元素
    public static class NormalItmeViewHolder extends RecyclerView.ViewHolder {
        public TextView titleTv, contentTv;
        public ImageView iv;

        public NormalItmeViewHolder(View view) {
            super(view);
            titleTv = (TextView) view.findViewById(R.id.item_title_tv);
            contentTv = (TextView) view.findViewById(R.id.item_content_tv);
            iv = (ImageView) view.findViewById(R.id.item_iv);
        }
    }

    /**
     * 底部FooterView布局
     */
    public static class FooterViewHolder extends RecyclerView.ViewHolder {
        public TextView foot_view_item_tv;
        public ProgressBar pb;

        public FooterViewHolder(View view) {
            super(view);
            pb = (ProgressBar) view.findViewById(R.id.progress_view);
            foot_view_item_tv = (TextView) view.findViewById(R.id.tv_content);
        }
    }

    public void setMoreStatus(int status){
        load_more_status=status;
        notifyDataSetChanged();
    }

    public void setOnItemClickListener(OnItemClickListener listener) {
        mClickListener = listener;
    }


    public interface OnItemClickListener {
        public void onItemClick(View itemView, int pos);
    }
}
```
Activity中的代码如下：
```java
public class CardViewActivity extends BaseActivity {
    private RecyclerView rv;
    private SwipeRefreshLayout swipeRefreshWidget;
    private PullMoreRecyclerAdapter adapter;
    private LinearLayoutManager mLayoutManager;
    List<CardInfo> list = new ArrayList<CardInfo>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_card_view);
        rv = (RecyclerView) findViewById(R.id.rv);
        swipeRefreshWidget = (SwipeRefreshLayout) findViewById(R.id.swipe_refresh_widget);
        swipeRefreshWidget.setColorSchemeResources(R.color.colorAccent, R.color.add_bg_color, R
                .color.colorPrimary, R.color.colorPrimaryDark, R.color.add_selected_color);
        //创建默认的线性LayoutManager
        mLayoutManager = new LinearLayoutManager(this);
        rv.setLayoutManager(mLayoutManager);
        //创建并设置Adapter
        adapter = new PullMoreRecyclerAdapter(getDatas());
        rv.setAdapter(adapter);

        swipeRefreshWidget.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                swipeRefreshWidget.setEnabled(false);
                new Handler().postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        list.clear();
                        getDatas();
                        adapter.notifyDataSetChanged();
                        swipeRefreshWidget.setEnabled(true);
                        swipeRefreshWidget.setRefreshing(false);
                    }
                }, 2000);
            }
        });

        rv.addOnScrollListener(new RecyclerView.OnScrollListener() {
            private int lastVisibleItem;

            @Override
            public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
                super.onScrollStateChanged(recyclerView, newState);
                if (newState == RecyclerView.SCROLL_STATE_IDLE && lastVisibleItem + 1 == adapter
                        .getItemCount()) {
                    adapter.setMoreStatus(PullMoreRecyclerAdapter.LOADING_MORE);
                    new Handler().postDelayed(new Runnable() {
                        @Override
                        public void run() {
                            getDatas();
                            adapter.setMoreStatus(PullMoreRecyclerAdapter.PULLUP_LOAD_MORE);
                            adapter.notifyDataSetChanged();
                        }
                    }, 2000);
                }
            }

            @Override
            public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
                lastVisibleItem = mLayoutManager.findLastVisibleItemPosition();
            }
        });
    }

    private List<CardInfo> getDatas() {
        for (int i = 0; i < 10; i++) {
            CardInfo ci = new CardInfo();
            ci.setContent("美女说：非著名程序员公众号是东半球最好的技术分享公众号");
            ci.setTitle("非著名程序员" + i);
            list.add(ci);
        }
        return list;
    }

}
```

### CardView

#### CardView介绍
CardView是Android5.0之后为新增的控件，CardView是一个卡片布局，布局可以包含圆角和阴影，本质上CardView是一个FrameLayout，因此它作为一个布局容器，可以布局其他的View。

#### CardView属性

CardView中常用的属性有：
* cardElevation:设置阴影的大小
* cardBackgroundColor:卡片布局的背景颜色
* cardCornerRadius：卡片布局的圆角的大小
* conentPadding：卡片布局和内容之间的距离

#### 效果图和实例代码
效果图如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/Screenshot_2016-06-25-20-34-34-413_MaterialDesign.png)
代码如下：
```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginBottom="12dp"
    android:layout_marginTop="12dp"
    android:layout_marginLeft="15dp"
    android:layout_marginRight="15dp"
    app:cardBackgroundColor="@color/white"
    app:contentPadding="8dp"
    app:cardCornerRadius="10dp"
    app:cardElevation="10dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingTop="8dp"
        android:paddingBottom="8dp"
        android:orientation="horizontal">

        <ImageView
            android:id="@+id/item_iv"
            android:layout_width="120dp"
            android:layout_height="90dp"
            android:layout_gravity="center_vertical"
            android:layout_margin="6dp"
            android:background="@drawable/item_one"
            android:scaleType="centerCrop"/>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginLeft="6dp"
            android:layout_marginRight="10dp"
            android:layout_gravity="center_vertical"
            android:orientation="vertical">

            <TextView
                android:id="@+id/item_title_tv"
                android:layout_width="wrap_content"
                android:textColor="#383838"
                android:textSize="20sp"
                android:textStyle="bold"
                android:layout_height="wrap_content"
                android:text="非著名程序员"/>

            <TextView
                android:id="@+id/item_content_tv"
                android:textColor="#545454"
                android:textSize="16sp"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginTop="8dp"
                android:text="美女说：非著名程序员公众号是东半球最好的技术分享公众号"/>
        </LinearLayout>

    </LinearLayout>
</android.support.v7.widget.CardView>
```
最后友情提醒一下，使用CardView别忘了添加依赖：
```xml
compile 'com.android.support:cardview-v7:23.4.0'
```

demo的github地址：https://github.com/loonggg/MaterialDesignDemo 去star吧，我会慢慢完善的。

