<template>
  <div>
    <div class="header-history">
      <h1>Quản lý người dùng</h1>
      <ManagerUsersFilters
        :filter="filter"
        @search="handleSearch"
        @add="openModal"
        @reload="handleSearch"
      />
      <a-modal :footer="null" v-model="visible" title="Thêm người dùng">
        <ManagerUsersCreateForm ref="addForm" @handleSubmit="handleAddUser" />
      </a-modal>
    </div>
    <a-table
      :columns="columns"
      :data-source="data"
      :row-selection="rowSelection"
      :pagination="pagination"
      :loading="loading"
      :style="'background-color:#fff'"
    >
      <template slot="key" slot-scope="record, text, index">
        {{ (pagination?.current_page - 1) * pagination?.per_page + index + 1 }}
      </template>
      <a slot="name" slot-scope="text">{{ text }}</a>
      <a slot="role" slot-scope="role"
        ><a-tag :color="getRole(role).color">
          {{ getRole(role).text }}
        </a-tag>
      </a>
      <a slot="status" slot-scope="data"
        ><a-tag :color="getStatus(data?.is_activated).color">
          {{ getStatus(data?.is_activated).text }}
        </a-tag>
      </a>
      <a slot="ekyc_status" slot-scope="is_verify"
        ><a-tag :color="getStatusKyc(is_verify).color">
          {{ getStatusKyc(is_verify).text }}
        </a-tag>
      </a>
      <span slot="created_at" slot-scope="created_at"
        >{{ moment(created_at).format("DD-MM-YYYY") }}
      </span>
      <a slot="actions" slot-scope="data">
        <div>
          <a-button type="primary" @click="redirectDetail(data)">
            Chi tiết
          </a-button>

          <a-popconfirm
            title="Bạn có muốn xóa người dùng này không?"
            ok-text="Xóa"
            cancel-text="Hủy"
            okType="danger"
            @confirm="confirmDelete(data)"
            @cancel="cancel"
          >
            <a-button type="danger"> Xóa </a-button>
          </a-popconfirm>

          <a-popconfirm
            :title="`Bạn có muốn ${data?.is_activated ? 'hủy kích hoạt' : 'kích hoạt'} tài khoản này không?`"
            ok-text="Xác nhận"
            cancel-text="Hủy"
            :okType="data?.is_activated ? 'danger' : 'primary'"
            @confirm="handleUpdateStatus(data)"
            @cancel="cancel"
          >
            <a-button :type="data?.is_activated ? 'danger' : 'primary'" ghost>
              {{ data?.is_activated ? "Hủy kích hoạt" : "Kích hoạt" }}
            </a-button>
          </a-popconfirm>
        </div></a
      >
    </a-table>
  </div>
</template>
<script>
import moment from "moment";
const columns = [
  {
    title: "STT",
    key: "key",
    width: 80,
    scopedSlots: { customRender: "key" },
  },
  {
    title: "Email",
    width: 240,
    dataIndex: "email",
    key: "email",
  },
  {
    title: "Họ và tên",
    width: 180,
    dataIndex: "name",
    key: "name",
  },
  {
    title: "Phân quyền",
    dataIndex: "role",
    key: "role",
    width: 160,
    scopedSlots: { customRender: "role" },
  },
  {
    title: "Trạng thái tài khoản",
    key: "status",
    scopedSlots: { customRender: "status" },
    width: 160,
  },
  {
    title: "Xác minh eKYC",
    dataIndex: "is_verify",
    key: "ekyc_status",
    scopedSlots: { customRender: "ekyc_status" },
    width: 160,
  },
  {
    title: "Ngày tạo tài khoản",
    dataIndex: "created_at",
    key: "created_at",
    scopedSlots: { customRender: "created_at" },
    width: 200,
  },
  {
    title: "",
    key: "actions",
    ellipsis: true,
    scopedSlots: { customRender: "actions" },
  },
];

const data = [];

const rowSelection = {
  onChange: (selectedRowKeys, selectedRows) => {
    console.log(
      `selectedRowKeys: ${selectedRowKeys}`,
      "selectedRows: ",
      selectedRows,
    );
  },
  onSelect: (record, selected, selectedRows) => {},
  onSelectAll: (selected, selectedRows, changeRows) => {},
};

export default {
  data() {
    return {
      data,
      columns,
      filter: { sort_by: "desc", per_page: 10 },
      rowSelection,
      loading: false,
      pagination: {},
      visible: false,
    };
  },
  mounted() {
    this.getListUsers();
  },
  methods: {
    moment,
    getStatus(status) {
      return {
        color: !status ? "orange" : "green",
        text: !status ? "Đang chờ" : "Đã xác nhận",
      };
    },
    getStatusKyc(status) {
      return {
        color: !status ? "orange" : "green",
        text: !status ? "Đang chờ" : "Đã xác nhận",
      };
    },
    getRole(role) {
      return {
        color: role === 0 ? "orange" : "green",
        text: role === 0 ? "User" : "Admin",
      };
    },
    handleChange(value) {
      this.filter = value;
    },
    redirectDetail(data) {
      this.$store.commit("admin/SET_DETAIL", data);
      this.$router.push(`/admin/manager-users/${data.id}`);
    },
    async confirmDelete(data) {
      try {
        const response = await this.$axios.delete(
          `laravel/admin/users/${data.id}`,
        );
        if (response.data.data) {
          this.getListUsers();
          this.$message.success("Đã xóa người dùng.");
        }
      } catch (error) {
        this.$message.error("Xóa người dùng thất bại.");
      }
    },
    async getListUsers() {
      this.loading = true;
      try {
        const response = await this.$axios.get("laravel/admin/users", {
          params: this.filter,
        });
        if (response) {
          this.data = response?.data?.data?.data;
          const { current_page, from, last_page, per_page, total } =
            response?.data?.data;
          this.pagination = { current_page, from, last_page, per_page, total };
        }
      } catch (error) {
        this.$message.error(error?.response?.data?.message);
      } finally {
        this.loading = false;
      }
    },
    handleSearch() {
      this.getListUsers(this.filter);
    },
    openModal() {
      this.visible = true;
    },
    async handleAddUser(form) {
      this.loading = true;
      try {
        const response = await this.$axios.post("laravel/admin/users", form);
        if (response.data.data) {
          this.$message.success("Thêm người dùng thành công.");
          this.getListUsers();
        }
      } catch (error) {
        this.$message.error(
          error?.response?.data?.message || "Thêm người dùng thất bại.",
        );
      } finally {
        this.loading = false;
        this.visible = false;
        this.$refs.addForm.resetForm();
      }
    },
    async handleUpdateStatus(data) {
      this.loading = true;
      const status = !data.is_activated ? "kích hoạt" : "hủy kích hoạt";
      try {
        const response = await this.$axios.put(
          `laravel/admin/users/${data.id}`,
          {
            active: !data.is_activated,
          },
        );
        if (response.data.data) {
          this.getListUsers();
          this.$message.success(`Đã ${status} thành công.`);
        }
      } catch (error) {
        this.$message.error(`${status} thất bại.`);
      } finally {
        this.loading = false;
      }
    },
  },
};
</script>

<style lang="css" scoped>
.header-history {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
</style>
