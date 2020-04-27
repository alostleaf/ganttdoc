# VIRTUAL-SECTION
> 类似于大数据加载列表，只渲染可视区域的数据，只不过这里有x,y轴，支持垂直，水平的滚动
参考文章
  * [使用 vue-virtual-collection 优化滚动性能](https://zhuanlan.zhihu.com/p/34380557) (`主要参考`[vue-virtual-collection](https://github.com/starkwang/vue-virtual-collection)进行代码移植)
  * [React virtualized](https://github.com/bvaughn/react-virtualized)

`gantt/virtual-section`代码与github上的不一样，是因为不能完全通用，需要理解原理后，做部分移植代码，核心逻辑是一致的。
