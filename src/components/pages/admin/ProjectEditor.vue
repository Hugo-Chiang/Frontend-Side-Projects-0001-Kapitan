<template>
  <section id="project-editor-page">
    <!-- 麵包屑元件開始 -->
    <Breadcrumb
      v-if="!inCreatingMode"
      :breadCrumbData="breadCrumbData"
    ></Breadcrumb>
    <!-- 麵包屑元件結束 -->
    <div class="d-flex justify-content-between">
      <h6 class="mt-2 mb-4 d-inline-block">
        正在<span v-if="inCreatingMode" @click="autofilledProjectContent"
          >新增</span
        ><span v-else>編輯</span>：{{ managingProjectID }}
        方案
        <span v-if="!inCreatingMode" class="delete-project-btn ml-1">
          <a
            href=""
            @click.prevent="deleteProject"
            data-toggle="modal"
            data-target="#modal"
            data-backdrop="static"
            >刪除專案</a
          >
        </span>
      </h6>
      <div
        class="d-inline-block"
        v-if="currentPath.indexOf('Project-Editor') == -1"
      >
        <div id="rechoose-mode-link" class="d-flex justify-content-end">
          <a href="" @click.prevent="$router.push('/Admin/Projects-Manager')"
            >重選模式
            <i class="fas fa-sign-out-alt"></i>
          </a>
        </div>
      </div>
    </div>
    <ValidationObserver v-slot="{ invalid }">
      <div class="row">
        <div class="col-12">
          <div class="form-row">
            <!-- 方案狀態開始 -->
            <div class="form-group col-2">
              <label :for="requiredInputTitle.projectStatus">{{
                requiredInputTitle.projectStatus
              }}</label>
              <select
                :id="requiredInputTitle.projectStatus"
                class="form-control form-select-lg"
                v-model="editDetails.projectStatus"
                :disabled="adminLevel > 2"
              >
                <option value="1">上線中</option>
                <option value="0">已下線</option>
                <option value="-1">僅供測試</option>
              </select>
            </div>
            <!-- 方案狀態結束 -->
            <!-- 方案名稱開始 -->
            <div class="form-group col-4">
              <ValidationProvider
                :rules="{ required: true }"
                v-slot="{ errors, classes }"
              >
                <label :for="requiredInputTitle.projectName">{{
                  requiredInputTitle.projectName
                }}</label>
                <input
                  type="text"
                  class="form-control"
                  :class="classes"
                  :id="requiredInputTitle.projectName"
                  placeholder="請輸入全名，可包含符號"
                  v-model="editDetails.projectName"
                />
                <span class="invalid-feedback">{{
                  errors[0]
                }}</span></ValidationProvider
              >
            </div>
            <!-- 方案名稱結束 -->
            <!-- 人均價格開始 -->
            <div class="form-group col-2">
              <ValidationProvider
                :rules="{ required: true }"
                v-slot="{ errors, classes }"
              >
                <label :for="requiredInputTitle.projectPricePerPerson">{{
                  requiredInputTitle.projectPricePerPerson
                }}</label>
                <input
                  type="number"
                  class="form-control"
                  :class="classes"
                  :id="requiredInputTitle.projectPricePerPerson"
                  placeholder="例：1000"
                  v-model="editDetails.projectPricePerPerson"
                />
                <span class="invalid-feedback">{{
                  errors[0]
                }}</span></ValidationProvider
              >
            </div>
            <!-- 人均價格結束 -->
            <!-- 最低人數開始 -->
            <div class="form-group col-2">
              <ValidationProvider
                :rules="{ required: true }"
                v-slot="{ errors, classes }"
              >
                <label :for="requiredInputTitle.projectMinNumOfPeople">{{
                  requiredInputTitle.projectMinNumOfPeople
                }}</label>
                <input
                  type="number"
                  class="form-control"
                  :class="classes"
                  :id="requiredInputTitle.projectMinNumOfPeople"
                  placeholder="例：3"
                  v-model="editDetails.projectMinNumOfPeople"
                />
                <span class="invalid-feedback">{{
                  errors[0]
                }}</span></ValidationProvider
              >
            </div>
            <!-- 最低人數結束 -->
            <!-- 最高人數開始 -->
            <div class="form-group col-2">
              <ValidationProvider
                :rules="{ required: true }"
                v-slot="{ errors, classes }"
              >
                <label :for="requiredInputTitle.projectMaxNumOfPeople">{{
                  requiredInputTitle.projectMaxNumOfPeople
                }}</label>
                <input
                  type="number"
                  class="form-control"
                  :class="classes"
                  :id="requiredInputTitle.projectMaxNumOfPeople"
                  placeholder="例：3"
                  v-model="editDetails.projectMaxNumOfPeople"
                />
                <span class="invalid-feedback">{{
                  errors[0]
                }}</span></ValidationProvider
              >
            </div>
            <!-- 最高人數結束 -->
          </div>
          <div class="form-row">
            <!-- 所屬分類開始 -->
            <div class="form-group col-2">
              <label :for="requiredInputTitle.projectCategory">{{
                requiredInputTitle.projectCategory
              }}</label>
              <select
                :id="requiredInputTitle.projectCategory"
                class="form-control form-select-lg"
                v-model="editDetails.projectCategory"
              >
                <option
                  v-for="category in renderData['categoryList']"
                  :key="category['CATEGORY_NAME']"
                  :value="category['CATEGORY_ID']"
                >
                  {{ category["CATEGORY_NAME"] }}
                </option>
              </select>
            </div>
            <!-- 所屬分類結束 -->
            <!-- 會合地點開始 -->
            <div class="form-group col-3">
              <label :for="requiredInputTitle.projectDepartureLocation">{{
                requiredInputTitle.projectDepartureLocation
              }}</label>
              <select
                :id="requiredInputTitle.projectDepartureLocation"
                class="form-control form-select-lg"
                v-model="editDetails.projectDepartureLocation"
              >
                <option
                  v-for="location in renderData['departureLocationList']"
                  :key="location['LOCATION_NAME']"
                  :value="location['LOCATION_ID']"
                >
                  {{ location["LOCATION_NAME"] }}
                </option>
              </select>
            </div>
            <!-- 會合地點結束 -->
            <!-- 方案大頭貼開始 -->
            <div class="form-group col-3">
              <label
                :for="requiredInputTitle.projectAvatarPublicID"
                class="form-label"
                >{{ requiredInputTitle.projectAvatarPublicID }}</label
              >
              <div class="custom-file">
                <input
                  :id="requiredInputTitle.projectAvatarPublicID"
                  class="custom-file-input"
                  type="file"
                  accept="image/png, image/jpeg"
                  @change="handleFileChange($event)"
                />
                <label
                  class="custom-file-label"
                  :for="requiredInputTitle.projectAvatarPublicID"
                  >{{
                    vueCloudinaryData.filesData.avatar.name || "請選擇檔案"
                  }}</label
                >
              </div>
            </div>
            <!-- 方案大頭貼結束 -->
            <!-- 方案輪播圖開始 -->
            <div class="form-group col-3">
              <label
                :for="requiredInputTitle.projectCarouselImgs"
                class="form-label"
                >{{ requiredInputTitle.projectCarouselImgs }}</label
              >
              <div class="custom-file">
                <input
                  :id="requiredInputTitle.projectCarouselImgs"
                  class="custom-file-input"
                  type="file"
                  multiple
                  accept="image/png, image/jpeg"
                  @change="handleFileChange($event)"
                />
                <label
                  class="custom-file-label"
                  :for="requiredInputTitle.projectCarouselImgs"
                  >{{
                    `已選擇 ${vueCloudinaryData.filesData.carouselImgs.length} 個檔案`
                  }}</label
                >
              </div>
            </div>
            <!-- 方案輪播圖結束 -->
          </div>
        </div>
      </div>
      <hr />
      <div class="row">
        <div class="col-6">
          <h6 class="d-inline-block">方案文本編輯器</h6>
          <!-- 編輯器提示語開始 -->
          <span
            id="editor-prompt"
            class="d-inline-block ml-2"
            v-html="editorPrompt"
          ></span>
          <!-- 編輯器提示語結束 -->
        </div>
      </div>
      <div class="row mt-2">
        <!-- VueEditor 文本編輯器開始 -->
        <div class="col-9">
          <!-- VueEditor 文本編輯器開始 -->
          <VueEditor
            ref="vueEditor"
            :editorToolbar="vueEditorData.ToolbarConfig"
            v-if="vueEditorData.inEditing == '方案摘要'"
            v-model="editDetails.projectSummary"
            :use-custom-image-handler="true"
            @image-added="uploadContentImg"
          ></VueEditor>
          <VueEditor
            ref="vueEditor"
            :editorToolbar="vueEditorData.ToolbarConfig"
            v-else
            v-model="editDetails.projectDescription"
            :use-custom-image-handler="true"
            @image-added="uploadContentImg"
          ></VueEditor>
          <!-- VueEditor 文本編輯器結束 -->
        </div>
        <div class="col-3 d-flex flex-column">
          <h6>正在編輯：</h6>
          <ul class="editingHTMLlist pl-2 mt-3">
            <li
              class="mb-2 d-flex justify-content-start align-items-center"
              v-for="target in vueEditorData.vModelTargets"
              :key="target"
              @click="vueEditorData.inEditing = target"
            >
              <div class="in-editing-arrow d-flex align-items-center mr-1">
                <div
                  class="w-100 h-100 align-items-center"
                  :class="
                    vueEditorData.inEditing == target ? 'd-flex' : 'd-none'
                  "
                >
                  <i class="fas fa-arrow-alt-circle-right"></i>
                </div>
              </div>
              {{ target }}
            </li>
          </ul>
          <!-- 操作按鈕開始 -->
          <div
            class="action-buttons-block w-100 mt-5 px-2 d-flex justify-content-between align-items-center"
          >
            <input
              type="button"
              class="btn btn-primary"
              :class="{ 'invalid-btn': invalid }"
              :value="inCreatingMode ? '新增完成' : '修改完成'"
              :disabled="invalid"
              @click.prevent="uploadImgAndUpdateData"
              data-toggle="modal"
              data-target="#modal"
              data-backdrop="static"
            />
            <a
              class="d-inline-block"
              href=""
              @click.prevent="$router.push('/Admin/Projects-Manager')"
              >不儲存關閉</a
            >
          </div>
          <!-- 操作按鈕結束 -->
        </div>
      </div>
    </ValidationObserver>
  </section>
