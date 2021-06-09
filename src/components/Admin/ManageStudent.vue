<template>
  <div>
    <div id="search_options">
      <el-divider content-position="left">新增学生信息</el-divider>
      <span>所在院系</span>
      <el-select
        v-model="depa_name"
        class="student_select"
        placeholder="请选择"
      >
        <el-option
          v-for="item in depa_names"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
        </el-option>
      </el-select>

      <span>专业</span>
      <el-input size="medium" class="student_select" v-model="major" clearable>
      </el-input>

      <span>学生学号</span>
      <el-input
        size="medium"
        class="student_select"
        v-model="stud_id"
        clearable
      >
      </el-input>

      <br />

      <span>学生姓名</span>
      <el-input
        size="medium"
        class="student_select"
        v-model="stud_name"
        clearable
      >
      </el-input>

      <span>性别</span>
      <el-select v-model="gender" class="student_select" placeholder="请选择">
        <el-option
          v-for="item in genders"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
        </el-option>
      </el-select>

      <span>总学分</span>
      <el-input
        size="medium"
        class="student_select"
        v-model="total_credit"
        clearable
      >
      </el-input>

      <el-button
        type="primary"
        icon="el-icon-upload"
        @click="addStudent"
        class="addButton"
        size="small"
        >添加学生</el-button
      >

      <br />
      <br />
      <el-divider content-position="left">所有学生</el-divider>
    </div>

    <div id="result_list">
      <el-table
        :data="StudData"
        style="width: 100%"
        row-key="id"
        border
        :row-class-name="gray"
      >
        <el-table-column prop="depa_name" label="所在院系"></el-table-column>
        <el-table-column prop="major" label="专业"></el-table-column>
        <el-table-column prop="stud_id" label="学生学号"></el-table-column>
        <el-table-column prop="stud_name" label="学生姓名"></el-table-column>
        <el-table-column prop="gender_face" label="性别"></el-table-column>
        <el-table-column prop="total_credit" label="总学分"></el-table-column>
        <el-table-column fixed="right" label="操作">
          <template #default="scope">
            <el-button
              class="selectButton"
              @click="deleteStudent(scope.row)"
              type="text"
              size="middle"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { ElMessage } from "element-plus";
export default {
  data() {
    return {
      depa_names: [
        {
          value: "软件学院",
          label: "软件学院",
        },
        {
          value: "计算机学院",
          label: "计算机学院",
        },
        {
          value: "化学学院",
          label: "化学学院",
        },
        {
          value: "物理学院",
          label: "物理学院",
        },
      ],
      depa_name: "",
      major: "",
      stud_id: "",
      stud_name: "",
      genders: [
        {
          value: 1,
          label: "男",
        },
        {
          value: 0,
          label: "女",
        },
      ],
      gender: "",
      total_credit: "",
      passwd: "123456",
      StudData: [],
    };
  },
  methods: {
    getStudentList() {
      let that = this;
      axios
        .post("http://muzi.fun:4455/class_selection/admin/getStudentList", {
          depa_name: "*",
          major: "*",
          year: 2019,
        })
        .then(function (response) {
          that.StudData = response.data;
          for (let i = 0; i < that.StudData.length; i++) {
            if (that.StudData[i].gender == 0)
              that.StudData[i].gender_face = "女";
            else that.StudData[i].gender_face = "男";
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },

    deleteStudent(row) {
      let that = this;
      this.$confirm("是否确认要删除该学生?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          that.$message({
            type: "success",
            message: "删除成功",
          });
          axios
            .post("http://muzi.fun:4455/class_selection/admin/dropStudent", {
              stud_id: row.stud_id,
            })
            .then(function () {
              that.getStudentList();
            })
            .catch(function (error) {
              console.log(error);
            });
        })
        .catch(() => {
          that.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },

    addStudent() {
      let that = this;
      axios
        .post("http://muzi.fun:4455/class_selection/admin/addStudent", {
          stud_id: this.stud_id,
          depa_name: this.depa_name,
          stud_name: this.stud_name,
          gender: this.gender,
          total_credit: this.total_credit,
          passwd: this.passwd,
          major: this.major,
        })
        .then(function () {
          that.getStudentList();
          (that.stud_id = ""),
            (that.depa_name = ""),
            (that.stud_name = ""),
            (that.gender = ""),
            (that.total_credit = ""),
            (that.major = "");
          ElMessage.success({
            message: "添加成功",
            type: "success",
          });
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },

  created() {
    this.getStudentList();
  },

  setup() {},
};
</script>

<style>
.addButton {
  float: right;
  margin-right: 80px;
  margin-top: 25px;
}

.student_select {
  width: 19%;
  margin: 25px;
}
</style>