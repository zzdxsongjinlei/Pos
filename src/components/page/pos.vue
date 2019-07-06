<template>
	<div class="pos">
		<div>
			<el-row>
				<el-col :span='7' id="order-list">
					<el-tabs>
						<el-tab-pane label="点餐">
							<el-table :data="tableData" border  style="width:100%">
								<el-table-column prop="goodsName" label="商品"></el-table-column>
								<el-table-column prop="count"  width="50"	label="数量"></el-table-column>
								<el-table-column prop="price"  width="70" label="金额"></el-table-column>
								<el-table-column label="操作" width="100" fixed="right">
									<template scope="scope">
										<el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
										<el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
									</template>
									
								</el-table-column>
							</el-table>
							<div>订单数量：{{totalCount}}</div>
							<div>合计：{{totalMoney}}</div>
							<el-button type="warning">挂单</el-button>
							<el-button type="danger" @click="delAllGoods">删除</el-button>
							<el-button type="success" @click="checkout">结账</el-button>
							
						</el-tab-pane>
						<el-tab-pane label="挂单">
							挂单
						</el-tab-pane>
						<el-tab-pane label="外卖">
							外卖
						</el-tab-pane>
					</el-tabs>
				</el-col>
				<el-col :span='17' id="order-list">					
					<div class="often-goods">
						<div class="title">
							常用商品
						</div>
						<div class="often-goods-list">
							<ul>
								<li v-for=" item in oftenGoods " @click="addOrderList(item)">
									<span>{{item.goodsName}}</span>
									<span class="o-price">${{item.price}}</span>
								</li>
							</ul>
						</div>
						
					</div>
					<div class="goods-type">
						<el-tabs>
							<el-tab-pane label="小食">
								<ul class="cookList">
									<li v-for="item in typeGoods[0]" @click="addOrderList(item)">
										<span class="footImg"><img v-bind:src="item.goodsImg"></span>
										<span class="footName">{{item.goodsName}}</span>
										<span class="footPrice">¥{{item.price}}元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="主食">
								<ul class="cookList">
									<li v-for="item in typeGoods[1]" @click="addOrderList(item)">
										<span class="footImg"><img v-bind:src="item.goodsImg"></span>
										<span class="footName">{{item.goodsName}}</span>
										<span class="footPrice">¥{{item.price}}元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="饮料">
								<ul class="cookList">
									<li v-for="item in typeGoods[2]" @click="addOrderList(item)">
										<span class="footImg"><img v-bind:src="item.goodsImg"></span>
										<span class="footName">{{item.goodsName}}</span>
										<span class="footPrice">¥{{item.price}}元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="套餐">
								<ul class="cookList">
									<li v-for="item in typeGoods[3]" @click="addOrderList(item)">
										<span class="footImg"><img v-bind:src="item.goodsImg"></span>
										<span class="footName">{{item.goodsName}}</span>
										<span class="footPrice">¥{{item.price}}元</span>
									</li>
								</ul>
							</el-tab-pane>
						</el-tabs> 
					</div>
				
				</el-col>
			</el-row>
		</div>
	</div>
	
</template>
<script>
	export default{
		name:'pos',
		created(){
			var _this=this;
			//ofterGoods

			this.$axios('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
			.then(function(response){_this.oftenGoods=response.data;})
			.catch(function(err){console.log(err)});

			this.$axios('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
			.then(function(response){_this.typeGoods=response.data;})
			.catch(function(err){console.log(err)});
		},
		data(){
			return{
				totalCount:0,
				totalMoney:0,
				tableData:[],
			    oftenGoods:[],
		        typeGoods:[]
			}
		},
		methods:{
			delSingleGoods(goods){
				console.log(goods);
				this.tableData=this.tableData.filter(function(o){
					return o.goodsId!=goods.goodsId;
				});
				this.getAll();

			},
			delAllGoods(){
				this.tableData=[];
				this.getAll();

			},
			addOrderList(goods){
				var _this=this;
				let isHave=false;
				console.log(goods.goodsId);
				
				for(let i=0;i<this.tableData.length;i++){
				
					if(this.tableData[i].goodsId==goods.goodsId){
						isHave=true;
					}
				}
				if(isHave){
					let arr=this.tableData.filter(function(o){return o.goodsId==goods.goodsId});
					arr[0].count++;		
				}
				else{
					let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
					this.tableData.push(newGoods);
				}
				this.getAll();
				
			},
			getAll(){
				var _this=this;
				_this.totalCount=0;
				_this.totalMoney=0;
				this.tableData.forEach(function(goods){
					_this.totalCount+=goods.count;
					_this.totalMoney=_this.totalMoney+goods.price*goods.count;
				});
			},
			checkout(){
				//应该和后端进行交互，
				if(this.totalCount!=0){
					this.tableData=[];
					this.totalCount=0;
					this.totalMoney=0;
					this.$message({
						message:'结账成功，欢迎下次光临',
						type:'success'
					});
				}
				else{
					this.$message.error('不能结账，请联系工程师，嘿嘿');
				}
			}
		},
		mounted:function(){
			var orderHeight=document.body.clientHeight;
			document.getElementById("order-list").style.height=orderHeight+'px';
		}
	}
</script>
<style scoped>
	.title{
		height:20px;
		border-bottom:1px solid #D3Dce6;
		background: #F9FAfc;
		padding:10px;
	}
	.often-goods-list ul li{
		list-style: none;
		float:left;
		border:1px solid #e5e9f2;
		padding:10px;
		margin:5px;
		background:#fff;
	}
	.o-price{
		color:#58B7FF;
	}
	.often-goods{
		overflow:hidden;
	}
	.cookList li{
		list-style:none;
		width:23%;
		height:auto;
		border:1px solid #e5e9f2;
		background:#fff;
		padding:2px;
		float:left;
		margin:2px;
	}
	.cookList li span{
		display: block;
		float:left;
	}
	.footImg{
		width: 40%;
	}
	.footName{
		font-size: 18px;
		padding-left:10px;
		color:brown;
	}
	.footPrice{
		font-size:16px;
		padding-left:10px;
		padding-top: 10px;
	}
</style>















