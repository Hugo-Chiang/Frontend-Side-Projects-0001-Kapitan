<template>
  <section id="checkout-step-03" class="card">
    <div class="card-header">
      <h5>第三步：填寫聯絡資訊</h5>
      <div class="form-check form-check-inline">
        <input
          class="form-check-input"
          type="checkbox"
          id="inline-checkbox-2"
          value="option1"
          v-model="syncOrdererContactInfoAll"
        />
        <label class="form-check-label" for="inline-checkbox-2"
          >同訂購人資訊（全部）</label
        >
      </div>
    </div>
    <div class="card-body">
      <!-- 方案項目開始 -->
      <div
        class="project-block-in-checkout-step-03"
        v-for="(project, index) in confirmProjectsArr"
        :data-id="project.bookingProjectID + '-' + project.bookingProjectDate"
        :key="index"
      >
        <CartListItem
          :list="confirmProjectsArr"
          :index="index"
          :listItem="project"
          :currentPage="currentPage"
          :key="index"
        ></CartListItem>
        <!-- 方案項目結束 -->
        <!-- 聯絡資訊開始 -->
        <div class="mb-3 px-4">
          <input
            class="form-check-input checkbox-for-project"
            type="checkbox"
            :id="'inline-checkbox-' + (index + 3)"
            :key="'inline-checkbox-' + (index + 3)"
            v-model="syncOrdererContactInfoArr[index]"
            @change="simulateBlurEvt(index)"
          />
          <label
            class="form-check-label"
            :for="'inline-checkbox-' + (index + 3)"
            >同訂購資訊（個案）</label
          >
        </div>
        <div
          class="form-row contact-info-input-block"
          :id="'contact-info-input-block' + index"
        >
          <div class="form-group col-md-3">
            <ValidationProvider
              :rules="{ required: true }"
              v-slot="{ errors, classes }"
            >
              <label :for="'input-main-name' + (index + 1)">{{
                requiredInputTitle.MCname
              }}</label>
              <input
                type="text"
                :id="'input-main-name' + (index + 1)"
                :name="requiredInputTitle.MCname"
                :key="'input-main-name' + (index + 1)"
                class="form-control"
                :class="classes"
                :placeholder="requiredInputTitle.MCname"
                v-model="inputContantInfoArr[index].MCname"
                @input="$set(syncOrdererContactInfoArr, index, false)"
              />
              <span class="invalid-feedback">{{ errors[0] }}</span>
            </ValidationProvider>
          </div>
          <div class="form-group col-md-3">
            <ValidationProvider
              :rules="{ required: true, regex: /^09\d{8}$/ }"
              v-slot="{ errors, classes }"
            >
              <label :for="'input-main-phone-number' + (index + 1)">{{
                requiredInputTitle.MCphone
              }}</label>
              <input
                type="text"
                maxlength="10"
                :id="'input-main-phone-number' + (index + 1)"
                :name="requiredInputTitle.MCphone"
                :key="'input-main-phone-number' + (index + 1)"
                class="form-control"
                :class="classes"
                placeholder="0900000000"
                v-model="inputContantInfoArr[index].MCphone"
                @input="$set(syncOrdererContactInfoArr, index, false)"
              />
              <span class="invalid-feedback">{{ errors[0] }}</span>
            </ValidationProvider>
          </div>
          <div class="form-group col-md-4">
            <ValidationProvider
              :rules="{ required: true, email: true }"
              v-slot="{ errors, classes }"
            >
              <label :for="'input-main-email' + (index + 1)">{{
                requiredInputTitle.MCemail
              }}</label>
              <input
                type="email"
                class="form-control"
                :class="classes"
                :id="'input-main-email' + (index + 1)"
                :name="requiredInputTitle.MCemail"
                :key="'input-main-email' + (index + 1)"
                placeholder="Hello-World@email.com"
                v-model="inputContantInfoArr[index].MCemail"
                @input="$set(syncOrdererContactInfoArr, index, false)"
              />
              <span class="invalid-feedback">{{ errors[0] }}</span>
            </ValidationProvider>
          </div>
        </div>
        <div
          class="form-row contact-info-input-block"
          :id="'contact-info-input-block' + (index + 1)"
        >
          <div class="form-group col-md-3">
            <ValidationProvider
              :rules="{ required: true }"
              v-slot="{ errors, classes }"
            >
              <label :for="'input-sub-name' + (index + 2)">{{
                requiredInputTitle.ECname
              }}</label>
              <input
                type="text"
                :id="'input-sub-name' + (index + 2)"
                :name="requiredInputTitle.ECname"
                :key="'input-sub-name' + (index + 2)"
                class="form-control"
                :class="classes"
                :placeholder="requiredInputTitle.ECname"
                v-model="inputContantInfoArr[index].ECname"
                @input="$set(syncOrdererContactInfoArr, index, false)"
              />
              <span class="invalid-feedback">{{ errors[0] }}</span>
            </ValidationProvider>
          </div>
          <div class="form-group col-md-3">
            <ValidationProvider
              :rules="{ required: true, regex: /^09\d{8}$/ }"
              v-slot="{ errors, classes }"
            >
              <label :for="'input-sub-phone-number' + (index + 2)">{{
                requiredInputTitle.ECphone
              }}</label>
              <input
                type="text"
                maxlength="10"
                :id="'input-sub-phone-number' + (index + 2)"
                :name="requiredInputTitle.ECphone"
                :key="'input-sub-phone-number' + (index + 2)"
                class="form-control"
                :class="classes"
                placeholder="0900000000"
                v-model="inputContantInfoArr[index].ECphone"
                @input="$set(syncOrdererContactInfoArr, index, false)"
              />
              <span class="invalid-feedback">{{ errors[0] }}</span>
            </ValidationProvider>
          </div>
          <div class="form-group col-md-4">
            <ValidationProvider
              :rules="{ required: true, email: true }"
              v-slot="{ errors, classes }"
            >
              <label :for="'input-sub-email' + (index + 2)">{{
                requiredInputTitle.ECemail
              }}</label>
              <input
                type="email"
                class="form-control"
                :class="classes"
                :id="'input-sub-email' + (index + 2)"
                :name="requiredInputTitle.ECemail"
                :key="'input-sub-email' + (index + 2)"
                placeholder="Hello-World@email.com"
                v-model="inputContantInfoArr[index].ECemail"
                @input="$set(syncOrdererContactInfoArr, index, false)"
              />
              <span class="invalid-feedback">{{ errors[0] }}</span>
            </ValidationProvider>
          </div>
        </div>
        <!-- 聯絡資訊結束 -->
        <hr v-if="index < confirmProjectsArr.length - 1" />
        <!-- 方案項目結束 -->
      </div>
    </div>
  </section>
