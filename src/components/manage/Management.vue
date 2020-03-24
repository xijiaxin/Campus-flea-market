<template>
    <div>
           <!-- 面包屑导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>分类管理</el-breadcrumb-item>
            <el-breadcrumb-item>分类列表</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- 卡片视图区 -->
        <el-card>
            <!-- 权限表格区域 -->
            <el-table :data="manageList" stripe style="width: 100%" border>
                <el-table-column prop="id" label="id">
                </el-table-column>
                <el-table-column prop="name" label="分类名称">
                </el-table-column>
                <el-table-column prop="image" label="图片">
                </el-table-column>
                <el-table-column label="操作" >
                    <template slot-scope="scope">
                    <!-- 修改分类按钮 -->
                    <el-tooltip  effect="dark" content="修改一级分类" placement="top" :enterable="false">
                        <el-button type="primary" icon="el-icon-edit"  @click="showManageDialog(scope.row.id)"></el-button>
                    </el-tooltip>
                    <!-- 删除分类按钮 -->
                     <el-tooltip  effect="dark" content="删除一级分类" placement="top" :enterable="false">      
                        <el-button type="danger" icon="el-icon-delete" @click="removeManageById(scope.row.id)"></el-button>
                     </el-tooltip>
                    <!-- 管理分类按钮 -->
                     <el-tooltip  effect="dark" content="管理二级分类" placement="top" :enterable="false" >      
                     <el-button type="warning" icon="el-icon-setting" @click="showChildDialog(scope.row.id)"></el-button> 
                </el-tooltip>
                    <!-- 添加二级分类 -->
                    <el-button type="primary" @click="addChild(scope.row.id)">添加二级分类</el-button>
                </template>
                </el-table-column>
                </el-table>
        </el-card>
        <!-- 修改一级分类对话框 -->
        <el-dialog title="修改一级分类" :visible.sync="editDialogVisible" width="50%" @close="editDialogClosed">
            <!-- 内容主体区域 -->
            <el-form  v-model="editForm" label-width="80px" ref="editFormRef">
                <el-form-item label="分类ID">
                <el-input v-model="editForm.id" disabled></el-input>
                </el-form-item>
                <el-form-item label="分类名称">
                <el-input v-model="editForm.name"></el-input>
                </el-form-item>
            </el-form>
            <!-- 底部按钮区域 -->
            <span slot="footer" class="dialog-footer">
            <el-button @click="editDialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="editManageInfo">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 查询二级分类对话框 -->
        <el-dialog title="管理二级级分类" :visible.sync="childDialogVisible" width="70%" >
            <!-- 内容主体区域 -->
            
            <el-table :data="childList" stripe style="width: 100%" >
                <el-table-column  prop="id" label="分类ID" width="180">
                </el-table-column>
                <el-table-column  prop="name" label="分类名称" width="180">
                </el-table-column>
                <el-table-column  prop="classify1id" label="classify1id" width="180">
                </el-table-column>
                <el-table-column label="操作按钮">
                <template slot-scope="scope">
                     <!-- 修改分类按钮 -->
                    <el-tooltip  effect="dark" content="修改二级分类" placement="top" :enterable="false">
                        <el-button type="primary" icon="el-icon-edit" @click="editChild"></el-button>
                    </el-tooltip>
                    <!-- 删除分类按钮 -->
                     <el-tooltip  effect="dark" content="删除二级分类" placement="top" :enterable="false">      
                        <el-button type="danger" icon="el-icon-delete" @click="removeChildById(scope.row.id)"></el-button>
                     </el-tooltip>
                </template>
                </el-table-column>
             </el-table>
            <!-- 底部按钮区域 -->
            <span slot="footer" class="dialog-footer">
            <el-button @click="childDialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="childDialogVisible = false">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 添加二级分类对话框 -->
        <el-dialog title="添加二级分类" :visible.sync="addChildDialogVisible" width="50%" >
            <el-form :model="addChildForm" :rules="addChildRules" ref="addChildRef" label-width="100px">
                <el-form-item label="新分类名称" prop="name">
                    <el-input v-model="addChildForm.name"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
            <el-button @click="addChildDialogVisible = false">取 消</el-button>
             <el-button type="primary" @click="addChildDialogVisible = false">确 定</el-button>
            </span>
        </el-dialog>,
        <!-- 修改二级分类对话框 -->
        <el-dialog  title="提示" :visible.sync="editchildDialogVisible" width="30%">
            <el-form ref="editeChildFormRef" :model="editeChildForm" label-width="80px">
                <el-form-item label="活动名称">
                    <el-input v-model="editeChildForm.name"></el-input>
                </el-form-item>
            </el-form>
                <span slot="footer" class="dialog-footer">
                    <el-button @click="editchildDialogVisible = false">取 消</el-button>
                    <el-button type="primary" @click="editchildDialogVisible = false">确 定</el-button>
                </span>
        </el-dialog>
    </div>
