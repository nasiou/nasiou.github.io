<template>
  <div id="app">
    <header>
      <div>
        <img src="./assets/logo-white.png" width="167px">
      </div>
    </header>
    <main>
      <div class="main_content_wrap">
        <section class="main_header">
          <div class="main_logo">
            <img src="./assets/logo-black.png" width="167px">
          </div>
          <div>
            <div>好借好还，再借不难</div>
            <div>仅需一分钟，您就能创建一份您和他（她）之间的一个借条，本借条不可篡改，</div>
            <div>并永久保存在星云链中，基于区块链3.0</div>
            <div style="margin-top:10px">It is not difficult to borrow well and borrow again</div>
            <div>In just one minute, you can create a receipt between you and him (her), which is not tampered with.</div>
            <div>And permanently exist in the NEBULAS chain, based on the 3 chain of NEBULAS.</div>
          </div>
        </section>
        <section class="main_content">
          <div class="create">
            <div class="create_title">
              <img src="./assets/create.png">
              <span>创建借条 Create IOU</span>
            </div>
            <div class="input_wrap">
              <span>借款人</span>
              <input type="text" placeholder="请输入借款人姓名">
            </div>
            <div class="input_wrap">
              <span>借条金额</span>
              <input type="text" placeholder="单位：元">
            </div>
            <div class="text_wrap">
              <span>借条详情</span>
              <textarea placeholder="在这里填写列条详情（比如利息等）"></textarea>
            </div>
            <div class="button_wrap">
              <button>生成借条</button>
            </div>
          </div>
          <div class="find">
            <div class="create_title">
              <img src="./assets/find.png">
              <span>查询借条 Query IOU</span>
            </div>
            <div class="input_wrap">
              <span>借条ID</span>
              <input type="text" placeholder="请输入借条ID">
            </div>
            <div class="button_wrap white">
              <button>查询借条</button>
            </div>
            <div class="find_result">
              <div class="find_title">查询结果：</div>
              <div class="find_result_content">
                <div class="result_content_amt">
                  <span>借款金额</span>
                  <span class="right amt_right">￥24000.00</span>
                </div>
                <div class="result_content_id">
                  <span>借款ID</span>
                  <span class="right ">test</span>
                </div>
                <div class="result_content_des">
                  <span>借款详情</span>
                  <span class="right des_right">借款人xx巴拉巴拉巴拉借款人xx巴拉巴拉巴拉借款人xx巴拉巴拉巴拉借款人xx巴拉巴拉巴拉借款人xx巴拉巴拉巴拉借款人xx巴拉巴拉巴拉</span>
                </div>
              </div>
            </div>
          </div>
        </section>
      </div>
    </main>
  </div>
</template>

<script>


export default {
  name: 'app',
  data () {
    return {
      
    }
  }
}

function saveFun(callArgs) {
  let to = dappAddress,
    value = "0",
    callFunction = "save";

  window.serialNumber = nebPay.call(to, value, callFunction, callArgs, {
    listener: cbPush
  });

  window.intervalQuery = setInterval(function () {
    funcIntervalQuery();
  }, 1500);
}

function cbPush(resp) {
  console.log("response of push: " + JSON.stringify(resp))
}

function funcIntervalQuery() {
  nebPay.queryPayInfo(window.serialNumber)
    .then(function (resp) {
      var respObject = JSON.parse(resp)
      if (respObject.code === 0) {
        alert('添加成功');
        clearInterval(window.intervalQuery)
      }
    })
    .catch(function (err) {
      console.log(err);
    });
}

function dealResult(resp, _this) {
  let result = JSON.parse(resp.result)

  _this.findFundTotal = result.fund_total
  _this.findFundWhere = result.fund_where.split(',')
  _this.findFundAtm = result.where_amt.split(',')
  _this.findFundWhere[0] = '总金额'
  _this.findFundAtm[0] = _this.findFundTotal

  _this.nowFundId = _this.findFundId

  updateChart(_this)
  _this.hasResult = true
}

function initChart(_this) {
  window.myChart = echarts.init(document.getElementById('resultEcharts'));
}

function updateChart(_this) {
  var option = {
    title: {
      text: '善款' + _this.findFundId + '去向说明'
    },
    tooltip: {},
    legend: {
      data:['去向']
    },
    xAxis: {
      data: _this.findFundWhere
    },
    yAxis: {},
    series: [{
      name: '去向',
      type: 'bar',
      data: _this.findFundAtm
    }]
  };

  window.myChart.setOption(option);
}

</script>

<style>
* {
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
}

html, body, #app{
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  background-color: rgb(249,249,249);
  height: 100%;
}

header {
  height: 60px;
  background-color: rgb(38,102,194);
}
header div {
  width: 1200px;
  margin: 0 auto;
}

header div img {
  margin-top: 16px;
}

main {
  width: 1200px;
  margin: 0 auto;
  padding: 50px 100px;
  font-size: 14px;
  color: rgb(120,120,120);
}

.main_logo {
  margin-bottom: 15px;
}

.main_content {
  margin-top: 30px;
}

.create {
  float: left;
  width: 400px;
  height: 380px;
}
.create_title {
  height: 56px;
}
.create_title img {
  width: 56px;
  float: left;
}
.create_title span {
  float: left;
  font-size: 20px;
  line-height: 56px;
  margin-left: 5px;
}
.find {
  float: right;
  width: 400px;
  height: 400px;
}

.input_wrap {
  position: relative;
  height: 48px;
  width: 100%;
  font-size: 18px;
  margin-top: 20px;
}
.input_wrap span {
  position: absolute;
  font-weight: bold;
  left: 20px;
  top: 11px;
  color: #404040;
  user-select: none;
}
.input_wrap input {
  height: 48px;
  width: 100%;
  outline: none;
  border: none;
  line-height: 48px;
  font-size: 18px;
  padding-left: 105px;
}

.text_wrap {
  position: relative;
  width: 100%;
  font-size: 18px;
  margin-top: 20px;
}
.text_wrap span {
  position: absolute;
  font-weight: bold;
  left: 20px;
  top: 11px;
  color: #404040;
  user-select: none;
}
.text_wrap textarea {
  height: 126px;
  width: 100%;
  outline: none;
  border: none;
  font-size: 18px;
  padding-left: 20px;
  padding-top: 45px;
}

.button_wrap {
  margin-top: 20px;
}
.button_wrap button{
  font-size: 18px;
  height: 48px;
  width: 100%;
  border-radius: 5px;
  outline: none;
  border: none;
  background-color: rgb(38,102,194);
  color: #fff;
  cursor: pointer;
}
.button_wrap button:hover {
  opacity: .6;
}
.button_wrap.white button {
  border: 1px solid #ddd;
  background-color: #fff;
  color: #404040;
}

.find_result {
  margin-top: 10px;
}
.find_title {
  color: #404040;
  font-size: 18px;
  font-weight: bold;
  padding-left: 5px;
  margin-bottom: 10px;
}
.find_result_content {
  padding: 20px;
  background-color: #fff;
}

.result_content_amt {
  font-size: 18px;
}
.right {
  float: right;
}
.amt_right {
  color: #404040;
  font-weight: bold;
}

.result_content_id {
  margin-top: 40px;
}

.result_content_des {
  margin-top: 15px;
}
.result_content_des::after {
  content: '';
  display: block;
  clear: both;
}
.des_right {
  display: inline-block;
  width: 250px;
}
</style>
