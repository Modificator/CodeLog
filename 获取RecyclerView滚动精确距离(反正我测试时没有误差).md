```java
int firstPosition = -1;
int firstPositionTop = 0;
int totalScrollDistance = 0;

public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
    LinearLayoutManager layoutManager = (LinearLayoutManager) mLayoutManager;
    if (firstPosition != layoutManager.findFirstVisibleItemPosition()) {
        totalScrollDistance += (layoutManager.findViewByPosition(firstPosition < 0 ? 0 : firstPosition).getTop() - firstPositionTop);
        firstPosition = layoutManager.findFirstVisibleItemPosition();
        firstPositionTop = layoutManager.findViewByPosition(lastFirstPosition).getTop();
    }
    if (firstPosition == 0 && firstPositionTop == 0 && layoutManager.findFirstVisibleItemPosition() == 0) {
        firstPositionTop = layoutManager.findViewByPosition(0).getTop();
    }
    L.e("scrolled:" + (totalScrollDistance + layoutManager.findViewByPosition(lastFirstPosition).getTop() - firstPositionTop));
}

```
因为公司的项目item比较高,`findFirstVisibleItemPosition`和`findLastVisibleItemPosition`  只相差1或者没有所以这里直接取的当前显示的第一个,当然取其他位置也没问题


由于前面代码写的是第一个item,以下文字均用第一个item表示选定的item,如果你是其他item 请自行脑补替换

### 原理
之前写过状态栏根据列表滑动变色啊,toolbar根据列表滑动变色什么的,都用列表的第一个item的滑动距离和阀值来控制颜色,透明度,显隐 之类的
这里方法类似,获取第一个item显示距离顶部的距离
当`layoutManager.findFirstVisibleItemPosition()==0`时`item0` 滚动时`getTop()`就是列表滚动的距离
当`layoutManager.findFirstVisibleItemPosition()==1`时`item0并没有被销毁`,这时的滚动距离就是`item0.getTop()+Math.abs(item1.getTop())`
当firstItem切换时记录上一个item滚动的距离和当前item距离顶部的距离

上一个item的滚动距离加到总的滚动距离+当前item.getTop - 记录的当前item距离顶部距离
就是总距离了

当然,如果在小于firstPosition 的位置插入item,或者之前的item高度发生变化 这个就不准了

padding+clipchildren firstVisiblePosition是按照padding后的位置取的,而不是显示的位置

所以,貌似这段代码,用处并不是很大, 我用的也只是模拟CoordinatorLayout的效果,因为要和列表同步,而CoordinatorLayout有往上有阻尼,下拉要么阻尼,要么干巴巴的啥效果都没有,要惯性啊= =
