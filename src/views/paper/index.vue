<template>
  <div class="app-contain paper-card">
    <el-tabs type="border-card" v-model="tabId"  @tab-click="subjectChange" v-if="subjectList.length">
      <el-tab-pane :label="item.name"  :key="item.id" :name="item.id" v-for="item in subjectList" style="margin-left: 20px;" >
        <el-row  style="float: right">
          <el-radio-group v-model="queryParam.paperType" size="mini" @change="paperTypeChange" >
            <el-radio v-for="item in paperTypeEnum" size="mini" :key="item.key" :label="item.key">{{item.value}}</el-radio>
          </el-radio-group>
        </el-row>
        <el-table v-loading="listLoading" :data="tableData" fit highlight-current-row style="width: 100%">
          <el-table-column prop="id" label="序号" width="90px"/>
          <el-table-column prop="name" label="名称"  />
          <el-table-column align="right">
            <template slot-scope="{row}">
              <router-link target="_blank" :to="{path:'/do',query:{id:row.id}}">
                <el-button  type="text" size="small">开始答题</el-button>
              </router-link>
            </template>
          </el-table-column>
        </el-table>
        <pagination v-show="total>0" :total="total" :background="false" :page.sync="queryParam.pageIndex" :limit.sync="queryParam.pageSize"
                    @pagination="search" style="margin-top: 20px"/>
      </el-tab-pane>
    </el-tabs>
    <div v-else class="empty-container"></div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import Pagination from '@/components/Pagination'
import examPaperApi from '@/api/examPaper'
import subjectApi from '@/api/subject'

export default {
  components: { Pagination },
  data () {
    return {
      queryParam: {
        paperType: 1,
        subjectId: 0,
        pageIndex: 1,
        pageSize: 10
      },
      tabId: '',
      listLoading: true,
      subjectList: [],
      tableData: [],
      total: 0
    }
  },
  created () {
    this.initSubject()
  },
  methods: {
    initSubject () {
      let _this = this
      subjectApi.list().then(re => {
        _this.subjectList = re.response

        if (!_this.subjectList.length) { return }

        let subjectId = _this.subjectList[0].id
        _this.queryParam.subjectId = subjectId
        _this.tabId = subjectId.toString()
        _this.search()
      })
    },
    search () {
      this.listLoading = true
      examPaperApi.pageList(this.queryParam).then(data => {
        const re = data.response
        this.tableData = re.list
        this.total = re.total
        this.queryParam.pageIndex = re.pageNum
        this.listLoading = false
      })
    },
    paperTypeChange (val) {
      this.search()
    },
    subjectChange (tab, event) {
      this.queryParam.subjectId = Number(this.tabId)
      this.search()
    }
  },
  computed: {
    ...mapState('enumItem', {
      paperTypeEnum: state => state.exam.examPaper.paperTypeEnum
    })
  }
}
</script>
<style scoped>
  .paper-card {
    margin-top: 30px !important;
  }
  ::v-deep.el-tabs{
    border-radius: 10px !important;
    border: 1px #e3e3e3 solid  !important;
    box-shadow: 0 0 0 ;
    overflow-x: hidden;
  }

  .empty-container{
    width: 1200px;
    margin: 0 auto;
    height: 50vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: url(~@/assets/empty.png) center no-repeat;
    background-size: 20%;
    position: relative;
  }
  .empty-container:after{
    content: '暂无试题';
    color: #b6b6b6;
    font-size: 20px;
    position: absolute;
    bottom: 25px;
    left: 50%;
    transform: translate(-50%, 0);
  }
</style>
