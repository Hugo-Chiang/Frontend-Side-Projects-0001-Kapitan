<template>
  <!-- 挑選行程頁開始 -->
  <main id="store-page" class="container-fluid">
    <!-- 橫幅開始 -->
    <Cover :coverData="coverData"></Cover>
    <!-- 橫幅結束 -->
    <div class="container position-relative">
      <!-- 麵包屑開始 -->
      <Breadcrumb :breadCrumbData="breadCrumbData"></Breadcrumb>
      <!-- 麵包屑結束 -->
      <!-- 方案篩選器與方案列表開始 -->
      <div class="row d-flex">
        <!-- （桌面版）方案篩選器開始 -->
        <aside
          id="aside-bar"
          class="card pb-4 col-xl-2 d-xl-block order-xl-0 order-3"
          :class="{
            'show-mobile-filter-anim':
              filterData.showMobileFilters.showFramework,
          }"
        >
          <div class="card-body">
            <!-- 挑選地點開始 -->
            <h5
              class="mb-0 d-xl-block"
              :class="{
                'd-none': !filterData.showMobileFilters.showCategories,
              }"
            >
              挑選地點
            </h5>
            <hr
              class="d-xl-block"
              :class="{
                'd-none': !filterData.showMobileFilters.showCategories,
              }"
            />
            <ul
              id="category-list"
              class="pl-3 d-xl-block"
              :class="{
                'd-none': !filterData.showMobileFilters.showCategories,
              }"
            >
              <li>
                <input
                  type="checkbox"
                  name="全選"
                  class="d-xl-inline-block"
                  :class="{
                    'd-none': !filterData.showMobileFilters.showCategories,
                  }"
                  v-model="filterData.selectedAll"
                  @change="checkSelectAll"
                />
                全選
              </li>
              <li
                v-for="category in categoryList"
                :key="category['CATEGORY_ID']"
              >
                <input
                  type="checkbox"
                  :name="category['CATEGORY_NAME']"
                  :value="category['CATEGORY_ID']"
                  class="category-checkbox d-xl-inline-block"
                  :class="{
                    'd-none': !filterData.showMobileFilters.showCategories,
                  }"
                  v-model="filterData.selectedCategories"
                />
                {{ category["CATEGORY_NAME"] }}
              </li>
            </ul>
            <!-- 挑選地點結束 -->
            <!-- 人均預算開始 -->
            <h5
              class="mt-4 mb-0 d-xl-block"
              :class="{
                'd-none': !filterData.showMobileFilters.showBudget,
              }"
            >
              人均預算
            </h5>
            <hr
              class="d-xl-block"
              :class="{ 'd-none': !filterData.showMobileFilters.showBudget }"
            />
            <div
              class="budget mb-1 d-xl-block"
              :class="{ 'd-none': !filterData.showMobileFilters.showBudget }"
            >
              {{ Number(filterData.budget) | currency | dollarSign
              }}<span class="budget-remark">（含）以下</span>
            </div>
            <div>
              <input
                type="range"
                class="form-range d-xl-block"
                :class="{ 'd-none': !filterData.showMobileFilters.showBudget }"
                min="0"
                max="5000"
                step="500"
                id="budget"
                v-model="filterData.budget"
              />
            </div>
            <!-- 人均預算結束 -->
            <!-- 結果排序開始 -->
            <h5
              class="mt-4 mb-0 d-xl-block"
              :class="{ 'd-none': !filterData.showMobileFilters.showSort }"
            >
              結果排序
            </h5>
            <hr
              class="d-xl-block"
              :class="{ 'd-none': !filterData.showMobileFilters.showSort }"
            />
            <select
              class="form-control form-select-lg d-xl-block"
              :class="{ 'd-none': !filterData.showMobileFilters.showSort }"
              v-model="filterData.sort"
            >
              <option disabled selected value>－請選擇－</option>
              <option value="'%%'">預設</option>
              <option value="PROJECT_ORIGINAL_PRICE_PER_PERSON ASC">
                價格 ▲
              </option>
              <!-- <option value="'%%'">評價（高）</option> -->
            </select>
            <!-- 結果排序結束 -->
            <!-- 操作按鈕開始 -->
            <button
              id="filter-button"
              class="btn btn-primary d-xl-block mx-xl-auto mt-5 mb-2"
              :class="
                filterData.showMobileFilters.showFramework
                  ? 'd-block'
                  : 'd-none'
              "
              @click.prevent="queryFilteringProjects"
            >
              套用條件
            </button>
            <a
              id="set-filter-default"
              class="d-block mx-xl-auto text-center"
              @click.prevent="setFilterDefault"
              >恢復預設</a
            >
            <!-- 操作按鈕結束 -->
            <!-- 套用設定通知開始 -->
            <h3
              class="mt-5 text-center d-xl-none"
              :class="{
                'd-none': filterData.showMobileFilters.showFramework,
              }"
            >
              收回視窗
            </h3>
            <!-- 套用設定通知結束 -->
          </div>
        </aside>
        <!-- （桌面版）方案篩選器結束 -->
        <!-- 方案列表開始 -->
        <section class="container col-xl-10 col-12 mb-5">
          <ul
            v-if="currentPageContentArr.length > 0"
            id="cards-list"
            class="d-flex flex-wrap justify-content-sm-between justify-content-center"
          >
            <!-- 方案卡片開始 -->
            <router-link
              v-for="(project, index) in currentPageContentArr"
              :key="project['PROJECT_ID']"
              :data-id="project['PROJECT_ID']"
              :to="{
                name: '方案',
                params: {
                  selectedProjectID: currentPageContentArr[index]['PROJECT_ID'],
                },
              }"
              class="router-link d-block mx-lg-0 mx-md-1 mb-5"
            >
              <li class="card" :key="project['PROJECT_NAME']">
                <img
                  class="card-img-top"
                  :src="
                    project['PROJECT_AVATAR_URL'] != ''
                      ? GlobalVariables.cloudUrlprefix +
                        project['PROJECT_AVATAR_URL']
                      : GlobalVariables.cloudUrlprefix +
                        GlobalVariables.cloudNoImgUrl
                  "
                  alt="這裡是卡片頂圖"
                />
                <div class="card-body position-relative">
                  <h6 class="card-title">
                    {{ project["PROJECT_NAME"] }}
                  </h6>
                  <p class="card-text"></p>
                  <div class="row">
                    <div
                      class="card-people-num col-6 pl-0 position-absolute d-flex align-items-center"
                    >
                      <span class="d-inline-block mr-2">
                        <i class="fas fa-users"></i>
                      </span>
                      <span class="d-inline-block">
                        {{ project["PROJECT_MIN_NUM_OF_PEOPLE"] }} -
                        {{ project["PROJECT_MAX_NUM_OF_PEOPLE"] }}
                      </span>
                    </div>
                    <div
                      class="card-price col-6 pr-0 text-right position-absolute"
                    >
                      每人
                      <span class="price">
                        {{
                          Number(project["PROJECT_ORIGINAL_PRICE_PER_PERSON"])
                            | currency
                            | dollarSign
                        }}
                      </span>
                    </div>
                  </div>
                </div>
              </li>
            </router-link>
            <!-- 方案卡片結束 -->
          </ul>
          <!-- 查無資料開始 -->
          <div v-else>
            <div
              id="no-project-found-block"
              class="mx-auto py-5 d-flex flex-column justify-content-center align-items-center"
            >
              <h3 class="text-center">未能找到符合您篩選的條件</h3>
              <img
                :src="
                  this.GlobalVariables.cloudUrlprefix +
                  'Side-Projects/Frontend-Side-Projects-0001-Kapitan/Web-Imgs/glng164ywqhmq6s7xaje.png'
                "
                alt=""
                class="my-5"
              />
              <h5 class="text-center mt-2">請再嘗試調整看看吧！</h5>
            </div>
          </div>
          <!-- 查無資料結束 -->
        </section>
        <!-- 方案列表結束 -->
        <!-- （行動版）方案篩選器開始 -->
        <ul
          id="mobile-filter"
          class="position-fixed m-0 px-0 py-2 d-xl-none d-flex aligm-items-center"
        >
          <li
            @click="
              [
                (filterData.showMobileFilters.showCategories = !filterData
                  .showMobileFilters.showCategories),
                (filterData.showMobileFilters.showBudget = false),
                (filterData.showMobileFilters.showSort = false),
              ]
            "
          >
            <i class="fas fa-filter"></i> 挑選地點
          </li>
          <li
            @click="
              [
                (filterData.showMobileFilters.showBudget = !filterData
                  .showMobileFilters.showBudget),
                (filterData.showMobileFilters.showCategories = false),
                (filterData.showMobileFilters.showSort = false),
              ]
            "
          >
            <i class="fas fa-comment-dollar"></i> 人均預算
          </li>
          <li
            @click="
              [
                (filterData.showMobileFilters.showSort = !filterData
                  .showMobileFilters.showSort),
                (filterData.showMobileFilters.showBudget = false),
                (filterData.showMobileFilters.showCategories = false),
              ]
            "
          >
            <i class="fas fa-sort-alpha-up"></i> 結果排序
          </li>
        </ul>
        <!-- （行動版）方案篩選器結束 -->
      </div>
      <!-- 方案篩選器與方案列表結束 -->
      <!-- 頁碼開始 -->
      <div id="pagination-row" class="row position-relative mb-5">
        <div
          class="col-xl-10 ml-xl-auto col-12 d-flex justify-content-center"
          id="pagination-container"
        >
          <Pagination
            v-show="currentPageContentArr.length > 0"
            :allContentArr="allProjectsArr"
            :itemsNumPerPage="itemsNumPerPage"
            @emitCurrentContentAndSerial="getCurrentContentAndSerial"
          ></Pagination>
        </div>
      </div>
      <!-- 頁碼結束 -->
    </div>
  </main>
  <!-- 挑選行程頁結束 -->
