<template>
  <div>
    <el-row>
      <el-carousel :interval="5000" arrow="always">
        <el-carousel-item >
          <img src="@/assets/carousel/1.png" class="carousel-img">
        </el-carousel-item>
        <el-carousel-item >
          <img src="@/assets/carousel/2.png" class="carousel-img">
        </el-carousel-item>
        <el-carousel-item >
          <img src="@/assets/carousel/3.png" class="carousel-img">
        </el-carousel-item>
        <el-carousel-item >
          <img src="@/assets/carousel/4.png" class="carousel-img">
        </el-carousel-item>
      </el-carousel>
    </el-row>
    <el-row class="app-item-contain">
      <h3 class="index-title-h3">任务中心</h3>
      <div>
        <el-collapse  v-loading="taskLoading"  accordion v-if="taskList.length!==0">
          <el-collapse-item :title="taskItem.title" :name="taskItem.id" :key="taskItem.id" v-for="taskItem in taskList">
            <table class="index-task-table">
              <tr v-for="paperItem in taskItem.paperItems" :key="paperItem.examPaperId">
                <td class="index-task-table-paper">
                  {{paperItem.examPaperName}}
                </td>
                <td width="70px">
                  <el-tag :type="statusTagFormatter(paperItem.status)" v-if="paperItem.status !== null" size="mini">
                    {{ statusTextFormatter(paperItem.status) }}
                  </el-tag>
                </td>
                <td width="80px">
                  <router-link target="_blank" :to="{path:'/do',query:{id:paperItem.examPaperId}}" v-if="paperItem.status === null">
                    <el-button  type="text" size="small">开始答题</el-button>
                  </router-link>
                  <router-link target="_blank" :to="{path:'/edit',query:{id:paperItem.examPaperAnswerId}}" v-else-if="paperItem.status === 1">
                    <el-button  type="text" size="small">批改试卷</el-button>
                  </router-link>
                  <router-link target="_blank" :to="{path:'/read',query:{id:paperItem.examPaperAnswerId}}" v-else-if="paperItem.status === 2">
                    <el-button  type="text" size="small">查看试卷</el-button>
                  </router-link>
                </td>
              </tr>
            </table>
          </el-collapse-item>
        </el-collapse>
        <div class="empty" v-else></div>
      </div>
    </el-row>
    <el-row class="app-item-contain">
      <h3 class="index-title-h3">固定试卷</h3>
      <div>
        <el-col :span="4" v-for="(item, index) in fixedPaper" :key="index" :offset="index > 0 ? 1 : 0">
          <el-card :body-style="{ padding: '0px' }" v-loading="loading">
            <img src="@/assets/exam-paper/show1.png" class="image">
            <div>
              <span>{{item.name}}</span>
              <div class="bottom clearfix">
                <router-link target="_blank" :to="{path:'/do',query:{id:item.id}}">
                  <el-button type="text" class="button">开始做题</el-button>
                </router-link>
              </div>
            </div>
          </el-card>
        </el-col>
      </div>
    </el-row>
    <el-row class="app-item-contain">
      <h3 class="index-title-h3">时段试卷</h3>
      <div>
        <el-col :span="4" v-for="(item, index) in timeLimitPaper" :key="index" :offset="index > 0 ? 1 : 0">
          <el-card :body-style="{ padding: '0px' }" v-loading="loading">
            <img src="@/assets/exam-paper/show2.png" class="image">
            <div style="padding: 14px;">
              <span>{{item.name}}</span>
              <p class="index-limit-paper-time">
                <span>{{item.startTime}}</span>
                <br/>
                <span>{{item.endTime}}</span>
              </p>
              <div class="bottom clearfix">
                <router-link target="_blank" :to="{path:'/do',query:{id:item.id}}">
                  <el-button type="text" class="button">开始做题</el-button>
                </router-link>
              </div>
            </div>
          </el-card>
        </el-col>
      </div>
    </el-row>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'
import indexApi from '@/api/dashboard'
export default {
  data () {
    return {
      fixedPaper: [],
      timeLimitPaper: [],
      pushPaper: [],
      loading: false,
      taskLoading: false,
      taskList: []
    }
  },
  created () {
    let _this = this
    this.loading = true
    indexApi.index().then(re => {
      _this.fixedPaper = re.response.fixedPaper
      _this.timeLimitPaper = re.response.timeLimitPaper
      _this.pushPaper = re.response.pushPaper
      _this.loading = false
    })

    this.taskLoading = true
    indexApi.task().then(re => {
      _this.taskList = re.response
      _this.taskLoading = false
    })
  },
  methods: {
    statusTagFormatter (status) {
      return this.enumFormat(this.statusTag, status)
    },
    statusTextFormatter (status) {
      return this.enumFormat(this.statusEnum, status)
    }
  },
  computed: {
    ...mapGetters('enumItem', [
      'enumFormat'
    ]),
    ...mapState('enumItem', {
      statusEnum: state => state.exam.examPaperAnswer.statusEnum,
      statusTag: state => state.exam.examPaperAnswer.statusTag
    })
  }
}
</script>

<style lang="scss" scoped>
  .el-carousel{
    margin: 30px 0 !important;
    box-shadow: 0 10px 20px rgba(0,0,0,.2);
    border-radius: 10px;
    height: 400px;
  }
  .carousel-img,.el-carousel__item,::v-deep.el-carousel>div{
    height: 400px !important;
  }

  .el-carousel_arrow{
    border-radius: 0 !important;
  }

  .app-item-contain{
    margin-bottom: 30px;
  }
  .index-title-h3{
    color: #5a5e66;
    font-size: 24px;
    padding-bottom: 15px;
    margin-bottom: 15px;
    position: relative;
    width: fit-content;
  }

  .index-title-h3:before{
    content: '';
    width: 100%;
    height: 10px;
    border-radius: 5px;
    bottom: 0;
    position: absolute;
    background: #5cb76a;
  }

  .el-card{
    border-radius: 10px !important;
    padding: 10px !important;
    width: fit-content !important;
  }
</style>
