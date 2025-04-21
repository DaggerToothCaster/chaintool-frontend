<template>
  <div>
    <div class="mainRow">事件Data</div>
    <el-input
      class="funInput"
      v-model="inputData"
      :placeholder="$t('calldata.inputData')"
      type="textarea"
    ></el-input>
    <div class="mainRow">事件Topics</div>
    <el-input
      class="funInput"
      v-model="inputTopic"
      :placeholder="$t('calldata.inputTopic')"
      type="textarea"
    ></el-input>

    <div class="mainRow">事件函数</div>
    <el-input
      v-model="codingParameter"
      :placeholder="$t('calldata.inputParameterPrompt')"
      type="textarea"
      autosize
    ></el-input>

    <h5 class="result">
      {{ encodingResult }}

      <!-- 暂时隐藏复制按钮 -->
      <!-- <img
        class="copyButton"
        v-if="canCopy"
        src="../../assets/imgs/copy.png"
        @click="copy(encodingResult)"
      /> -->
    </h5>

    <div class="bottomButton" @click="decoding()">
      {{ $t("calldata.coding") }}
    </div>
    <!-- 解析结果 -->
    <div class="mainRow">{{ $t("calldata.decodingResult") }}</div>
    <el-table :data="decodingResult">
      <el-table-column prop="name" :label="'Name'" width="80">
      </el-table-column>
      <el-table-column prop="type" :label="'Type'" width="80">
      </el-table-column>
      <el-table-column prop="indexed" :label="'Indexed'" width="80">
      </el-table-column>
      <el-table-column prop="val" :label="'Value'" width="450">
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { ethers } from "ethers";
import Clipboard from "clipboard";
export default {
  data() {
    return {
      inputData:
        "0x00000000000000000000000042d99c326bbc8aed650baeb20e638c6dfcb0e0860000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000006423b872dd000000000000000000000000f73d8f5bfb7f03b0af375b1b5cf6581c367890e8000000000000000000000000a9617b1aa0316b5b29056cc4dd645e6e9120c1000000000000000000000000000000000000000000000000000de0b6b3a764000000000000000000000000000000000000000000000000000000000000",
      inputTopic: [
        "0x327f46e39a6c37954baf42da546bba2aae907719613a083657640cdc53654d46",
        "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
        "0x0000000000000000000000008ba1f109551bd432803012645ac136ddd64dba72",
        "0x000000000000000000000000ab7c8803962c0f2f5bbbe3fa8bf41cd82aa1923c",
      ],

      // 事件签名
      codingParameter: `event NewSender(address sender,bytes sig);`,

      // 编码结果
      encodingResult: "",
      // 可以复制
      canCopy: false,
      decodingResult: [],
    };
  },

  methods: {
    //增加表格样式
    increaseTableStyle({ row, rowIndex }) {
      if (String(JSON.parse(JSON.stringify(row.id))).indexOf("[var") == "-1") {
        return "success-row";
      }
      return "";
    },
    //解码
    async decoding() {
      //清空上次编码的数据
      this.encodingResult = "";
      try {
        console.log("开始解析");
        const iface = new ethers.utils.Interface([this.codingParameter]);
        const decode = iface.parseLog({
          data: JSON.parse(JSON.stringify(this.inputData)),
          topics: JSON.parse(JSON.stringify(this.inputTopic)),
        });
        console.log("解析结果", decode);
        //格式化输出结果

        let dataArr = [];
        for (let i = 0; i < decode.args.length; i++) {
          let ele = decode.eventFragment.inputs[i];

          dataArr.push({
            name: ele.name,
            type: ele.type,
            indexed: ele.indexed + "",
            val: decode.args[i],
          });
        }
        this.encodingResult = dataArr;
        this.decodingResult = dataArr;
        this.canCopy = true;
        return;
      } catch (error) {
        console.log("error", error);
        this.encodingResult = this.$t("calldata.inputError");
      }
    },

    //调用复制的方法
    copy(text) {
      console.log("开始复制");
      const clipboard = new Clipboard(".result", {
        text: () => {
          return text;
        },
      });
      clipboard.on("success", () => {
        console.log("复制成功");
        this.$message.success(this.$t("pubilc.copySauccessfully"));
        clipboard.destroy();
      });
      clipboard.on("error", () => {
        console.log("复制失败");
        this.$message.error(this.$t("pubilc.copyFailed"));
        clipboard.destroy();
      });
    },
  },
};
</script>

<style scoped>
.mainRow {
  margin: 11px 0;
}
/deep/ .el-textarea {
  margin-bottom: 20px;
}
.bottomButton {
  margin: 15px 40%;
}
.result {
  width: 100% !important;
  word-break: break-all;
}
</style>