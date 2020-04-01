<template>
  <div>
      <!--查询表单-->
      <el-form :model="params">
        <el-button type="primary" v-on:click="query" size="small">查询</el-button>
        <!-- <router-link class="mui-tab-item" :to="{path:'/cms/tamplate/add/',query:{
          page: this.params.page,
          siteId: this.params.siteId}}">
          <el-button  type="primary" size="small">新增模板</el-button>
        </router-link> -->
        <el-upload action="http://localhost:11000/api/cms/template/upload">
          <el-button size="small" type="primary">上传模板</el-button>
        </el-upload>
      </el-form>
      <!--列表-->
      <el-table :data="list" highlight-current-row v-loading="listLoading" style="width: 100%;">
        <el-table-column type="index" width="60">
        </el-table-column>
        <el-table-column prop="siteId" label="站点id" >
        </el-table-column>
        <el-table-column prop="templateName" label="模板名称" >
        </el-table-column>
        <el-table-column prop="templateParams" label="模板参数" >
        </el-table-column>
        <el-table-column prop="templateFileId" label="模板文件id" >
        </el-table-column>
        <el-table-column label="删除" width="80">
          <template slot-scope="scope">
            <el-button
              size="mini"
              type="danger"
              @click="del(scope.$index, scope.row)">删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    <!--分页-->
    <el-col :span="24" class="toolbar">

      <el-pagination background layout="prev, pager, next" @current-change="changePage" :page-size="this.params.size"
                     :total="total" :current-page="this.params.page"
                     style="float:right;">
      </el-pagination>
    </el-col>
  </div>
</template>
<script>
  import * as cmsApi from '../api/cms'
  import utilApi from '@/common/utils';
  export default{
    data(){
      return {
        params:{
          page:1,//页码
          size:5,//每页显示个数
          siteId:'5a751fab6abb5044e0d19ea1'//站点id 5a751fab6abb5044e0d19ea1
        },
        listLoading:false,
        list:[],
        total:0,

        siteList:[]//站点列表
      }
    },
    methods:{
      formatCreatetime(row, column){
        var createTime = new Date(row.pageCreateTime);
        if (createTime) {
          return utilApi.formatDate(createTime, 'yyyy-MM-dd hh:mm:ss');
        }
      },

      edit(pageId){
        this.$router.push({ path: '/cms/page/edit/'+pageId,query:{
          page: this.params.page,
          siteId: this.params.siteId}})
      },
      //删除
      del (index, row) {
        this.$confirm('确认删除该记录吗?', '提示', {
          type: 'warning'
        }).then(() => {
          this.listLoading = true;
          let templateId = row.templateId;
          cmsApi.page_del(templateId).then((res) => {
            this.listLoading = false;
            if(res.success){
              this.$message.success("删除成功")
              this.query();
            }else{
              this.$message.error('删除失败');
            }

          });
        }).catch(() => {

        });
      },
      changePage(page){
        this.params.page = page;
        this.query()
      },
      query(){
        cmsApi.template_list(this.params.page,this.params.size,this.params).then((res)=>{
          console.log(res)
          this.total = res.queryResult.total
          this.list = res.queryResult.list
        })
      },
    },
    created(){
        //存储 请求参数
      /*if(this.$route.query.page){
       this.params.page = Number.parseInt(this.$route.query.page)
       }*/
      this.params.page = Number.parseInt(this.$route.query.page||1);
      this.params.siteId = this.$route.query.siteId||'';
    },
    mounted() {
      //默认查询页面
      this.query()
      //初始化站点列表
      this.siteList = [
        {
          siteId:'5a751fab6abb5044e0d19ea1',
          siteName:'门户主站'
        },
        {
          siteId:'102',
          siteName:'测试站'
        }
      ]
    }
  }
</script>
<style>

</style>
