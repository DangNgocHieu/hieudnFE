<template>
  <div>
    <a-breadcrumb :style="'margin-bottom:10px'">
      <a-breadcrumb-item
        ><a @click="$router.push('/invest')">Đầu tư</a></a-breadcrumb-item
      >
      <a-breadcrumb-item
        ><a @click="$router.push('/invest/recharge')"
          >Nạp tiền</a
        ></a-breadcrumb-item
      >
      <a-breadcrumb-item
        ><a @click="$router.push('/invest/recharge/customize')"
          >Các gói linh hoạt</a
        ></a-breadcrumb-item
      >
      <a-breadcrumb-item
        ><a
          @click="$router.push(`/invest/recharge/customize/${dataDetail?.id}`)"
          >{{ dataDetail?.id }}</a
        ></a-breadcrumb-item
      >
    </a-breadcrumb>
    <div class="invest-customize-block">
      <div class="invest_customize_detail">
        <br />
        <div class="invest_customize_detail--title">
          {{ dataDetail?.name }}
        </div>
        <br />
        <img :src="dataDetail?.avatar" class="customize-detail-img" />
        <br />
        <br />
        <div class="investment">
          <div class="">
            <div class="">
              <div>
                {{
                  dataDetail?.investment_amount
                    ? dataDetail?.investment_amount
                    : "0"
                }}
                đ
              </div>
            </div>
            <br />
            <div class="investment_amount">
              <div class="text-medium">
                <a-icon
                  @click="showModal('nap')"
                  type="plus"
                  style="
                    padding: 0.8rem;
                    font-size: 1.2rem;
                    background-color: rgb(50, 198, 16);
                    border-radius: 50%;
                    color: rgb(255, 255, 255);
                    border: 1.5px solid rgb(50, 198, 16);
                    cursor: pointer;
                  "
                />
                <div>Nạp tiền</div>
              </div>
              <div class="text-medium" v-if="dataDetail?.investment_amount">
                <a-icon
                  @click="showModal('rut')"
                  type="arrow-down"
                  style="
                    padding: 0.8rem;
                    font-size: 1.2rem;
                    background-color: rgb(50, 198, 16);
                    border-radius: 50%;
                    color: rgb(255, 255, 255);
                    border: 1.5px solid rgb(50, 198, 16);
                    cursor: pointer;
                  "
                />
                <p>Rút tiền</p>
              </div>
            </div>
            <br />
            <br />
          </div>
        </div>
      </div>
      <div class="invest_customize_detail">
        <br />
        <div class="invest_customize_detail--title">Chi tiết phân bổ</div>
        <div>
          <canvas id="myChart"></canvas>
        </div>
        <br />
      </div>
      <!-- <div class="invest_customize_detail">
        <br />
        <div class="invest_customize_detail--title">Lịch sử giao dịch</div>
        <a-table
          :dataSource="dataDetail?.transactions"
          :columns="columns"
          class="table-history"
        >
          <span
            style="font-weight: bold"
            slot="created_at"
            slot-scope="created_at"
            >{{ formatDate(created_at) }}</span
          >
          <div style="font-weight: bold" slot="price" slot-scope="price">
            {{
              price?.toString()?.replace(/\B(?=(\d{3})+(?!\d))/g, ",")
                ? price?.toString()?.replace(/\B(?=(\d{3})+(?!\d))/g, ",")
                : 0
            }}
            đ
          </div>
          <div style="font-weight: bold" slot="volume" slot-scope="volume">
            {{ volume ? volume : "0" }}
          </div>
        </a-table>
      </div> -->
    </div>

    <a-modal
      v-model="isOpenModal"
      :title="typeModal == 1 ? 'Nạp tiền đầu tư' : 'Rút tiền đầu tư'"
      @ok="handleSubmit()"
      :okText="
        typeModal == 1 ? 'Nạp tiền' : step == 3 ? 'Xác nhận' : 'Rút tiền'
      "
      cancelText="Hủy"
      @cancel="handleCancel"
      :ok-button-props="{ props: { disabled: disableMoney } }"
    >
      <div v-if="step === 1">
        <a-form-model ref="ruleForm" :model="formMoney" :rules="rules">
          <a-form-model-item
            label="Số tiền"
            ref="moneyAmount"
            prop="moneyAmount"
          >
            <a-input name="moneyAmount" v-model="moneyAmount" />
          </a-form-model-item>
        </a-form-model>
      </div>
      <div v-if="step === 2" class="main-modal">
        <div class="info-bank">
          <img
            src="../../../../assets/images/logo-acb.png"
            alt="ACB"
            width="150"
            height="150"
          />
          <div class="info-bank__info">
            <div class="info-bank__block">
              <div>CHỦ TÀI KHOẢN:</div>
              <div>
                <b>{{ dataInfoBank.beneficiary_name }}</b>
              </div>
            </div>
            <div class="info-bank__block">
              <div>SỐ TÀI KHOẢN:</div>
              <div>
                <b>{{ dataInfoBank.account_number }}</b>
              </div>
            </div>
            <div class="info-bank__block">
              <div>NỘI DUNG CHUYỂN KHOẢN:</div>
              <div>
                <b>{{ dataInfoBank.reference_number }}</b>
              </div>
            </div>
            <div class="info-bank__block">
              <div>SỐ TIỀN CHUYỂN:</div>
              <div>
                <b>{{ dataInfoBank.transfer_amount }}đ</b>
              </div>
            </div>
          </div>
        </div>
        <div class="info-bank">
          <b class="info-bank__title">Quét mã thanh toán QR</b>
        </div>
        <div class="info-bank">
          <img
            :src="dataInfoBank.qr"
            class="info-bank__qr"
            width="{150}"
            height="{150}"
            alt="qr-payment"
          />
        </div>
      </div>
      <template slot="footer" v-if="step == 2">
        <a-button key="back" @click="handleCancel"> Đóng </a-button>
      </template>
      <div v-if="step === 3" class="modal-bank">
        <div :style="'padding-bottom:10px'">Chọn ngân hàng</div>
        <a-select @change="handleSelectBank" style="width: 100%">
          <a-select-option v-for="(el, key) in list_bank" :key="key">
            {{ el.label }}
          </a-select-option>
        </a-select>

        <br />
        <br />
        <div :style="'padding-bottom:10px'">Số tài khoản</div>
        <a-input
          v-model="account_id"
          placeholder="Vui lòng nhập số tài khoản"
        />
        <br />
        <br />
        <div v-if="namePeopleBank">
          <div>Chủ tài khoản</div>
          <a-input v-model="namePeopleBank" :disabled="true" />
        </div>
      </div>
      <div v-if="step === 4" class="modal-password">
        <div>Nhập mật khẩu để tiến hành rút tiền</div>
        <a-input v-model="password" type="password"></a-input>
      </div>
    </a-modal>
  </div>
