<template>
  <b-card title="สรุปรายการเล่น">
    <div class="custom-search">
      <!-- advance search input -->
      <b-row class="align-items-end">
        <b-col md="3">
          <b-form-group>
            <label for="password">ประเภทหวย</label>
            <v-select
              v-model="filter.typeHuay"
              :options="optionsTypeHuay"
              label="lotto_type_name"
              placeholder="ประเภทหวย"
              :clearable="true"
              @input="getData()"
            />
          </b-form-group>
        </b-col>
        <b-col cols="12" md="3">
          <b-form-group>
            <h5 class="font-weight-bold">วันที่</h5>
            <flat-pickr
              v-model="form.startDate"
              class="form-control"
              @input="getData()"
              :config="{
                dateFormat: 'Y-m-d',
                mode: 'range',
                defaultDate: [new Date()],
              }"
            />
          </b-form-group>
        </b-col>
        <b-col md="3">
          <b-form-group>
            <b-button
              v-ripple.400="'rgba(255, 255, 255, 0.15)'"
              type="button"
              variant="success"
              class="float-left"
              @click="handleSearch"
            >
              <feather-icon icon="SearchIcon" class="mr-50" />
              <span class="align-middle">ค้นหา</span>
            </b-button>
          </b-form-group>
        </b-col>
      </b-row>
    </div>
    <b-overlay
      :show="OverlayFlag"
      spinner-variant="primary"
      spinner-type="grow"
      spinner-small
      rounded="sm"
    >
      <div style="overflow-x: auto; padding: 0">
        <hr />

        <table class="table table-bordered">
          <thead>
            <tr class="text-center">
              <th colspan="12">
                สรุปผลหวย
                {{ filter.typeHuay ? filter.typeHuay.lotto_type_name : "" }}
              </th>
            </tr>
          </thead>
          <thead>
            <tr class="text-center">
              <th colspan="2">วิ่งบน</th>
              <th colspan="2">วิ่งล่าง</th>
              <th colspan="2">2 ตัวบน</th>
              <th colspan="2">2 ตัวล่าง</th>
              <th colspan="2">3 ตัวบน</th>
              <th colspan="2">3 ตัวโต๊ด</th>
            </tr>
          </thead>
          <thead>
            <tr class="text-center">
              <th style="padding: 5px; background-color: gold">
                <b><span class="text-header">ทั้งหมด</span></b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header text-right"
              >
                <b>{{ numberWithCommas(totalSummary["วิ่งบน"]) }}</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header"
              >
                <b>ทั้งหมด</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header text-right"
              >
                <b>{{ numberWithCommas(totalSummary["วิ่งล่าง"]) }}</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header"
              >
                <b>ทั้งหมด</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header text-right"
              >
                <b>{{ numberWithCommas(totalSummary["2 ตัวบน"]) }}</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header"
              >
                <b>ทั้งหมด</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header text-right"
              >
                <b>{{ numberWithCommas(totalSummary["2 ตัวล่าง"]) }}</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header"
              >
                <b>ทั้งหมด</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header text-right"
              >
                <b>{{ numberWithCommas(totalSummary["3 ตัวบน"]) }}</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header"
              >
                <b>ทั้งหมด</b>
              </th>
              <th
                style="padding: 5px; background-color: gold"
                class="text-header text-right"
              >
                <b>{{ numberWithCommas(totalSummary["3 ตัวโต๊ด"]) }}</b>
              </th>
            </tr>
          </thead>
          <thead>
            <tr class="text-center">
              <th style="padding: 5px">ตัวเลข</th>
              <th style="padding: 5px">ถือเลข</th>
              <th style="padding: 5px">ตัวเลข</th>
              <th style="padding: 5px">ถือเลข</th>
              <th style="padding: 5px">ตัวเลข</th>
              <th style="padding: 5px">ถือเลข</th>
              <th style="padding: 5px">ตัวเลข</th>
              <th style="padding: 5px">ถือเลข</th>
              <th style="padding: 5px">ตัวเลข</th>
              <th style="padding: 5px">ถือเลข</th>
              <th style="padding: 5px">ตัวเลข</th>
              <th style="padding: 5px">ถือเลข</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(row, index) in sortedData" :key="index">
              <td style="text-align: center; padding: 5px">
                <b>{{ row["วิ่งบน"].number }}</b>
              </td>
              <td style="text-align: end; padding: 5px">
                <b>{{ numberWithCommas(row["วิ่งบน"].total) }}</b>
              </td>
              <td style="text-align: center; padding: 5px">
                <b>{{ row["วิ่งล่าง"].number }}</b>
              </td>
              <td style="text-align: end; padding: 5px">
                <b>{{ numberWithCommas(row["วิ่งล่าง"].total) }}</b>
              </td>
              <td style="text-align: center; padding: 5px">
                <b>{{ row["2 ตัวบน"].number }}</b>
              </td>
              <td style="text-align: end; padding: 5px">
                <b>{{ numberWithCommas(row["2 ตัวบน"].total) }}</b>
              </td>
              <td style="text-align: center; padding: 5px">
                <b>{{ row["2 ตัวล่าง"].number }}</b>
              </td>
              <td style="text-align: end; padding: 5px">
                <b>{{ numberWithCommas(row["2 ตัวล่าง"].total) }}</b>
              </td>
              <td style="text-align: center; padding: 5px">
                <b>{{ row["3 ตัวบน"].number }}</b>
              </td>
              <td style="text-align: end; padding: 5px">
                <b>{{ numberWithCommas(row["3 ตัวบน"].total) }}</b>
              </td>
              <td style="text-align: center; padding: 5px">
                <b>{{ row["3 ตัวโต๊ด"].number }}</b>
              </td>
              <td style="text-align: end; padding: 5px">
                <b>{{ numberWithCommas(row["3 ตัวโต๊ด"].total) }}</b>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </b-overlay>
  </b-card>
