<template>
    <el-form ref="form" :model="form" label-width="80px">

        <el-form-item label="所属类别">
            <el-select v-model="form.category_id" placeholder="请选择">
                <el-option 
                v-for="(item, index) in categorys" 
                :key ="index"
                :label="item.title" 
                :value="item.category_id">
                </el-option>
            </el-select>
        </el-form-item>

        <el-form-item label="是否发布">
            <el-switch v-model="form.status"></el-switch>
        </el-form-item>

        <el-form-item label="活动性质">
            <el-checkbox label="置顶" v-model="form.is_top" ></el-checkbox>
            <el-checkbox label="热门" v-model="form.is_hot"></el-checkbox>
        </el-form-item>

        <el-form-item label="内容标题">
            <el-input v-model="form.title"></el-input>
        </el-form-item>

        <el-form-item label="副标题">
            <el-input v-model="form.sub_title"></el-input>
        </el-form-item>

        <el-form-item label="封面图片">
            <el-upload
                class="avatar-uploader"
                action="http://localhost:8899/admin/article/uploadimg"
                :show-file-list="false"
                :on-success="handleAvatarSuccess"
                :before-upload="beforeAvatarUpload">
                <img v-if="imageUrl" :src="imageUrl" class="avatar">
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
        </el-form-item>

         <el-form-item label="商品货号">
            <el-input v-model="form.goods_no"></el-input>
        </el-form-item>

         <el-form-item label="库存数量">
            <el-input v-model="form.stock_quantity"></el-input>
        </el-form-item>

         <el-form-item label="市场价格">
            <el-input v-model="form.market_price"></el-input>
        </el-form-item>

         <el-form-item label="销售价格">
            <el-input v-model="form.sell_price"></el-input>
        </el-form-item>

        <el-form-item label="相册">
            <el-upload
                action="http://localhost:8899/admin/article/uploadimg"
                list-type="picture-card"
                :on-success="handleCartSuccess"
                :on-preview="handlePictureCardPreview"
                :on-remove="handleRemove">
                <i class="el-icon-plus"></i>
            </el-upload>
                <el-dialog :visible.sync="dialogVisible">
                <img width="100%" :src="dialogImageUrl" alt="">
            </el-dialog>
        </el-form-item>

        <el-form-item label="内容摘要">
            <el-input type="textarea" v-model="form.zhaiyao"></el-input>
        </el-form-item>

        <el-form-item label="内容描述" class="editor">
            <quillEditor v-model="form.content" ></quillEditor>
        </el-form-item>

        <el-form-item>
            <el-button type="primary" @click="onSubmit">立即创建</el-button>
            <el-button>取消</el-button>
        </el-form-item>
    </el-form>
</template>

<script>

    import 'quill/dist/quill.core.css'
    import 'quill/dist/quill.snow.css'
    import 'quill/dist/quill.bubble.css'

    import { quillEditor } from 'vue-quill-editor'

  export default {
    data() {
      return {
        form: {

            category_id:"",
            status:false,
            is_top:false,
            is_hot:false,
            title:"",
            sub_title:"",
            imgList:[],
            goods_no:"", 
            stock_quantity:"", 
            market_price:"",
            sell_price:"",
            fileList:[],
            zhaiyao:"",
            content:"",

            is_slide:false
        },

        categorys:[],
        imageUrl: '',
        dialogImageUrl: '',
        dialogVisible: false,
      }
    },

    components: {
        quillEditor
    },

    methods: {
        onSubmit() {
            // 提交到添加商品的接口
            this.$axios({
                url: "http://localhost:8899/admin/goods/add/goods",
                method: "POST",
                data: this.form,
                // 处理session跨域
                withCredentials: true
            }).then(res => {
                const {status, message} = res.data;

                if(status === 0){
                    // 成功的提示
                    this.$message.success(message);
                    // 返回上一页
                    this.$router.back();
                }else{
                    // 跳转到登录页
                    this.$router.push("/login");
                }
            })
        },

        //上传成功之后的回调函数
        handleAvatarSuccess(res, file) {
            //设置图片地址
            this.imageUrl = URL.createObjectURL(file.raw);
            //把上传成功的结果赋值给form.imgList
            this.form.imgList = [res];

        },

        beforeAvatarUpload(file) {
            const isLt2M = file.size / 1024 / 1024 < 2;

            if (!isLt2M) {
            this.$message.error('上传头像图片大小不能超过 2MB!');
            }
            return isLt2M;
        },

        handleRemove(file, fileList) {
            console.log(file, fileList);
            //把删除之后的列表赋值给this.form.fileList
            const files = fileList.map(v =>{
                return v.response;
            });

            this.form.fileList = files;
        },
        handlePictureCardPreview(file) {
            this.dialogImageUrl = file.url;
            this.dialogVisible = true;
        },
        handleCartSuccess(res,file,fileList){
            const files = fileList.map(v=>{
                return v.response;
            });

            this.form.fileList = files;
        },
    },

    mounted(){
        this.$axios({
            url:`http://localhost:8899/admin/category/getlist/:tablename`,
            method:"GET",

        }).then(res=>{
            // console.log(res.data);
            const {status, message} = res.data;
            this.categorys = message;
        })
    }
  }
</script>


<style>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }

  .editor .ql-toolbar{
      line-height: 1;
  }
</style>