</template>

<script>
// 導入結帳項目元件
import CartListItem from "@/components/pages/sub-components/CartListItem";

export default {
  data() {
    return {
      inputOrdererInfo: {
        MCname: "",
        MCphone: "",
        MCemail: "",
        ECname: "",
        ECphone: "",
        ECemail: "",
      },
      syncOrdererContactInfoAll: false,
      syncOrdererContactInfoArr: [],
      inputContantInfoArr: [],
    };
  },
  props: ["confirmProjectsArr", "requiredInputTitle", "currentPage"],
  components: { CartListItem },
  created() {
    this.establishSyncOrdererContactInfoArr();
    this.$eventBus.$on("emitInputOrdererInfo", (infoObj) => {
      this.inputOrdererInfo = infoObj;
    });
  },
  methods: {
    // 方法：建立「第三步：填寫聯絡資訊」同步訂購資訊用的工具陣列
    establishSyncOrdererContactInfoArr() {
      let projectsNum = 0;
      if (JSON.parse(localStorage.getItem("savingProjects")) != null) {
        projectsNum = JSON.parse(localStorage.getItem("savingProjects")).length;
      }

      for (let i = 0; i < projectsNum; i++) {
        this.$set(this.syncOrdererContactInfoArr, i, false);
        this.$set(this.inputContantInfoArr, i, {
          MCname: "",
          MCphone: "",
          MCemail: "",
          ECname: "",
          ECphone: "",
          ECemail: "",
        });
      }
    },
    // 方法：個別方案模擬 blur 事件以觸發表單驗證
    simulateBlurEvt(index) {
      let targetProject = document.querySelectorAll(
        ".project-block-in-checkout-step-03"
      )[index];
      let targetInputs = targetProject.querySelectorAll("input");

      targetInputs.forEach((input) => input.dispatchEvent(new Event("blur")));
    },
    // 方法：透過參數 emit 結帳所需資料予父層結帳方法
    emitCheckOutData() {
      this.$emit(
        "getCheckOutData",
        this.inputOrdererInfo,
        this.inputContantInfoArr
      );
    },
  },
  watch: {
    // 監看（方法）：選擇全部同步訂購資訊與否，將決定個別同步訂購資訊的 checkbox 勾選與否
    syncOrdererContactInfoAll(watchingBoolean) {
      const vm = this;
      let ifAllSelected = vm.syncOrdererContactInfoArr.every(
        (boolean) => boolean == true
      );

      if (watchingBoolean && !ifAllSelected) {
        this.syncOrdererContactInfoArr = this.syncOrdererContactInfoArr.map(
          (boolean) => true
        );
      } else if (!watchingBoolean && ifAllSelected) {
        this.syncOrdererContactInfoArr = this.syncOrdererContactInfoArr.map(
          (boolean) => false
        );
      }

      // 模擬 blur 事件以觸發表單驗證
      let Projects = document.querySelectorAll(
        ".project-block-in-checkout-step-03"
      );
      Projects.forEach((project, index) => vm.simulateBlurEvt(index));
    },
    // 監看（方法）：確認同步訂購資訊時，複製訂購資訊予「第三步：填寫聯絡資訊」的個別輸入欄
    syncOrdererContactInfoArr: {
      handler(newArr) {
        const vm = this;
        let ifAllSelected = vm.syncOrdererContactInfoArr.every(
          (boolean) => boolean == true
        );

        if (ifAllSelected) vm.syncOrdererContactInfoAll = true;
        else vm.syncOrdererContactInfoAll = false;

        newArr.forEach(function (boolean, index) {
          if (boolean) {
            vm.$set(vm.inputContantInfoArr, index, {
              ...vm.inputOrdererInfo,
            });
          } else {
            // 增進使用者體驗：取消同步訂購資訊時，欄位內容完全沒有更改過才會清除
            let inputContactInfoStr = JSON.stringify(
              vm.inputContantInfoArr[index]
            );
            let inputOrdererInfoStr = JSON.stringify(vm.inputOrdererInfo);

            if (inputContactInfoStr == inputOrdererInfoStr) {
              vm.$set(vm.inputContantInfoArr, index, {
                MCname: "",
                MCphone: "",
                MCemail: "",
                ECname: "",
                ECphone: "",
                ECemail: "",
              });
            }
          }
        });
      },
      deep: true,
    },
  },
};
</script>

<style lang="scss" scoped>
@import "../../../assets/scss/all.scss";

.router-link {
  color: black;
  .project-avatar-block {
    width: 120px;
    height: 80px;
    overflow: hidden;
    .project-avatar {
      height: 100%;
    }
  }
  .project-name {
    font-weight: 600;
    color: $deep-teal;
    @include media-breakpoint-down(md) {
      font-size: 15px;
    }
  }
  .project-name-booking-date {
    font-size: 14px;
    @include media-breakpoint-down(md) {
      font-size: 13px;
    }
  }
}
</style>