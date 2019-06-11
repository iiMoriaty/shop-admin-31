<template>
    <el-container>
        <el-aside width="auto">
            <!-- 菜单栏组件 isShow表示是否展开 -->
            <!-- 子组件也是父组件本身的一部分 -->
            <!-- 为子组件绑定自定义属性 -->
            <Menu :isShow="isShow"/>
        </el-aside>
        <el-container>
            <el-header>
                <!-- 为子组件绑定事件 -->
                <Header @iclick="handleChangeShow"/>
            </el-header>
            <el-main>
                <Mianbao/>
                <!-- 子路由匹配到的组件 -->
                <router-view></router-view>
            </el-main>
        </el-container>
    </el-container>
</template>

<script>
//导入头部组件
import Header from "../components/Header";
//导入面包屑组件
import Mianbao from "../components/Mianbao";
//导入菜单栏组件
import Menu from "../components/Menu";

export default {

    data(){
        return {
            // 属性在父组件的data里定义
            isShow:false
        }
    },

    components:{
        Header,
        Mianbao,
        Menu
    },

    methods:{
        //修改isShow的事件
        handleChangeShow(){
            this.isShow = !this.isShow;
        }
    }
}
</script>

<style>
    .el-header, .el-footer {
        color: #333;
        text-align: center;
        line-height: 60px;
    }
    
    .el-aside {
        background-color: #D3DCE6;
    }
    
    .el-main {
        background-color: #E9EEF3;
        color: #333;
    }
    
    body > div > .el-container {
        position: absolute;
        top:0;
        bottom: 0;
        width: 100%;
    }
    
    .el-container:nth-child(5) .el-aside,
    .el-container:nth-child(6) .el-aside {
        line-height: 260px;
    }
    
    .el-container:nth-child(7) .el-aside {
        line-height: 320px;
    }
</style>
