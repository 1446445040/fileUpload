<template>
    <div>
      <van-sticky>
        <form action="/"><van-search
          v-model="searchText"
          placeholder="请输入学校关键词"
          show-action
          autofocus
          shape="round"
          @search="onSearch"
          @input="onSearch"
          @cancel="$emit('update:show', false)"/>
        </form>
      </van-sticky>
<!--      大学列表-->
      <van-list
        @click.native="chooseSchool"
        @load="onLoad"
        v-model="loading"
        :finished="finished"
        :immediate-check="false"
        :finished-text="finishedText">
        <van-cell v-for="(item, index) in result" :key="index"
                  :title="item" />
      </van-list>
    </div>
</template>

<script>
import _ from 'lodash'
import { getUniversity } from '../../api/api'
export default {
  name: 'schoolPicker',
  data () {
    return {
      finished: false,
      finishedText: '没有更多了',
      loading: false,
      schools: {}, // 大学信息
      uniName: [], // 大学名称集合，用来搜索
      searchText: '',
      listChunk: [],
      result: [] // 显示结果
    }
  },
  methods: {
    onLoad () {
      // 异步更新数据
      setTimeout(() => {
        this.result = this.result.concat(this.listChunk.shift())
        // 加载状态结束
        this.loading = false
        // 数据全部加载完成
        if (this.listChunk.length === 0) {
          this.finished = true
        }
      }, 500)
    },
    // 模糊搜索，并返回结果数组，lodash节流
    onSearch: _.throttle(function (value) {
      if (!value) {
        this.result = []
        return
      }
      let result = _.filter(this.uniName, (item) => {
        if (item.indexOf(value) !== -1) {
          return true
        }
      })
      this.listChunk = _.chunk(result, 20)
      this.result = this.listChunk.shift() // 初始化首屏数据
      this.finished = false // 表示没有加载完
    }, 200),
    // 选择学校
    chooseSchool (event) {
      let name = event.target.innerText
      if (!name || name === this.finishedText) {
        return
      }
      let university = {
        id: this.schools[name],
        name: name
      }
      this.$emit('update:school', university)
      this.$emit('update:show', false)
    },
    // 获取大学信息
    getUniInfo () {
      let storage = window.sessionStorage
      let schools = JSON.parse(storage.getItem('schools'))
      if (schools) {
        this.schools = schools
        this.uniName = Object.keys(schools)
      } else {
        // 本地没有学校信息就请求远程
        getUniversity().then(result => {
          this.schools = result
          this.uniName = Object.keys(result)
          storage.setItem('schools', JSON.stringify(result))
        }).catch(e => {
          console.error('大学信息获取失败')
        })
      }
    }
  },
  mounted () {
    this.getUniInfo()
  }
}
</script>

<style scoped lang="stylus">

</style>
