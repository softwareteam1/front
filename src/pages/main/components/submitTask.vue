<template>
  <div>
    <el-row>
      <!-- <el-button style="float:right;position:relative;" @click="checkBefore" type="small">查看提交过的版本</el-button> -->
      <el-col :lg="12" :offset="4" style="text-align:center">
        <span style="font-size:3rem;color:red;" v-if="isTimeOut"></span>
        <h2 v-else>提交任务</h2>
        <el-form :model="taskInfo" :ref="taskInfo" label-width="100px">
          <el-form-item label="任务名" prop="name">
            <Input
              v-model="taskInfo.name"
              readonly
              placeholder="Enter something..."
              style="width: 300px"
            ></Input>
          </el-form-item>
          <el-form-item label="任务描述" prop="description">
            <Input
              v-model="taskInfo.description"
              readonly
              type="textarea"
              placeholder="Enter something..."
              style="width: 300px"
            ></Input>
          </el-form-item>
          <el-form-item label="截止时间" prop="ddl">
            <el-date-picker
              v-model="taskInfo.ddl"
              format="yyyy-MM-dd HH:mm:ss"
              value-format="yyyy-MM-dd HH:mm:ss"
              type="datetime"
              :disabled="true"
              placeholder="选择截止时间"
            ></el-date-picker>
          </el-form-item>
          <!-- <el-form-item label="任务类型" prop="type">
            <el-radio-group v-model="taskInfo.type">
              <el-radio :label="0" :disabled="true">文件类</el-radio>
              <el-radio :label="1" :disabled="true">回复类</el-radio>
            </el-radio-group>
          </el-form-item>-->
          <el-form-item label="回复内容" prop="content">
            <Input v-model="userTask.content" placeholder="Enter something..." style="width: 300px"></Input>
          </el-form-item>
          <el-form-item>
            <Button type="primary" @click="submit">提 交</Button>
            <Button>取消</Button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { submitTask } from "../../../api/api";
export default {
  data() {
    return {
      taskInfo: {
        id: "",
        name: "",
        ddl: "",
        description: "",
        type: "",
        isOpen: true
      },
      userTask: {
        user: { id: "" },
        task: { id: "" },
        content: "",
        timeOut: false
      }
    };
  },
  computed: {
    isTimeOut() {
      return new Date() > new Date(this.taskInfo.ddl) ? true : false;
    }
  },
  created() {
    if (this.$route.query.taskInfo == null) {
      this.$message({ type: "error", message: "参数错误,请重试" });
      setTimeout(() => {
        this.$router.push("/listTasks");
      }, 1000);
    } else {
      this.taskInfo = JSON.parse(this.$route.query.taskInfo);
    }
    this.$forceUpdate();
  },
  methods: {
    submit() {
      if (this.userTask.content == "") {
        this.$message({ type: "error", message: "请输入回复内容" });
        return;
      }
      this.userTask.user.id = this.$store.state.userInfo.id;
      this.userTask.task.id = this.taskInfo.id;
      console.log(this.userTask);
      submitTask(this.userTask).then(() => {
        this.$message({ type: "success", message: "回复成功" });
        setTimeout(() => {
          this.$router.push("/listTasks");
        }, 1500);
      });
    }
    // checkBefore() {
    //   checkIsSubmitedTasks(this.$store.state.userInfo.id).then(res => {
    //     console.log(res);
    //   });
    // }
  }
};
</script>
<style>
</style>