</template>

<script>
import { mapFields } from "vuex-map-fields";
import { Chart, registerables } from "chart.js";
import generate from "../../../../mixins/generate";
Chart.register(...registerables);

export default {
  mixins: [generate],
  props: {},
  components: {},
  computed: {
    ...mapFields({
      dataInfoBank: "customize.dataInfoBank",
      dataCustomize: "customize.dataCustomize",
      dataDetail: "customize.dataDetail",
      isOpenModal: "customize.isOpenModal",
      formMoney: "customize.formMoney",
      moneyAmount: "customize.formMoney.moneyAmount",
      step: "customize.step",
      typeModal: "customize.typeModal",
      bank_id: "customize.bank_id",
      account_id: "customize.account_id",
      namePeopleBank: "customize.namePeopleBank",
      password: "customize.password",
    }),
    disableMoney() {
      const regex = /^\d+$/;
      return this.typeModal == 1
        ? !regex.test(this.moneyAmount) || !this.moneyAmount
        : this.step == 3
          ? !this.bank_id || !this.account_id
          : !regex.test(this.moneyAmount) || !this.moneyAmount;
    },
  },
  data() {
    return {
      list_bank: [
        {
          label: "Ngân hàng TMCP Ngoại thương Việt Nam (Vietcombank)",
          value: "970436",
        },
        {
          label: "Ngân hàng TMCP Kỹ Thương Việt Nam (Techcombank)",
          value: "970407",
        },
        {
          label: "Ngân hàng TMCP Tiên Phong (Tpbank)",
          value: "970423",
        },
        {
          label: "Ngân hàng TMCP Á Châu",
          value: "970416",
        },
      ],

      columns: [
        {
          title: "Ngày giao dịch",
          dataIndex: "created_at",
          key: "created_at ",
          scopedSlots: { customRender: "created_at" },
        },
        {
          title: "Số tham chiếu",
          dataIndex: "transaction_id",
          key: "transaction_id",
        },
        {
          title: "Số tiền giao dịch",
          dataIndex: "price",
          key: "price",
          scopedSlots: { customRender: "price" },
        },
        {
          title: "Số lượng CCQ",
          dataIndex: "volume",
          key: "volume",
          scopedSlots: { customRender: "volume" },
        },
      ],
      dataChart: [],
      labelsChart: [],
      rules: {
        moneyAmount: [
          {
            required: true,
            message:
              this.typeModal == 1
                ? "Vui lòng nhập số tiền nạp"
                : "Vui lòng nhập số tiền rút",
            trigger: "blur",
          },
          {
            pattern: /^\d+$/,
            message: "Vui lòng nhập số",
            trigger: ["blur", "change"],
          },
        ],
      },
    };
  },

  async mounted() {
    this.createChart();
    this.$store.commit("SET_LOADING", true);
    const res = await this.$axios.get(
      `laravel/packages/${this.$route.params.id}`,
    );
    if (res) {
      this.$store.commit("SET_LOADING", false);
      this.$store.commit("customize/SET_DATA_DETAIL", res?.data?.data);
      res?.data?.data?.allocation?.forEach((el) => {
        this.dataChart.push(el.percentage);
        this.labelsChart.push(el.code);
      });
      this.myChart.update();
    }
  },
  methods: {
    createChart() {
      var ctx = document.getElementById("myChart");
      this.myChart = new Chart(ctx, {
        type: "doughnut",
        data: {
          labels: this.labelsChart,
          datasets: [
            {
              data: this.dataChart,
              backgroundColor: [
                "rgb(255, 99, 132)",
                "rgb(54, 162, 235)",
                "rgb(255, 205, 86)",
              ],
              hoverOffset: 4,
            },
          ],
        },
        options: {
          scales: {
            x: {
              display: false,
            },
          },
        },
      });
      // this.myChart.resize(100, 200);
    },
    showModal(type) {
      if (type == "nap") {
        this.$store.commit("customize/SET_TYPE_MODAL", 1);
      } else {
        this.$store.commit("customize/SET_TYPE_MODAL", 2);
      }
      this.$store.commit("customize/SET_IS_OPEN_MODAL", !this.isOpenModal);
    },
    handleOk() {},
    async handleSubmit(type) {
      //typemodal == 1 => nap
      if (this.step == 1 && this.typeModal == 2) {
        this.step = 3;
        this.$store.commit("customize/SET_TYPE_MODAL", 3);
        return;
      }
      if (this.step === 3 && this.typeModal === 3) {
        this.$store.commit("SET_LOADING", true);
        try {
          const res = await this.$axios.get(
            `/laravel/banks/${this.bank_id}/${this.account_id}`,
          );
          if (res?.data?.data) {
            this.$store.commit(
              "customize/SET_NAME_PEOPLE_BANK",
              res.data.data.name,
            );
            this.$store.commit("SET_LOADING", false);
            //confirm password

            setTimeout(() => {
              this.step = 4;
              this.typeModal = 4;
            }, 3000);
          } else {
            this.$store.commit("SET_LOADING", false);
          }
        } catch (error) {
          this.openNotificationWithIcon(
            "error",
            error?.response?.data?.message,
          );
          this.$store.commit("SET_LOADING", false);
        }
      }
      if (this.step === 4) {
        this.$store.commit("SET_LOADING", true);
        try {
          const res = await this.$axios.post(
            "/laravel/auth/user/confirm-password",
            { password: this.password },
          );

          if (res?.data.status == 201) {
            await this.onWithdrawMoney();

            this.$store.commit("SET_LOADING", false);
          } else {
            this.$store.commit("SET_LOADING", false);
          }
        } catch (error) {
          this.$store.commit("SET_LOADING", false);
        }
      }
      if (this.typeModal == 1) {
        this.$store.commit("SET_LOADING", true);
        try {
          const res = await this.$axios.post(
            `laravel/packages/${this.$route.params.id}/invest`,
            {
              amount: this.moneyAmount,
            },
          );
          if (res.data.data) {
            this.step = 2;
            this.dataInfoBank = res.data.data;
            this.$store.commit("SET_LOADING", false);
          } else {
            this.$store.commit("SET_LOADING", false);
          }
        } catch (error) {
          this.$store.commit("SET_LOADING", false);
        }
      }
      //call api rut tien
    },
    async onWithdrawMoney() {
      try {
        const res = await this.$axios.post(
          `/laravel/packages/${this.$route.params.id}/withdraw`,
          {
            bank_id: this.bank_id,
            bank_account_id: this.account_id,
            amount: this.formMoney.moneyAmount,
          },
        );
        if (res) {
          this.openNotificationWithIcon(
            "success",
            "Bạn đã tạo lệnh rút tiền thành công!",
          );
          this.$store.commit("customize/SET_IS_OPEN_MODAL", false);
        }
      } catch (error) {
        this.openNotificationWithIcon("error", error?.response?.data.message);
      }
    },
    handleCancel() {
      this.$store.commit("customize/SET_IS_OPEN_MODAL", false);
      this.$store.commit("customize/SET_STEP", 1);
      this.$store.commit("customize/SET_MONEY_AMOUNT", null);
      this.$store.commit("customize/SET_TYPE_MODAL", 0);
      this.$store.commit("customize/SET_BANK_ID", "");
      this.$store.commit("customize/SET_ACCOUNT_ID", "");
      this.$store.commit("customize/SET_NAME_PEOPLE_BANK", "");
      this.$store.commit("customize/SET_PASSWORD", "");
    },
    handleSelectBank(value) {
      this.bank_id = this.list_bank[value].value;
    },
  },
};
</script>

<style lang="scss" scoped>
.customize-detail-img {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  border: 1px solid #ddd;
}
.invest_customize_detail {
  margin-bottom: 20px;
  background-color: #fff;
  display: flex;
  flex-direction: column;
  align-items: center;
  .text-medium {
    font-weight: 600;
    display: flex;
    flex-direction: column;
    padding-right: 15px;
    width: 65px;
  }
  .table-history {
    width: 100%;
  }
  .invest_customize_detail--title {
  }
}
.info-bank {
  display: flex;
  flex-direction: row;
  margin-bottom: 20px;
}
.info-bank__info {
  .info-bank__block {
    display: flex;
  }
}
.info-bank__info {
  width: 100%;
  padding-left: 20px;
  .info-bank__block {
    padding-bottom: 15px;
  }
}
b {
  padding-left: 10px;
}
.main-modal {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.investment {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}
.investment_amount {
  display: flex;
  flex-direction: row;
}
</style>
