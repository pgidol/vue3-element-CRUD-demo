<template>
  <div class="common-layout">
    <el-container>
      <el-header>
        <h1>快捷粘贴文本</h1>
      </el-header>
      <el-container>

        <el-aside width="60px">Aside</el-aside>
        <el-container>
          <el-main>
            <div class="query-box">
              <el-input v-model="queryInput" placeholder="Please input" @input="handleSearch">
              </el-input>
              <el-button class="del-btn" v-show="delVisivle" type="danger" @click="delList">全部删除</el-button>
              <el-button type="primary" @click="dialogTitle = 'add';dialogFormVisible = true">增加</el-button>


              <el-dialog @closed="dialogCancel" v-model="dialogFormVisible" :title="dialogTitle === 'add' ? '新增信息填写' : '修改信息'">
                <el-form :model="memberTable">
                  <el-form-item label="姓名" :label-width="formLabelWidth">
                    <el-input v-model="memberTable.name" autocomplete="off" />
                  </el-form-item>
                  <el-form-item label="电话" :label-width="formLabelWidth">
                    <el-input v-model="memberTable.phone" autocomplete="off" />
                  </el-form-item>
                  <el-form-item label="邮箱" :label-width="formLabelWidth">
                    <el-input v-model="memberTable.email" autocomplete="off" />
                  </el-form-item>
                  <el-form-item label="状态" :label-width="formLabelWidth">
                    <el-input v-model="memberTable.state" autocomplete="off" />
                  </el-form-item>
                  <el-form-item label="地址" :label-width="formLabelWidth">
                    <el-input v-model="memberTable.address" autocomplete="off" />
                  </el-form-item>

                </el-form>
                <template #footer>
                  <span class="dialog-footer">
                    <el-button @click="dialogFormVisible = false;dialogCancel()">取消</el-button>
                    <el-button type="primary" @click="dialogFormVisible = false; dialogConfirm();">确认</el-button>
                  </span>
                </template>
              </el-dialog>
            </div>

            <div class="table-box">
              <el-table ref="multipleTableRef" :data="tableData" style="width: 100%" border
                @selection-change="handleSelectionChange" max-height="500">
                <el-table-column type="selection" width="55" />
                <el-table-column fixed prop="id" label="ID" width="150" />
                <el-table-column prop="name" label="Name" width="120" />
                <el-table-column prop="phone" label="Phone" width="120" />
                <el-table-column prop="email" label="Email" width="120" />
                <el-table-column prop="state" label="State" width="120" />
                <el-table-column prop="address" label="Address" width="400" />

                <el-table-column fixed="right" label="Operations" width="120">
                  <template #default="scope">
                    <el-button link type="danger" size="large" @click.prevent="deleteRow(scope.$index)">删除</el-button>
                    <el-button link type="primary" size="large" @click="handleEdit(scope.row)">编辑</el-button>
                  </template>
                </el-table-column>
              </el-table>
            </div>

          </el-main>
          <el-footer>Footer</el-footer>
        </el-container>
      </el-container>
    </el-container>
  </div>
</template>
<script setup>

