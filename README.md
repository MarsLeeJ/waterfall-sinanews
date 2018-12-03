## 新闻瀑布流
## 懒加载原理
```
bind: function () {
    var _this = this
    $(window).on('scroll', function () {
        if (_this.loading) { return }
        //当页面滚动到指定位置时，才会加载图片
        if ($('#load').offset().top < $(window).scrollTop() + $(window).height()) {
            if (_this.clock) {
                clearTimeout(_this.clock)
            }
            _this.clock = setTimeout(_this.update(), 500)
        }
    })
}
```
## 瀑布流原理
每次加载图片时都进行以下步骤
```
putNode: function ($node) {
    var minHeight = this.colHeightNum[0];
    var minIndex = 0;
    var _this = this;
    //1.事先计算出，元素的宽高和列数，用一个数组记录各列的高度，然后每次加载图片时，寻找高度最小的列
    for (var i = 0; i < this.colNum; i++) {
        if (this.colHeightNum[i] < minHeight) {
            minIndex = i;
            minHeight = this.colHeightNum[i];
        }
    }
    //2.找到最小的列将图片放在此列的最后面
    $node.css({
        top: minHeight,
        left: _this.colWidth * minIndex
    })
    //3.最后更新此列的高度
    _this.colHeightNum[minIndex] += $node.outerHeight(true);
    var maxHeight = this.colHeightNum[0];
    var maxIndex = 0;
    for (var i = 0; i < this.colNum; i++) {
        if (this.colHeightNum[i] > maxHeight) {
            maxIndex = i;
            maxHeight = this.colHeightNum[i];
        }
    }
    $('#load').css({
        top: maxHeight
    })
}
```
## 实现原理
```
$(window).on('scroll', function () {
    if (_this.loading) { return }
    //每次滚动到指定位置时，再加载图片
    if ($('#load').offset().top < $(window).scrollTop() + $(window).height()) {
        if (_this.clock) {
            clearTimeout(_this.clock)
        }
        //这里的updat会通过瀑布流的方式放置图片
        _this.clock = setTimeout(_this.update(), 500)
    }
})