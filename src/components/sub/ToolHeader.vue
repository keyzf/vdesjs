<template>
  <el-row style="line-height: 30px">
    <el-col :span="5"> &nbsp;</el-col>
    <el-col :span="14">
      <el-link @click="deleteAll"
        >清空画布<i class="el-icon-delete"></i>
      </el-link>
      <el-link @click="generateJson"
        >生成JSON<i class="el-icon-document"></i>
      </el-link>
      <el-link @click="generateCode"
        >生成代码<i class="el-icon-document"></i>
      </el-link>
      <el-link @click="preview">预览<i class="el-icon-view"></i> </el-link>
    </el-col>
    <el-col :span="5"> &nbsp;</el-col>

    <el-dialog title="" :visible.sync="jsonDialogVisible">
      <codemirror :value="curJsonCode" :options="cmOptions" class="code">
      </codemirror>
      <span slot="footer" class="dialog-footer">
        <el-button @click="jsonDialogVisible = false">取 消</el-button>

        <el-button type="primary" @click="copyJson" class="copyJson"
          >复制代码</el-button
        >
      </span>
    </el-dialog>

    <el-dialog title="" :visible.sync="codeDialogVisible">
      <el-tabs v-model="activeName">
        <el-tab-pane label="vue代码" name="vueCode">
          <codemirror :value="curVueCode" :options="cmOptions" class="code">
          </codemirror>
        </el-tab-pane>
        <el-tab-pane label="html代码" name="htmlCode"
          >功能正在开发中，敬请期待</el-tab-pane
        >
      </el-tabs>
      <span slot="footer" class="dialog-footer">
        <el-button @click="codeDialogVisible = false">取 消</el-button>
        <el-tooltip
          class="item"
          effect="dark"
          content="复制代码后请放入vscode中进行代码格式化"
          placement="top-start"
        >
          <el-button type="primary" @click="copyCode" class="copyCode"
            >复制代码</el-button
          >
        </el-tooltip>
      </span>
    </el-dialog>
  </el-row>
</template>
<script>
import Clipboard from "clipboard";
import { codemirror } from "vue-codemirror";
import "codemirror/theme/ambiance-mobile.css"; // 这里引入的是主题样式，根据设置的theme的主题引入，一定要引入！！
require("codemirror/mode/javascript/javascript"); // 这里引入的模式的js，根据设置的mode引入，一定要引入！！
import handlebars from "@/handlebars";
export default {
  data() {
    return {
      deviceMode: "h5",
      activeName: "vueCode",
      curVueCode: "",
      cmOptions: {
        value: "",
        mode: "text/javascript",
        theme: "ambiance-mobile",
        lineNumbers: true,
      },
      jsonDialogVisible: false,
      codeDialogVisible: false,
    };
  },
  computed: {
    curJsonCode() {
      return JSON.stringify(this.$store.state.list, null, "\t");
    },
  },

  components: {
    codemirror,
  },
  methods: {
    deleteAll() {
      this.$confirm("确定清除画布?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.$store.commit("deleteAll");

          //重置globalId
          this.$store.commit("globalIdSet", 0);
        })
        .catch(() => {
          console.log("取消");
        });
    },
    generateJson() {
      this.jsonDialogVisible = true;
    },
    generateCode() {
      this.codeDialogVisible = true;
      this.curVueCode = handlebars.generateVueCode(this.$store.state.list);
    },
    copyJson() {
      console.log("复制代码");
      this.jsonDialogVisible = false;
      let that = this;
      let clipboard = new Clipboard(".copyJson", {
        text: function () {
          return that.curJsonCode;
        },
      });
      clipboard.on("success", (e) => {
        that.$message({
          showClose: true,
          message: "复制成功",
          duration: 1000,
        });
        clipboard.destroy();
      });
      clipboard.on("error", (e) => {
        that.$message({
          showClose: true,
          message: "复制失败",
          duration: 1000,
        });
        clipboard.destroy();
      });
    },
    copyCode() {
      if (this.activeName == "vueCode") {
        // 复制vue代码
        this.codeDialogVisible = false;
        let that = this;
        let clipboard = new Clipboard(".copyCode", {
          text: function () {
            return that.curVueCode;
          },
        });
        clipboard.on("success", (e) => {
          that.$message({
            showClose: true,
            message: "复制成功",
            duration: 1000,
          });
          clipboard.destroy();
        });
        clipboard.on("error", (e) => {
          that.$message({
            showClose: true,
            message: "复制失败",
            duration: 1000,
          });
          clipboard.destroy();
        });
      } else {
        // 复制html代码
      }
    },

    preview() {
      this.$notify({
        title: "提示",
        message: "功能正在开发中，敬请期待",
        duration: 2000,
      });
    },
  },
};
</script>
<style scoped>
.CodeMirror {
  text-align: left !important;
}
.CodeMirror-line {
  height: 25px;
}
.el-link {
  margin-left: 10px;
  margin-right: 10px;
}
</style>