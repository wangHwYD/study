出现No layout manager attached; skipping layout错误

是没有设置LayoutManager

```
mRecyclerView.setLayoutManager(new LinearLayoutManager(this));//这里用线性显示 类似于listview
mRecyclerView.setLayoutManager(new GridLayoutManager(this, 2));//这里用线性宫格显示 类似于gridview
mRecyclerView.setLayoutManager( new        
StaggeredGridLayoutManager(2,OrientationHelper.VERTICAL));//这里用线性宫格显示 类似于瀑布流
```
设置完再设置Adapter就解决了