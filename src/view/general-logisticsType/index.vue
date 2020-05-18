<template>
  <div>
    <div style="text-align: left; padding-bottom: 10px">
      <Button icon="ios-add-circle-outline" type="primary" @click="addRow">新增</Button>
    </div>
    <i-table :columns="columns" :data="data"></i-table>
    <Modal v-model="modal.model" title="新增" @on-cancel="onCancel" @on-ok="onOk">
      <Form ref="form" :model="form" :label-width="120" style="padding: 0px 20px">
        <FormItem :label="item.title" :prop="item.key" v-for="(item, index) in columns.slice(0, -1)" :key="'columns' + index">
            <Input v-model="form[item.key]" placeholder="请输入"/>
        </FormItem>
      </Form>
    </Modal>
  </div>
</template>

<script>
export default {
  name: 'general-logisticsType',
  data () {
    const _self = this
    return {
      columns: [
        { title: 'createdBy', key: 'createdBy', width: 100, ellipsis: true },
        { title: 'createdTime', key: 'createdTime', width: 180, ellipsis: true, sortable: true },
        { title: 'enableFlag', key: 'enableFlag', width: 100, ellipsis: true },
        { title: 'goodsTypeId', key: 'goodsTypeId', width: 120, ellipsis: true },
        { title: 'goodsTypeName', key: 'goodsTypeName', width: 150, ellipsis: true },
        { title: 'revision', key: 'revision', width: 100, ellipsis: true },
        { title: 'updatedBy', key: 'updatedBy', width: 100, ellipsis: true },
        { title: 'updatedTime', key: 'updatedTime', width: 180, ellipsis: true, sortable: true },
        {
          title: '操作',
          key: 'action',
          fixed: 'right',
          width: 90,
          render (h, { row, column, index }) {
            return h('div', [
              h('Icon', {
                props: { type: 'ios-trash', size: '20', color: '#2d8cf0' },
                style: { cursor: 'pointer', paddingRight: '10px' },
                on: { click: _self.deleteRow(row) }
              }),
              h('Icon', {
                props: { type: 'ios-eye', size: '20', color: '#2d8cf0' },
                style: { cursor: 'pointer' },
                on: { click: _self.viewDetail(row) }
              })
            ])
          }
        }
      ],
      data: [],
      modal: { model: false },
      form: {}
    }
  },
  mounted () {
    this.getTable()
  },
  methods: {
    getTable () {
      this.$http.request({
        url: '/goodsType/selectAll'
      }).then(res => { this.data = res.data || [] })
    },
    addRow () { this.modal.model = true },
    deleteRow (row) {
      return () => {
        this.$http.request({
          url: `/goodsType/delete/${row.goodsTypeId}`
        }).then(res => this.getTable())
      }
    },
    viewDetail (row) {
      return () => {
        this.$http.request({
          method: 'get',
          url: `/goodsType/get/${row.goodsTypeId}`
        }).then(res => {
          this.form = res.data
          this.modal.model = true
        })
      }
    },
    onCancel () {
      this.$refs.form.resetFields()
    },
    onOk () {
      this.$http.request({
        url: '/goodsType/save',
        data: this.form
      }).then(res => this.getTable())
    }
  }
}
</script>

<style>

</style>
