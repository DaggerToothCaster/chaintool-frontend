<template>
  <div class="bulkQuery" v-loading="isloading">
    <Navigation></Navigation>
    <div class="scroll">
      <div class="container">
        <div class="title">Zoom 数据</div>
        <p>统计时间大概需要2分钟</p>
        <!-- <div class="usingHelp">
          <span
            ><a
              href="https://github.com/ChainToolDao/chaintool-frontend/wiki/%E6%89%B9%E9%87%8F%E6%9F%A5%E8%AF%A2%E9%92%B1%E5%8C%85%E4%BD%99%E9%A2%9D"
              target="_blank"
              >{{ $t("pubilc.usingHelp") }}
              <img src="../assets/imgs/explain.png" alt="" /></a
          ></span>
        </div> -->
        <!-- <div class="tips">查询网络</div>
				<el-select v-model="select">
					<el-option v-for="(item, index) in chainlist" :key="'chainlist' + index" :value="item.value">
					</el-option>
				</el-select> -->
        <div class="tips">Zoom 地址</div>
        <el-input
          v-model="tokenAdress"
          :placeholder="$t('bulkQuery.enterAddressPrompt')"
        ></el-input>
        <div class="tips">分析地址</div>
        <el-input
          type="textarea"
          v-model="walletAddress"
          :placeholder="$t('bulkQuery.enterWalletAddressPrompt')"
        ></el-input>
        <div class="tips" v-if="searchResult.length > 0">
          {{ $t("bulkQuery.inquireResult") }}
        </div>
        <el-table
          v-if="searchResult.length > 0"
          :data="searchResult"
          border
          slot="empty"
          :empty-text="$t('pubilc.noData')"
          id="outExcel"
          class="table"
        >
          <el-table-column
            prop="walletAddress"
            :label="$t('bulkQuery.list[0]')"
            width="363"
          >
          </el-table-column>
          <el-table-column
            prop="token"
            :label="$t('bulkQuery.list[1]')"
            width="90"
          >
          </el-table-column>
          <el-table-column
            prop="balance"
            :label="$t('bulkQuery.list[2]')"
            width="250"
          >
          </el-table-column>
          <el-table-column prop="schedule" label="进度" width="250">
          </el-table-column>
        </el-table>
        <div class="collapse">
          <el-collapse
            accordion
            v-for="item in searchResult"
            :key="item.walletAddress"
          >
            <el-collapse-item>
              <template slot="title">
                {{ $t("bulkQuery.list[0]") }}: {{ item.walletAddress }}
              </template>
              <div>{{ $t("bulkQuery.list[1]") }}: {{ item.token }}</div>
              <div>{{ $t("bulkQuery.list[2]") }}: {{ item.balance }}</div>
            </el-collapse-item>
          </el-collapse>
        </div>
        <br />
        <div>
          <span>Total: {{ totalBal }}</span>
          &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;
          <span>Schedule: {{ totalBal / 2100 }}/1000</span>
        </div>
        <br />
        <div>
          <div class="bottomBtn" @click="checkBalance">
            {{ $t("bulkQuery.bntCheckBalance") }}
          </div>
          <span class="bottomBtn" @click="exportexcel">{{
            $t("bulkQuery.btnExportExcel")
          }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ethers } from "ethers";
import Navigation from "../components/Navigation.vue";
import FileSaver from "file-saver";
import * as XLSX from "xlsx";

