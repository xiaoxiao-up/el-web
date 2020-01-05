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
          <el-form-item label="用户名称">
            <el-input v-model="form.userId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="充值金额" prop="rechargePrice">
            <el-input v-model="form.rechargePrice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="实际到账" prop="actualPrice">
            <el-input v-model="form.actualPrice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="以前金额" prop="preRecharge">
            <el-input v-model="form.preRecharge" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="充值方式" prop="type">
            <el-radio-group v-model="form.type" style="width: 250px">
              <el-radio label="微信">微信</el-radio>
              <el-radio label="支付宝">支付宝</el-radio>
              <el-radio label="转账">转账</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="备注">
            <el-input v-model="form.bak" style="width: 370px;" />
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
        <el-table-column v-if="columns.visible('userId')" prop="userId" label="用户名称" />
        <el-table-column v-if="columns.visible('rechargePrice')" prop="rechargePrice" label="充值金额" />
        <el-table-column v-if="columns.visible('actualPrice')" prop="actualPrice" label="实际到账" />
        <el-table-column v-if="columns.visible('preRecharge')" prop="preRecharge" label="充值前金额" />
        <el-table-column v-if="columns.visible('type')" prop="type" label="充值方式" />
        <el-table-column v-if="columns.visible('bak')" prop="bak" label="备注" />
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
        <el-table-column v-permission="['admin','recharge:edit','recharge:del']" label="操作" width="150px" align="center">
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
import crudRecharge from '@/api/recharge'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

// crud交由presenter持有
const defaultCrud = CRUD({ title: '充值', url: 'api/recharge', sort: 'id,desc', crudMethod: { ...crudRecharge }})
const defaultForm = { id: null, orderId: null, userId: null, rechargePrice: null, actualPrice: null, preRecharge: null, type: null, bak: null, stat: null, createTime: null, updateTime: null }
export default {
  name: 'Recharge',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(defaultCrud), header(), form(defaultForm), crud()],
  data() {
    return {
      permission: {
        add: ['admin', 'recharge:add'],
        edit: ['admin', 'recharge:edit'],
        del: ['admin', 'recharge:del']
      },
      rules: {
        orderId: [
          { required: true, message: '单号不能为空', trigger: 'blur' }
        ],
        rechargePrice: [
          { required: true, message: '充值金额不能为空', trigger: 'blur' }
        ],
        actualPrice: [
          { required: true, message: '实际到账不能为空', trigger: 'blur' }
        ],
        preRecharge: [
          { required: true, message: '充值前金额不能为空', trigger: 'blur' }
        ],
        type: [
          { required: true, message: '充值方式不能为空', trigger: 'blur' }
        ],
        stat: [
          { required: true, message: '状态不能为空', trigger: 'blur' }
        ]
      }}
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
