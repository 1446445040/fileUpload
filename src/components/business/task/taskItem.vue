<template>
    <div class="van-hairline--bottom box animated fadeInUp">
      <van-row class="box-header">
        {{curTask.title}}
      </van-row>
      <van-row class="box-footer">
        <van-count-down :time="time" class="count-down"
                        format="DD 天 HH 时 mm 分 ss 秒"/>
      </van-row>
      <van-row class="box-body">
        <van-notice-bar
          class="notice-bar"
          :text="curTask.content"
        />
      </van-row>
    </div>
</template>

<script>
import moment from 'moment'
export default {
  name: 'taskItem',
  props: {
    task: Object
  },
  data () {
    return {
      id: '',
      creator: '',
      title: '',
      content: '',
      deadline: Date.now(),
      userList: []
    }
  },
  computed: {
    curTask () {
      return this.task
    },
    time () {
      // 返回剩余时间
      return moment(this.task.deadline).subtract(Date.now()).valueOf()
    }
  }
}
</script>

<style scoped lang="stylus">
.box
  display flex
  flex-direction column
  justify-content space-around
  width 90%
  height 100px
  padding 5px
  margin 8px auto
  border-radius 5px
  box-sizing border-box
  box-shadow 0 0 10px lightgrey
.box-header
  font-size 20px
  font-weight bold
.box-body
  font-size 16px
  color grey
.count-down
  color red
.notice-bar
  color grey
  background-color transparent
  height 20px
  padding 0
</style>
