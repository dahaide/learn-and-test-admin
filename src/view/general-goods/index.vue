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
  name: 'general-goods',
  data () {
    const _self = this
    return {
      columns: [
        { title: 'buyingPrice', key: 'buyingPrice', width: 100, ellipsis: true },
        { title: 'createdBy', key: 'createdBy', width: 100, ellipsis: true, sortable: true },
        { title: 'createdTime', key: 'createdTime', width: 180, ellipsis: true },
        { title: 'enableFlag', key: 'enableFlag', width: 100, ellipsis: true },
        { title: 'goodsId', key: 'goodsId', width: 100, ellipsis: true },
        { title: 'guidancePrice', key: 'guidancePrice', width: 100, ellipsis: true },
        { title: 'maximumDailyDelivery', key: 'maximumDailyDelivery', width: 100, ellipsis: true },
        { title: 'period', key: 'period', width: 100, ellipsis: true, sortable: true },
        { title: 'productName', key: 'productName', width: 100, ellipsis: true, sortable: true },
        { title: 'region', key: 'region', width: 100, ellipsis: true, sortable: true },
        { title: 'revision', key: 'revision', width: 100, ellipsis: true, sortable: true },
        { title: 'specification', key: 'specification', width: 100, ellipsis: true, sortable: true },
        { title: 'stock', key: 'stock', width: 100, ellipsis: true, sortable: true },
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
        url: '/goods/selectAll',
        data: { currPage: 1, pageSize: 10 }
      }).then(res => { this.data = res.data.list || [] })
    },
    addRow () { this.modal.model = true },
    deleteRow (row) {
      return () => {
        this.$http.request({
          url: `/goods/delete/${row.goodsTypeId}`
        }).then(res => this.getTable())
      }
    },
    viewDetail (row) {
      return () => {
        this.$http.request({
          method: 'get',
          url: `/goods/get/${row.goodsTypeId}`
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
        url: '/goods/save',
        data: this.form
      }).then(res => this.getTable())
    }
  }
}
</script>

<style>

</style>