</template>

<script>
// 導入麵包屑元件
import Breadcrumb from "@/components/pages/sub-components/Breadcrumb";
// 導入 axios 元件
import axios from "axios";
// 導入 vue2-editor 文本編輯器元件
import { VueEditor } from "vue2-editor";

export default {
  data() {
    return {
      adminLevel: null,
      inCreatingMode: null,
      breadCrumbData: {
        pagesArr: ["管理系統：查詢方案", "管理系統：編輯方案"],
        currentPage: 2,
      },
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
          // 遭遇失敗情境將導回管理頁
          if (this.situation.event.indexOf("失敗") != -1) {
            setTimeout(
              () => this.callBy.$router.push({ name: "管理系統：方案管理" }),
              1000
            );
          }

          // 刪除方案詢問經確認後進行刪除，成敗與否都將倒回管理頁
          if (this.situation.event.indexOf("刪除方案") != -1) {
            const deleteProjectAPI = `${process.env.REMOTE_HOST_PATH}/API/Backstage/DeleteProject.php`;
            const session = this.callBy.GlobalFunctions.getKapitanSession(
              "backstage"
            );

            let sendingObj = {
              session: session,
              projectID: this.callBy.managingProjectID,
            };

            if (this.emitValue == 1) {
              this.situation.buttonType = "checked";

              this.callBy.$http
                .post(deleteProjectAPI, JSON.stringify(sendingObj))
                .then((response) => {
                  this.situation.event = "刪除方案成功";
                  this.situation.message = response.data;
                })
                .catch((error) => {
                  this.situation.event = "刪除方案失敗";
                  this.situation.message = error.data;
                });
            } else if (this.emitValue == "checked") {
              setTimeout(
                () =>
                  this.callBy.$router.push({
                    name: "管理系統：方案管理",
                  }),
                1500
              );
            }
          }

          this.callBy.modalData.situation.event = "";
          this.callBy.modalData.situation.message = "";
        },
      },
      requiredInputTitle: {
        projectStatus: "方案狀態",
        projectName: "方案名稱",
        projectPricePerPerson: "人均價格",
        projectMinNumOfPeople: "最低人數",
        projectMaxNumOfPeople: "最高人數",
        projectCategory: "所屬分類",
        projectDepartureLocation: "會合地點",
        projectAvatarPublicID: "方案大頭貼",
        projectCarouselImgs: "方案輪播圖",
      },
      renderData: {
        categoryList: [],
        departureLocationList: [],
      },
      managingProjectID: {},
      editDetails: {
        projectStatus: "",
        projectName: "",
        projectAvatarPublicID: "",
        projectCarouselImgs: [],
        projectSummary: "",
        projectDescription: "",
        projectPricePerPerson: 0,
        projectMinNumOfPeople: 0,
        projectMaxNumOfPeople: 0,
        projectCategory: "",
        projectDepartureLocation: "",
        projectDepartureLocationDescription: "",
      },
      editorPrompt: "",
      // vue2-editor 文本編輯器的配置或應用資料
      vueEditorData: {
        ToolbarConfig: [
          [{ header: [false, 1, 2, 3, 4, 5, 6] }],
          ["bold"],
          [
            { align: "" },
            { align: "center" },
            { align: "right" },
            { align: "justify" },
          ],
          [{ list: "ordered" }, { list: "bullet" }],
          [{ indent: "-1" }, { indent: "+1" }],
          ["link", "image"],
          ["clean"],
        ],
        vModelTargets: ["方案摘要", "方案內容"],
        inEditing: "方案摘要",
      },
      // cloudinary-vue 雲端圖庫元件的配置或應用資料
      vueCloudinaryData: {
        filesData: {
          avatar: "",
          carouselImgs: [],
        },
        uploadStatus: "",
      },
    };
  },
  props: ["currentManager", "currentPath"],
  created() {
    this.modalData.callBy = this;
    this.$eventBus.$on("emitModalValue", (value) => {
      this.modalData.emitValue = value;
    });
    this.initializeEditor();
  },
  components: {
    Breadcrumb,
    VueEditor,
  },
  methods: {
    // 方法：初始化編輯器內容，以便呈現新增或編輯模式的差異功能
    initializeEditor() {
      const queryAdminAuthAPI = `${process.env.REMOTE_HOST_PATH}/API/Backstage/AdminSignInAuthentication.php`;
      const categoryListAPI = `${process.env.REMOTE_HOST_PATH}/API/Forestage/QueryCategoryList.php`;
      const departureLocationListAPI = `${process.env.REMOTE_HOST_PATH}/API/Backstage/QueryDepartureLocationList.php`;
      const queryCreatingIDAPI = `${process.env.REMOTE_HOST_PATH}/API/Backstage/QueryCreatingID.php`;
      const session = this.GlobalFunctions.getKapitanSession("backstage");
      const vm = this;

      vm.editDetails.projectName = "";
      vm.editDetails.projectPricePerPerson = 0;
      vm.editDetails.projectMinNumOfPeople = 0;
      vm.editDetails.projectMaxNumOfPeople = 0;
      vm.vueCloudinaryData.filesData.avatar = "";
      vm.vueCloudinaryData.filesData.carouselImgs = [];
      vm.editDetails.projectSummary = "";
      vm.editDetails.projectDescription = "";
      vm.editDetails.projectStatus = "0";
      vm.editDetails.projectCategory = "CG0001";
      vm.editDetails.projectDepartureLocation = "LC0001";
      vm.editorPrompt = "";

      if (this.currentPath.indexOf("Project-Creation") == -1)
        this.inCreatingMode = false;
      else this.inCreatingMode = true;

      Promise.all([
        this.$http.get(categoryListAPI),
        this.$http.get(departureLocationListAPI),
        this.$http.post(queryAdminAuthAPI, session),
      ])
        .then((response) => {
          vm.renderData.categoryList = response[0].data;
          vm.renderData.departureLocationList = response[1].data;
          vm.adminLevel = response[2].data.adminLevel;

          if (vm.inCreatingMode) {
            if (vm.adminLevel > 2) vm.editDetails.projectStatus = -1;
            else vm.editDetails.projectStatus = 0;
            return vm.$http.post(queryCreatingIDAPI, vm.currentManager);
          } else {
            vm.managingProjectID = localStorage.getItem("managingProject");
            vm.queryProjectDetails();
          }
        })
        .then((response) => {
          if (vm.inCreatingMode) vm.managingProjectID = response.data;
        });
    },
    // 方法：（演示用）自動填入方案內容
    autofilledProjectContent() {
      this.editDetails.projectStatus = 1;
      this.editDetails.projectName = "台南│這是一個演示方案";
      this.editDetails.projectSummary =
        "<ul><li>這是方案的第一個特點</li><li>這是方案的第二個特點</li><li>這是方案的第三個特點</li></ul>";
      this.editDetails.projectDescription =
        "<p>▲ 這裡放第一張圖</p><p>▲ 這裡放第二張圖</p><p>▲ 這裡放第三張圖</p>";
      this.editDetails.projectPricePerPerson = 1680;
      this.editDetails.projectMinNumOfPeople = 8;
      this.editDetails.projectMaxNumOfPeople = 12;
      this.editDetails.projectCategory = "CG0003";
      this.editDetails.projectDepartureLocation = "LC0002";
      this.editDetails.projectDepartureLocationDescription =
        "<p>台南安平遊艇城，包含水域及陸域共占地12公頃，由會員制的遊艇會、海洋學院、海景餐廳、新加坡知名飯店集團-悅榕集團進駐之渡假村、泊位房產等海洋休閒項目組成，將成為台灣第一個，也是最大的遊艇生活重鎮，其中安平亞果遊艇碼頭的建置更讓許多船主、船艇玩家引頸期盼。</p><p>安平亞果遊艇碼頭位於台灣的第一個城市且充滿人文風情的古都安平，西南部沿岸綿延的海岸線，被港務局評比為最適合興建遊艇碼頭之地，特別是安平亞果遊艇碼頭旁依偎著漁光島，這座突出的小島成為天然的擋浪堤，提供碼頭絕佳的安全性，形成全台灣靜穩度最高的遊艇碼頭。</p>";
    },
    // 方法：向後端查詢欲編輯項目的內容，以利進行雙向綁定編修
    queryProjectDetails() {
      const api = `${process.env.REMOTE_HOST_PATH}/API/Backstage/QueryProjectDetails.php`;
      const vm = this;

      this.$http
        .post(api, vm.managingProjectID)
        .then((response) => {
          vm.editDetails.projectStatus = response.data["PROJECT_STATUS"];
          vm.editDetails.projectName = response.data["PROJECT_NAME"];
          vm.editDetails.projectAvatarPublicID =
            response.data["PROJECT_AVATAR_URL"];
          vm.editDetails.projectSummary = response.data["PROJECT_SUMMARY"];
          vm.editDetails.projectDescription =
            response.data["PROJECT_DESCRIPITION"];
          vm.editDetails.projectPricePerPerson =
            response.data["PROJECT_ORIGINAL_PRICE_PER_PERSON"];
          vm.editDetails.projectMinNumOfPeople =
            response.data["PROJECT_MIN_NUM_OF_PEOPLE"];
          vm.editDetails.projectMaxNumOfPeople =
            response.data["PROJECT_MAX_NUM_OF_PEOPLE"];
          vm.editDetails.projectCategory = response.data["CATEGORY_ID"];
          vm.editDetails.projectDepartureLocation =
            response.data["LOCATION_ID"];
          vm.editDetails.projectDepartureLocationDescription =
            response.data["LOCATION_DESCRIPTION"];
        })
        .catch((error) => {
          console.log(error);
        });
    },
    // 方法：紀錄觀察上傳檔案的內容
    handleFileChange: function (e) {
      if (e.target.id == "方案大頭貼") {
        this.vueCloudinaryData.filesData.avatar = e.target.files[0];
        console.log("上傳檔案更換：", this.vueCloudinaryData.filesData.avatar);
      } else if (e.target.id == "方案輪播圖") {
        this.vueCloudinaryData.filesData.carouselImgs = [];
        for (let i = 0; i < e.target.files.length; i++) {
          this.vueCloudinaryData.filesData.carouselImgs.push(e.target.files[i]);
        }
        console.log(
          "上傳檔案更換：",
          this.vueCloudinaryData.filesData.carouselImgs
        );
      }
    },
    // 方法：透過 axios 將圖檔上傳至指定的 Cloudinary 位置（並旋即啟動更新資料庫的方法）
    uploadImgAndUpdateData() {
      this.$eventBus.$emit("emitModalData", this.modalData);
      this.modalData.situation.buttonType = "checked";

      const cloudinaryUploadAPI = `https://api.cloudinary.com/v1_1/${this.GlobalVariables.cloudName}/upload`;
      const vm = this;

      // 判定是否有選擇圖檔，決定是否要執行上傳雲端的動作（抑或是跳至下一步）
      let avatarFile = this.vueCloudinaryData.filesData.avatar;
      let carouselFiles = this.vueCloudinaryData.filesData.carouselImgs;

      if (avatarFile == "" && !avatarFile.name && carouselFiles.length <= 0) {
        this.updateProjectDetails();
      } else {
        let totalUploadObj = this.vueCloudinaryData.filesData.carouselImgs;

        if (avatarFile != "") {
          let toolArr = [];
          toolArr.push(avatarFile);
          for (let prop in totalUploadObj) {
            toolArr.push(totalUploadObj[prop]);
          }
          totalUploadObj = Object.assign({}, toolArr);
        }

        // 透過檔案名稱定義屬性名稱以做識別
        let newObj = {};

        for (let prop in totalUploadObj) {
          newObj[`${totalUploadObj[prop].size}-${prop}`] = totalUploadObj[prop];
        }

        totalUploadObj = newObj;

        // 透過物件大小與索引作為後續對應行為的識別值
        let objSize = Object.keys(totalUploadObj).length;
        let objIndex = 1;

        for (let prop in totalUploadObj) {
          let fileReader = new FileReader();

          fileReader.addEventListener(
            "load",
            function () {
              let preset = "";
              let symbolIndex = prop.indexOf("-");
              let index = prop.substr(symbolIndex + 1);

              if (avatarFile != "" && index == 0) {
                preset = vm.GlobalVariables.projectAvatarPreset;
              } else {
                preset = vm.GlobalVariables.projectCarouselPreset;
              }

              let formData = new FormData();
              formData.append("upload_preset", preset);
              formData.append("file", fileReader.result);

              let requestObj = {
                url: cloudinaryUploadAPI,
                method: "POST",
                data: formData,
                onUploadProgress: function (progressEvent) {
                  vm.modalData.situation.message = `<p>開始處理上傳事件<p>`;
                  vm.progress = Math.round(
                    (progressEvent.loaded * 100.0) / progressEvent.total
                  );
                  vm.modalData.situation.message = `<p>正在上傳第 ${objIndex} 張圖片，總共有 ${objSize} 張圖片。<p>第 ${objIndex} 張圖片的上傳進度：${vm.progress}％`;
                },
              };

              axios(requestObj)
                .then((response) => {
                  let public_id = response.data.public_id;

                  if (
                    avatarFile != "" &&
                    public_id.indexOf("Projects-Avatar") != -1
                  ) {
                    vm.editDetails.projectAvatarPublicID = public_id;
                  } else {
                    vm.editDetails.projectCarouselImgs.push(response.data);
                  }

                  if (objIndex == objSize) {
                    vm.modalData.situation.event = "圖片上傳成功。";
                    vm.modalData.situation.message = `<p>${objIndex} 張圖片全部上傳成功！</p>`;

                    for (let prop in totalUploadObj) {
                      let symbolIndex = prop.indexOf("-");
                      let size = prop.substr(0, symbolIndex);
                      let index = prop.substr(symbolIndex + 1);

                      for (let file of vm.editDetails.projectCarouselImgs) {
                        if (file.bytes == size) {
                          file.sortIndex = index;
                        }
                      }
                    }

                    vm.editDetails.projectCarouselImgs.sort(function (a, b) {
                      return a.sortIndex - b.sortIndex;
                    });

                    let newArr = vm.editDetails.projectCarouselImgs;
                    vm.editDetails.projectCarouselImgs = [];

                    for (let file of newArr) {
                      vm.editDetails.projectCarouselImgs.push(file.public_id);
                    }

                    vm.updateProjectDetails();
                  } else {
                    vm.modalData.situation.message = `第 ${objIndex} 張圖片上傳成功`;
                  }

                  objIndex++;
                })
                .catch((error) => {
                  console.log(error);
                  vm.modalData.situation.event = "圖片上傳失敗。";
                  vm.modalData.situation.message = `圖片上傳失敗！請稍後再試。`;
                });
            },
            false
          );

          fileReader.readAsDataURL(totalUploadObj[prop]);
        }
      }
    },
    // 方法：將方案更新數據寫入資料庫
    updateProjectDetails() {
      const updateProjectDetailsAPI = `${process.env.REMOTE_HOST_PATH}/API/Backstage/UpdateProjectDetails.php`;
      const createProjectDetailsAPI = `${process.env.REMOTE_HOST_PATH}/API/Backstage/InsertNewProject.php`;
      const vm = this;
      const session = vm.GlobalFunctions.getKapitanSession("backstage");

      let sendingObj = {
        session: session,
        projectID: vm.managingProjectID,
        editedDetails: vm.editDetails,
      };
      let api = "";

      if (this.inCreatingMode) api = createProjectDetailsAPI;
      else api = updateProjectDetailsAPI;

      vm.$http
        .post(api, JSON.stringify(sendingObj))
        .then((response) => {
          vm.modalData.situation.event += "資料庫寫入成功。";
          vm.modalData.situation.message += response.data;
        })
        .catch((error) => {
          vm.modalData.situation.event += "資料庫寫入失敗。";
          vm.modalData.situation.message += error.data;
        })
        .then(() => {
          vm.initializeEditor();
        });
    },
    // 方法：向提示視窗送刪除方案事件
    deleteProject() {
      this.$eventBus.$emit("emitModalData", this.modalData);

      this.modalData.situation.event = "刪除方案";
      this.modalData.situation.message = `確定要刪除 ${this.managingProjectID} 專案嗎？`;
      this.modalData.situation.buttonType = "yesNo";
    },
    // 方法：自定義 vue2-editor 文本編輯器的圖片上傳方式，改為 Cloudinary 的上傳方式
    uploadContentImg(file, editor, cursorLocation) {
      const cloudinaryUploadAPI = `https://api.cloudinary.com/v1_1/${this.GlobalVariables.cloudName}/upload`;
      const vm = this;

      vm.editorPrompt = "";
      vm.editorPrompt = "編輯器圖片上傳器已啟動";

      let formData = new FormData();
      formData.append("upload_preset", vm.GlobalVariables.projectContentPreset);
      formData.append("file", file);

      let requestObj = {
        url: cloudinaryUploadAPI,
        method: "POST",
        data: formData,
        onUploadProgress: function (progressEvent) {
          vm.editorPrompt = `正在處理：${progressEvent}`;
          vm.progress = Math.round(
            (progressEvent.loaded * 100.0) / progressEvent.total
          );
          vm.editorPrompt = `編輯器圖片上傳進度：${vm.progress}％`;
        },
      };

      axios(requestObj)
        .then((response) => {
          vm.editorPrompt = "編輯器圖片上傳成功！";
          let url = response.data.secure_url;
          editor.insertEmbed(cursorLocation, "image", url);
        })
        .catch((error) => {
          vm.editorPrompt = "編輯器圖片上傳失敗！";
          console.log(error);
        });
    },
  },
  computed: {
    // 計算（方法）：返回提示視窗的選擇植
    returnModalEmitValue() {
      return this.modalData.emitValue;
    },
  },
};
</script>