</template>

<script>
// 導入橫幅元件
import Cover from "@/components/pages/sub-components/Cover";
// 導入麵包屑元件
import Breadcrumb from "@/components/pages/sub-components/Breadcrumb";
// 導入頁碼元件
import Pagination from "@/components/pages/sub-components/Pagination";

export default {
  data() {
    return {
      coverData: {
        imgUrl:
          this.GlobalVariables.cloudUrlprefix +
          "Side-Projects/Frontend-Side-Projects-0001-Kapitan/Web-Imgs/xenzeoqyu3uyqnjumwig.png",
        position: "bottom",
        mainTitle: "挑選航程",
        subTitle: "",
      },
      breadCrumbData: {
        pagesArr: ["首頁", "挑選航程"],
        currentPage: 2,
      },
      filterData: {
        selectedAll: true,
        selectedCategories: [
          "CG0001",
          "CG0002",
          "CG0003",
          "CG0004",
          "CG0005",
          "CG0006",
        ],
        budget: 5000,
        sort: "'%%'",
        showMobileFilters: {
          showNothing: true,
          showFramework: false,
          showCategories: false,
          showBudget: false,
          showSort: false,
        },
      },
      categoryList: [],
      allProjectsArr: [],
      currentPageContentArr: [],
      currentPageContentArrSerial: [],
      itemsNumPerPage: 9,
      currentPageNum: 1,
    };
  },
  components: {
    Cover,
    Breadcrumb,
    Pagination,
  },
  created() {
    window.scrollTo(0, 0);

    // 初始化航程分類與清單
    const categoryListAPI = `${process.env.REMOTE_HOST_PATH}/API/Forestage/QueryCategoryList.php`;
    const projectsListAPI = `${process.env.REMOTE_HOST_PATH}/API/Forestage/QueryProjectsList.php`;
    const vm = this;

    this.$http
      .get(categoryListAPI)
      .then((response) => {
        vm.categoryList = response.data;
        return vm.$http.post(projectsListAPI, JSON.stringify(vm.filterData));
      })
      .then((response) => {
        vm.allProjectsArr = response.data;
      });
  },
  methods: {
    // 方法：向後端詢問符合篩選條件的內容
    queryFilteringProjects() {
      const projectsListAPI = `${process.env.REMOTE_HOST_PATH}/API/Forestage/QueryProjectsList.php`;
      const vm = this;

      this.$http
        .post(projectsListAPI, JSON.stringify(vm.filterData))
        .then((response) => {
          vm.allProjectsArr = response.data;
        });

      this.filterData.showMobileFilters.showNothing = true;
    },
    // 方法：將篩選條件恢復為預設狀態
    setFilterDefault() {
      this.filterData.selectedCategories = [];
      this.filterData.budget = 0;
      this.filterData.sort = "";

      let checkboxs = document.querySelectorAll(".category-checkbox");
      for (const category of checkboxs) {
        this.filterData.selectedCategories.push(category.value);
      }
      this.filterData.budget = 5000;
      this.filterData.sort = "'%%'";
    },
    // 方法：獲得頁碼元件傳回的當前頁面內容
    getCurrentContentAndSerial(arr, num) {
      this.currentPageContentArr = arr;
      this.currentPageContentArrSerial = num;
    },
    // 方法：檢查是否全選，以進行相關渲染
    checkSelectAll() {
      let checkboxs = document.querySelectorAll(".category-checkbox");

      // 判斷：點擊全選 checkbox 與否將影響其餘所有選項是否全選
      if (this.filterData.selectedAll) {
        for (const category of checkboxs) {
          this.filterData.selectedCategories.push(category.value);
        }
      } else {
        this.filterData.selectedCategories = [];
      }
    },
  },
  watch: {
    // 監看（方法）：篩選器一旦更動，即向後端調回所屬產品
    filterData: {
      handler() {
        // 判斷：任一 checkbox 未選將取消全選，所有 checkbox 皆選則連帶全選
        let checkboxs = document.querySelectorAll(".category-checkbox");
        let selectedNum = this.filterData.selectedCategories.length;

        if (selectedNum == checkboxs.length) {
          this.filterData.selectedAll = true;
        } else {
          this.filterData.selectedAll = false;
        }

        // 判斷：任一行動版篩選鈕被點擊時，關聯外框 css 的布靈值就要連動
        let showNothing = this.filterData.showMobileFilters.showNothing;
        let showCategories = this.filterData.showMobileFilters.showCategories;
        let showBudget = this.filterData.showMobileFilters.showBudget;
        let showSort = this.filterData.showMobileFilters.showSort;

        if (showCategories || showBudget || showSort) {
          this.filterData.showMobileFilters.showFramework = true;
          this.filterData.showMobileFilters.showNothing = false;
        } else if (!showCategories || !showBudget || !showSort) {
          this.filterData.showMobileFilters.showFramework = false;
        }

        // 判斷：行動版篩選項目與外框皆不顯示時，觸發全不顯示的布靈值
        if (showNothing) {
          this.filterData.showMobileFilters.showFramework = false;
          this.filterData.showMobileFilters.showCategories = false;
          this.filterData.showMobileFilters.showBudget = false;
          this.filterData.showMobileFilters.showSort = false;
        }
      },
      deep: true,
    },
    // 監看（方法）：換頁即將捲軸置頂
    currentPageContentArr() {
      window.scrollTo(0, 0);
    },
  },
};
</script>

