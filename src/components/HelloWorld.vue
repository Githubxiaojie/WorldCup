<template>
  <div class="my-3">
    <div class="filter mb-3">
      <el-radio-group v-model="LenChecked" size="small">
        <el-radio-button v-for="item in Len" :label="item" :key="item">{{item}}</el-radio-button>
      </el-radio-group>
    </div>
    <div>
      <el-table style="width: 100%" :data="Match" stripe>
        <el-table-column v-if="LenChecked === '中文'" prop="league[0]" label="赛事"></el-table-column>
        <el-table-column v-else prop="league[2]" label="赛事"></el-table-column>
        <el-table-column label="时间">
          <template slot-scope="scope">
            {{scope.row.matchDate+' '+scope.row.matchTime}}
          </template>
        </el-table-column>
        <el-table-column v-if="LenChecked === '中文'" label="主队">
          <template slot-scope="scope">
            <el-tag type="warning" v-if="scope.row.homeYellow != 0">{{scope.row.homeYellow}}</el-tag>
            <el-tag type="danger" v-if="scope.row.homeRed != 0">{{scope.row.homeRed}}</el-tag>
            {{scope.row.home[0]}}
          </template>
        </el-table-column>
        <el-table-column v-else label="主队">
          <template slot-scope="scope">
            <el-tag type="warning" v-if="scope.row.homeYellow != 0">{{scope.row.homeYellow}}</el-tag>
            <el-tag type="danger" v-if="scope.row.homeRed != 0">{{scope.row.homeRed}}</el-tag>
            {{scope.row.home[2]}}
          </template>
        </el-table-column>
        <el-table-column label="全场比分">
          <template slot-scope="scope">
            {{scope.row.homeScore + ' - ' + scope.row.guestScore}}
          </template>
        </el-table-column>
        <el-table-column v-if="LenChecked === '中文'" label="客队">
          <template slot-scope="scope">
            {{scope.row.guest[0]}}
            <el-tag type="danger" v-if="scope.row.guestRed != 0">{{scope.row.guestRed}}</el-tag>
            <el-tag type="warning" v-if="scope.row.guestYellow != 0">{{scope.row.guestYellow}}</el-tag>
          </template>
        </el-table-column>
        <el-table-column v-else label="客队">
          <template slot-scope="scope">
            {{scope.row.guest[2]}}
            <el-tag type="danger" v-if="scope.row.guestRed != 0">{{scope.row.guestRed}}</el-tag>
            <el-tag type="warning" v-if="scope.row.guestYellow != 0">{{scope.row.guestYellow}}</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="半场比分">
          <template slot-scope="scope">
            {{scope.row.homeHalfScore + ' - ' + scope.row.guestHalfScore}}
          </template>
        </el-table-column>
        <el-table-column label="本地模拟" width="100px">
          <template slot-scope="scope">
          <el-dropdown  @command="handleCommand" trigger="click" size="mini">
            <span class="el-dropdown-link">模拟<i class="el-icon-arrow-down el-icon--right"></i></span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item :command={ID:scope.row.matchId,type:type[0]}>主队进球 +1</el-dropdown-item>
              <el-dropdown-item :command={ID:scope.row.matchId,type:type[1]}>主队红牌 +1</el-dropdown-item>
              <el-dropdown-item :command={ID:scope.row.matchId,type:type[2]}>主队黄牌 +1</el-dropdown-item>
              <el-dropdown-item :command={ID:scope.row.matchId,type:type[3]}>客队进球 +1</el-dropdown-item>
              <el-dropdown-item :command={ID:scope.row.matchId,type:type[4]}>客队红牌 +1</el-dropdown-item>
              <el-dropdown-item :command={ID:scope.row.matchId,type:type[5]}>客队黄牌 +1</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
          </template>
        </el-table-column>
    </el-table>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      checkList: ['显示黄牌', '显示红牌'],
      LenChecked: '中文',
      Len: ['中文', 'English'],
      Match: [],
      type: ['homeScore', 'homeRed', 'homeYellow', 'guestScore', 'guestRed', 'guestYellow']
    }
  },
  mounted () {
    this.fetch()
  },
  methods: {
    async fetch () {
      const { data: res } = await this.axios({
        url: '/worldcup_2018.json',
        method: 'get',
        type: 'json'
      })
      this.Match = res.results
      console.log(this.Match)
    },
    handleCommand (command) {
      var Index = this.Match.findIndex(x => x.matchId === command.ID)
      if (Index !== -1) {
        var currentMatch = this.Match[Index]
        switch (command.type) {
          case 'homeScore':
            currentMatch.homeScore++
            break
          case 'homeRed':
            currentMatch.homeRed++
            break
          case 'homeYellow':
            currentMatch.homeYellow++
            break
          case 'guestScore':
            currentMatch.guestScore++
            break
          case 'guestRed':
            currentMatch.guestRed++
            break
          case 'guestYellow':
            currentMatch.guestYellow++
            break
        }
      }
    }
  }
}
</script>

<style scoped>
  .el-checkbox-group,
  .el-radio-group{
    float: left
  }
  .el-checkbox-group{
    margin-top: 5px;
    padding-right: 5px;
  }
  .el-tag.el-tag--warning{
    background-color: yellow;
    color: gray;
  }
  .el-tag.el-tag--danger{
    background-color: red;
    color: white;
  }
  .el-dropdown-link {
    cursor: pointer;
    color: #409EFF;
  }
  .el-icon-arrow-down {
    font-size: 12px;
    color: #409EFF;
  }
</style>
