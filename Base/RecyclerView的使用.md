# RecyclerView的使用
RecyclerView是5.0开始推出的功能类似于ListView，但更强大的一个控件。在support.v7包中。

## 使用方法
涉及到的是LayoutMananger， ItemDecoration，Adapter。相对于ListView更强大之处是将做到了更解耦，让LayoutManager负责布局， ItemDecoration辅助Item的装饰，如分隔线等。Adapter也完全是使用ViewHolder的形式。

## 默认支持的布局类型
使用RecyclerView可以实现横排或者竖排ListView形式，可以实现GridView形式， 可以实现流式布局。 只需要给RecyclerView设置对应的LayoutManager就行。StaggerLayoutManager。


## demo示例


## LayoutManager知识
参考[《RecyclerView 和 ListView 使用对比分析》](http://www.jianshu.com/p/f592f3715ae2)
其有以下方法
	canScrollHorizontally();//能否横向滚动
    canScrollVertically();//能否纵向滚动
    scrollToPosition(int position);//滚动到指定位置

    setOrientation(int orientation);//设置滚动的方向
    getOrientation();//获取滚动方向

    findViewByPosition(int position);//获取指定位置的Item View
    findFirstCompletelyVisibleItemPosition();//获取第一个完全可见的Item位置
    findFirstVisibleItemPosition();//获取第一个可见Item的位置
    findLastCompletelyVisibleItemPosition();//获取最后一个完全可见的Item位置
    findLastVisibleItemPosition();//获取最后一个可见Item的位置

## ItemTouchHelper的使用
关键是实现ItemTouchHelper.Callback接口。
该接口以下几个关键的方法：
	public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
    // 设置可滑动和可拖动方向。 根据布局情况来设置
	}

    @Override
    public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
    // 拖动
	}

    @Override
    public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
	// 滑动实现删除。
    }
以下为示例

	@Override
    public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
        if (recyclerView.getLayoutManager() instanceof GridLayoutManager) {
            final int dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN | ItemTouchHelper.LEFT | ItemTouchHelper.RIGHT;
            final int swipeFlags = 0;
        } else {
            final int dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
            final int swipeFlags = 0; 
        }
		// 如果要控制某个特殊的item不能拖动或者不能滑动， 则也是在这里根据条件来设置对应的flag
        return makeMovementFlags(dragFlags, swipeFlags);
    }

为了增加交互的友好性，我们往往在拖动或滑动item时想要在视觉上有所变化，则用到如下两个方法
	//当长按选中item的时候（拖拽开始的时候）调用
    @Override
    public void onSelectedChanged(RecyclerView.ViewHolder viewHolder, int actionState) {
    }

    //当手指松开的时候（拖拽完成的时候）调用
    @Override
    public void clearView(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
	}
### 值得注意的方法
startDrag(ViewHolder)方法。直接调用这个方法会开始拖动。具体的使用场景请参照参考文章

[《android开发游记：ItemTouchHelper 使用RecyclerView打造可拖拽的GridView》](http://blog.csdn.net/liaoinstan/article/details/51200618)

## 注意点
ViewHolder的方法getLayoutPosition，或者getAdapterPosition都会存在一个延迟。需要注意，避免获取到的postion与实际想要的对应不上。

---
# ItemDecoration详解
Item Decoration作用就是让用户自己来实现item之间的装饰，如分隔线等。其主要的方法有如下：
- setOffset
- onDraw
- onDrawOver
下面跟别说下这三个方法的作用， 以及onDraw与onDrawOver的异同，及使用场景。

## 添加Header和Footer
请参考[《优雅的为RecyclerView添加HeaderView和FooterView 》](http://blog.csdn.net/lmj623565791/article/details/51854533)

## ItemAnimator 操作item的动画