<style lang="scss" scope>
@import "../../assets/scss/all.scss";

#store-page {
  padding: $desktop-nav-bar-height 0 $main-container-pt;
  // 方案篩選器開始
  aside {
    height: 520px;
    border: 1px solid $bootstrap-border-color;
    box-shadow: 1px 1px 1px 0.5px rgba(0, 0, 0, 0.2);
    position: sticky;
    left: 0;
    top: $desktop-nav-bar-height;
    z-index: 9998;
    @include media-breakpoint-down(lg) {
      width: 100%;
      height: auto;
      position: fixed;
      left: 0;
      bottom: 0;
      transform: translate3d(0, 100%, 0);
      transition: transform 1.3s;
    }
    h5 {
      font-weight: 500;
    }
    #category-list {
      li {
        list-style: none;
      }
    }
    .budget {
      font-size: 15px;
      font-weight: 500;
      color: darkred;
      .budget-remark {
        font-size: 12px;
        color: black;
      }
    }
    #set-filter-default {
      width: 65px;
      font-size: 14px;
      color: black;
      cursor: pointer;
    }
  }

  .show-mobile-filter-anim {
    transform: translate3d(0, 0, 0);
    transition: transform 1s;
  }
  // 方案篩選器結束
  // 方案列表開始
  #cards-list {
    padding: 0px 30px;
    &::after {
      content: "";
      width: 255px;
    }
    @include media-breakpoint-down(md) {
      padding: 0px 60px;
    }
    @include media-breakpoint-down(sm) {
      padding: 0;
    }
    .router-link {
      width: 255px;
      .card {
        height: 22rem;
        color: black;
        box-shadow: 1px 1px 1px 0.5px rgba(0, 0, 0, 0.2);
        img {
          width: 100%;
          height: 160px;
          object-fit: cover;
          @include border-top-radius($card-inner-border-radius);
        }
        .card-body {
          height: 75%;
          .card-title {
            font-weight: 500;
          }
          .card-text {
            height: 40%;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
          }
          .card-people-num,
          .card-price {
            bottom: 20px;
          }
          .card-people-num {
            left: 20px;
            font-size: 14px;
          }
          .card-price {
            right: 20px;
            font-size: 14px;
            .price {
              font-size: 18px;
              font-weight: 700;
              color: darkred;
            }
          }
        }
      }
    }
  }
  // 方案列表結束
  #no-project-found-block {
    width: 80%;
    img {
      width: 60%;
    }
  }
  #mobile-filter {
    width: 100%;
    height: 50px;
    border-top: 1px solid $bootstrap-border-color;
    background-color: $sail;
    left: 0;
    bottom: 0;
    z-index: 9999;
    li {
      width: 33.3%;
      list-style: none;
      text-align: center;
      line-height: 2rem;
      cursor: pointer;
      &:nth-child(2n + 2) {
        border-left: 1px solid $bootstrap-border-color;
        border-right: 1px solid $bootstrap-border-color;
      }
    }
  }
}
</style>