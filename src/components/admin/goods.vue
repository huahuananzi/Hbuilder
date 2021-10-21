<template>
	<div>
		<el-card>
	<el-row :gutter="25">
					<el-col :span="10">
						 <!-- 搜索添加 -->
						 <el-input placeholder="请输入搜索内容" v-model="queryInfo.query" clearable @clear="getGoodsList"> 
							<el-button slot="append" icon="el-icon-search" @click="getGoodsList"></el-button>
						 </el-input>
					</el-col>
					<el-col :span="4">
						 <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
					</el-col>
						
				</el-row>
	
	  <el-table
		:data="goodsList"
		height="780"
		border
		style="width: 100%">
			<el-table-column type="index"></el-table-column>
			<el-table-column prop="code" label="条码"></el-table-column>
			<el-table-column prop="identificationCode" label="标识码"></el-table-column>
			<el-table-column prop="name" label="名称"></el-table-column>
			<el-table-column prop="distributors" label="商贸名"></el-table-column>
			<el-table-column prop="purchasingPrice" label="进价"></el-table-column>
			<el-table-column prop="sellingPrice" label="卖价"></el-table-column>
			<el-table-column label="操作">
				<template slot-scope="scope">
					<!-- 编辑 -->
					<el-button type="primary" icon="el-icon-edit" size="mini" @click="showEditDialog(scope.row.id)"></el-button>
					<!-- 修改 -->
					<el-button type="danger" icon="el-icon-delete" size="mini" @click="deleteGoods(scope.row.id)"></el-button>
				</template>
			</el-table-column>
		
	  </el-table>
	  </el-card>
	
		<!-- 修改商品对话框 -->
	  <el-dialog title="修改商品信息" :visible.sync="editDialogVisible" width="50%" @colse="editDialogClosed">
	     <el-form :model="editForm"  ref="editFormRef" label-width="70px">
	      <!-- 条码 -->
	      <el-form-item label="条码" prop="code">
	        <el-input v-model="editForm.code" disabled></el-input>
	      </el-form-item>
	      <!-- 标识码 -->
	      <el-form-item label="标识码" prop="identificationCode">
	        <el-input v-model="editForm.identificationCode"></el-input>
	      </el-form-item>
	      <!-- 名称 -->
	      <el-form-item label="名称" prop="name">
	        <el-input v-model="editForm.name"></el-input>
			<!-- 商贸名 -->
			<el-form-item label="商贸名" prop="distributors">
			  <el-input v-model="editForm.distributors" ></el-input>
			</el-form-item>
			<!-- 进价 -->
			<el-form-item label="进价" prop="purchasingPrice">
			  <el-input v-model="editForm.purchasingPrice"></el-input>
			</el-form-item>
			<!-- 卖价 -->
			<el-form-item label="卖价" prop="sellingPrice">
			  <el-input v-model="editForm.sellingPrice"></el-input>
			</el-form-item>
	      </el-form-item>
	    </el-form>
		<!-- 内同底部区域 -->
	    <span slot="footer" class="dialog-footer">
	      <el-button @click="editDialogVisible = false">取 消</el-button>
	      <el-button type="primary" @click="editGoodsInfo">确 定</el-button>
	    </span>
	  </el-dialog>
	  
	  <!-- 新增商品界面 -->
	  	  <el-dialog title="添加商品" :visible.sync="addDialogVisible" :close-on-click-modal="false"  width="50%" @close="addDialogClosed">
	  		  <el-form :model="addForm"  ref="addFormRef" label-width="70px">
	  			  <!-- 条码 -->
	  			<el-form-item label="条码" prop="code">
	  				<el-input v-model="addForm.code"></el-input>
	  			</el-form-item>
	  			  <!-- 标识码 -->
	  			<el-form-item label="标识码" prop="identificationCode">
	  				<el-input v-model="addForm.identificationCode"></el-input>
	  			</el-form-item>
	  			  
	  			<!-- 商品名 -->
	  			<el-form-item label="商品名" prop="nmae">
	  				<el-input v-model="addForm.name"></el-input>
	  			</el-form-item>  
	  			  
	  			
	  			<!-- 商贸名 -->
	  			<el-form-item label="商贸名" prop="distributors">
	  				<el-input v-model="addForm.distributors"></el-input>
	  			</el-form-item>  
	  			    
	  			
	  			<!-- 进价 -->
	  			<el-form-item label="进价" prop="purchasingPrice">
	  				<el-input v-model="addForm.purchasingPrice"></el-input>
	  			</el-form-item>  
	  			    
	  			<!-- 卖价 -->
	  			<el-form-item label="卖价" prop="sellingPrice">
	  				<el-input v-model="addForm.sellingPrice"></el-input>
	  			</el-form-item>   
	  			  
	  		  </el-form>
	  			<!-- 添加的内同底部区域 -->
	  			<span slot="footer" class="dialog-footer">
	  			  <el-button @click="addDialogVisible = false">取 消</el-button>
	  			  <el-button type="primary" @click="addGoods">确 定</el-button>
	  			</span>
	  			
	  		
	  		
	  	  </el-dialog>
	  
	  
  </div>
