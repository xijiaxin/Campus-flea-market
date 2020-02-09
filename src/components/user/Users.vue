<template>
    <div>
        <!-- 面包屑导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>用户管理</el-breadcrumb-item>
            <el-breadcrumb-item>用户列表</el-breadcrumb-item>
        </el-breadcrumb>
<!-- 卡片视图区 -->
  <el-card>
    <!-- 搜素与添加区 -->
    <el-row :gutter="20">
      <el-col :span="15"><el-input placeholder="请输入内容"  
      v-model="queryInfo.qurry" clearable @clear="getUserList">
      <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
       </el-input></el-col>
      <el-col :span="4">
          <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
      </el-col>
    </el-row>
    <!-- 用户列表区 -->
    <el-table :data="userlist" border stripe>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="姓名" prop="username"></el-table-column>
        <el-table-column label="邮箱" prop="email"></el-table-column>
        <el-table-column label="电话" prop="phone"></el-table-column>
        <el-table-column label="角色" prop="sex"></el-table-column>
        <!-- <el-table-column label="状态" >
            <template slot-scope="scope">
                {{scope.row}}
                <el-switch v-model="value" >
                </el-switch>
            </template> 
        </el-table-column> -->
        <el-table-column label="操作" >
            <template slot-scope="scope">
                <!-- 修改按钮 -->
                <el-tooltip  effect="dark" content="修改用户" placement="top" :enterable="false">
                        <el-button type="primary" icon="el-icon-edit" @click="showEditDialog(scope.row.id)"></el-button>
                </el-tooltip>
                <!-- 删除按钮 -->               
                <el-tooltip  effect="dark" content="删除用户" placement="top" :enterable="false">      
                        <el-button type="danger" icon="el-icon-delete" @click="removeUserById(scope.row.id)"></el-button>
                </el-tooltip>
                <!-- 分配角色按钮 -->
                <el-tooltip  effect="dark" content="分配角色" placement="top" :enterable="false">      
                        <el-button type="warning" icon="el-icon-setting"></el-button>
                </el-tooltip>
            </template>
        </el-table-column>
    </el-table>
    <!-- 分页区域 -->
     <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pages"
      
      :page-size="queryInfo.pagesize"
      layout="total, prev, pager, next, jumper"
      :total="total">  
    </el-pagination>
</el-card>
<!-- 添加用户的对话框 -->
<el-dialog
  title="添加用户"
  :visible.sync="addDialogVisible"
  width="50%" @close="addDialogClosed">
 <!-- 内容主体区域 -->
 
    <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="70px">
    <!-- 用户名输入 -->
        <el-form-item label="用户名" prop="username">
             <el-input v-model="addForm.username"></el-input>
        </el-form-item>
    <!-- 密码输入 -->
        <el-form-item label="密码" prop="password">
            <el-input v-model="addForm.password"></el-input>
        </el-form-item>
    <!-- 邮箱输入 -->
        <el-form-item label="邮箱" prop="email">
             <el-input v-model="addForm.email"></el-input>
        </el-form-item>
    <!-- 手机号输入 -->
        <el-form-item label="手机" prop="mobile">
            <el-input v-model="addForm.mobile"></el-input>
        </el-form-item>
    </el-form> 
  <!-- 底部区域 -->
        <span slot="footer" class="dialog-footer">
             <el-button @click="addDialogVisible = false">取 消</el-button>
             <el-button type="primary" @click="addUser">确 定</el-button>
        </span>
</el-dialog>
<!-- 修改用户的对话框 -->
<el-dialog
     title="修改用户"
    :visible.sync="editDialogVisible"
    width="50%" @close="editDialogClosed">
        <el-form :model="editForm" :rules="editFormRules" ref="editFormRef" label-width="70px">
        <el-form-item label="用户名">
             <el-input v-model="editForm.username" disabled></el-input>
        </el-form-item>
        <el-form-item label="邮箱">
             <el-input v-model="editForm.email" prop="email"></el-input>
        </el-form-item>
        <el-form-item label="手机号">
             <el-input v-model="editForm.phone" prop="phone"></el-input>
        </el-form-item>
    </el-form>
         <span slot="footer" class="dialog-footer">

         <el-button @click="editDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="editUserInfo = false">确 定</el-button>
    </span>
</el-dialog>
    </div>
</template>

