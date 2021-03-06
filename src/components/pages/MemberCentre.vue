<template>
  <main id="member-centre-page" class="container-fluid mb-5">
    <!-- 橫幅開始 -->
    <Cover :coverData="coverData"></Cover>
    <!-- 橫幅結束 -->
    <div class="container">
      <!-- 麵包屑開始 -->
      <Breadcrumb :breadCrumbData="breadCrumbData"></Breadcrumb>
      <!-- 麵包屑結束 -->
      <div class="row">
        <!-- 側邊欄位開始 -->
        <aside class="col-lg-3 mb-lg-0 col-12 mb-4">
          <div class="card">
            <div class="card-body">
              <div
                class="avatar-block d-flex justify-content-center align-items-center mx-auto my-3"
              >
                <div class="d-flex justify-content-center align-items-center">
                  <img
                    :src="
                      memberInfo.avatarUrl == null || ''
                        ? GlobalVariables.cloudUrlprefix +
                          GlobalVariables.cloudNoAvatarUrl
                        : GlobalVariables.cloudUrlprefix + memberInfo.avatarUrl
                    "
                    alt=""
                  />
                </div>
              </div>
              <div class="text-center">
                <h6>
                  {{ memberInfo.nickname || "尊敬的甲必丹會員" }}
                </h6>
              </div>
              <hr />
              <ul class="p-0">
                <li
                  class="text-center mt-4 d-flex justify-content-center align-items-center"
                  v-for="list in asideList"
                  :key="list.name"
                  :class="currentSubPage == list.name ? 'current-sub-page' : ''"
                  @click="currentSubPage = list.name"
                >
                  <div
                    class="icon-block d-flex justify-content-center align-items-center mr-1"
                  >
                    <i :class="list.iconClass"></i>
                  </div>
                  {{ list.name }}
                </li>
              </ul>
            </div>
          </div>
        </aside>
        <!-- 側邊欄位結束 -->
        <!-- 主要內容（切換）開始 -->
        <section class="col-lg-9 col-12">
          <div class="card">
            <!-- （切換）基本資料開始 -->
            <MemberInfoEditor
              v-if="currentSubPage == '基本資料'"
              :memberID="memberInfo.ID"
              :memberInfo="memberInfo.propsObj"
              @emitCloseEditor="backToWelcomePage"
              @emitUpdateFinished="queryMemberInfo"
            ></MemberInfoEditor>
            <!-- （切換）基本資料結束 -->
            <!-- （切換）查詢訂單開始 -->
            <MemberOrdersSearching
              v-else-if="currentSubPage == '查詢訂單'"
              :memberID="memberInfo.ID"
            ></MemberOrdersSearching>
            <!-- （切換）查詢訂單開始 -->
            <div class="card-body welcome-sub-page" v-else>
              <div id="welcome-message-block" class="mx-auto py-5">
                <img
                  :src="
                    this.GlobalVariables.cloudUrlprefix +
                    'Side-Projects/Frontend-Side-Projects-0001-Kapitan/Web-Imgs/mnacpujka8ttmlxf5n5w.jpg'
                  "
                  alt=""
                />
                <h3 class="text-center mb-3">準備啟航了嗎？</h3>
                <h6 class="text-center">點選大頭貼下方的功能以進行操作</h6>
              </div>
            </div>
          </div>
        </section>
        <!-- 主要內容（切換）結束 -->
      </div>
    </div>
  </main>
</template>

<script>
// 導入橫幅元件
import Cover from "@/components/pages/sub-components/Cover";
// 導入麵包屑元件
import Breadcrumb from "@/components/pages/sub-components/Breadcrumb";
// 導入會員中心的 2 個次級內容元件
import MemberInfoEditor from "@/components/pages/sub-components/MemberInfoEditor";
import MemberOrdersSearching from "@/components/pages/sub-components/MemberOrdersSearching";

export default {
  data() {
    return {
      coverData: {
        imgUrl:
          this.GlobalVariables.cloudUrlprefix +
          "Side-Projects/Frontend-Side-Projects-0001-Kapitan/Web-Imgs/vy4coyk24onrob2tcy4c.jpg",
        position: "center",
        mainTitle: "會員中心",
        subTitle: "",
      },
      breadCrumbData: {
        pagesArr: ["首頁", "會員中心"],
        currentPage: 2,
      },
      memberInfo: {
        ID: "",
        nickname: "",
        avatarUrl: "",
        propsObj: {},
      },
      asideList: [
        { name: "基本資料", iconClass: ["far", "fa-user-circle"] },
        { name: "查詢訂單", iconClass: ["fas", "fa-tasks"] },
      ],
      currentSubPage: "",
    };
  },
  components: {
    Cover,
    Breadcrumb,
    MemberInfoEditor,
    MemberOrdersSearching,
  },
  created() {
    window.scrollTo(0, 0);

    this.queryMemberInfo();
  },
  methods: {
    // 方法：驗證登入 session 後，回傳會員資料
    queryMemberInfo() {
      const signInAuthenticationAPI = `${process.env.REMOTE_HOST_PATH}/API/Forestage/SignInAuthentication.php`;
      const queryMemberInfoAPI = `${process.env.REMOTE_HOST_PATH}/API/Universal/QueryMemberInfo.php`;
      const session = this.GlobalFunctions.getKapitanSession("forestage");
      const vm = this;

      let sendingObj = {
        session: session,
      };

      this.$http
        .post(signInAuthenticationAPI, JSON.stringify(sendingObj))
        .then((response) => {
          if (response.data.sessionCheck) {
            vm.memberInfo.ID = response.data.signInedID;
            return vm.$http.post(queryMemberInfoAPI, vm.memberInfo.ID);
          }
        })
        .catch((error) => {
          console.log(error);
        })
        .then((response) => {
          vm.memberInfo.propsObj = response.data;
          vm.memberInfo.nickname = response.data["MEMBER_NICKNAME"];
          vm.memberInfo.avatarUrl = response.data["MEMBER_AVATAR_URL"];
        })
        .catch((error) => {
          console.log(error);
        });
    },
    // 方法：回到歡迎頁
    backToWelcomePage() {
      this.currentSubPage = "";
      window.scrollTo(0, 0);
    },
  },
};
</script>

<style lang="scss" scoped>
@import "../../assets/scss/all.scss";

#member-centre-page {
  padding: $desktop-nav-bar-height 0 $main-container-pt;
  h1 {
    margin-top: 200px;
  }
  .avatar-block {
    width: 150px;
    height: 150px;
    border-radius: 150px;
    background-color: $deep-teal;
    div {
      width: 95%;
      height: 95%;
      border-radius: 95%;
      overflow: hidden;
      background-color: $sail;
      img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        object-position: center center;
        &:hover {
          ::after {
            content: "點擊更換大頭貼";
            width: 120px;
            height: 30px;
            background-color: black;
            color: $sail;
            opacity: 1;
            position: absolute;
            text-align: center;
            line-height: 30px;
          }
        }
      }
    }
  }
  ul {
    li {
      font-size: 18px;
      list-style: none;
      cursor: pointer;
      &:hover {
        opacity: 0.8;
      }
    }
    .current-sub-page {
      font-weight: 500;
      color: $tiffany-blue;
    }
  }
  .welcome-sub-page {
    #welcome-message-block {
      width: 80%;
      img {
        width: 100%;
      }
    }
  }
}
</style>