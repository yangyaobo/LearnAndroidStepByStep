# ListView的使用
ListView是最早的实现竖直排列的线性空间。
## 使用步骤
我们以一个Demo来掌握，大概步骤为如下：
集成BaseAdapter来实现自己的Adaper。其中重要的是getView方法。为了复用。我们还有自己定义ViewHoldr， 根据convertView==null来判断是否可以复用还是新建itemView。

## addHeader, addFooter, setEmpty