export default {
  name: "bulkQuery",
  components: {
    Navigation,
  },
  metaInfo() {
    return {
      title: "Chaintool - " + this.title,

      meta: [
        {
          name: "keyword",
          content: "批量查询钱包余额，通过钱包地址查询余额",
        },
      ],
    };
  },

  data() {
    return {
      select: "https://mainnet-eth.compound.finance/",
      chainlist: [
        {
          value: "https://mainnet-eth.compound.finance/",
        },

        {
          value: "https://http-mainnet.hecochain.com",
        },

        {
          value: "https://bsc-dataseed3.binance.org",
        },

        {
          value: "https://rpc-mainnet.matic.network",
        },
      ],
      //代币地址
      tokenAdress: "0xb9802331D88c8Dcfe44A31858c13E2e69cbeEA5a",
      //钱包地址
      walletAddress: `0x566cc1eD251Ab30Ab5fa5facF3CEc95A92BaD3B3
0x8C1fa94Ad95A7a27622B982Fb82F98552B36f9cD
0x02E5efc166e8714144d6911C8218815B0Fc6b00B
0xEB15B8B253696c4F539a001665167A6573754570
0xf22213cC1618Bfb3120C1365B6d7188aD4720460
0xE31e898A84d0aA20685A6d18589EafA62E711a67
0xE026b2e99b22e61AE7F438Df498Bc5717DAaCeD3
0x1FEE057e5EE932065F9AcF2A1a8c322310c0F956
0x9e30105e09c041F8C949F20839035e05BE8C7B77
0xAb9C4D0C4a75981fCDC6785FDfC4fD1Ac130DA00
0x3CB3f999c8F62F000a8A0D41FfD9B4211f4f1828
0xfd01eA455f24974505229F8Db1419cDd6b3ad7E9
0xd9Ec36c3407A7CD5005fd07bf26F06c63C9C1d77
0x3FC328Aa225DB778f4313947Dc60Ce15162ebFFc
0xB0e7bda809282e548242F27473b38d8797732AF3
0x95160D9547ba29258F1C2A3CAfd81535Afab1B63
0xCCDCdbd006887E234c5a28CA7158d42911E96DA5
0xDBd03b75eC94a255F0DbA5B10FFb605cb23efc0E
0xF9FE953dD04872047421e327eE72FC7a73797f4e
0xebB5BFCBbc5678caDb8571F5Af62204baDB5dF86
0x827A75ecdcb3b6324dAce84B76B090FECE652904
0x7468B18eF5bDc6189Ea1538a92DF8cD5f3f830c5
0x5c03a564676906590095D2F24E37145935A52571
0x0A879dE9E9769A62CD327462EC20BEd45346f631
0xf8aCfe166EA7e805f1134027cea18211ABd0f025
0xC00746E73e325d402569E983EDA3CE5cb53B3591
0x6c3f23Bd3F5b4fd450764854f633856c70f0246b
0x409Cfd6097C6b50d760A4aadBF51eaF83A491468
0xD86B2ea226b70B730bd8ba174943b1feBc9CEc2c
0x85cB21c65a219989E2e5292AfFd6D898aaE820b2
0x98D218241dE670b3f6d4528d6f18F416D80F977b
0x66B902823aB09bfc061A2BC8EC748c97da080d6F
0x770Ce4E69b6395074AD06130B8a6Ee150deB21E2
0xD5A1bbfB067be4D1282c16aAbd24cE8c118B3872
0xEeAf6A31839913a3660638dB325894828e838e42
0x4b577f8b70E015bA8943D20f64746039f708848e
0x35DfFD9C82f0aFe5DcE9229881CC92158BC6EAaa
0xC58Fe919d536c3f3802C11309f8089581af5acE2
0xB068DcE1F7A020A2EF23a31FDD4C64419dda70C6
0x67Cc42121775eD31B9A4E66cAC265F4F6394627f
0x3a9A36A218F287999aCf4f599AcF3b6BdE6e2EfB
0x5cbD76fF7db939de86637CBE223E24C9b3Aa5cd5
0x0645A7725628008062Bf37381B7eaFb4Ea8a6Aa7
0x638Fe37116e9C3a432e23958c6B5D57d477631E7
0xdf508405bed23Fb93AeD0914D7baBF9d959b2315`,
      //查询结果
      searchResult: [],
      //是加载中
      isloading: false,
      //总资产
      totalBal: 0,
      //基础数量
      baseAmount: 2100,
      //地址目标数量 张
      goal: 1000,
    };
  },

  async created() {
    await this.initAccount();
  },

  computed: {
    title() {
      return this.$t("title.bulkQuery");
    },
  },

  methods: {
    //导出excel
    exportexcel() {
      if (this.searchResult.length == 0) {
        this.$message(this.$t("bulkQuery.exportExcelPrompt"));
        return;
      }
      /** 从表生成工作簿对象*/
      const wb = XLSX.utils.table_to_book(document.querySelector("#outExcel"), {
        raw: true,
      });
      /** 获取二进制字符串作为输出 */
      const wbout = XLSX.write(wb, {
        bookType: "xlsx",
        bookSST: true,
        type: "array",
      });
      try {
        FileSaver.saveAs(
          new Blob([wbout], { type: "application/octet-stream" }),
          //设置导出文件名称
          "钱包余额表格.xlsx"
        );
      } catch (error) {}
      return wbout;
    },

    //查询余额
    async checkBalance() {
      this.searchResult = [];
      let walletBalance = [];
      let token = [];
      let decimals = [];
      let totalBal = 0;
      let holds = [];
      let walletAddress = this.walletAddress.split("\n");
      let single = this.goal / walletAddress.length;
      let provider = new ethers.providers.InfuraProvider("mainnet");
      if (this.walletAddress == "") {
        this.$message(this.$t("bulkQuery.checkBalancePrompt[0]"));
        return;
      }
      this.isloading = true;

      try {
        var source = await provider.getCode(this.tokenAdress);
      } catch (error) {
        var source = 1;
      }

      console.log("查询信息", source);
      if (source == 1 || source.length == 2) {
        this.$message(this.$t("bulkQuery.checkBalancePrompt[1]"));
        return;
      }
      let abi = [
        "function balanceOf(address) view returns (uint256)",
        "function symbol() view returns (string)",
        "function decimals() view returns (uint8)",
      ];
      let contract = new ethers.Contract(this.tokenAdress, abi, provider);
      for (let i in walletAddress) {
        try {
          let balance = await contract.balanceOf(walletAddress[i]);
          walletBalance.push(balance._hex);
          let tokenAddress = await contract.symbol();
          token[i] = tokenAddress;
          let decimal = await contract.decimals();
          decimals[i] = decimal;
        } catch (error) {
          walletBalance.push("查询失败");
        }
      }

      //   console.log("精度列表",decimals)
      for (let i in walletAddress) {
        walletBalance[i] = parseInt(walletBalance[i], 16);
        let data = {
          walletAddress: walletAddress[i],
          balance: Math.floor(walletBalance[i] / 10 ** decimals[i]),
          token: token[i],
        };
        //持有铭文张数
        data.holds = data.balance / this.baseAmount;
        //进度
        data.schedule = `${data.holds} / ${single}`;
        this.searchResult.push(data);
        totalBal += walletBalance[i] / 10 ** decimals[i];
      }
      this.totalBal = totalBal;
      this.isloading = false;
    },

    // ----------初始化账户-------------
    async initAccount() {
      if (window.ethereum) {
        try {
          this.accounts = await window.ethereum.enable();
          this.account = this.accounts[0];
          this.provider = window.ethereum;
          this.signer = new ethers.providers.Web3Provider(
            this.provider
          ).getSigner();
          this.chainId = parseInt(
            await window.ethereum.request({ method: "eth_chainId" })
          );
        } catch (error) {
          // User denied account access
        }
      } else {
        // Need install MetaMask
      }
      // Verify Accounts!
    },
  },
};
</script>

