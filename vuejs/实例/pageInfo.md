```vue
<template> 
   <div style="margin-left:10px;"> 
    <el-row style="text-align:center;ma"><h1>人力管理</h1></el-row> 
    <!--查询-->
    <el-row> 
     <el-form :inline="true"> 
      <el-form-item label="姓名"> 
       <el-input placeholder="姓名" v-model="name"></el-input> 
      </el-form-item> 
      <el-form-item label="部门"> 
       <el-select placeholder="部门" v-model="departmentId"> 
        <el-option :value="0" label="全部"></el-option> 
        <el-option v-for="item in dictionary" :key="item.key" :label="item.name" :value="item.key"> 
        </el-option> 
       </el-select> 
      </el-form-item> 
      <el-form-item> 
       <el-button @click="searchStaffs" type="primary">
        查询
       </el-button> 
       <el-button @click="handleAdd" type="primary">
         新增
       </el-button> 
      </el-form-item> 
     </el-form> 
    </el-row>
    <!--表格-->
     <el-table :data="tableData" stripe="" style="width: 100%"> 
      <el-table-column prop="name" label="姓名" width="100" /> 
      <el-table-column prop="sex" label="性别" :formatter="formatSex" width="100" /> 
      <el-table-column prop="jobNum" label="工号" width="100" /> 
      <el-table-column prop="entryTime" label="入职时间" :formatter="formatDate" width="120" /> 
      <el-table-column prop="entryTime" label="合同开始时间" :formatter="formatDate" width="200" /> 
      <el-table-column prop="contractEndTime" label="在职情况" :formatter="formateState" width="180" /> 
      <el-table-column label="操作" width="100"> 
       <template slot-scope="scope"> 
        <el-button @click="handleRead(scope.row)" type="text" size="small">详情</el-button> 
        <el-button @click="handleEdit(scope.row)" type="text" size="small">编辑</el-button> 
        <!-- <el-button @click="handleDelete(scope.row)" type="text" size="small">删除</el-button>  -->
       </template> 
      </el-table-column> 
     </el-table> 
     <!--弹框-->
     <info :dialog="dialog" :selectedRow="selectedRow" @addStaff="addStaff"/> 
     <!--分页-->
     <div class="block"> 
      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="page.currentPage" :page-sizes="page.pageSizes" :page-size="page.pageSize" layout="total, sizes, prev, pager, next, jumper" :total="page.total"> 
      </el-pagination> 
     </div>
      <div>{{selectedRow.BaseInfo}}</div>
      <div>{{selectedRow.ContractInfo}}</div>
      <div>{{selectedRow.LeaveInfo}}</div>
      <div>{{selectedRow.WorkInfo}}</div>
   </div> 
  </template> 


<script>
import Info from './../components/Info'

  export default {
    components:{
        Info
    },
    data() {
      return {
        tableData: [],
        dialog:{
            show:false,
            title:'',
            option:''//1:查阅 2:编辑 3:添加，在dialog中依据来确定提交数据到请求服务器url
        },
        selectedRow:{BaseInfo:1,ContractInfo:2, LeaveInfo:3, WorkInfo:4},
        departmentId:0,
        name:null,
        dictionary:[],
        page:{
            currentPage:1,
            pageSizes:[1, 2, 3, 4,5,6],
            pageSize:1,
            total:10
        },
      }
    },
    created(){
        this.searchStaffs()
        this.$http.get(this.$BASE_URL+"getDictionary").then(
            response=>{
                this.$log("response.data",response.data)
                if(response.data.success){
                    this.dictionary = response.data.data.list
                }
            }
        ).catch(error=>this.$log("error",error))
    },
    methods:{
        handleRead(row){
            this.dialog = {
                show:true,
                title:"详情",
                option:1
            }
            this.selectedRow = row
        },
        handleEdit(row){
            this.dialog = {
                show:true,
                title:"编辑",
                option:2,
            }
            this.selectedRow = row
        },
        handleAdd(){
            this.dialog = {
                show:true,
                title:"新增",
                option:3
            }
            this.selectedRow = {}
        },
        handleDelete(row){
             this.$http.post(this.$BASE_URL+"deleteBook",{id:row.id}).then(
                 response=>{
                  this.$log("response.data",response.data)
                 }
             ).catch(error=>{this.$log("error",error)})
             this.tableData = this.tableData.filter(item => item.id != row.id)
        },
        formatDate:function(row, column, cellValue, index){
            return  this.$moment(cellValue).format('YYYY-MM-DD')
        },
        formatdePartmentId(row){
            //转化部门
        },
        formatSex(row){
            //转化性别
        },
        formateState(row){
            //转化在在职状态
        },
        addStaff(staff){
            this.tableData.push(staff)
        },
        searchStaffs(){
            this.$http.post(this.$BASE_URL+"searchStaffs",{
                staffParams:{
                    departmentId:this.departmentId,
                    name:this.name
                },
                pageParams:{
                    pageNum:this.page.currentPage,
                    pageSize:this.page.pageSize,
                }
            }).then(response=>{
               if(response.data.success){
                    this.tableData = response.data.data.list
                    this.page.total = response.data.data.total
               }
            }).catch(error=>{this.$log("error",error)})
        },
        handleSizeChange(val) {
           console.log(`每页 ${val} 条`)
           this.page.pageSize = val
           if(this.page.total/val + (this.page.tatal/val? 1 : 0) >= this.page.currentPage){
               this.handleCurrentChange(this.page.currentPage)
           }
        },
       handleCurrentChange(val) {
           console.log(`当前页: ${val}`)
           this.page.currentPage = val
           this.searchStaffs()
        }
    },
    watch:{
        dictionary(){
            localStorage.setItem("dictionary",JSON.stringify(this.dictionary))
        }
    }
  }
</script>

<style scoped>
.center{
    display:flex;
    justify-content:center;
}
</style>

```

