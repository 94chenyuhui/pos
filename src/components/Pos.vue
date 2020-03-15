<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="order-list">

        <el-tabs type="border-card">
          <el-tab-pane label="点餐" class="gao">
            <el-table :data="tableData" show-summary style="width: 100%">
              <el-table-column prop='goodsName' label="商品"></el-table-column>
              <el-table-column prop='count' label="数量" sortable></el-table-column>
              <el-table-column prop='price' label="金额" sortable></el-table-column>
              <el-table-column label="操作" fixed="right">
                <template slot-scope='scope'>
                  <el-button type='text' size='small' @click="shanchu(scope.row)">删除</el-button>
                  <el-button type='text' size='small' @click="add(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <span>合计：{{totalCount}}</span>
            <span>金额：{{totalMoney}}</span>
          </el-tab-pane>
          <el-tab-pane label="挂单">2</el-tab-pane>
          <el-tab-pane label="外卖">3</el-tab-pane>
        </el-tabs>
        <div class="button-top">
          <el-button type="warning">挂单</el-button>
          <el-button type="danger" @click='shan()'>删除</el-button>
          <el-button type="success" @click='checkout()'>结账</el-button>
        </div>

      </el-col>
      <el-col :span="17" class="order-list dian">
        <div class="cctv"><span class="shangPing">常用商品</span></div>
        <div style='height: 50%'>
          <el-tag v-for="i in oftenGoods" class="goods" @click="add(i)" :key='i.id'>
            <span class="goodsname">{{i.goodsName}}</span>
            <span>￥{{i.price}}元</span>
          </el-tag>
        </div>
        <el-tabs type="border-card" class="hanbao">
          <el-tab-pane label="汉堡" class="hanbaos">
            <el-tag v-for="i in fen0" class="hanbaoss" @click='add(i)' :key='i.id'>
              <span class="tupian"><img :src="i.goodsImg"></span>
              <span class="ming">{{i.goodsName}}</span>
              <span class="qian">￥{{i.price}}元</span>
            </el-tag>
          </el-tab-pane>
          <el-tab-pane label="小食">
            <el-tag v-for="i in fen1" class="hanbaoss"  @click='add(i)' :key='i.id'>
              <span class="tupian"><img :src="i.goodsImg"></span>
              <span class="ming">{{i.goodsName}}</span>
              <span class="qian">￥{{i.price}}元</span>
            </el-tag>
          </el-tab-pane>
          <el-tab-pane label="饮料">
            <el-tag v-for="i in fen2" class="hanbaoss"  @click='add(i)' :key='i.id'>
              <span class="tupian"><img :src="i.goodsImg"></span>
              <span class="ming">{{i.goodsName}}</span>
              <span class="qian">￥{{i.price}}元</span>
            </el-tag>
          </el-tab-pane>
          <el-tab-pane label="套餐">
            <el-tag v-for="i in fen3" class="hanbaoss"  @click='add(i)' :key='i.id'>
              <span class="tupian"><img :src="i.goodsImg"></span>
              <span class="ming">{{i.goodsName}}</span>
              <span class="qian">￥{{i.price}}元</span>
            </el-tag>
          </el-tab-pane>
        </el-tabs>
      </el-col>
    </el-row>

  </div>

</template>