<style lang="scss" scoped>
@import "../../../assets/scss/all.scss";

#rechoose-mode-link {
  a {
    color: black;
    &:hover {
      opacity: 0.7;
    }
  }
}

#project-details-page {
  height: 600px;
  .breadcrumb {
    padding: 0;
  }
  .query-resultsTable {
    tr {
      th {
        font-size: 14px;
      }
      td {
        font-size: 12px;
      }
    }
    .order-item {
      cursor: pointer;
    }
  }
}
.custom-file-label {
  &::after {
    content: "開啟";
  }
}
.quillWrapper {
  position: relative;
  height: 200px;
  .ql-toolbar {
    position: -webkit-sticky;
    position: sticky;
    top: 0;
    z-index: 2;
    background-color: #fff;
  }
}
h6 {
  font-weight: 600;
  .delete-project-btn {
    font-size: 13px;
    a {
      font-weight: 400;
      color: darkred;
    }
  }
}
#editor-prompt {
  font-size: 14px;
}
.editingHTMLlist {
  li {
    font-weight: 500;
    list-style: none;
    cursor: pointer;
    .in-editing-arrow {
      width: 20px;
      height: 20px;
    }
  }
}
.action-buttons-block {
  a {
    font-size: 14px;
    color: black;
    &:hover {
      color: red;
    }
  }
}
</style>