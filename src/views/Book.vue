<template>
  <div>
    <el-card>
      <el-input style="width: 240px; margin-right: 10px;" v-model="data.name" placeholder="请输入名称"></el-input>
      <el-button type="primary" @click="load">查询</el-button>
      <el-button type="warning" @click="reset">重置</el-button>
    </el-card>
    <el-card>
      <el-button type="primary" @click="handleAdd">新增</el-button>
      <el-button type="warning" @click="handleDeleteBatch">批量删除</el-button>
      <el-button type="info">导入</el-button>
      <el-button type="success">导出</el-button>
    </el-card>
    <el-card>
      <el-table :data="data.books" stripe @selection-change="handleSelectionChange">
        <el-table-column type="selection" width="55px"></el-table-column>
        <el-table-column label="ID" prop="id"></el-table-column>
        <el-table-column label="名称" prop="name"></el-table-column>
        <el-table-column label="价格" prop="price"></el-table-column>
        <el-table-column label="类别" prop="category"></el-table-column>
        <el-table-column label="ISBN" prop="isbn"></el-table-column>
        <el-table-column label="出版时间" prop="time"></el-table-column>
        <el-table-column label="出版社" prop="press"></el-table-column>
        <el-table-column label="操作" >
          <template #default="scope" style="width: 120px">
            <el-button el-button type="primary" :icon="Edit" circle @click="handleUpdate(scope.row)"/>

            <el-button el-button type="danger" :icon="Delete" circle @click="handleDelete(scope.row)"/>
          </template>
        </el-table-column>

      </el-table>

      <div>
        <el-pagination
            @size-change="load"
            @current-change = "load"
            v-model:current-page="data.pageNum"
            v-model:page-size="data.pageSize"
            :page-sizes="[5,10,15,20]"
            background
            layout="total, sizes, prev, pager, next, jumper"
            :total="data.total" />
      </div>
      <el-dialog v-model="data.dialogFormVisible" title="图书信息" width="500px">
        <el-form :model="data.form">
          <el-form-item label="图书名称" label-width="80px">
            <el-input v-model="data.form.name" autocomplete="off" />
          </el-form-item>
          <el-form-item label="图书价格" label-width="80px">
            <el-input v-model="data.form.price" autocomplete="off" />
          </el-form-item>
          <el-form-item label="图书类别" label-width="80px">
            <el-select v-model="data.form.category" placeholder="请选择图书类别" >
              <el-option label="计算机类" value="计算机类" />
              <el-option label="文学类" value="文学类" />
              <el-option label="科技类" value="科技类" />
            </el-select>
          </el-form-item>
          <el-form-item label="出版时间" label-width="80px">
            <el-date-picker
                v-model="data.form.time"
                type="date"
                placeholder="请选择一个日期"
                style="width: 100%"
              >
            </el-date-picker>
          </el-form-item>
          <el-form-item label="ISBN" label-width="80px">
            <el-input v-model="data.form.isbn" autocomplete="off" />
          </el-form-item>
          <el-form-item label="图书作者" label-width="80px">
            <el-input v-model="data.form.author" autocomplete="off" />
          </el-form-item>
          <el-form-item label="出版社" label-width="80px">
            <el-input v-model="data.form.press" autocomplete="off" />
          </el-form-item>
        </el-form>
        <template #footer>
          <div class="dialog-footer">
            <el-button @click="data.dialogFormVisible = false">取消</el-button>
            <el-button type="primary" @click="save()">
              保存
            </el-button>
          </div>
        </template>
      </el-dialog>
    </el-card>
  </div>
</template>

<style scoped>

</style>

<script setup lang="ts">
import request from "@/utils/request";
import { reactive } from "vue";
import {ElMessage, ElMessageBox} from "element-plus";
import {Delete, Edit,} from '@element-plus/icons-vue'

// 后端统一返回结构（响应拦截器已剥离外层 AxiosResponse，此处直接对应业务体）
interface Result {
  code: string
  msg: string
  data: any
}

const data = reactive({
  name: null,
  books:[],
  pageNum: 1,
  pageSize: 5,
  total: 0,
  dialogFormVisible: false,
  form: {
    id: null,
    name: null,
    price: null,
    category: null,
    time: null,
    isbn: null,
    author: null,
    press: null
  },
  ids:[]
})

const load = () =>{
  request.get<unknown, Result>('/book/books',{
    params: {
      name: data.name,
      pageNum: data.pageNum,
      pageSize: data.pageSize
    }
  }).then(res => {
    console.log(res)
    data.books = res.data.records
    data.total = res.data.total
  })
}

load()

const reset = () => {
  data.name = null
  load()
}

const handleAdd = () => {
  data.dialogFormVisible = true
  Object.assign(data.form,
      {
        id: null,
        name: null,
        price: null,
        category: null,
        time: null,
        isbn: null,
        author: null,
        press: null })//清空数据
}

const save = () => {
  data.form.id ? update() :add()
}

const add = () => {
  request.post<unknown, Result>('/book/books',data.form).then(res => {
    if (res.code === '200') {
      console.log(res)
      data.dialogFormVisible = false;
      ElMessage.success('操作成功！')
      load()
    }else{
      ElMessage.error(res.msg)
    }
  })
}

const update = () => {
  request.put<unknown, Result>('/book/books',data.form).then(res => {
    if (res.code === '200') {
      console.log(res)
      data.dialogFormVisible = false;
      ElMessage.success('操作成功！')
      load()
    }else{
      ElMessage.error(res.msg)
    }
  })
}

const handleUpdate = (row) => {
  Object.assign(data.form, JSON.parse(JSON.stringify(row)))//深拷贝一个新的对象，不影响行对象
  data.dialogFormVisible=true;
}

const handleDelete = (row) => {
  ElMessageBox.confirm('删除数据后无法恢复，确定删除吗？','删除确认',{type: 'warning'}).then(() => {
    request.delete<unknown, Result>('/book/books/'+row.id).then(res => {
      if (res.code === '200') {
        console.log(res)
        data.dialogFormVisible = false;
        ElMessage.success('操作成功！')
        load()
      }else{
        ElMessage.error(res.msg)
      }
    })
  }).catch()
}

const handleSelectionChange = (row) => {
  data.ids = row.map( row => row.id )
  console.log(data.ids)
}

const handleDeleteBatch = () => {
  if(data.ids.length ===0){
    ElMessage.warning('请选择数据！')
    return;
  }
  ElMessageBox.confirm('删除数据后无法恢复，确定删除吗？','删除确认',{type: 'warning'}).then(() => {
    request.delete<unknown, Result>('/book/books',{data:data.ids}).then(res => {
      if (res.code === '200') {
        console.log(res)
        data.dialogFormVisible = false;
        ElMessage.success('操作成功！')
        load()
      }else{
        ElMessage.error(res.msg)
      }
    })
  }).catch()
}

</script>