<script>
  import axios from 'axios'
  export default {
    name: 'Pos',
    data: function() {
      return {

        tableData: [],

        oftenGoods: [],

        fen0: [],
        fen1: [],
        fen2: [],
        fen3: [],
        totalCount:0,
        totalMoney:0

      }
    },

    created() {//创建完成dom后执行
      //读取常用商品列表
      axios.get('https://www.fastmock.site/mock/0bf6a5bae7eab8507e44b56191ddff36/vuepos/oftenGoods')
        .then(response1 => { //请求成功，获得数据操作
          this.oftenGoods = response1.data.oftenGoods;
         // console.log(response1.data.oftenGoods);
        })
        .catch(error => { //请求失败，回应给页面
          alert('网络错误，无法访问')
        }),
        //读取分类商品列表
        axios.get('https://www.fastmock.site/mock/0bf6a5bae7eab8507e44b56191ddff36/vuepos/typeGoods')
        .then(response => {
          this.fen0 = response.data.data[0];
          this.fen1 = response.data.data[1];
          this.fen2 = response.data.data[2];
          this.fen3 = response.data.data[3];
         // console.log(response.data.data[0]);
        })
        .catch(error => {
          alert('网络错误，无法访问')
        });

    },


    mounted(){//挂载之后执行
    //设置高度
      var orderHeight = document.body.clientHeight;
      document.getElementsByClassName("order-list")[0].style.height = orderHeight + 'px';
      document.getElementsByClassName('dian')[0].style.height = orderHeight + 'px';


    },


    methods: {
      add(goods) { //添加商品到列表的方法
        this.totalCount=0;//汇总数量清零
        this.totalMoney=0;
        let isHave = false;
        //判断这个商品是否已经存在于订单列表
        for (let i = 0; i < this.tableData.length; i++) {
         // console.log(this.tableData[i].goodsId);

           if (this.tableData[i].goodsId == goods.goodsId) {
            isHave = true;
          }
        }
        //根据isHave的值判断订单列表是否存在此商品
        if (isHave) {
          //存在就进行数量添加
          let arr = this.tableData.filter(o =>o.goodsId == goods.goodsId);
          arr[0].count++;

        } else {//不存在此商品，就推入数组
          let newGoods = {
            goodsId: goods.goodsId,
            goodsName: goods.goodsName,
            price: goods.price,
            count: 1
          }
          this.tableData.push(newGoods);

        }
        //进行数量和夹跟的汇总
        this.monet();

    },
    //汇总数量和金额
    monet(){
      this.totalCount=0;
      this.totalMoney=0;
      if(this.tableData){
        this.tableData.forEach((i)=>{
          this.totalCount+=i.count;
          this.totalMoney=this.totalMoney+(i.price*i.count);
        });
      }
    },
    //删除当商品
    shanchu(goods){
     // this.tableData.filter(o=>o.goods!=goods.goodsId)

      this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
      this.monet();
    },
    //删除全部商品
    shan(){
      this.tableData=[],
      this.totalCount=0;
      this.totalMoney=0;
    },
    /*
    结账
    ** 模拟结账 ** 因为模拟结账需要Post数据到后台，我的服务器又不能提供这样的借口给大家，所以我只说制作思路，大家可以在自己的服务器上去实现。
    
    1.设置我们Aixos的Pos方法。
    2.接受返回值进行处理。
    3.如果成功，清空现有构造器里的tableData，totalMoney，totalCount数据。
    4.进行用户的友好提示。
    由于前两个步骤不能演示，所以这里我们只模拟3和4步。在methods里作一个结账方法，清空数据和进行友好提示。
    
    */
    checkout(){
      if(this.totalCount!=0){
        this.totalCount=0;
        this.totalMoney=0;
        this.tableData=[];
        this.$message({
          message:"结账成功！",
          type:'success'
        })
      }else{
        this.$message.error('不能结算')
      }
    }
  }


  }
</script>

<style scoped>
  .order-list {
    background-color: #efeeee;
    border-right: 3px solid #e1e1e1;

  }

  .button-top {
    padding-top: 10px;
  }

  .pos {
    margin-left: 5%;
  }

  .dian {
    background-color: #eff2fa;
    border-bottom: 1px solid #e4e7ed;

  }

  .cctv {
    border-bottom: 1px solid #e4e7ed;
    height: 40px;
    background-color: #F5F7FA;
  }

  .shangPing {
    float: left;
    padding: 10px;
    font-size: 14px;
    color: #909399;


  }

  .goods {
    margin: 15px;
    float: left;
    font-size: 16px;
  }

  .goodsname {
    color: #606266;
  }

  .hanbao {
    height: 50%;
    background-color: #eff2fa;
  }

  .hanbaos {
    background-color: #eff2fa;
  }

  .hanbaoss {
    width: 250px;
    height: 100px;
    float: left;
    font-size: 16px;
    margin: 12px;

  }

  .tupian img {
    height: 80px;
    width: 80px;
    float: left;
    margin: 10px;
  }

  .ming {
    color: #909399;
    display: block;
  }

  .ming,
  .qian {
    padding-top: 15px;
  }

</style>
<style>
  .el-table__footer .el-table_1_column_3 .cell{
    color:red;
  }
  .el-table__footer-wrapper,.el-table__fixed-footer-wrapper{
    display: none;
  }
</style>
