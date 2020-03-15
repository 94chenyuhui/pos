<template>
  <div class="left-nav">
    <ul>
      <li>
        <i class="icon iconfont icon-31gouwuche"></i>
        <div>收银</div>
      </li>
      <li>
        <i class="icon iconfont icon-31shouyexuanzhong"></i>
        <div>店铺</div>
      </li>
      <li>
        <i class="icon iconfont icon-31liebiao"></i>
        <div>商品</div>
      </li>
      <li>
        <i class="icon iconfont icon-31wode"></i>
        <div>会员</div>
      </li>
      <li>
        <i class="icon iconfont icon-liebiao"></i>
        <div>统计</div>
      </li>
    </ul>
  </div>
</template>

<script>
  export default{
    name:'leftNav',
    data(){
      return {}
    }
  }
</script>

<style>
  .left-nav{
    color: #fff;
    font-size: 10px;
    height: 100%;
    background-color: #1d8ce0;
    float: left;
    width: 5%;
  }
  .icon{
    font-size: 24px;
  }
  .left-nav ul{
    padding: 0;
    margin: 0;
  }
  .left-nav li {
    list-style: none;
    text-align: center;
    border-bottom: 1px solid #20a0ff;
    padding: 10px;
  }
</style>
