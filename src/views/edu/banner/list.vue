<template>
  <div class="app-container">
    Banner列表

    <!--查询表单-->
    <el-form :inline="true" class="demo-form-inline">
      <el-form-item>
        <el-input v-model="bannerQuery.title" placeholder="标题"/>
      </el-form-item>

      <el-form-item label="添加时间">
        <el-date-picker
          v-model="bannerQuery.begin"
          type="datetime"
          placeholder="选择开始时间"
          value-format="yyyy-MM-dd HH:mm:ss"
          default-time="00:00:00"
        />
      </el-form-item>
      <el-form-item>
        <el-date-picker
          v-model="bannerQuery.end"
          type="datetime"
          placeholder="选择截止时间"
          value-format="yyyy-MM-dd HH:mm:ss"
          default-time="00:00:00"
        />
      </el-form-item>

      <el-button type="primary" icon="el-icon-search" @click="getList()">查询</el-button>
      <el-button type="default" @click="resetData()">清空</el-button>
    </el-form>

    <!-- 表格 -->
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="数据加载中"
      border
      fit
      highlight-current-row>

      <el-table-column
        label="序号"
        width="70"
        align="center">
        <template slot-scope="scope">
          {{ (page - 1) * limit + scope.$index + 1 }}
        </template>
      </el-table-column>

      <el-table-column prop="title" label="标题" align="center"/>

      <el-table-column label="图片信息" width="470" align="center">
        <template slot-scope="scope">
          <div>
            <div>
              <img :src="scope.row.imageUrl" :alt="scope.row.title" width="150px">
            </div>
          </div>

        </template>
      </el-table-column>

      <el-table-column prop="gmtCreate" label="添加时间" width="160" align="center"/>

      <el-table-column prop="sort" label="排序" align="center"/>

      <el-table-column label="操作"align="center">
        <template slot-scope="scope">
          <router-link :to="'/banner/edit/'+scope.row.id">
            <el-button type="primary" size="mini" icon="el-icon-edit">修改</el-button>
          </router-link>
          <el-button type="danger" size="mini" icon="el-icon-delete" @click="removeDataById(scope.row.id)">删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页 -->
    <el-pagination
      :current-page="page"
      :page-size="limit"
      :total="total"
      style="padding: 30px 0; text-align: center;"
      layout="total, prev, pager, next, jumper"
      @current-change="getList"
    />

  </div>
</template>
<script>
  //引入调用banner.js文件
  import banner from '@/api/edu/banner'

  export default {
    //写核心代码位置
    // data:{
    // },
    data() { //定义变量和初始值
      return {
        listLoading: true, // 是否显示loading信息
        list: null,//查询之后接口返回集合
        page: 1,//当前页
        limit: 5,//每页记录数
        total: 0,//总记录数
        bannerQuery: {} //条件封装对象
      }
    },
    created() { //页面渲染之前执行，一般调用methods定义的方法
      //调用
      this.getList()
    },
    methods: {  //创建具体的方法，调用banner.js定义的方法
      //banner列表的方法
      getList(page = 1) {
        this.page = page
        this.listLoading = true
        banner.getBannerListPage(this.page, this.limit, this.bannerQuery)
          .then(response => {//请求成功
            //response接口返回的数据
            //console.log(response)
            this.list = response.data.rows
            this.total = response.data.total
            console.log(this.list)
            console.log(this.total)
            this.listLoading = false
          })
      },
      resetData() {//清空的方法
        //表单输入项数据清空
        this.bannerQuery = {}
        //查询所有讲师数据
        this.getList()
      },
      //删除banner的方法
      removeDataById(id) {
        this.$confirm('此操作将永久删除banner记录, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {  //点击确定，执行then方法
          //调用删除的方法
          banner.deleteBannerById(id)
            .then(response => {//删除成功
              //提示信息
              this.$message({
                type: 'success',
                message: '删除成功!'
              })
              //回到列表页面
              this.getList()
            })
        }) //点击取消，执行catch方法
          .catch(() => {
            this.$message({
              type: 'info',
              message: '取消删除'
            });
          })
      }

    }
  }
</script>

