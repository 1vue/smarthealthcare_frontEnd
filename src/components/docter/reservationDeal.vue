<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item
        :to="{ path: '/docterBaseData' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>预约信息查询</el-breadcrumb-item>

    </el-breadcrumb>
    <el-card class="box-card">
      <div
        slot="header"
        class="clearfix"
      >

        <span>预约信息查询</span>

      </div>

      <div class="find">

        <div class="search">
          <el-form :inline="true">
            <el-form-item label="患者姓名">
              <el-input
                v-model="searchForm.patientName"
                placeholder="患者姓名"
              ></el-input>
            </el-form-item>
            <el-form-item label="预约状态">
              <el-select
                v-model="searchForm.status"
                placeholder="请选择"
              >
                <el-option
                  label="全部"
                  value=""
                ></el-option>
                <el-option
                  label="未处理"
                  value="0"
                ></el-option>
                <el-option
                  label="已接受"
                  value="1"
                ></el-option>
                <el-option
                  label="已拒绝"
                  value="2"
                ></el-option>
              </el-select>
            </el-form-item>

            <el-tooltip
              class="item"
              effect="dark"
              content="点击查询预约信息"
              placement="top-start"
            >
              <el-button
                @click="search"
                type="primary"
              >查询</el-button>

            </el-tooltip>

          </el-form>
        </div>

        <div class="data">
          <el-table
            :data="reservationData"
            stripe
            style="width: 100%"
            border
          >

            <el-table-column
              prop="patientName"
              label="患者姓名"
            >
            </el-table-column>

            <el-table-column
              prop="startTime"
              label="预约开始时间"
            >

            </el-table-column>
            <el-table-column
              prop="endTime"
              label="预约结束时间"
            >

            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">

                <el-button
                  round
                  size="small"
                  type="primary"
                  @click="acceptReservation(scope.row.id)"
                >接受</el-button>

                <el-button
                  round
                  size="small"
                  type="danger"
                  @click="refuseReservation(scope.row.id)"
                >拒绝</el-button>

              </template>

            </el-table-column>

          </el-table>
        </div>

        <div class="page">
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            background
            layout=" prev, pager, next"
            :page-sizes="[5,6,7]"
            :page-size.sync="pageSize"
            :current-page.sync="currentPage"
            :total="total"
          >
          </el-pagination>

        </div>

      </div>

    </el-card>

  </div>
</template>

<script>
export default {
  data () {
    return {

      count: 0,
      total: 100,

      currentPage: 1,
      pageSize: 5,


      searchForm: {
        patientName: '',
        startTime: '',
        endTime: '',
        status: ''

      },

      reservationData: []


    }
  },



  methods: {

    search () {



      let formdata = new FormData()

      formdata.append('status', this.searchForm.status)
      formdata.append('patientName', this.searchForm.patientName)

      this.$http({
        url: '/docter/getReservation',
        method: "post",
        params: {
          pageSize: this.pageSize,
          currentPage: this.currentPage
        },
        headers: {
          token: window.sessionStorage.getItem('token'),
          "Content-Type": "multipart/form-data",
        },
        data: formdata,

      }).then((res) => {
        console.log(res);
        this.reservationData = res.data.data.table;
        this.total = res.data.data.total;
        for (let index = 0; index < this.reservationData.length; index++) {
          this.reservationData[index].startTime = this.reservationData[index].startTime.replace('T', ' ')
          this.reservationData[index].endTime = this.reservationData[index].endTime.replace('T', ' ')
        }


        if (res.data.code === 1) {
          if (this.count == 0) {
            this.$message.success('查询成功,共查询到' + this.total + '条数据')
            this.count++;
          }
        }

      })

    },



    acceptReservation (id) {
      this.$alert('确定接受该预约？', '提示', {
        confirmButtonText: '确定',
        callback: action => {

          if (action === 'confirm') {

            let formdata = new FormData()
            formdata.append('id', id)

            formdata.append('status', 1)

            this.$http({
              url: '/docter/dealReservation',
              method: "put",
              headers: {
                token: window.sessionStorage.getItem('token'),
                "Content-Type": "multipart/form-data",
              },
              data: formdata,

            }).then((res) => {
              console.log(res);

              if (res.data.code === 1) {

                this.$message.success('预约接收成功')
                this.search()
              }

              else
                this.$message.error('预约接受失败')
            })
          }


        },



      });
    },

    refuseReservation (id) {
      this.$alert('确定拒绝该预约？', '提示', {
        confirmButtonText: '确定',
        callback: action => {

          if (action === 'confirm') {

            let formdata = new FormData()
            formdata.append('id', id)

            formdata.append('status', 2)

            this.$http({
              url: '/docter/dealReservation',
              method: "put",
              headers: {
                token: window.sessionStorage.getItem('token'),
                "Content-Type": "multipart/form-data",
              },
              data: formdata,

            }).then((res) => {
              console.log(res);

              if (res.data.code === 1) {

                this.$message.success('预约拒绝成功')
                this.search()
              }

              else
                this.$message.error('预约拒绝失败')
            })
          }


        },



      });
    },

    handleSizeChange (size) {
      this.$message.success("一页大小改变为：" + this.pageSize);
      this.search();
    },

    handleCurrentChange (currentPage) {


      this.$message.success("页码数改变为" + this.currentPage);
      this.search();

    },

  },

  mounted () {
    this.search()
  },

}
</script>









<style scoped lang="less">
.box-card {
  margin-top: 20px;
  height: 650px;
}
.find {
  display: flex;
  flex-direction: column;
  .data {
    height: 400px;
  }
}
.search {
  display: flex;
  flex-direction: row;
}

.page {
  // margin-top: 10px;
  // text-align: center;
  .el-pagination {
    margin-left: 400px;
  }
}
</style>