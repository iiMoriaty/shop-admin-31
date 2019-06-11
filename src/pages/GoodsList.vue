<template>
    <div>
        <el-row type="flex" justify="space-between">
            <!-- 按钮列表 -->
           <div>
                <el-button>新增</el-button>
                <el-button type="danger" @click="handleDeleteMore">删除</el-button>
           </div>

            <div class="input-search">
                <el-input placeholder="请输入内容"  class="input-with-select" v-model="searchValue">
                    <el-button slot="append" icon="el-icon-search" @click="handleSearch"></el-button>
                </el-input>
            </div>
        </el-row>

        <!-- 商品列表的表格 -->
        <!-- data:表格的数据 -->
        <!-- tooltip-effect：工具栏的效果 -->
        <!-- selection-change：选择表格的每一项时触发的事件 -->
        <el-table
            ref="multipleTable"
            :data="tableData"
            tooltip-effect="dark"
            style="width: 100%"
            @selection-change="handleSelectionChange">
            
            <el-table-column type="selection" width="55">
            </el-table-column>

            <!-- label：每一列的标题 -->
            <el-table-column
                label="标题"
                width="300">
                <template slot-scope="scope">
                    <el-row type="flex" align="middle" >
                        <img :src ="scope.row.imgurl" class="goods-img"/>
                        <div>
                            {{scope.row.title}}
                        </div>
                    </el-row>
                </template>
            </el-table-column>

            <el-table-column
                prop="categoryname"
                label="类型"
                width="120">
            </el-table-column>

            <el-table-column
                prop="sell_price"
                label="价格"
                width="120">
            </el-table-column>

            <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button
                    size="mini"
                    @click="handleEdit(scope.row)">编辑</el-button>
                    <el-button
                    size="mini"
                    type="danger"
                    @click="handleDelete(scope.row)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>

        <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="pageIndex"
            :page-sizes="[5, 10, 15, 20]"
            :page-size="5"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total">
        </el-pagination>
    </div>
</template>

<script>
export default {
    data() {
      return {
        tableData: [{
            // id: 103,        
            // title: "骆驼男装2017秋季新款运动休闲纯色夹克青年宽松长袖针织开衫卫",
            // is_top: 1,
            // is_hot: 1,
            // is_slide: 1,      
            // categoryname: "男装",
            // img_url: "/imgs/SJ4EgwosX0wTqvyAvhtFGT1w.jpg",
            // imgurl:"https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1560177150369&di=9fe2827be9679a391cf83499aa3c4db4&imgtype=0&src=http%3A%2F%2Fimg5.duitang.com%2Fuploads%2Fblog%2F201308%2F02%2F20130802111244_uECFX.jpeg",
            // goods_no: "NZ0000000002",
            // stock_quantity: 200,
            // market_price: 1000,
            // sell_price: 800 
        }],

        selectGoods:[],
        searchValue:"",
        pageIndex: 1,
        pageSize:5,
        total:0
      }
    },

    methods: {

        getList(){
            this.$axios({
                url:`http://localhost:8899/admin/goods/getlist?pageIndex=${this.pageIndex}&pageSize=${this.pageSize}&searchvalue=${this.searchValue}`,
                method: "GET"
            }).then(res => {
                console.log(123);
                const data = res.data;
                // 商品列表的数据
                this.tableData = data.message;
                // 总条数
                this.total = data.totalcount;
            })
        },

        handleSelectionChange(val) {
            this.selectGoods = val;
        },

        handleSearch(){
            this.getList();
        },

        //编辑商品
        handleEdit(goods){
            console.log(goods);
        },
        //删除商品
        handleDeleteMore(){
            // console.log(this.selectGoods);
            //获取到id
            const arr = this.selectGoods.map(v=>{
                return v.id;
            });

            const ids = arr.join(",");

            //调用删除商品的接口
            this.$axios({
                url:`http://localhost:8899/admin/goods/del/${ids}`,
                method:"GET"
            }).then(res =>{
                // console.log(res.data);
                const {message, status} = res.data;

                if(status === 0){
                    this.$message.success(message);
                    this.getList();
                }else{
                    this.$message.error(message);
                }
            })
        },
        //删除单个商品
        handleDelete(goods){
            const id = goods.id;

            //调用删除商品的接口
            this.$axios({
                url:`http://localhost:8899/admin/goods/del/${id}`,
                method:"GET"
            }).then(res =>{
                // console.log(res.data);
                const {message, status} = res.data;

                if(status === 0){
                    this.$message.success(message);
                    this.getList();
                }else{
                    this.$message.error(message);
                }
            })
        },

        handleSizeChange(val) {
            this.pageSize = val;
            this.getList();
        },
        handleCurrentChange(val) {
            this.pageIndex = val;
            this.getList();
        },
    },

     mounted(){
            this.getList();
        }

}
</script>



<style>
    .el-select .el-input {
        width: 130px;
    }
  .input-with-select .el-input-group__prepend {
        background-color: #fff;
    }
    .input-search {
        width: 300px;
        margin-bottom: 20px;
    }

    .goods-img{
        width:60px;
        height: 60px;
        flex-shrink: 0;
        margin-right:5px;
    }
</style>




