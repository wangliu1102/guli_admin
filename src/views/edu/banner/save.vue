<template>
  <div class="app-container">
    Banner表单
    <el-form label-width="120px">
      <el-form-item label="Banner标题">
        <el-input v-model="banner.title"/>
      </el-form-item>
      <el-form-item label="Banner排序">
        <el-input-number v-model="banner.sort" controls-position="right" :min="0"/>
      </el-form-item>
      <el-form-item label="链接地址">
        <el-input v-model="banner.linkUrl"/>
      </el-form-item>
      <el-form-item label="图片地址">
        <el-upload
          :show-file-list="false"
          :on-success="handleBannerSuccess"
          :before-upload="beforeBannerUpload"
          :action="BASE_API+'/eduoss/fileoss'"
          class="avatar-uploader">
          <img v-if="banner.imageUrl" :src="banner.imageUrl" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>

      </el-form-item>

      <el-form-item>
        <el-button :disabled="saveBtnDisabled" type="primary" @click="saveOrUpdate">保存</el-button>
      </el-form-item>
    </el-form>

  </div>
</template>
<script>
  import banner from '@/api/edu/banner'

  const defaultForm = {
    title: '',
    sort: 0,
    imageUrl: '',
    linkUrl: ''
  }

  export default {
    data() {
      return {
        banner: defaultForm,
        BASE_API: process.env.BASE_API, //获取dev.env.js里面地址
        saveBtnDisabled: false  // 保存按钮是否禁用,
      }
    },
    created() { //页面渲染之前执行
      this.init()
    },
    watch: {  //监听
      $route(to, from) { //路由变化方式，路由发生变化，方法就会执行
        this.init()
      }
    },
    methods: {
      init() {
        //判断路径有id值,做修改
        if (this.$route.params && this.$route.params.id) {
          //从路径获取id值
          const id = this.$route.params.id
          //调用根据id查询的方法
          this.getInfo(id)
        } else { //路径没有id值，做添加
          //清空表单
          this.banner = { ...defaultForm }
        }
      },
      //根据讲师id查询的方法
      getInfo(id) {
        banner.getBannerInfo(id)
          .then(response => {
            this.banner = response.data.item
          })
      },
      saveOrUpdate() {
        //判断修改还是添加
        //根据banner是否有id
        if (!this.banner.id) {
          //添加
          this.saveBanner()
        } else {
          //修改
          this.updateBanner()
        }
      },
      //修改Banner的方法
      updateBanner() {
        this.banner.linkUrl = this.banner.linkUrl.trim()
        banner.updateBanner(this.banner)
          .then(response => {
            //提示信息
            this.$message({
              type: 'success',
              message: '修改成功!'
            })
            //回到列表页面 路由跳转
            this.$router.push({ path: '/banner/table' })
          })
      },
      //添加Banner的方法
      saveBanner() {
        this.banner.linkUrl = this.banner.linkUrl.trim()
        banner.addBanner(this.banner)
          .then(response => {//添加成功
            //提示信息
            this.$message({
              type: 'success',
              message: '添加成功!'
            })
            //回到列表页面 路由跳转
            this.$router.push({ path: '/banner/table' })
          })
      },
      //上传封面成功调用的方法
      handleBannerSuccess(res, file) {
        this.banner.imageUrl = res.data.url
      },
      //上传之前调用的方法
      beforeBannerUpload(file) {
        const isJPG = file.type === 'image/jpeg'
        const isLt2M = file.size / 1024 / 1024 < 2

        if (!isJPG) {
          this.$message.error('上传图片只能是 JPG 格式!')
        }
        if (!isLt2M) {
          this.$message.error('上传图片大小不能超过 2MB!')
        }
        return isJPG && isLt2M
      },

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

</style>
