<template>
    <el-row>
      <el-col :xs="24" :sm="7" class="order-list">
        <el-tabs>
            <el-tab-pane label="点餐">
              <el-table :data="tableData" border>
                <el-table-column prop="goodsName" label="商品"></el-table-column>  
                <el-table-column prop="count" label="数量" width="60"></el-table-column>  
                <el-table-column prop="price" label="价格" width="60"></el-table-column>  
                <el-table-column label="操作" width="90" fixed="right">
                  <template slot-scope="scope">
                      <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                      <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  </template>  
                </el-table-column>  
              </el-table>
              <div class="total">
                <small>数量：</small>
                <strong>{{totalCount}}</strong>     
                  &nbsp;&nbsp;&nbsp;&nbsp;                             
                <small>总计：</small>
                <strong>{{totalMoney}}</strong>
                元
              </div>
              <div class="btn-group">
                <el-button type="danger" @click="delAllGoods()">撤单</el-button>
                <el-button type="warning">挂单</el-button>
                <el-button type="success" @click="checkout()">结账</el-button>
              </div>
           </el-tab-pane>
              <el-tab-pane label="挂单">
              挂单
              </el-tab-pane>
              <el-tab-pane label="外卖">
              外卖
            </el-tab-pane>
        </el-tabs>

      </el-col>


      <!-- 右边 -->
      <el-col :xs="24" :sm="17" class="goods">
        <div class="top">
          <p class="top-title">店家推荐</p>
          <ul>
            <li v-for="goods in hotGoods" @click="addOrderList(goods)">
              <span>{{goods.goodsName}}</span>
              <span class="price">￥{{goods.price}}元</span>
            </li>     
          </ul>
        </div>

        <div class="right-bottom">
          <el-tabs>
            <el-tab-pane label="汉堡">
               <ul class="food-list">
                  <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                    <span class="food-img"><img :src="goods.goodsImg"></span>
                    <span class="food-name">{{goods.goodsName}}</span>
                    <span class="food-price">￥{{goods.price}}元</span>
                  </li>
               </ul>
            </el-tab-pane>
            <el-tab-pane label="小食">
               <ul class="food-list">
                  <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                    <span class="food-img"><img :src="goods.goodsImg"></span>
                    <span class="food-name">{{goods.goodsName}}</span>
                    <span class="food-price">￥{{goods.price}}元</span>
                  </li>
               </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
               <ul class="food-list">
                  <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                    <span class="food-img"><img :src="goods.goodsImg"></span>
                    <span class="food-name">{{goods.goodsName}}</span>
                    <span class="food-price">￥{{goods.price}}元</span>
                  </li>
               </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
               <ul class="food-list">
                  <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                    <span class="food-img"><img :src="goods.goodsImg"></span>
                    <span class="food-name">{{goods.goodsName}}</span>
                    <span class="food-price">￥{{goods.price}}元</span>
                  </li>
               </ul>
            </el-tab-pane>

          </el-tabs>
        </div>


      </el-col>
    </el-row>
</template>

<script>
import axios from 'axios';
import style from '@/assets/css/style.css';

export default {
  name:'orderList',
  mounted:function(){


  },
  data(){
    return{
        totalCount:0,
        totalMoney:0,
        tableData: [],
        hotGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],

    }
  },
  created(){
    axios.get('http://www.coolivan.com/demo/vue/hotgoods.php')
    .then(response=>{
      // console.log(response);
      this.hotGoods=response.data;
    })
    .catch(error=>{
      // console.log(error);
      alert('网络错误，不能访问')
    });

    axios.get('http://www.coolivan.com/demo/vue/typegoods.php')
    .then(response=>{
      this.type0Goods=response.data[0];
      this.type1Goods=response.data[1];
      this.type2Goods=response.data[2];
      this.type3Goods=response.data[3];    
    })

  },

methods:{
      //添加订单列表的方法
      addOrderList(goods){
            this.totalCount=0; //汇总数量清0
            this.totalMoney=0;
            let isHave=false;
            //判断是否这个商品已经存在于订单列表
            for (let i=0; i<this.tableData.length;i++){
                // console.log(this.tableData[i].goodsId);
                if(this.tableData[i].goodsId==goods.goodsId){
                    isHave=true; //存在
                }
            }
            //根据isHave的值判断订单列表中是否已经有此商品
            if(isHave){
                //存在就进行数量添加
                 let arr = this.tableData.filter(o =>o.goodsId == goods.goodsId);
                 arr[0].count++;
                 //console.log(arr);
            }else{
                //不存在就推入数组
                let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
                 this.tableData.push(newGoods);
 
            }


            //调用求和
            this.getAllMoney()
           
      },

     //删除单个商品
      delSingleGoods(goods){
        this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
        this.getAllMoney();


      },
      //删除所有商品
      delAllGoods() {
          this.tableData = [];
          this.totalCount = 0;
          this.totalMoney = 0;

      },
      //汇总数量和金额
      getAllMoney(){
          this.totalCount=0;
          this.totalMoney=0;
          if(this.tableData){
              this.tableData.forEach((element) => {
                this.totalCount+=element.count;
                this.totalMoney=this.totalMoney+(element.price*element.count);   
              });
              

          }
      },
      
      //结账
      checkout() {
          if (this.totalCount!=0) {
              this.tableData = [];
              this.totalCount = 0;
              this.totalMoney = 0;
              this.$message({
                  message: '结账成功，感谢你又为店里出了一份力!',
                  type: 'success'
              });
      
          }else{
              this.$message.error('不能空结。老板了解你急切的心情！');
          }
      
      }  
  } 
}
</script>




