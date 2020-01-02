<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="单号" prop="orderId">
            <el-input v-model="form.orderId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="用户id">
            <el-input v-model="form.userId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="手续费" prop="fee">
            <el-input v-model="form.fee" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="提现金额" prop="withdrawPrice">
            <el-input v-model="form.withdrawPrice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="开户银行" prop="bank">
            <el-input v-model="form.bank" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="银行类型" prop="bankType">
            <el-input v-model="form.bankType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="开户姓名" prop="bankName">
            <el-input v-model="form.bankName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="银行账号" prop="bankAccount">
            <el-input v-model="form.bankAccount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="状态" prop="stat">
            <el-input v-model="form.stat" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column v-if="columns.visible('orderId')" prop="orderId" label="单号" />
        <el-table-column v-if="columns.visible('userId')" prop="userId" label="用户id" />
        <el-table-column v-if="columns.visible('fee')" prop="fee" label="手续费" />
        <el-table-column v-if="columns.visible('withdrawPrice')" prop="withdrawPrice" label="提现金额" />
        <el-table-column v-if="columns.visible('bank')" prop="bank" label="开户银行" />
        <el-table-column v-if="columns.visible('bankType')" prop="bankType" label="银行类型" />
        <el-table-column v-if="columns.visible('bankName')" prop="bankName" label="开户姓名" />
        <el-table-column v-if="columns.visible('bankAccount')" prop="bankAccount" label="银行账号" />
        <el-table-column v-if="columns.visible('stat')" prop="stat" label="状态" />
        <el-table-column v-if="columns.visible('createTime')" prop="createTime" label="创建时间">
          <template slot-scope="scope">
            <span>{{ parseTime(scope.row.createTime) }}</span>
          </template>
        </el-table-column>
        <el-table-column v-if="columns.visible('updateTime')" prop="updateTime" label="更新时间">
          <template slot-scope="scope">
            <span>{{ parseTime(scope.row.updateTime) }}</span>
          </template>
        </el-table-column>
        <el-table-column v-permission="['admin','withdraw:edit','withdraw:del']" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudWithdraw from '@/api/withdraw'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

// crud交由presenter持有
const defaultCrud = CRUD({ title: 'me.zhengjie.lottery', url: 'api/withdraw', sort: 'id,desc', crudMethod: { ...crudWithdraw }})
const defaultForm = { id: null, orderId: null, userId: null, fee: null, withdrawPrice: null, bank: null, bankType: null, bankName: null, bankAccount: null, stat: null, createTime: null, updateTime: null }
export default {
  name: 'Withdraw',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(defaultCrud), header(), form(defaultForm), crud()],
  data() {
    return {
      permission: {
        add: ['admin', 'withdraw:add'],
        edit: ['admin', 'withdraw:edit'],
        del: ['admin', 'withdraw:del']
      },
      rules: {
        orderId: [
          { required: true, message: '单号不能为空', trigger: 'blur' }
        ],
        fee: [
          { required: true, message: '手续费不能为空', trigger: 'blur' }
        ],
        withdrawPrice: [
          { required: true, message: '提现金额不能为空', trigger: 'blur' }
        ],
        bank: [
          { required: true, message: '开户银行不能为空', trigger: 'blur' }
        ],
        bankType: [
          { required: true, message: '银行类型不能为空', trigger: 'blur' }
        ],
        bankName: [
          { required: true, message: '开户姓名不能为空', trigger: 'blur' }
        ],
        bankAccount: [
          { required: true, message: '银行账号不能为空', trigger: 'blur' }
        ],
        stat: [
          { required: true, message: '状态不能为空', trigger: 'blur' }
        ]
      }    }
  },
  methods: {
    // 获取数据前设置好接口地址
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
