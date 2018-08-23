<template>
  <div class="sm-container">
    <div :ref="`sm-${id}`" class="sm-content">
      {{ data }}
    </div>
    <div v-if="isShowMore" class="sm-btn pointer" @click="showMore">
      <i v-if="iconFront" :class="['sm-btn-icon', isUnfold ? showLessBtnIcon : showMoreBtnIcon]"></i>
      <span class="sm-btn-txt">{{ isUnfold ? showLessBtnTxt : showMoreBtnTxt }}</span>
      <span v-if="showPercentage && (!isUnfold)">{{ percentage }}%</span>
      <i v-if="!iconFront" :class="['sm-btn-icon', isUnfold ? showLessBtnIcon : showMoreBtnIcon]"></i>
    </div>
  </div>
</template>

<script>

export default {
  name: 'show-more',
  props: {
    id: {
      type: String,
      default: ''
    },
    data: {
      type: String,
      default: ''
    },
    showContentHeight: {
      type: String,
      default: '80'
    },
    showPercentage: {
      type: Boolean,
      default: false
    },
    iconFront: {
      type: Boolean,
      default: false
    },
    showMoreBtnTxt: {
      type: String,
      default: '显示更多'
    },
    showMoreBtnIcon: {
      type: String,
      default: 'iconfont icon-jiantouxia'
    },
    showLessBtnTxt: {
      type: String,
      default: '收起'
    },
    showLessBtnIcon: {
      type: String,
      default: 'iconfont icon-jiantoushang'
    }
  },
  data () {
    return {
      isShowMore: false,
      isUnfold: false,
      percentage: 0
    }
  },
  mounted () {
    const smContentEle = this.$refs[`sm-${this.id}`] || document.querySelector('.sm-content')
    if (smContentEle.clientHeight > this.showContentHeight) {
      this.isShowMore = true
      this.percentage = Math.floor(this.showContentHeight / smContentEle.clientHeight * 100)
      smContentEle.style.height = this.showContentHeight + 'px'
    } else {
      this.isShowMore = false
      smContentEle.style.height = 'auto'
    }
  },
  methods: {
    showMore () {
      const smContentEle = this.$refs[`sm-${this.id}`] || document.querySelector('.sm-content')
      if (this.isUnfold) {
        this.isUnfold = false
        smContentEle.style.height = this.showContentHeight + 'px'
      } else {
        this.isUnfold = true
        smContentEle.style.height = 'auto'
      }
    }
  }
}

</script>

<style scoped>
.sm-content{
  overflow: hidden;
  white-space: pre-wrap;
}
</style>
