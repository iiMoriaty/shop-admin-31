<template>
   <div class="mianbao">
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item v-for="(item,index) in info" :key="index">
                {{item}}
            </el-breadcrumb-item>
        </el-breadcrumb>
   </div>
</template>

<script>
export default {
    data(){
        return{
            info:[]
        }
    },
    //获取meta信息
    watch:{
        $route(){
            this.getRout();
        }
    },
    mounted(){
        this.getRout();
    },

    methods:{
        getRout(){
            //获取到matched的每一项的meta信息
            console.log(this);
            console.log(this.$route);
            const arr = this.$route.matched;
            let info = [];
            arr.forEach(v=>{
                info.push(v.meta)
            });
            this.info = info;
        }
    }


}
</script>

<style scoped>
    .mianbao{
        padding-bottom: 10px;
        border-bottom: 1px solid #ccc;
        margin-bottom: 20px;
    }
</style>
