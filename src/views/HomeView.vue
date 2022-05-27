<template>
  <v-container>
    <v-overlay v-show="loadOverlay">
      <v-progress-circular indeterminate size="64"> </v-progress-circular>
    </v-overlay>
    <v-card class="mx-auto" max-width="400" elevation="0">
      <v-btn class="ma-2" rounded color="info" @click="camaraStatus">
        <v-icon left>mdi-camera</v-icon> 開啟 / 關閉相機
      </v-btn>
      <qrcode-stream
        :camera="camera"
        @decode="onCameraChange"
        v-show="camera_show"
      ></qrcode-stream>

      <h3>{{ itemid }}</h3>
      <br />
      <p><b>姓名</b>：{{ showData.provider.name }}</p>
      <!-- <br /> -->
      <p><b>物品名稱</b>：{{ showData.item.name }}</p>
      <!-- <br /> -->
      <p><b>物品訊息</b>：{{ showData.item.detail }}</p>
      <v-btn v-if="findFlag" class="ma-2" rounded color="orange" @click="gotIt">
        <v-icon left>mdi-alien</v-icon> 好喔收到了
      </v-btn>
      <p style="color: red">{{ alertText }}</p>
      <br />
      <br />
      <div v-for="data in showData.item.image" :key="data">
        <img :src="googleDriveImg(data)" style="max-width: 300px" />
      </div>
    </v-card>
  </v-container>
</template>

<script>
// import HelloWorld from "../components/HelloWorld";

export default {
  name: "HomeView",
  data: () => ({
    apiUrl:
      "https://script.google.com/macros/s/AKfycbzzqK217FfOevF_g1KTRoJrylFUJOi8xUcgylwV_nn6ZcmwEMcZDmgwyizfAGGXoTNS1g/exec",
    apiUrl2:
      "https://script.google.com/macros/s/AKfycbzTap0qx5IT_0sk0Y1IEfANbvV-__ytWENCFfH1J3qd5TwLKkp6s3QYpxqCcYQX5pAc/exec",
    showData: {
      item: {
        name: "N/A",
        detail: "N/A",
        image: [
          "https://memeprod.sgp1.digitaloceanspaces.com/user-wtf/1653637098267.jpg",
        ],
      },
      provider: {
        name: "N/A",
      },
    },
    camera: "off",
    camera_show: false,
    itemid: "N/A",
    loadOverlay: false,
    findFlag: false,
    access_code: "",
    alertText: "",
  }),
  methods: {
    // 取得資料
    getData(itemid) {
      this.findFlag = false;
      this.alertText = "";
      this.showData = {
        item: {
          name: "正在跑啦！急什麼！",
          detail: "正在跑啦！急什麼！",
          image: [
            "https://memeprod.sgp1.digitaloceanspaces.com/user-wtf/1653639984383.jpg",
          ],
        },
        provider: {
          name: "正在跑啦！急什麼！",
        },
      };
      this.$axios
        .get(this.apiUrl + "?itemid=" + itemid + "&code=" + this.access_code)
        .then((resp) => {
          console.log(resp.data);
          if (!resp.data.err) {
            this.findFlag = true;
            this.showData = resp.data.message;
            // this.content = Base64.decode(resp.data.t);
            // this.progressLinear.color = "info";
            // this.form.summit = false;
          } else {
            this.content = resp.data.message;
            this.showData = {
              item: {
                name: "找不到啦！",
                detail: "找不到啦！",
                image: [
                  "https://memeprod.sgp1.digitaloceanspaces.com/user-wtf/1653629455115.jpg",
                ],
              },
              provider: {
                name: "找不到啦！",
              },
            };
          }
        })
        .catch((err) => {
          alert(err);
        });
      this.loadOverlay = false;
    },
    googleDriveImg(url) {
      return url.replace(
        "https://drive.google.com/open?",
        "https://drive.google.com/uc?export=view&"
      );
    },
    camaraStatus() {
      if (this.camera == "off") {
        this.tic_id = "";
        this.camera = "auto";
        this.camera_show = true;
      } else {
        this.camera = "off";
        this.camera_show = false;
      }
    },
    onCameraChange(content) {
      this.loadOverlay = true;
      this.itemid = content;
      this.camaraStatus();
      this.getData(content);
    },
    gotIt() {
      this.$axios
        .get(
          this.apiUrl2 + "?itemid=" + this.itemid + "&code=" + this.access_code
        )
        .then((resp) => {
          console.log(resp.data);
          if (!resp.data.err) {
            alert("成功！");
            this.findFlag = false;
            this.alertText = "已經完成登錄";
          } else {
            alert(
              "失敗，如果想不到原因就截圖，而且附上 ID，之後傳訊息給主委，然後告知會再處理就可以了！"
            );
          }
        })
        .catch((err) => {
          alert(err);
        });
    },
  },
  components: {
    // HelloWorld,
  },
  created: function () {
    // this.getData();
    this.access_code = this.$cookie.get("access_code");
    console.log("Access Code > " + this.access_code);
  },
};
</script>
