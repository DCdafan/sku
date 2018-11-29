<template>
  <div class="main">
    <div class="list">
      <div class="child" v-for="(item,index) in typeList" :key="index">
        <span class="title">类别{{index}}:</span>
        <span @click="checkType(item,index,index2)" :class="{'btn':true,'sel':item.selected,'dis':item.disabled}" v-for="(item,index2) in item" :key="index2">{{item.name}}</span>
      </div>
    </div>
    <hr>
    <p>价格:{{min + ' - '+max}}</p>
    <p>数量:{{count}}</p>

    <p v-for="(item,index) in data" :key="index">{{item.sku_key}}</p>
  </div>
</template>

<script>
export default {
  name: 'sku',
  data() {
    return {
      typeList:[], // 选择类别的数组
      sku_result:{}, // sku 单元集合
      result:{}, // 选择的结果
      userList:{}, // 用户的选择对象
    }
  },
  computed:{
    max(){
      let price = this.result.sell_price === undefined?[0]:this.result.sell_price
      let max = Math.max.apply(Math,price)
      return max
    },
    min(){
      let price = this.result.sell_price === undefined?[0]:this.result.sell_price
      let min = Math.min.apply(Math,price)
      return min
    },
    count(){
      var s = 0;
      var arr = this.result.store_nums === undefined?[0]:this.result.store_nums
      for (var i=arr.length-1; i>=0; i--) {
          s += arr[i];
      }
      return s;
    }
  },
  props: {
    data: Array
  },
  methods: {
    // 初始化选择类别
    init() {
      let _this = this
      this.data.forEach((item,i) => {
        let list = item.sku_key.split(';');
        _this.addSku(item)
        //
        list.forEach((item2,j) => {
          if(_this.typeList[j] === undefined){
            _this.typeList[j] = []
          }
          if(_this.isInArray(_this.typeList[j],item2)) return false
          _this.typeList[j].push({
            name : item2,
            selected: false,
            disabled: false
          })
        })
        //
      })
    },
    // 初始化sku 数组
    addSku(item){
      let _this = this 
      let id         = item.id
      let sell_price = item.sell_price 
      let sku_key    = item.sku_key 
      let store_nums = item.store_nums

      let list = sku_key.split(';')
      for(let i=1,length=list.length;i<=length;i++){
        let result = this.combo(list,i)
        result.forEach((item,index)=>{
          let str = item.join(';')
          if(_this.sku_result[str] === undefined){
            _this.sku_result[str] = {sell_price:[sell_price],store_nums:[store_nums]}
          }else{
            _this.sku_result[str].sell_price.push(sell_price)
            _this.sku_result[str].store_nums.push(store_nums)
          }
          if(item.length === list.length){
            _this.sku_result[str][id] = id
          }
        })
      }

    },
    /**
     * @param  {[item] 当前选中对象}
     * @param  {[index] 父级分类索引}
     * @param  {[index2] 当前节点索引}
     * @return {[type]}
     */
    checkType(item,index,index2){
      // console.log(item.name,index,index2)
      // 先将当前行全部取消选择
      this.typeList[index].forEach((item,i)=>{
        if(i !== index2){
          item.selected = false
        }
      })
      item.selected = !item.selected // 当前状态置为反
      // 添加用户选择的列表
      if(item.selected){
        this.userList['type_' + index] = item.name
      }else{
        this.userList['type_' + index] = ''
      }
      // 获取选择的集合
      let list = []
      this.typeList.forEach((item,i) => {
        item.forEach((item,j)=>{
          if(item.selected){
            list.push(item.name)
          }
        })
      })
      // 获取 SKU 对应的结果集
      this.result = this.sku_result[list.join(';')] === undefined?{}:this.sku_result[list.join(';')]
      this.updateType()
    },
    updateType() {
      let _this = this
      let length = this.typeList.length // 可选类别的长度
      // 双层循环用来做一一对应
      this.typeList.forEach((item,i)=>{
        item.forEach((val,k)=>{
          let list = []
          for(let j =0;j<length;j++){
            // 简单的讲就是 如果用户选择了，那么就用用户选择的，当前循环的是哪行数据，哪行的数据就随着外层循环进行变动
            if(!!_this.userList['type_' + j] && i!==j){
              list.push(_this.userList['type_' + j])
            }
            if(i===j){
              list.push(val.name)
            }
            
          }
          console.log(_this.sku_result[list.join(';')],list.join(';'))
          // 简单的sku 判断，如果存在数据就 给false 如果没有数据就给true
          if(_this.sku_result[list.join(';')] === undefined){
            val.disabled = true
          }else{
            val.disabled = false
          }
        })
      })

      // 重新赋值 刷新节点
      this.typeList = JSON.parse(JSON.stringify(this.typeList))
    },
    /**
     * @param  {[array]: 需要比对的数组}
     * @param  {[str]: name}
     * @return {Boolean}
     */
    isInArray(array,str){
      for(let i =0,length = array.length;i<length;i++){
        if(array[i].name === str){
          return true
        }
      }
      return false
    },
    /**
     * [组合运算]
     * @param  {array} arr [进行组合运算的数组]
     * @example [1,3,2]
     * @param  {number} num [每几个元素为一组]
     * @return {array}     [返回组合集合]
     * @example [[1,3],[1,2],[3,2]]
     */
    combo : function(arr, num) {
      var result = [];
      var range = function(r, _arr) {
        if (r.length == num) {
          result.push(r)
        } else {
          let l = r.length;
          for (var i = 0, len = _arr.length - num + l; i <= len; i++) {
            range(r.concat(_arr[i]), _arr.slice(i + 1))
          }
        }
      }
      range([], arr);
      return result
    }
  },
  created() {
    this.init()
  }
}
</script>

<style scoped lang="scss">
.main{
  width: 400px;
  margin:50px;
  border:1px dashed #ccc;
  text-align: left;
  padding:20px 0;
  user-select:none;
}
.list{
  font-size: 0;
  line-height: 40px;
  span{
    height: 30px;
    line-height: 30px;
    display: inline-block;
    vertical-align: middle;
    font-size: 14px;
  }
  .title{
    width: 80px;
    text-align: center;
  }
  .btn{
    width: 50px;
    text-align: center;
    border:1px solid #ccc;
    margin-right: 10px;
    cursor: pointer;
    &:hover{
      background: rgba(0,0,0,.1);
    }
    &.dis{
      background: #ddd;
      color:#aaa;
    }
    &.sel{
      border-color:red;
    }
  }
}
</style>