<style scoped>
.bulkQuery {
  width: 100%;
  height: auto;
  min-height: 94%;
}

.scroll {
  width: 100%;
  height: auto;
  display: flex;
  justify-content: center;
  overflow: auto;
}

.container {
  max-width: 768px;
  height: min-content;
  padding: 32px;
  width: 100%;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 30px 20px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
}

.title {
  font-size: 18px;
  font-weight: 700;
  margin-bottom: 15px;
  position: relative;
}

.usingHelp {
  width: 100%;
  height: 21px;
  margin-bottom: 15px;
}

.usingHelp span {
  float: right;
}

.usingHelp span a {
  text-decoration: none;
  cursor: pointer;
  font-size: 15px;
  color: #909399;
  width: 90px;
  display: inline-block;
}

.usingHelp span a:hover {
  color: #409eff;
}

.usingHelp span img {
  margin-bottom: -3px;
  width: 15px;
  display: inline-block;
}

.container .tips {
  font-size: 14px;
  color: #000;
  font-weight: 700;
  align-self: flex-start;
  margin-bottom: 10px;
}

.container .el-select,
.container .el-input,
.container .el-textarea {
  width: 100%;
  margin-bottom: 30px;
}

/deep/ .container .el-select .el-input__inner,
/deep/ .container .el-input .el-input__inner,
/deep/ .container .el-textarea .el-textarea__inner {
  border: none;
  background-color: #f5f5f5;
  border-radius: 6px;
}

/deep/ .container .el-textarea .el-textarea__inner {
  height: 220px;
}

.container .list {
  display: flex;
  flex-direction: column;
  width: 100%;
  border-radius: 6px;
  border-bottom: 1px solid #e8eaec;
  margin-bottom: 30px;
}

.container .list .header {
  display: flex;
  width: 100%;
  height: 40px;
  background-color: #f5f5f5;
  justify-content: space-between;
  padding: 0 30px;
  box-sizing: border-box;
  line-height: 40px;
  color: #515a6e;
  font-weight: bold;
  font-size: 14px;
}

.container .list .header > div:last-child {
  width: 30%;
}

.container .list .none {
  display: flex;
  width: 100%;
  height: 40px;
  line-height: 40px;
  justify-content: center;
  color: #515a6e;
  font-size: 14px;
  font-weight: 300;
}

.container .bottomBtn {
  vertical-align: top;
  flex-direction: row;
  display: inline-block;
  text-align: center;
  margin-top: 10px;
  margin-left: 20px;
  width: 96px;
  height: 36px;
  line-height: 35px;
  background-color: #404040;
  cursor: pointer;
  color: #fff;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  font-weight: 700;
  border-radius: 6px;
}

.container .bottomBtn:hover {
  background-color: #575757;
}

.container .bottomBtn:active {
  background-color: #000;
}

.table {
  width: 100%;
}

.collapse {
  display: none;
}

@media (max-width: 768px) {
  .collapse {
    display: inline-block;
    width: 100%;
  }
  .table {
    display: none;
  }
  .container {
    max-width: calc(100vw - 40px);
  }
  /deep/ .el-collapse-item__header {
    line-height: 15px;
    height: auto;
    padding-top: 10px;
    padding-bottom: 10px;
    word-break: break-all;
  }
}
</style>