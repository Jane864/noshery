<template>
    <div class="pos">
      <el-row>
        <el-col :span="7" class="pos-order" id="order-list">
          <el-tabs>
            <el-tab-pane label="点餐">
              <el-table :data="tableData" border style="width:100%">
                <el-table-column prop="goodsName" label="商品"></el-table-column>
                <el-table-column prop="count" label="数量" width="50"></el-table-column>
                <el-table-column prop="price" label="单价" width="70"></el-table-column>
                <el-table-column label="操作" width="100"  fixed="right">
                  <template scope="scope">
                    <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="totalDiv">
                <small>数量：</small>{{totalCount}}  &nbsp;&nbsp;&nbsp;&nbsp;    <small>金额：</small>{{totalMoney}}元
              </div>
              <div class="div-btn">
                <el-button type="warning">挂单</el-button>
                <el-button type="danger"  @click="delAllGoods">删除</el-button>
                <el-button type="success" @click="checkout">结账</el-button>
              </div>
            </el-tab-pane>
            <el-tab-pane label="挂单">2</el-tab-pane>
            <el-tab-pane label="外卖">3</el-tab-pane>
          </el-tabs>
        </el-col>
        <el-col :span="17">
          <div class="often-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">
              <ul>
                <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                  <span>{{goods.goodsName}}</span>
                  <span class="o-price">￥{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </div>
          <div class="goods-type">
            <el-tabs>
              <el-tab-pane label="汉堡">
                <div>
                  <ul class="cookList">
                    <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="140" height="100"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label="小食">
                <ul class="cookList">
                  <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="140" height="100"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="饮料">
                <ul class="cookList">
                  <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="套餐">
                <ul class="cookList">
                  <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
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
        name: "Pos.vue",
      data(){
          return{
            tableData:[],
            oftenGoods:[],
            type0Goods:[],
            type1Goods:[],
            type2Goods:[],
            type3Goods:[],
            totalMoney:0,
            totalCount:0
          }
      },
      created:function(){
          axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
            .then(response=>{
              // console.log(response);
              this.oftenGoods=response.data;
            })
            .catch(error=>{
              // console.log(error);
              alert('网络错误，不能访问');
            })
        axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
          .then(response=>{
            // console.log(response);
            this.type0Goods=response.data[0];
            this.type1Goods=response.data[1];
            this.type2Goods=response.data[2];
            this.type3Goods=response.data[3];
          })
          .catch(error=>{
            // console.log(error);
            alert('网络错误，不能访问');
          })
      },
      mounted:function(){
          var orderHeight=document.body.clientHeight;
          // console.log(orderHeight);
          document.getElementById('order-list').style.height=orderHeight+'px';
      },
      methods:{
          //1、添加商品
          addOrderList(goods){
            this.totalMoney=0;
            this.totalCount=0;

                // 商品是否已经存在订单列表
            let isHave=false;
            for(let i=0;i<this.tableData.length;i++){
              if(this.tableData[i].goodsId==goods.goodsId){
                // 已经存在于列表当中
                isHave=true;
              }
            }
              // 根据判断值编写业务逻辑
            if(isHave){
              // 改变列表中商品的数量
              let arr=this.tableData.filter(o=>o.goodsId==goods.goodsId);
              arr[0].count++;
            }else{
              let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
              this.tableData.push(newGoods);
            }
            this.getAllMoney();
          },
        //2、 删除单个商品
        delSingleGoods(goods){
            this.tableData=this.tableData.filter(o=>o.goodsId!=goods.goodsId);
            this.getAllMoney();
        },
        //3、模拟结账
        checkout(){
          if(this.totalCount!=0){
            this.tableData=[];
            this.tableCount=[];
            this.tableMoney=[];
            this.$message({
              message:'结账成功，谢谢惠顾',
              type:'success'
            });
          }else{
            this.$message.error('不能空结账，老板了解你的急切心情！！');
          }
        },
        //4、删除所有商品
        delAllGoods(){
          this.tableData=[];
          this.totalCount=0;
          this.totalMoney=0;
        },
        //5、汇总数量和金额
        getAllMoney(){
            this.totalCount=0;
            this.totalMoney=0;
            if(this.tableData){
              this.tableData.forEach((element)=>{
                this.totalCount+=element.count;
                this.totalMoney=this.totalMoney+(element.price*element.count);
              })
            }
        }
      }
    }
</script>

<style scoped>
  .pos-order{
    background-color: #f9fafc;
    border-right:1px solid #c0ccda;
    height: 100%;
  }
.div-btn{
  text-align: center;
  margin-top:10px;
}
  .title{
    height: 20px;
    border-bottom: 1px solid #d3dce6;
    background-color: #f9fafc;
    padding:10px;
  }
  .often-goods-list ul li{
    list-style-type: none;
    float: left;
    border:1px solid #e5e9f2;
    padding:10px;
    margin:10px;
    background-color: #fff;
    cursor: pointer;
  }
  .o-price{
    color:#58b7ff;
  }
  .goods-type{
    clear:both;
  }
  .cookList li{
    list-style-type: none;
    width: 23%;
    border:1px solid #e5e9f2;
    height: auto;
    overflow:hidden;
    background-color: #fff;
    padding: 2px;
    float: left;
    margin:2px;
    cursor: pointer;
  }
  .cookList li span{
    display: block;
    float: left;
  }
  .foodImg{
    width: 40%;
  }
  .foodName{
    font-size:18px;
    padding-left:10px;
    color: brown;
  }
  .foodPrice{
    font-size:16px;
    padding-left:10px;
    padding-top:10px;
  }
  .totalDiv{
    background-color: #fff;
    padding:10px;
    border-bottom:1px solid #d3dce6;
    text-align: center;
  }
</style>
