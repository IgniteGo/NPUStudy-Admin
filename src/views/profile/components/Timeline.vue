<template>
  <div class="block">
    <el-timeline>
      <el-timeline-item placement="top" :timestamp="userInfo.lastActiveTime">
        <el-card>
          <h4>最后活动时间</h4>
          <p>{{ userInfo.realName+'在小瓜答题宝中最后活动了' }}</p>
        </el-card>
      </el-timeline-item>
      <el-timeline-item placement="top" :timestamp="userInfo.createTime">
        <el-card>
          <h4>加入时间</h4>
          <p>{{ userInfo.realName+'加入了小瓜答题宝' }}</p>
        </el-card>
      </el-timeline-item>
    </el-timeline>
  </div>
</template>

<script>
export default {
  props: {
    userInfo: {
      type: Object,
      default: () => {
        return {
          realName: "",
          lastActiveTime: new Date(),
          createTime: ""
        };
      }
    }
  }
};
</script>