</template>

<script>
import {
  BPagination,
  BFormGroup,
  BFormSelect,
  BDropdownItem,
  BDropdown,
  BRow,
  BCol,
  BButton,
  BCard,
  BFormInput,
  BModal,
  BFormTextarea,
  BForm,
  BOverlay,
} from "bootstrap-vue";
import { VueGoodTable } from "vue-good-table";
import Ripple from "vue-ripple-directive";
import store from "@/store/index";
import { HTTP } from "@/services/http-common";
import ToastificationContent from "@core/components/toastification/ToastificationContent.vue";
import { BFormCheckbox } from "bootstrap-vue";
import vSelect from "vue-select";
import { ValidationProvider, ValidationObserver } from "vee-validate";
import { required, integer } from "@validations";
import moment from "moment";
import flatPickr from "vue-flatpickr-component";

export default {
  components: {
    VueGoodTable,
    BPagination,
    BFormGroup,
    BFormInput,
    BFormSelect,
    BDropdownItem,
    BDropdown,
    BCol,
    BRow,
    BButton,
    BCard,
    BOverlay,
    BFormCheckbox,
    BModal,
    BFormTextarea,
    BForm,
    vSelect,
    ValidationProvider,
    ValidationObserver,
    flatPickr,
  },
  directives: {
    Ripple,
  },
  data() {
    return {
      modelId: this.$route.params.modelId,
      log: [],
      OverlayFlag: false,
      currentPage: 1,
      pageLength: 10,
      totalRecords: 0,
      sort: [
        {
          field: "",
          sort: "",
        },
      ],
      dir: false,
      pages: ["5", "10", "25"],
      rows: [],
      option: [],
      searchTerm: "",
      filter: {
        phone: "",
        lotto_type_id: "",
      },
      form: {
        prize3top: "",
        prize2bottom: "",
        typeHuay: "",
        startDate: moment(new Date()).format("YYYY-MM-DD"),
        endDate: moment(new Date()).format("YYYY-MM-DD"),
        type: "",
        type3top: "3 ตัวบน",
        type2bottom: "2 ตัวล่าง",
      },
      optionsType: ["3 ตัวบน", "3 ตัวโต๊ด", "2 ตัวบน", "2 ตัวล่าง"],
      optionsTypeHuay: [],
      optionsStatus: [{ code: "suc", msg: "ถูกรางวัล" }],
      required,
      integer,
      dataCredit: "",
      dataDisCredit: "",
      max: 3,
      detail: [],
      sumAll: "",
      grandTotal: "",
      OverlayFlag: false,
    };
  },
  computed: {
    direction() {
      if (store.state.appConfig.isRTL) {
        this.dir = true;
        return this.dir;
      }
      this.dir = false;
      return this.dir;
    },
    // mergedRows() {
    //   let grouped = {};

    //   this.rows.forEach((item) => {
    //     if (!grouped[item.number]) {
    //       grouped[item.number] = { number: item.number, total: 0 };
    //     }
    //     console.log(grouped, "grouped");
    //     grouped[item.number][item.type_option] = item;
    //     grouped[item.number].total += item.total;
    //   });
    //   return Object.values(grouped).sort((a, b) => b.total - a.total);
    // },
    sortedData() {
      let categories = [
        "วิ่งบน",
        "วิ่งล่าง",
        "2 ตัวบน",
        "2 ตัวล่าง",
        "3 ตัวบน",
        "3 ตัวโต๊ด",
      ];

      // จัดกลุ่มข้อมูลตามประเภท
      let groupedData = {};
      categories.forEach((category) => {
        groupedData[category] = [];
      });

      this.rows.forEach((item) => {
        if (groupedData[item.type_option]) {
          groupedData[item.type_option].push(item);
        }
      });

      // สร้างข้อมูลตารางใหม่ให้แต่ละประเภทมีจำนวนแถวเท่ากัน
      let maxRows = Math.max(
        ...Object.values(groupedData).map((arr) => arr.length)
      );

      let tableData = [];
      for (let i = 0; i < maxRows; i++) {
        let row = {};
        categories.forEach((category) => {
          row[category] = groupedData[category][i] || { number: "", total: "" };
        });
        tableData.push(row);
      }

      return tableData;
    },
    totalSummary() {
      let totals = {
        วิ่งบน: 0,
        วิ่งล่าง: 0,
        "2 ตัวบน": 0,
        "2 ตัวล่าง": 0,
        "3 ตัวบน": 0,
        "3 ตัวโต๊ด": 0,
      };

      this.rows.forEach((item) => {
        if (totals[item.type_option] !== undefined) {
          totals[item.type_option] += item.total;
        }
      });

      return totals;
    },
    startDate() {
      return this.form.startDate
        ? this.form.startDate.split(" to ")[0]
        : this.form.endDate;
    },
    endDate() {
      return this.form.startDate
        ? this.form.startDate.split(" to ")[1]
        : this.form.endDate;
    },
  },
  created() {
    this.getData();
  },
  mounted() {
    this.fetchTypeHuayAll();
  },
  methods: {
    getData() {
      this.OverlayFlag = true;
      HTTP.get("api/admin/get-number-buy/lotto-play-total", {
        params: {
          lotto_type_id:
            this.filter.typeHuay != null
              ? this.filter.typeHuay.lotto_type_id
              : undefined,
          date: this.startDate,
          endDate: this.endDate ? this.endDate : this.startDate,
        },
      })
        .then((response) => {
          this.rows = response.data.data;
          this.OverlayFlag = false;
        })
        .catch((error) => {
          this.$toast({
            component: ToastificationContent,
            position: "top-right",
            props: {
              title: "แจ้งเตือน",
              text:
                error.response.data.msg !== undefined
                  ? error.response.data.msg
                  : error.status,
              icon: "XCircleIcon",
              variant: "danger",
            },
          });
          this.OverlayFlag = false;
        });
    },
    fetchTypeHuayAll() {
      HTTP.get("api/lotto-type/all", {})
        .then((response) => {
          this.option = response.data.data;
          this.optionsTypeHuay = response.data.data;
        })
        .catch((error) => {
          this.$toast({
            component: ToastificationContent,
            position: "top-right",
            props: {
              title: "แจ้งเตือน",
              text:
                error.response.data.msg !== undefined
                  ? error.response.data.msg
                  : error.status,
              icon: "XCircleIcon",
              variant: "danger",
            },
          });
        });
    },
    handleSearch() {
      this.getData();
    },
    showToast(text, variant) {
      this.$toast({
        component: ToastificationContent,
        position: "top-right",
        props: {
          title: "แจ้งเตือน...",
          text: text,
          icon: variant === "success" ? "CheckIcon" : "BellIcon",
          variant: variant,
        },
      });
    },
    numberWithCommas(x) {
      let sum = 0;
      if (x) {
        sum = x;
      }
      return x
        ? parseFloat(sum)
            .toFixed(2)
            .toString()
            .replace(/\B(?=(\d{3})+(?!\d))/g, ",")
        : "";
    },
    formatDate(date) {
      return moment(date).format("DD-MM-YYYY HH:mm:ss");
    },
  },
};
</script>
<style lang="scss">
@import "@core/scss/vue/libs/vue-good-table.scss";
@import "@core/scss/vue/libs/vue-flatpicker.scss";
</style>
<style lang="scss">
@import "@core/scss/vue/libs/vue-select.scss";
</style>
<style scoped>
.text-header {
  font-weight: bold;
  font-size: 14px;
}
</style>