<script>
export default{
  
    
    data(){
   // 验证邮箱的规则
    var checkEmail = (rule, value, cb) => {
      // 验证邮箱的正则表达式
      const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/

      if (regEmail.test(value)) {
        // 合法的邮箱
        return cb()
      }

      cb(new Error('请输入合法的邮箱'))
    }

    // 验证手机号的规则
    var checkMobile = (rule, value, cb) => {
      // 验证手机号的正则表达式
      const regMobile = /^(0|86|17951)?(13[0-9]|15[012356789]|17[678]|18[0-9]|14[57])[0-9]{8}$/

      if (regMobile.test(value)) {
        return cb()
      }

      cb(new Error('请输入合法的手机号'))
    }
    
        return {
            //获取用户列表的数据参数
            queryInfo:{
                qurry:'',
                //当前的页数
                pages: 1,
                //当前每页显示多少条数据
                pagesize:5
            },
            userlist:[],
            total: 0,
            //控制添加用户对话框的显示与隐藏
            addDialogVisible: false,
            //添加用户的表单数据
            addForm: {
                username: '',
                password: '',
                email: '',
                mobile: ''
            },
            //添加表单的验证规则对象
            addFormRules: {
                // 用户名校验规则
                username:[{required: true, message: '请输入用户名', trigger:'blur'},{min: 3,max: 10,message: '用户名的长度应该在3~10个之间', trigger: 'blur'}],
                // 密码校验规则
                 password:[{required: true, message: '请输入密码', trigger:'blur'},{min: 6,max: 15,message: '密码的长度应该在6~15个之间', trigger: 'blur'}],
                 // 邮箱校验规则
                 email: [
                 { required: true, message: '请输入邮箱', trigger: 'blur' },
                 { validator: checkEmail, trigger: 'blur' }
                 ],
                mobile: [
                    { required: true, message: '请输入手机号', trigger: 'blur' },
                    { validator: checkMobile, trigger: 'blur' }
                ]

            },
            //控制修改用户对话框的显示与隐藏
            editDialogVisible : false,
            //查询到的用户信息对象
            editForm:{},
            editFormRules: {
                email:[
                    { equired: true,message:'请输入用户邮箱号' ,trigger:'blur' },
                    {validator: checkEmail,trigger: 'blur'}
                ],
                phone: [
                    { equired: true,message:'请输入用户手机' ,trigger:'blur' },
                ]
            }
        }
    },
    created(){
        this.getUserList()
    },
    methods: {
        async getUserList(){
        const{data: res} = await this.$http.get('http://39.106.188.22:8800/api/user/findAll?page=1&rows=10',{ params: this.queryInfo })
         this.userlist = res.data
         this.total = res.total
        console.log(res)
        console.log(this.userlist)
        console.log(this.total)
        },
        //监听pagesize改变事件
        handleSizeChange(newSize){
            console.log(newSize)
            this.queryInfo.pagesize = newSize
        },
        //监听页码值改变的事件
        handleCurrentChange(newPage){
            this.queryInfo.pages = newPage
            this.getUserList()
            console.log(newPage)
        },
        //监听对话框的监听事件
       addDialogClosed(){
           this.$refs.addFormRef.resetFields()
       },
       //点击按钮添加新用户
       addUser(){
           this.$refs.addFormRef.validate(async valid =>{
               if(!valid) return
               //可以发起添加用户的网络请求
              const {data: res} = await this.$http.post('http://39.106.188.22:8800/api/user/save',this.addForm)
              if(res.code !=-1){
                  this.$message.success('添加用户成功')
              }
              this.$message.error('添加用户失败')
              //隐藏添加用户的对话框
             this.addDialogVisible = false
             //重新获取用户列表数据
             this.getUserList()
           })
       },
        //展示编辑用户的对话框
        async showEditDialog(id){
            console.log(id)
            this.editDialogVisible = true
            const {data: res} = await this.$http.get('http://39.106.188.22:8800/api/user/findById?id='+ id)
             this.editForm = res
         this.editDialogVisible = true
      },
        //监听修改用户对话框的关闭
      editDialogClosed(){
          //this.editFormRef.restFields()
      },
        //  功能没实现 
      editUserInfo(){
          this.$refs.editFormRef.validate(valid => {
              console.log(valid)
              //发起修改用户的数据请求
              this.$http.put('http://39.106.188.22:8800/api/user/update' + this.editForm.id,{
                  email:this.editForm.email,
                  mobile:this.editForm.phone
              })
              //关闭对话框
              this.editDialogVisible = false
              //刷新数据列表
              this.getUserList()
              //提示修改成功
              this.$message.success('更新用户信息成功！')
          })
      },
      //根据Id删除对应的用户信息
      async removeUserById(id){
          console.log(id)
          //弹框询问是否删除
       const confirmResult = await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).catch(err => err)
        //如果用户确认删除，则返回值为字符串 confirm
        //如果用户取消删除，则返回值为字符串 cancel
        //console.log(confirmResult)
        if(confirmResult !== 'confirm'){
            return this.$message.info('已取消删除')
        }
        console.log('确认了删除')
        //执行完删除后需要更新用户列表
        this.getUserList()
      }
    }
}
</script>
<style lang="less" scoped>
</style>