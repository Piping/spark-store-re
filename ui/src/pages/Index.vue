<template>
  <div id="home">
    <center>
      <br><br>
      <h3 >Spark Store</h3>
      <h5 class="more-info">
        Have more.
      </h5>
      <div class="buttons">
       <!--
          愿望墙: http://www.shenmo.tech:420/?p=91
          应用问题反馈: http://www.shenmo.tech:420/?p=419
          APP建议: http://www.shenmo.tech:420/?p=422
          国际排名：http://distrowatch.com/table.php?distribution=deepin

          商店官网: https://www.spark-app.store/
          所有的问题反馈换成这统一的入口
          社区反馈中心: https://www.deepinos.org/t/spark-feedback
          gitee反馈中心: https://gitee.com/deepin-community-store/feedback
      -->
        <!--
        <button @click="Search" class="bt-feedback" disabled>搜索应用(暂未开放)</button>
        <br>
        <button @click="distrowatch" class="bt-feedback"><b>Deepin国际排名</b></button>
        -->
        <br>
        <button @click="Wishwall" class="bt-feedback">愿望墙</button>
        <br>
        <button @click="FeedBack" class="bt-feedback">社区中心</button>
        <br>
        <button @click="Contri" class="bt-feedback">参与贡献</button>
        <br>
        <button @click="Donate" class="bt-feedback">随喜捐赠</button>
        <br>
        <button @click="Monitor" class="bt-feedback">节点监控</button>
        <br>
      </div>
    </center>
  </div>

</template>


<script>
import { defineComponent } from 'vue';

export default defineComponent({
    name: 'HelloWorld',
    data() {
      return {}
    },
    methods: {
      FeedBack: function () {
        window.open("https://www.deepinos.org/t/spark-feedback", "_self", "")
      },
      Wishwall: function () {
        window.open("https://www.deepinos.org/t/Wish-wall", "_self", "")
      },
      Monitor: function () {
        window.open("https://status.spark-app.store", "_self", "")
      },
      officialwebsite: function () {
        window.open(" https://www.spark-app.store/", "_self", "")
      },
      distrowatch: function () {
        window.open("https://distrowatch.com/table.php?distribution=deepin", "_self", "")
      },
      GiteeFeedBack: function () {
        window.open("https://gitee.com/deepin-community-store/feedback", "_self", "")
      },
      Contri: function () {
        window.open("https://gitee.com/deepin-community-store", "_self", "")
      },
      Donate: function () {
        window.open("https://spark.deepinos.org.cn/", "_self", "")
      },
      Search: function () {
        this.$router.push({name: "Search"})
      }
    }
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #home {
    border: transparent;
    font-family: Bahnschrift;
  }
  .more-info {
    color: gray;
    font-weight: lighter;
  }

  .bt-feedback {
    width: 300px;
    padding: 10px;
    margin: 15px;
    /*margin-top: 40px;*/
    border: transparent;
    background: #F4F4F6;
    transition: all 0.25s;
    border-radius: 18px;
    color: #6d6d6d;
    -webkit-transition: all 0.6s cubic-bezier(0.165, 0.84, 0.44, 1);
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
    overflow: hidden;
  }

  .bt-feedback:hover {
    box-shadow: 0 5px 15px rgba(0,0,0,0.3);
    -webkit-transform: scale(1.07, 1.07);
    transform: scale(1.07, 1.07);
  }

</style>
