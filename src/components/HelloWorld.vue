<template>
  <el-container class="container">
    <el-container>
      <el-main>
        <el-header>{{ msg }}</el-header>
        <el-table
          v-loading="loading"
          :data="characters"
          style="width: 100%"
          :height="tableH"
          :default-sort = "{prop: 'id', order: 'descending'}"
        >
          <el-table-column
            prop="id"
            label="id"
            sortable
            width="60">
          </el-table-column>
          <el-table-column
            prop="name"
            label="name"
            width="150">
          </el-table-column>
          <el-table-column
            prop="hp"
            label="hp"
            sortable
            width="60">
          </el-table-column>
          <el-table-column
            prop="mp"
            label="mp"
            sortable
            width="60">
          </el-table-column>
          <el-table-column
            prop="str"
            label="str"
            sortable
            width="60">
          </el-table-column>
          <el-table-column
            prop="inteli"
            label="inteli"
            sortable
            width="60">
          </el-table-column>
          <el-table-column
            prop="san"
            label="san"
            sortable
            width="60">
          </el-table-column>
          <el-table-column
            prop="luck"
            label="luck"
            sortable
            width="60">
          </el-table-column>
          <el-table-column
            prop="charm"
            label="charm"
            sortable
            width="60">
          </el-table-column>
          <el-table-column
            prop="buff"
            label="buff"
            width="120">
          </el-table-column>
        </el-table>
      </el-main>
      <el-footer>没有数据了</el-footer>
    </el-container>
    <el-container class="character-logger">
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>新增角色日志</span>
        </div>
        <div v-for="logger in loggers" class="text item">
          {{ logger }}
        </div>
      </el-card>
    </el-container>
  </el-container>
</template>

<script>
  import GXClientFactory from 'gxclient'

export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: '玩家角色列表',
      characters: [],
      gxClient: null,
      loading: true,
      intervalId: 0,
      count: 0,
      tableH: 0,
      loggers: []
    }
  },
  created () {
    let height = window.innerHeight;
    this.tableH = height * 0.8;
    const private_key = "5K9veg5tG7fhsPepfvg8y8eRwMTLPwMTsJURDMUa52ZpZPBCCKc";
    const account_id = "morse1113";

    this.gxClient = GXClientFactory.instance({
      network: "https://node1.gxb.io"
    });
    this.intervalId = setInterval(() => {
      this.getTableObjects();
    }, 5000)
  },
  beforeDestroy () {
    clearInterval(this.intervalId)
  },
  methods: {
    getTableObjects: function () {
      this.gxClient.getTableObjects("genesis", 'characount', 0, 1).then(result => {
        let count = result[0].count;
        if (this.count !== count) {
          this.gxClient.getTableObjects("genesis", "character", this.count, count - this.count).then(characters => {
            if (this.loading) {
              this.characters = characters;
              this.loading = false;
            } else {
              let time = new Date().toLocaleString();     //获取当前时间
              for (let c in characters) {
                this.characters.push(characters[c]);
                let logger = time + "【" + characters[c].name + "】 " + "上链成功";
                this.loggers.push(logger);
              }
            }
            this.count = count;
          })
        }
      });
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .el-container {
    margin: 20px auto;
  }

  .el-header, .el-footer {
    color: #333;
    text-align: center;
    line-height: 60px;
  }

  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 160px;
    margin: 0 auto;
  }

  .text {
    font-size: 14px;
  }

  .item {
    margin-bottom: 18px;
  }

  .box-card {
    width: 100%;
  }

  .character-logger {
    width: 30px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
  }

</style>
