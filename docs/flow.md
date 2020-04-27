# 流程

## 初始化

1.`界面组件`获取相关业务数据及甘特图数据，进行自己业务逻辑处理后，调用 initData

```js
let gantt = this.$refs.gantt;
gantt.initData(this.ganttData);
```

2. `gantt-section`组件依次对应界面上待分区，已分区数据;gantt/index.vue 组件调用`gantt-section.vue`的 makeGanttBar 方法，进行 ganttBar 初始化，及数据构造

```js
if (marker[0]) {
  this.originData[0] = bottomData;
  //将数据传递给甘特图section组件（待分，已分），进行ganttbar数据初始化
  this.$refs.content.init(this.rows);
  this.$nextTick(() => {
    this.$refs.content.makeGanttBar(bottomData, false, this.cacheStyleFunc);
  });
}
if (marker[1]) {
  this.originData[1] = topData;
  this.$refs.wait.makeGanttBar(topData, true, this.cacheStyleFunc);
}
```

3.`gantt-section.makeGanttBar`将数据转换为

```
ganttBars={
  120:[
    ...
  ],
  123:[
    ...
  ]
}
```

> 其中 key 指机位 code,value 是属于这个机位的航班数组

4.进行`virtual-section`初始化，进行大数据量优化

5.
`gantt-section`组件内部，对传入的 rows（行数据）进行 for 遍历，界面渲染出行（没有甘特条）

```html
<div class="area-outer gantt-flight" ref="areaOuterFlight">
  <div class="area" ref="ganttArea" @scroll.passive="ganttListScroll()">
    <div
      v-if="nowInScale"
      class="now-before-area"
      :class="[type=='wait-section'?'top':'bottom']"
      ref="nowBeforeAarea"
      :style="{left:'0px',height:areaHeight+'px',width: nowBeforeWidth+'px'}"
      :title="'当前时间'+$moment(now).format('DD日 HH:mm')"
    ></div>
    <gantt-row
      :width="scaleWidth"
      v-for="row in rows"
      :ref="'ganttRow$'+row.code"
      :key="row.code"
      :code="row.code"
      :pos="type=='wait-section'?'top':'bottom'"
      @mouseenter.native="onRowHover(row.code)"
    >
      <template slot="gantt" slot-scope="{data}">
        <slot name="gantt" :data="data"></slot>
      </template>
    </gantt-row>
  </div>
</div>
```

6.
`gantt-row.vue`组件对应界面上一行,这里的`displayTmpl`是通过动态实时计算当前可视窗口的数据,`slot="gantt"`即为甘特条的实现

```html
<template>
  <!-- 甘特条实现slot -->
  <div
    v-for="(ganttBar) in displayTmpl"
    :key="ganttBar.id"
    @contextmenu="contextmenuClick($event,ganttBar)"
  >
    <slot name="gantt" :data="ganttBar"></slot>
  </div>
</template>
```

## 滚动
  > `gantt-section`的`ganttListScroll`方法，用于当界面滚动时，实时计算当前可视窗口的数据,相关方法位于`gantt/mixins/performance.js`,相关实现和概念在`virtual-section性能优化`一章
  * performanceInit用于初始化`section`数据,并register `cell`
  * 页面滚动时，调用`flushDisplayItems`方法，获取当前可视窗口包含的section
  * 从section集合中获取所包含的cell，及航班数据
  * 进行扁平化，将数组转化为 Map{key(stand)=>value(flights)}
  * 进行遍历，调用`gantt-row`的`updateRowGantt`方法，对displayTmpl赋值，即完成当前界面可视数据的刷新
