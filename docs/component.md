# 组件参数及API说明
## 组件参数
  ```js
  props:{
    /**
     * 甘特图开始时间/毫秒值
     */
    start:{
      type:Number,
      required:true,
    },
    /**
     * 甘特图结束时间/毫秒值
     */
    end:{
      type:Number,
      required:true,
    },
    /**
     * 甘特图当前时间/毫秒值
     */
    now:{
      type:Number,
      required:true,
    },
    /**
     * 行数据，必须具有id,code字段
     */
    rows:{
      type:Array,
      required:false,
      default:()=>[],
      validator(rows){
        let isPass = true
        let len = rows.length
        for(let idx=0;idx<len;idx++){
          let item =rows[idx]
          if(item.id==undefined||item.code==undefined){
            isPass = false;
            break;
          }
        }
        return isPass
      }
    },
    /**
     * rowKey
     */
    rowKey:{
      type:String,
      required:false,
      default:'code'
    },
    /**
     * 当存在多个甘特条对应一个数据实体时，用于标识定位相关数据，如机位甘特图
     * 进行机位分割，多个id的甘特条数据对应一个航班ID
     * 在进行数据更新或者操作时，不能仅依靠data.id来处理，可能造成数据遗漏，或者错误
     *
     */
    oneToManyKey:{
      type:String,
      default:'flightId'
    },
    /**
     * 是否显示待分区
     */
    isShowWaitSection:{
      type:Boolean,
      required:false,
      default:true,
    },
    /**
     * 是否显示待分区刻度
     */
    isShowTopScale:{
      type:Boolean,
      required:false,
      default:false,
    },
    /**
     * 获取甘特图所需的相关字段，根据每一条数据，该方法应返回如下数据(在数据挂载一个字段)
     * [
     *  {
     *    ...
     *    $gantt:{
     *
     *    }
     *  }
     * ]
     * 参数：
     * data:原始数据，数组
     * isWaitSection：是否是待分区数据
     * 返回值:{
     *  id://数据唯一标识,
     *  start://在甘特图的起始时间，毫秒
     *  end://在甘特图的结束时间，毫秒
     *  `${rowKey}`:rowKey对应的field,如code，标识该数据位于哪行，
     *      如果是WaitSection（甘特图待分区），则可以不穿，由组件自己生成
     * }
     */
    getGanttProps:{
      type:Function,
      required:true
    },
    /**
     * 甘特图内容计算方法，用于模板初始化时，计算缓存数据，用于界面展示，这样可以不必实时计算甘特条的显示内容
     */
    cacheStyleFunc:{
      type:Function,
      required:false,
    },
    /* 时候允许拖拽改变甘特图区域高度 */
    changeHeight:{
      type:Boolean,
      default:true,
    }
  }
  ```

## 组件API
> 通过this.$refs调用
```js
methods:{
    /**
     * 移动视窗到指定位置(水平方向)
     */
    moveViewPort(position='center') {
    },
    /**
     * 初始化数据，一般在mounted加载甘特图数据后，或者整表刷新数据调用本组件的initData方法
     * ganttData =[reality=[],virtual=[]]
     * 需传递数组参数，有2个item,reality代表已分配机位（已分配资源），virtual代表未分配机位（资源）
     */
    initData(ganttData=[]){
    },
    /**
     * 刷新数据，websocket更新数据，或者本地更新航班数据，一般调用此接口
     */
    refreshData([top,bottom]=[],){

    },
    //立即刷新当前界面的可视区域数据
    refreshDataDisplay(){

    },
    /**
     * 添加一条数据，data为一条航班数据,type 0 已分配机位，1 未分配机位
     */
    addData(data={},type=0){

    },
    /**
     * 更新一条数据，data为一条航班数据,type 0 已分配机位，1 未分配机位
     */
    updateData(data,type){

    },
    /**
     * 删除一条数据，data为一条航班数据,type 0 已分配机位，1 未分配机位
     */
    deleteData(data,type){

    },
    /**
     * 删除一条数据根据oneToManyKey，
     * oneToManyKeyValue
     * type 0 已分配机位，1 未分配机位
     * 比如oneToManyKey=‘flightId’
     * deleteByOneToManyKey（12345，0）则会删除所有flightId为12345的数据，比如机位分割，flightId为12345有3条数据，
     * 调用这个API，三条数据都会被删除
     */
    deleteByOneToManyKey(oneToManyKeyValue,type){

    },
    /**
     * 搜索数据,type 0 已分配机位，1 未分配机位
     * search 搜索的关键字
     * field 搜索的字段
     * type 0 已分配机位，1 未分配机位
     */
    getDataByField(search,field='flightId',type){

    },
    /**
     * 判断数据是否分配机位 根据$gantt[this.rowKey]判断是否有值
     */
    hasRowCode(data){

    },

    //刷新资源不可用
    refreshMaintain(list,type,colorConfigs=null){

    }
  }
```

## 样式说明
> 甘特图组件样式文件入口在gantt/style/index.scss,分模块构建，可以根据需要在界面组件内部复写相关样式
```scss
@import "./responsive.scss";
@import './vars.scss';
@import "./scale.scss";
@import "./gantt.scss";
@import "./gantt-bar.scss";
@import "./marking-line.scss";
@import "./gantt-section.scss";
@import "./gantt-row.scss";
@import "./drag.scss";
@import "./row-list.scss";
```
