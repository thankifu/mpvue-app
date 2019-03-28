<template>
    <div class="container">
        <div class="login">
            <img src="/static/images/guide_bg.jpg"/>
            <div class="learn">
                <button open-type="getUserInfo" @getuserinfo="getUserInfo">开始</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    methods: {
        getUserInfo(e) {
            if(e.mp.detail.userInfo) {
                //console.log(e.mp.detail.userInfo)
                //存储到vuex
                this.$store.dispatch("setIsAuthenticated",true);
                this.$store.dispatch("setUser",e.mp.detail.userInfo);
                //获取code
                this.getCode();
            }

        },
        getCode() {
            //在mpvue中，提供了一个全局小程序对象 wx
            wx.login({
                success: res => {
                    console.log(res);
                    this.getOpenid(res.code);
                }
            });
        },
        getOpenid(code) {
            //三个参数 appid secret code
            const appid = "wx016d20632dcc9693";
            const secret = "e5d16cc77a7a5005ff0915353df4d270";
            this.$https.request({
                url: this.$interfaces.getOpenid + appid + "/" + secret + "/" + code,
                method: "get"
            })
            .then(res => {
                //console.log(res);
                //将openid存储到vuex中
                this.$store.dispatch("setOpenId", res.openid);

                //请求课程数据
                this.isLearned(res.openid);
            })
            .catch(err => {
                console.log(err);
            })
        },
        isLearned(openid) {
            this.$https.request({
                url: this.$interfaces.getMyLesson + openid,
                method: "get"
            })
            .then(res =>{
                //console.log("已经测试过了");
                wx.switchTab({
                    url: "../learn/main"
                });
            })
            .catch(res => {
                //console.log("还没有测试");
                wx.redirectTo({
                    url: "../question/main"
                });
            })
        }
    }
}
</script>
<style>
page{
    width: 100%;
    height:100%;
}
</style>
<style scoped>
.container {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.login {
  width: 100%;
  height: 100%;
  position: relative;
}
.login img {
  display: inline-block;
  width: 100%;
  height: 100%;
}
.login .learn {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  top: 0;
  left: 0;
}
.learn button {
  position: absolute;
  top: 90%;
  left: 10%;
  width: 80%;
  background-color: #009eef;
  color: white;
}
</style>