<template>
  <div>
    <Form ref="searchForm" :model="searchForm" :label-width="60" style="padding: 0px 20px">
      <Row :gutter="10">
        <Col span="6" v-for="(item, index) in searchFormList" :key="'columns' + index">
          <FormItem :label="item.title" :prop="item.key" style="margin-bottom: 10px">
              <Input v-model.trim="searchForm[item.key]" placeholder="请输入"/>
          </FormItem>
        </Col>
        <Col span="6">
          <Button icon="ios-add-circle-outline" type="primary" @click="getTable">查询</Button>
        </Col>
      </Row>
    </Form>
    <div style="text-align: right; padding-bottom: 10px">
      <Button icon="ios-add-circle-outline" type="primary" @click="addRow">新增</Button>
    </div>
    <i-table :columns="columns.filter(i => i.table)" :data="data"></i-table>
    <Page show-elevator style="margin-top: 10px"
      :total="page.total" :current="searchForm.currPage" :page-size="searchForm.pageSize"
      @on-change="pageChange"
    />
    <Modal v-model="modal.model" :footer-hide="modal.footerHide" title="新增" @on-cancel="onCancel" @on-ok="onOk">
      <Form ref="form" :model="form" :label-width="120" style="padding: 0px 20px">
        <FormItem :label="item.title" :prop="item.key" v-for="(item, index) in columns.filter(i => i.detail)" :key="'columns' + index">
            <Input v-model="form[item.key]" :readonly="modal.readonly" placeholder="请输入"/>
        </FormItem>
      </Form>
    </Modal>
  </div>
</template>

<script>
export default {
  name: 'general-interface',
  data () {
    const _self = this
    return {
      columns: [
        { title: 'createdBy', key: 'createdBy', width: 100, ellipsis: true, table: false, detail: false },
        { title: 'createdTime', key: 'createdTime', width: 180, ellipsis: true, sortable: true, table: false, detail: false },
        { title: 'enableFlag', key: 'enableFlag', width: 100, ellipsis: true, table: false, detail: false },
        { title: '商品类别ID', key: 'goodsTypeId', width: 120, ellipsis: true, table: true, detail: true },
        { title: '商品类别名称', key: 'goodsTypeName', width: 150, ellipsis: true, table: true, detail: true },
        { title: 'revision', key: 'revision', width: 100, ellipsis: true, table: false, detail: false },
        { title: 'updatedBy', key: 'updatedBy', width: 100, ellipsis: true, table: false, detail: false },
        { title: 'updatedTime', key: 'updatedTime', width: 180, ellipsis: true, sortable: true, table: false, detail: false },
        {
          title: '操作',
          key: 'action',
          fixed: 'right',
          width: 120,
          table: true,
          render (h, { row, column, index }) {
            return h('div', [
              h('Icon', {
                props: { type: 'ios-eye', size: '20', color: '#ff9900' },
                style: { cursor: 'pointer', paddingRight: '10px' },
                on: { click: _self.viewDetail(row, 'get') }
              }),
              h('Icon', {
                props: { type: 'md-create', size: '20', color: '#2d8cf0' },
                style: { cursor: 'pointer', paddingRight: '10px' },
                on: { click: _self.viewDetail(row, 'update') }
              }),
              h('Icon', {
                props: { type: 'ios-trash', size: '20', color: '#2d8cf0' },
                style: { cursor: 'pointer' },
                on: { click: _self.deleteRow(row) }
              })
            ])
          }
        }
      ],
      data: [],
      modal: { type: 'save', model: false, footerHide: false, readonly: false },
      form: {},
      searchFormList: [
        // { title: 'name', key: 'name' }
      ],
      searchForm: {
        // name: '', pageSize: 10, currPage: 1
      },
      page: { total: 0 }
    }
  },
  mounted () {
    this.getTable()
  },
  methods: {
    getTable () {
      this.$http.request({
        url: '/goodsType/selectAll',
        data: this.searchForm
      }).then(res => {
        this.data = res.data || []
        this.page.total = this.data.length
      })
    },
    addRow () { [this.modal.model, this.modal.type] = [true, 'save'] },
    deleteRow (row) {
      return () => {
        this.$http.request({
          url: `/goodsType/delete/${row.goodsTypeId}`
        }).then(res => this.getTable())
      }
    },
    viewDetail (row, type) {
      return () => {
        this.$http.request({
          method: 'get',
          url: `/goodsType/get/${row.goodsTypeId}`
        }).then(res => {
          this.form = res.data
          this.modal.model = true;
          [this.modal.footerHide, this.modal.readonly, this.modal.type] = [type === 'get', type === 'get', type]
        })
      }
    },
    onCancel () {
      [this.modal.footerHide, this.modal.readonly, this.modal.type] = [false, false, 'save']
      this.$refs.form.resetFields()
    },
    onOk () {
      this.$http.request({
        url: `/goodsType/${this.modal.type}`,
        data: this.form
      }).then(res => this.getTable())
    },
    pageChange (page) {
      this.searchForm.currPage = page
      this.getTable()
    }
  }
}
</script>

<style>

</style>
