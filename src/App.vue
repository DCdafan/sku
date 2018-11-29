<template>
  <div id="app">
    <sku :data = 'data' /> 
  </div>
</template>

<script>
import sku from './components/sku'

export default {
  name: 'app',
  data(){
    return {
      data:[
          {'sku_key': '10;20;30;40', 'id': 120, 'store_nums': 100, 'sell_price': 10.7},
          {'sku_key': '10;21;30;40', 'id': 10, 'store_nums': 1540, 'sell_price': 45.7},
          {'sku_key': '10;22;30;40', 'id': 28, 'store_nums': 150, 'sell_price': 72.7},
          {'sku_key': '10;22;31;41', 'id': 220, 'store_nums': 1050, 'sell_price': 85.7},
          {'sku_key': '10;22;32;40', 'id': 130, 'store_nums': 106, 'sell_price': 543.7},
          {'sku_key': '10;22;32;41', 'id': 135, 'store_nums': 1530, 'sell_price': 324.7},
          {'sku_key': '11;23;32;41', 'num': 120, 'store_nums': 160, 'sell_price': 14.7},
          {'sku_key': '11;22;32;41', 'num': 120, 'store_nums': 160, 'sell_price': 14.7},
      ]
    }
  },
  components: {
    sku
  }
}
</script>

<style lang="scss">
*{
  margin:0;
  padding:0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
