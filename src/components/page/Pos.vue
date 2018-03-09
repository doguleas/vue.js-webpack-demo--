<template>
  <div class="pos">
        <el-row>
          <el-col :span="5" class="pos-order" id="order-list">
            <el-tabs>
              <el-tab-pane label="点餐">
                <el-table :data="tableData" border style="width:100%;">
                  <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                  <el-table-column prop="goodsNum" label="数量" width="50"></el-table-column>
                  <el-table-column prop="price" label="金额" width="70"></el-table-column>
                  <el-table-column label="操作" width="100" fixed="right">
                  <template slot-scope="scope">
                    <el-button type="text" size="small" @click="deleteGoods(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addGoods(scope.row)">增加</el-button>
                  </template>
                  </el-table-column>
                </el-table>
                <div>数量：<span>{{totalNum}}</span> 金额：<span>￥{{totalMoney}}元</span>  </div>
                <hr>
                <div class="div-btn">
                  <el-button type="warning" >挂单</el-button>
                  <el-button type="danger" @click="deleteAll">删除</el-button>
                  <el-button type="success" @click="play">结账</el-button>
                </div>
              </el-tab-pane>
              <el-tab-pane label="挂单">挂单</el-tab-pane>
              <el-tab-pane label="外卖">外卖</el-tab-pane>
            </el-tabs>
            
          </el-col>
          <el-col :span="17" style="background:#eee">
            <div class="often-goods">
              <div class="title">常用商品</div>
              <div class="often-goods-list">
                  <ul>
                      <li v-for="(goods,index) in oftenGoods" :key="index" @click="addGoods(goods)">
                          <span>{{goods.goodsName}}</span>
                          <span class="o-price">￥{{goods.price}}元</span>
                      </li>
                  </ul>
              </div>
            </div>
            <div class="goods-type">
              <el-tabs>
                <el-tab-pane label="汉堡">
                  <ul class='cookList'>
                      <li v-for="(goods,index) in type0Goods" :key="index" @click="addGoods(goods)">
                          <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                          <span class="foodName">{{goods.goodsName}}</span>
                          <span class="foodPrice">￥{{goods.price}}.00元</span>
                      </li>
                  </ul>
                </el-tab-pane>
                <el-tab-pane label="小食">
                   <ul class='cookList'>
                      <li v-for="(goods,index) in type1Goods" :key="index" @click="addGoods(goods)">
                          <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                          <span class="foodName">{{goods.goodsName}}</span>
                          <span class="foodPrice">￥{{goods.price}}.00元</span>
                      </li>
                  </ul>
                </el-tab-pane>
                <el-tab-pane label="饮料">
                  <ul class='cookList'>
                      <li v-for="(goods,index) in type2Goods" :key="index" @click="addGoods(goods)">
                          <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                          <span class="foodName">{{goods.goodsName}}</span>
                          <span class="foodPrice">￥{{goods.price}}.00元</span>
                      </li>
                  </ul>
                </el-tab-pane>
                <el-tab-pane label="套餐">
                  <ul class='cookList'>
                      <li v-for="(goods,index) in type3Goods" :key="index" @click="addGoods(goods)">
                          <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                          <span class="foodName">{{goods.goodsName}}</span>
                          <span class="foodPrice">￥{{goods.price}}.00元</span>
                      </li>
                  </ul>
                </el-tab-pane>
              </el-tabs>
            </div>
          </el-col>
        </el-row>
  </div>
</template>
<script>
import axios from 'axios';
export default {
  name: 'pos',
  data () {
    return {
      totalNum:0,
      totalMoney:0,
      tableData:[],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
    }
  },
  created(){
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
    .then(response=>{
      this.oftenGoods = response.data;
    })
    .catch(error=>{
      console.log('error 网络错误');
    });

    axios.get('http://jspang.com/DemoApi/typeGoods.php')
    .then(response=>{
      this.type0Goods = response.data[0];
      this.type1Goods = response.data[1];
      this.type2Goods = response.data[2];
      this.type3Goods = response.data[3];
    })
    .catch(error=>{
      console.log('error 网络错误');
    });
  },
  mounted(){
    var orderHeight = document.body.clientHeight;
    document.getElementById('order-list').style.height = orderHeight +'px';
  },
  methods:{
    addGoods(goods){
      this.totalNum = 0;
      this.totalMoney = 0;
      let isHave = false;
      for(let i=0;i<this.tableData.length;i++){
        if(goods.goodsId == this.tableData[i].goodsId){
          isHave = true;
        }
      }

      if(isHave){
        let arr = this.tableData.filter(list=>list.goodsId == goods.goodsId);
        arr[0].goodsNum++;
      }else{
        let newArr = {goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,goodsNum:1};
        this.tableData.push(newArr);
      }

      this.getTotalMoney();


    },
    deleteGoods(goods){
      // 再赋值给tableData 除了传过来的id
      this.tableData = this.tableData.filter(list=>goods.goodsId != list.goodsId);
      this.getTotalMoney();
      this.$message({
        message:'已删除商品',
        type:'success'
      });

      
    },
    deleteAll(){
      if(this.tableData !=0){
        this.tableData = [];
        this.totalNum = 0;
        this.totalMoney = 0;
        this.$message({
          message:'已删除全部商品',
          type:'success'
        });
      }else{
        this.$message({
          message:'尚未选择商品',
          type:'error'
        });
      }
    },
    play(){
      if(this.tableData !=0){
        this.tableData = [];
        this.totalNum = 0;
        this.totalMoney = 0;
        this.$message({
          message:"结算成功",
          type:"success"
        });
      }else{
        this.$message.error('不可提交空订单');
      }
    },
    getTotalMoney(){
      this.totalNum = 0;
      this.totalMoney = 0;
      if(this.tableData){
          this.tableData.forEach(list=>{
          this.totalNum = this.totalNum + list.goodsNum;
          this.totalMoney = this.totalMoney + list.goodsNum*list.price;
        })
      }
    }
  }

}
</script>

<!-- Add "scoped"局部变量，只在该模版中管用-->
<style scoped>
  .pos-order{
    background-color: #f9fafc;
    border-right: 1px solid #eee;
  }
  .div-btn{
    margin-top: 15px;
  }
 .title{
       height: 20px;
       border-bottom:1px solid #D3DCE6;
       background-color: #F9FAFC;
       padding:10px;
   }
   .often-goods-list ul li{
      list-style: none;
      float:left;
      border:1px solid #E5E9F2;
      padding:10px;
      margin:5px;
      background-color:#fff;
   }
  .o-price{
      color:#58B7FF; 
   }
   .goods-type{
     clear: both;
   }
   .cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;

   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;

   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
</style>