</template>
<script>
export default {
    data(){
        return {
            queryInfo:{},
            manageList:[],
            //控制修改用户对话框的显示与隐藏
            editDialogVisible: false,
            //查询到的一级菜单信息
            editForm:{},
            //控制管理二级菜单对话框的显示与隐藏
            childDialogVisible: false,
            //查询到二级菜单的信息
            childList:[],
            //控制添加二级菜单对话框的显示与隐藏
            addChildDialogVisible: false,
            //添加二级菜单表单数据
            addChildForm:{
                name: ''
            },
            //添加二级菜单规则
            addChildRules:{
                name:[
                    {required: true, message: '请输入二级分类名', trigger:'blur'},{min: 2, max: 10,message:'二级分类名称应该在2~10个字符之间'}
                ]
            },
            //控制修改二级分类
            editchildDialogVisible: false,
            //查询到的二级分类的信息
            editeChildForm:{
                name:''

            }
            

        }
    },
    created(){
        this.getMessageList()
    },
    methods: {
        async getMessageList(){
          const{data: res} = await this.$http.get('http://39.106.188.22:8800/api/classify1/findClassify1',{params: this.queryInfo})
          console.log(res)
          this.manageList = res 
        //   this.manageList = res.classify2List
        },
        // 编辑修改一级分类的对话框
        async showManageDialog(id){
            //console.log(id)
          const {data:res} = await this.$http.get('http://39.106.188.22:8800/api/classify1/findById?id=' + id)
          this.editForm = res
          console.log(res)
            this.editDialogVisible = true
        console.log(res.classify2List)
        },
        //监听修改一级分类对话框的监听事件  功能未实现
        editDialogClosed(){
           // this.$ref.editFormRef.resetFields()
        },
        //修改用户信息并提交
        async editManageInfo(){
           const {data:res} = await this.$http.put('http://39.106.188.22:8800/api/classify1/update' + {id: this.editForm.id},{name: this.editForm.name})
           //关闭对话框
           this.editDialogVisible = false
           //刷新列表
           this.getMessageList(id)
           //提示信息

        },
        //获取二级分类对话框
        async showChildDialog(id){
            const{data:res} = await this.$http.get("http://39.106.188.22:8800/api/classify1/findChildById?id=" +id)
            console.log(res)
            this.childList = res
            this.childDialogVisible = true
        },
        addChild(id){
            this.addChildDialogVisible = true
            alert(id)
        },
        //根据ID删除对应的一级分类
        async removeManageById(id){
             const confirmResult = await this.$confirm('此操作将永久删除该一级分类, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
        }).catch(err => err)
            //console.log(confirmResult)
                if(confirmResult !== 'confirm'){
            return this.$message.info('取消删除操作!')
            } console.log('确认删除一级分类操作！')
            //应该在判断结束后进行网络请求
        },
        //根据id删除对应的二级分类
        async removeChildById(id){
           const childConfirmResult = await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).catch(eer => eer)
        console.log(childConfirmResult)
        if(childConfirmResult !== "confirm"){
            this.$message.info('取消删除二级分类操作！')
        }console.log('确认删除二级分类操作！')
        },
        //修改二级分类
        editChild(){
            this.editchildDialogVisible = true
        }
        
    }
}
</script>
<style lang="less" scoped>

</style>