import { reactive, ref } from 'vue'
let queryInput = ref("")
let tableData = ref([
  {
    id: '1',
    name: '王二狗',
    phone: '0411-3026544',
    email: '43567782678@qq.com',
    state: 'online',
    address: 'No. 189, Grove St, Los Angeles',
  },
  {
    id: '2',
    name: '李小狗',
    phone: '0381-1235544',
    email: '24@hotmail.com',
    state: 'online',
    address: '	55178 胡 巷',
  },
  {
    id: '3',
    name: '王左狗',
    phone: '2050-46418007',
    email: '4123577@qq.com',
    state: 'online',
    address: '19347 曹 中心',
  },
  {
    id: '4',
    name: '赵右狗',
    phone: '153-37427087',
    email: '94234234@qq.com',
    state: 'online',
    address: '天翊, 昊天 and 明哲',
  },
  {
    id: '5',
    name: '吴没狗',
    phone: '1297-44845603',
    email: '62411678@qq.com',
    state: 'online',
    address: '	鸿煊, 子轩 and 峻熙',
  },
  {
    id: '6',
    name: '方四狗',
    phone: '33076674764',
    email: '33076674764@qq.com',
    state: 'online',
    address: 'Nos Angeles',
  },
  {
    id: '7',
    name: '华狗',
    phone: '04128554560885',
    email: '28554560885@qq.com',
    state: 'online',
    address: '950 思聪 巷',
  },
  {
    id: '8',
    name: '华狗',
    phone: '04128554560885',
    email: '28554560885@qq.com',
    state: 'online',
    address: '950 思聪 巷',
  },
  {
    id: '9',
    name: '华狗',
    phone: '04128554560885',
    email: '28554560885@qq.com',
    state: 'online',
    address: '	950 思聪 巷',
  },
  {
    id: '10',
    name: '华狗',
    phone: '04128554560885',
    email: '28554560885@qq.com',
    state: 'online',
    address: '	950 思聪 巷',
  },
  {
    id: '11',
    name: '华狗',
    phone: '04128554560885',
    email: '28554560885@qq.com',
    state: 'online',
    address: '	950 思聪 巷',
  },
  {
    id: '12',
    name: '华狗',
    phone: '04128554560885',
    email: '28554560885@qq.com',
    state: 'online',
    address: '	950 思聪 巷',
  },
  {
    id: '13',
    name: '华狗',
    phone: '04128554560885',
    email: '28554560885@qq.com',
    state: 'online',
    address: '	950 思聪 巷',
  },
])
let tableDataCopy = Object.assign(tableData.value)
let dialogTitle = ref('')
let delVisivle = ref(false)
let multipleSelection = ref([])
/*   方法  */
const deleteRow = (index) => {
  tableData.value.splice(index, 1)
}

const handleSelectionChange = (val) => {

  val.length > 0 ? delVisivle.value = true : delVisivle.value = false
  multipleSelection.value = []
  val.forEach(item => {
    multipleSelection.value.push(item.id)
  });
  console.log(multipleSelection.value)
}
const dialogCancel = () => {
  memberTable.value = {};
  console.log(memberTable.value)
}

const delList = () => {
  multipleSelection.value.forEach(id => {
    let index = tableData.value.findIndex(item => item.id === id)
    tableData.value.splice(index, 1)
  })
}

const handleEdit = (row) => {
  console.log(row)

  //1.修改弹窗标题
  dialogTitle = 'edit'
  //2.打开弹窗
  dialogFormVisible.value = true
  //3.数据上载
  memberTable.value = row
  //4.保存修改
}

const handleSearch = (val) => {
console.log(val)
tableData.value = tableDataCopy
console.log(tableDataCopy)
tableData.value = tableData.value.filter(item => item.name.toLowerCase().match(val))

}



const dialogConfirm = () => {
  if (dialogTitle === 'add') {
    tableData.value.push({ id: (tableData.value.length + 1).toString(), ...memberTable.value })
    console.log(tableData.value)
    memberTable.value = {}
  }else{
    console.log(memberTable.value.id)
    let index = tableData.value.findIndex(item => item.id === memberTable.value.id)
    console.log(index)
    tableData.value[index] = memberTable.value
    memberTable.value = {}
    
  }

}

const dialogFormVisible = ref(false)
const formLabelWidth = '140px'
let memberTable = ref({
  name: '', phone: '',
  email: '', state: '', address: '',
})






</script>
<style>
.el-header {
  color: #606266;
  text-align: center;
}

.el-aside {
  text-align: center;
}

.el-input {
  width: 200px;
}

.query-box {
  width: 800px;
  display: flex;
  justify-content: space-between;
}

.table-box {
  width: 800px;
  height: 700px;

}

.el-main {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.del-btn {
  margin-right: -450px;

}
</style>