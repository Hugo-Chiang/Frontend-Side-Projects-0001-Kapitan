<template>
  <main id="forestage-login-page" class="backgroud container-fluid">
    <div class="container">
      <div class="row">
        <div class="col-12">
          <!-- 登入註冊卡片開始 -->
          <div class="d-flex justify-content-center">
            <div id="login-card" class="card">
              <form>
                <ValidationObserver v-slot="{ invalid }">
                  <div class="card-body">
                    <div
                      id="login-logo-block"
                      class="d-flex justify-content-center my-3"
                    >
                      <img
                        id="login-logo"
                        src="../../assets/img/Kapitan-Logo-Vertical-Version-01.png"
                        alt=""
                      />
                    </div>
                    <!-- 登入註冊切換欄開始 -->
                    <ul class="list-group flex-row justify-content-center mb-3">
                      <li
                        class="list-group-item"
                        :class="{ 'selected-item': signInMode }"
                        @click="signInMode = true"
                      >
                        <span v-if="signInMode">正在</span>
                        <span v-else>轉為</span>登入
                      </li>
                      <li
                        class="list-group-item"
                        :class="{ 'selected-item': !signInMode }"
                        @click="signInMode = false"
                      >
                        <span v-if="!signInMode">正在</span>
                        <span v-else>轉為</span>註冊
                      </li>
                    </ul>
                    <!-- 登入註冊切換欄結束 -->
                    <!-- 輸入帳號開始 -->
                    <div class="form-group">
                      <ValidationProvider
                        :rules="{ required: true }"
                        v-slot="{ errors, classes }"
                      >
                        <label for="">輸入帳號</label>
                        <input
                          type="email"
                          class="form-control"
                          :class="[
                            classes,
                            {
                              'is-invalid': loginData.repeatRegister,
                              shaking:
                                loginData.signInFailedFeedback
                                  .IncorrectAccountPasswordAnime,
                            },
                          ]"
                          id="帳號"
                          ref="帳號"
                          placeholder="Hello-World@email.com"
                          v-model="loginData.input.account"
                          @blur="queryMemberAccount"
                        />
                        <span
                          class="invalid-feedback"
                          :class="
                            loginData.repeatRegister
                              ? 'd-inline-block'
                              : 'd-none'
                          "
                          >此帳號已註冊過</span
                        >
                        <span
                          class="invalid-feedback"
                          v-show="!loginData.repeatRegister"
                          >{{ errors[0] }}</span
                        >
                      </ValidationProvider>
                    </div>
                    <!-- 輸入帳號結束 -->
                    <!-- 輸入密碼開始 -->
                    <div class="form-group position-relative">
                      <ValidationProvider
                        :rules="{
                          required: true,
                          regex: !signInMode
                            ? /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[^]{8,16}$/
                            : false,
                        }"
                        v-slot="{ errors, classes }"
                      >
                        <label for="密碼">輸入密碼</label>
                        <span
                          id="password-remark-trigger"
                          @mouseenter="loginData.showRemark = true"
                          @mouseleave="loginData.showRemark = false"
                          @click="loginData.showRemark = !loginData.showRemark"
                          >［?］</span
                        >
                        <span
                          id="password-remark"
                          class="position-absolute"
                          v-show="loginData.showRemark"
                          >8到16位字符，至少1個大寫字母、1個小寫字母、1個數字。</span
                        >
                        <input
                          type="password"
                          class="form-control"
                          :class="[
                            classes,
                            {
                              shaking:
                                loginData.signInFailedFeedback
                                  .IncorrectAccountPasswordAnime,
                            },
                          ]"
                          id="密碼"
                          placeholder="問號處可得格式提示"
                          v-model="loginData.input.password"
                        />
                        <span class="invalid-feedback">{{ errors[0] }}</span>
                      </ValidationProvider>
                    </div>
                    <!-- 輸入密碼結束 -->
                    <!-- 輸入確認密碼開始 -->
                    <div class="form-froup mb-3">
                      <ValidationProvider
                        v-if="!signInMode"
                        :rules="{
                          required: true,
                          regex: /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[^]{8,16}$/,
                        }"
                        v-slot="{ errors, classes }"
                      >
                        <label for="確認密碼">確認密碼</label>
                        <input
                          type="password"
                          class="form-control"
                          :class="[
                            classes,
                            { 'is-invalid': loginData.passwordsUnequal },
                          ]"
                          id="確認密碼"
                          placeholder="請再輸入一次密碼"
                          v-model="loginData.input.passwordChecked"
                          @blur="comparisePassword"
                        />
                        <span
                          class="invalid-feedback"
                          :class="
                            loginData.passwordsUnequal
                              ? 'd-inline-block'
                              : 'd-none'
                          "
                          >確認密碼與輸入密碼不相等</span
                        >
                        <span
                          class="invalid-feedback"
                          v-show="!loginData.passwordsUnequal"
                          >{{ errors[0] }}</span
                        >
                      </ValidationProvider>
                    </div>
                    <!-- 輸入確認密碼結束 -->
                    <!-- 動作區域開始 -->
                    <div
                      class="action-block d-flex flex-column justify-content-center align-items-center"
                    >
                      <div v-if="signInMode" class="checkbox">
                        <input
                          type="checkbox"
                          class="form-check-input"
                          id="remember-me-checkbox"
                          v-model="loginData.remeberMe"
                        />
                        <label
                          class="form-check-label"
                          for="remember-me-checkbox"
                          >記住我</label
                        >
                      </div>
                      <button
                        id="action-button"
                        class="btn btn-primary mt-3 mb-3"
                        :class="{ 'invalid-btn': invalid }"
                        :disabled="
                          invalid ||
                          loginData.repeatRegister ||
                          loginData.passwordsUnequal
                        "
                        @click.prevent="signInOrRegister"
                        :data-toggle="signInMode ? '' : 'modal'"
                        data-target="#modal"
                        data-backdrop="static"
                      >
                        <span v-if="signInMode">登入</span>
                        <span v-else>註冊</span>
                      </button>
                      <div
                        id="sign-in-failed-warning"
                        class="mb-2"
                        v-show="
                          signInMode && loginData.signInFailedFeedback != ''
                        "
                      >
                        {{ loginData.signInFailedFeedback.signInFailedMessage }}
                      </div>
                    </div>
                    <!-- 動作區域結束 -->
                  </div>
                </ValidationObserver>
              </form>
            </div>
          </div>
          <!-- 登入註冊卡片結束 -->
        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      signInMode: null,
      signIned: false,
      loginData: {
        showRemark: false,
        repeatRegister: false,
        passwordsUnequal: false,
        remeberMe: false,
        input: {
          account: "",
          password: "",
          passwordChecked: "",
        },
        signInFailedFeedback: {
          signInFailedMessage: "",
          IncorrectAccountPassword: false,
          IncorrectAccountPasswordAnime: false,
        },
      },
      // 提示視窗資料
      modalData: {
        callBy: null,
        situation: {
          event: "",
          message: "",
          buttonType: "checked",
          data: {},
        },
        emitValue: null,
        // 反應（方法）：根據不同情境做出應對
        correspond() {
          switch (this.situation.event) {
            case "伺服器異常":
              setTimeout(
                () => this.callBy.$router.push({ name: "首頁" }),
                1000
              );
              break;
            case "資料庫寫入成功":
              setTimeout(
                () => this.callBy.$router.push({ name: "首頁" }),
                1000
              );
              break;
          }
        },
      },
    };
  },
  created() {
    window.scrollTo(0, 0);

    // 判斷是否為首頁帶來的註冊戶
    if (this.$route.params.signInMode != undefined) {
      this.signInMode = false;
    } else {
      this.signInMode = true;
    }

    this.modalData.callBy = this;
    this.$eventBus.$on("emitModalValue", (value) => {
      this.modalData.emitValue = value;
    });
  },
  methods: {
    // 方法：根據輸入電郵向後端詢問是否重複註冊
    queryMemberAccount() {
      const api = `${process.env.REMOTE_HOST_PATH}/API/Backstage/QueryMemberAccount.php`;
      const vm = this;
      let memberAccount = vm.loginData.input.account;
      this.loginData.repeatRegister = false;

      if (!this.signInMode) {
        this.$http
          .post(api, memberAccount)
          .then((response) => {
            if (response.data["MEMBER_ACCOUNT"]) {
              vm.loginData.repeatRegister = true;
            }
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
    // 方法：檢查確認密碼與輸入密碼是否一致
    comparisePassword() {
      let password = this.loginData.input.password;
      let passwordChecked = this.loginData.input.passwordChecked;
      this.loginData.passwordsUnequal = false;

      if (password != passwordChecked) {
        this.loginData.passwordsUnequal = true;
      }
    },
    // 方法：登入與註冊的複合函式
    signInOrRegister() {
      this.$eventBus.$emit("emitModalData", this.modalData);
      this.loginData.signInFailedFeedback.IncorrectAccountPasswordAnime = false;

      const signInAPI = `${process.env.REMOTE_HOST_PATH}/API/Forestage/SignIn.php`;
      const registerAPI = `${process.env.REMOTE_HOST_PATH}/API/Forestage/Register.php`;
      const vm = this;
      let api = "";

      if (this.signInMode) {
        api = signInAPI;
      } else {
        api = registerAPI;
      }

      this.$http
        .post(api, JSON.stringify(this.loginData.input))
        .then((response) => {
          if (response.data.singInStatus) {
            const memberID = response.data.memberID;
            const session = response.data.session;
            const expDate = new Date(response.data.expDate);
            const remeberMe = vm.loginData.remeberMe;

            document.cookie = `kapitanMembersSession="${session}"; expires="${
              remeberMe ? expDate : ""
            }"`;
            document.cookie = `kapitanMemberID="${memberID}"; expires="${
              remeberMe ? expDate : ""
            }"`;
            vm.signIned = true;

            vm.$eventBus.$emit("emitSignInStatus", vm.signIned);

            if (this.signInMode) {
              vm.$router.push(localStorage.getItem("ForestageBlockBefore"));
            } else {
              vm.modalData.situation.event = "資料庫寫入成功";
              vm.modalData.situation.message = response.data.message;
            }
          } else {
            vm.loginData.input.account = "";
            vm.loginData.input.password = "";
            vm.loginData.input.passwordChecked = "";
            vm.loginData.signInFailedFeedback.signInFailedMessage =
              response.data.message;

            let feedback =
              vm.loginData.signInFailedFeedback.signInFailedMessage;

            if (vm.signInMode && feedback == "帳號或密碼錯誤") {
              vm.loginData.signInFailedFeedback.IncorrectAccountPassword = true;
              vm.loginData.signInFailedFeedback.IncorrectAccountPasswordAnime = true;
              setTimeout(() => {
                vm.loginData.signInFailedFeedback.IncorrectAccountPasswordAnime = false;
              }, 1200);
            }
          }
        })
        .catch((error) => {
          vm.modalData.situation.event = "伺服器異常";
          vm.modalData.situation.message =
            "伺服器異常，請稍後再試。造成您的不便，敬請見諒！";
          vm.modalData.situation.data = error;
          console.log(error);
        });
    },
  },
  watch: {
    // 監看（方法）：切換登入註冊模式將重置各類回饋的初始值
    signInMode() {
      this.loginData.repeatRegister = false;
      this.loginData.passwordsUnequal = false;
      this.loginData.remeberMe = false;
      this.loginData.signInFailedFeedback.IncorrectAccountPassword = false;
    },
  },
};
</script>

<style lang="scss" scoped>
@import "../../assets/scss/all.scss";

.backgroud {
  background-image: url("https://res.cloudinary.com/hugo-chiang/image/upload/v1617899627/Side-Projects/Frontend-Side-Projects-0001-Kapitan/Web-Imgs/bowzalnszxt7mz9in6xx.png");
  opacity: 0.95;
  background-size: cover;
  background-position: center;
  padding: $desktop-nav-bar-height 0 $main-container-pt;
  .container {
    height: 800px;
    padding-top: 80px;
    #login-card {
      width: 320px;
      height: 640px;
      box-shadow: 2px 2px 2px 2px rgba(0, 0, 0, 0.2);
      #login-logo {
        width: 100px;
      }
      .list-group-item {
        width: 50%;
        height: 40px;
        line-height: 40px;
        padding: 0;
        text-align: center;
        border: 0.5px solid $bootstrap-border-color;
        border-radius: 0;
        &:hover {
          cursor: pointer;
        }
        &:first-child {
          border-right: none;
        }
      }
      .selected-item {
        background-color: $deep-teal;
        color: $sail;
      }
      #password-remark-trigger,
      #password-remark {
        font-size: 12px;
      }
      #password-remark {
        color: darkred;
        display: inline-block;
        width: 180px;
        top: -10px;
      }
      #action-button {
        width: 105px;
        height: 35px;
        padding: 0;
      }
      #sign-in-failed-warning {
        color: red;
      }

      @keyframes shaking {
        10%,
        90% {
          transform: translate3d(-1px, 0, 0);
        }
        20%,
        80% {
          transform: translate3d(2px, 0, 0);
        }
        30%,
        50%,
        70% {
          transform: translate3d(-4px, 0, 0);
        }
        40%,
        60% {
          transform: translate3d(4px, 0, 0);
        }
      }

      .shaking {
        animation: shaking 1.2s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
        transform: translate3d(0, 0, 0);
      }
    }
  }
}
</style>