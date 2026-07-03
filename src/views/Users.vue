<script setup>
import { ref, onMounted, computed } from 'vue'
import { ElMessage } from 'element-plus'
const users = ref([])
const loading = ref(false)
const keyword = ref('')
const addDialogVisible = ref(false) // 新增表单的显示状态
const editDialogVisible = ref(false) // 编辑表单的显示状态
const editForm = ref({}) // 编辑表单的数据
const addForm = ref({}) // 新增表单的数据
const addFormRef = ref(null) // 新增表单的引用
const editFormRef = ref(null) // 编辑表单的引用

const rules = {
  name: [
    { required: true, message: '请输入姓名', trigger: 'blur' }
  ],
  email: [
    { required: true, message: '请输入邮箱', trigger: 'blur' },
    { type: 'email', message: '请输入正确的邮箱格式', trigger: ['blur', 'change'] }
  ],
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' }
  ]
}

function handleEdit(row) {
  editForm.value = { ...row } // 复制一份数据到编辑表单
  editDialogVisible.value = true // 显示编辑表单
}

function saveEdit() {
  editFormRef.value.validate((valid) => {
    if (valid) {
    const index = users.value.findIndex(user => user.id === editForm.value.id)
    if (index !== -1) {
    users.value[index] = { ...editForm.value } // 更新用户数据
    ElMessage.success('编辑成功')
  }
    editDialogVisible.value = false // 隐藏编辑表单
  }
  })
}

function handleAdd() {
  // 把addfrom的值设置为一个空对象
  addForm.value = { name: '', email: '', username: '' }
  addDialogVisible.value = true // 显示新增表单
}

function saveAdd() {
  addFormRef.value.validate((valid) => {
    if (valid) {
      // 验证通过，才执行原来的保存逻辑
      users.value.push({ ...addForm.value, id: Date.now() })
      addDialogVisible.value = false
      ElMessage.success('新增成功')
    }
  })
}

async function fetchUsers() {
  loading.value = true
  try{
  const response = await fetch('https://jsonplaceholder.typicode.com/users')
  const data = await response.json()
  users.value = data
  } catch (e) {
    console.error('加载失败', e)
  } finally {
    loading.value = false
  }
}

const currentPage = ref(1) // 当前页码
const pageSize = ref(5) // 每页显示的条数

// 计算当前页的数据
const pageUsers = computed(() => {
  const start = (currentPage.value - 1) * pageSize.value
  const end = start + pageSize.value
  return filterUsers.value.slice(start, end)
})

const filterUsers = computed(() => {
  return users.value.filter(user =>
    user.name.toLowerCase().includes(keyword.value.toLowerCase())
  ) 
})

function handleDelete(row) {
  users.value = users.value.filter(user => user.id !== row.id)
  ElMessage.success('删除成功')
}

onMounted(() => {
  fetchUsers()
})
</script>

<template>
  <div>
    <h2>用户管理</h2>
    <el-input
      v-model="keyword"
      placeholder="按姓名搜索"
      style="width: 300px; margin-top: 16px;"
    />
    <el-button type="primary" @click="handleAdd">新增用户</el-button>
    <el-table :data="pageUsers" v-loading="loading" >
      <el-table-column prop="id" label="ID" width="80" />
      <el-table-column prop="name" label="姓名" width="180" />
      <el-table-column prop="email" label="邮箱" width="250" />
      <el-table-column prop="username" label="用户名" width="150" />
      <el-table-column label="操作" width="180">
        <template #default="scope">
          <el-button size="small" @click="handleEdit(scope.row)">编辑</el-button>
          <el-button size="small" type="danger" @click="handleDelete(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      v-model:current-page="currentPage"
      :page-size="pageSize"
      :total="filterUsers.length"
      layout="prev, pager, next"
    />
    <el-dialog v-model="editDialogVisible" title="编辑用户" width="400px">
      <el-form :model="editForm" :rules="rules" ref="editFormRef">
        <el-form-item label="姓名" prop="name">
          <el-input v-model="editForm.name" />
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="editForm.email" />
        </el-form-item>
        <el-form-item label="用户名" prop="username">
          <el-input v-model="editForm.username" />
        </el-form-item>
      </el-form>

      <template #footer>
        <el-button @click="editDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="saveEdit">确定</el-button>
      </template>
      </el-dialog>

      <el-dialog v-model="addDialogVisible" title="新增用户" width="400px">
      <el-form :model="addForm" :rules="rules" ref="addFormRef">
        <el-form-item label="姓名" prop="name">
          <el-input v-model="addForm.name" />
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addForm.email" />
        </el-form-item>
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addForm.username" />
        </el-form-item>
      </el-form>

      <template #footer>
        <el-button @click="addDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="saveAdd">确定</el-button>
      </template>
      </el-dialog>
  </div>
</template>