</template>

<script>
	export default{
		
		data() {
			return {
				//查询信息实体
				queryInfo: {
				  query: "",
				  //pageNum: 1,
				  //pageSize: 5,
				},
				goodsList: [],// 用户列表
				//total: 0, // 最大数据记录
				
				// 修改用户信息
				editForm:{},
				// 控制修改用户对话框显示/隐藏
				editDialogVisible: false,

				addDialogVisible:false ,// 对话框显示
				//添加表单信息
				addForm: {
					id: '',
					code: '',
					identificationCode: '',
					name: '',
					distributors: '',
					purchasingPrice: '',
					sellingPrice: '',
				},
					
	
				
			};
		},
		created(){
			this.getGoodsList();
		},
		methods: {
			//获取所有商品信息
			async getGoodsList(){
				const { data: res } = await this.$http.get("allGoods", {params: this.queryInfo});
				this.goodsList = res.data;
				//this.total = res.numbers;
				
			},
			
			// // 监听pageSize改变的事件
			// handleSizeChange(newSize) {
			//   this.queryInfo.pageSize = newSize;
			//   this.getGoodsList(); // 数据发生改变重新申请数据
			// },
			// // 监听pageNum改变的事件
			// handleCurrentChange(newPage) {
			//   this.queryInfo.pageNum = newPage;
			//   this.getGoodsList(); // 数据发生改变重新申请数据
			// },
			 // 展示修改框数据
			async showEditDialog(id){
			   
			    const {data:updateGoods} = await this.$http.get("getGoodsUpdate?id="+id);
			    this.editForm = updateGoods;			//查询你出用户信息反填到编辑表单
			    this.editDialogVisible = true;	//开启编辑对话框
			},
			// 关闭窗口
			editDialogClosed(){
			  this.$refs.editFormRef.resetFields();	//重置信息
			},
			// 确认修改
			editGoodsInfo(){
			  this.$refs.editFormRef.validate(async valid =>{
			    console.log(valid);
			    if( !valid ) return;
			    // 发起请求
			    const {data:res} = await this.$http.put("editGoods",this.editForm);
			    console.log(res);
			     if (res != "success") return this.$message.error("修改失败！！！");
			    this.$message.success("修改成功！！！");
			    //隐藏
			    this.editDialogVisible = false;
			    this.getGoodsList();
			  })
			},
			//监听添加用户
			addDialogClosed(){
				this.$refs.addFormRef.resetFields();
			},
			//添加用户
			addGoods(){
				this.$refs.addFormRef.validate(async valid=>{
					console.log(valid);
					
					if(!valid) return;
					//发起请求
					const {data:res} = await this.$http.post("addGoods",this.addForm);
					
					if(res != "success"){
						return this.$message.error("添加失败！！！");
					}
					this.$message.success("添加成功");
					//隐藏
					this.addDialogVisible = false;
					this.getGoodsList();
				})
			},
			
			// 删除按钮
			async deleteGoods(id){
			  // 弹框
			  const confirmResult = await this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
			      confirmButtonText: '确定',
			      cancelButtonText: '取消',
			      type: 'warning'
			    }).catch(err => err)
			  // 成功删除为confirm 取消为 cancel
			  if(confirmResult!='confirm'){
			    return this.$message.info("已取消删除");
			  }
			  const {data:res} = await this.$http.delete("deleteGoods?id="+id);
			  if (res != "success") {
			    return this.$message.error("操作失败！！！");
			  }
			  this.$message.success("操作成功！！！");
			  this.getGoodsList();
			},
	}
};

</script>

<style lang='less' scoped>
	
	.el-breadcrumb{
		margin-bottom: 15px;
		font-size: 17px;
	}
</style>