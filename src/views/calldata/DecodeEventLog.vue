<template>
  <div>
    <div class="mainRow">事件Logs</div>
    <el-input
      class="funInput"
      v-model="codingSelector"
      :placeholder="$t('calldata.inputFunction')"
      type="textarea"
    ></el-input>
    <div class="mainRow">事件函数</div>
    <el-input
      v-model="codingParameter"
      :placeholder="$t('calldata.inputParameterPrompt')"
      type="textarea"
    ></el-input>
    <h5 class="result">
      {{ encodingResult
      }}<img
        class="copyButton"
        v-if="canCopy"
        src="../../assets/imgs/copy.png"
        @click="copy(encodingResult)"
      />
    </h5>
    <div class="bottomButton" @click="decoding()">
      {{ $t("calldata.coding") }}
    </div>
  </div>
</template>

<script>
import { ethers } from "ethers";
import Clipboard from "clipboard";
export default {
  data() {
    return {
      // 编码函数
      codingSelector: `
{
    address: '0x467802A149906B164dBce4aAd5C682D866798Be9',
    topics: [
        '0xe03e26bc06735b33186170442e98c885221cc5572e961ea41b0a7def12cfdf97',
        '0x000000000000000000000000ff22c73a87f5fd26200165c9f02854637192a941',
        '0x000000000000000000000000f73d8f5bfb7f03b0af375b1b5cf6581c367890e8'
    ],
    data: '0x000000000000000000000000000000000000000000000000000000000000006100000000000000000000000000000000000000000000000000000000000000a00000000000000000000000000000000000000000000000000de0b6b3a7640000000000000000000000000000000000000000000000000000016345785d8a00000000000000000000000000000000000000000000000000000000000000000001000000000000000000000000000000000000000000000000000000000000001420948400e68434010cfcfc978b66cfa656629d9b000000000000000000000000',
    blockNumber: 421163,
    transactionHash: '0xdfd0ee27c5d40ac9962e3b26442600d8910dd6eb99863e2de60f435ef66184f2',
    transactionIndex: 0,
    blockHash: '0xe7ca0bedc5815f9b8cff81759d688f5d9c7b02384b8c6a05f64aa14e49d6c23d',
    logIndex: 2,
    removed: false,
    id: 'log_87f43315'
}`,
      // 编码参数
      codingParameter: `[{
    "anonymous": false,
    "inputs": [
        {
            "internalType": "address",
            "name": "sender",
            "type": "address"
        },
        {
            "internalType": "bytes",
            "name": "sig",
            "type": "bytes"
        },
    ],
    "name": "NewSender",
    "type": "event"
}]`,
      // 编码结果
      encodingResult: "",
      // 可以复制
      canCopy: false,
    };
  },

  methods: {
    //解码
    async coding() {
      //清空上次编码的数据
      this.encodingResult = "";
      try {
        
        const iface = new ethers.utils.Interface([codingSelector]);
        codingSelector = codingSelector.slice(8).replace(/^\s*/g, "");
        this.encodingResult = iface.encodeFunctionData(
          codingSelector,
          processingParameters
        );
        //把通过编码的数据加入数据库
        this.functionSelector.submitFunctionSelector(
          codingSelector,
          this.encodingResult.substring(0, 10)
        );
        this.canCopy = true;
      } catch (error) {
        this.encodingResult = this.$t("calldata.inputError");
      }
    },

    //调用复制的方法
    copy(text) {
      const clipboard = new Clipboard(".result", {
        text: () => {
          return text;
        },
      });
      clipboard.on("success", () => {
        this.$message.success(this.$t("pubilc.copySauccessfully"));
        clipboard.destroy();
      });
      clipboard.on("error", () => {
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