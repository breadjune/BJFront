<template>
  <div class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12">
          <card
            class="strpied-tabled-with-hover">
            <template slot="header">
              <h3 class="card-title">사용자 관리</h3>
              <hr>
            </template>
            <search :options="options" @keywordSearch="searchData"></search>
            <!-- <l-table
              class="table-hover table-striped"
              id="my-table"
              :headers="row.headers"
              :columns="row.columns"
              :data="row.data"
              :per-page="perPage"
              :current-page="currentPage"
              @rowSelected="onRowSelected"
            >
            </l-table> -->
            <b-table
                id="my-table"
                striped
                hover
                selectable
                select-mode="single"
                sort-icon-left
                :fields="fields"
                :items="row.data"
                :per-page="page.perPage"
                @row-selected="onRowSelected"
            ></b-table>

            <div id="noData" v-if="row.noData" style="text-align: center; height: 100px">
              데이터가 없습니다.
            </div>
            <b-pagination
              v-show="row.default"
              v-model="page.currentPage"
              :total-rows="page.totalPage"
              :per-page="page.perPage"
              @change="handle"
              aria-controls="my-table"
              style="position:relative;justify-content:center;margin-bottom:0;"
            ></b-pagination>
            <div>
              <b-button
                class=" btn btn-fill mb-2 mr-sm-2 mb-sm-1"
                variant="primary"
                @click="movePage()"
                >추가
              </b-button>
            </div>
          </card>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import LTable from "@/layout/Table.vue";
import Card from "src/components/Cards/Card.vue";
import Search from "@/layout/Search.vue";
import axios from "axios";
import axioMixin from "@/components/axioMixin";
const tableHeaders = ["NO.", "사용자 ID", "이름", "핸드폰 번호", "등록일"];
const tableColumns = ["seq", "userId", "name", "hpNo", "regDate"];
export default {
  components: {
    LTable,
    Card,
    Search,
  },
  mixins: [axioMixin],
  data() {
    return {
      fields: [
          { key: "seq", label: "NO.", sortable: true},
          { key: "userId", label: "사용자 ID", sortable: true},
          { key: "name", label: "이름", sortable: true},
          { key: "hpNo", label: "핸드폰 번호", sortable: true},
          { key: "regDate", label: "등록일", sortable: true},
        ],
      page: {
        currentPage: 1,
        perPage: 4,
        totalPage: 99,
      },
      row: {
        default: true,
        noData: false,
        headers: [...tableHeaders],
        columns: [...tableColumns],
        data: [],
      },
      form: {
        keyword: "",
        type: "all",
        start: "0",
        length: "",
      },
        options: [
          { value: "userId", text: tableHeaders[1] },
          { value: tableColumns[2], text: tableHeaders[2] },
        ],
    };
  },
  computed: {
    rows() {
      return this.row.data.length;
    },
  },
  mounted() {
    this.init();
  },
  created() {
    //session 가져오기
    if(this.$session.get("keyword")!=undefined )
      this.form.keyword = this.$session.get("keyword");
    if(this.$session.get("page")!=undefined){
     this.form.start = this.$session.get("page")-1;
     this.page.currentPage = this.$session.get("page");
   }
    if(this.$session.get("type")!=undefined)
      this.form.type = this.$session.get("type");
  },
  methods: {
    init: async function () {
      // var res = await this.request("/restapi/user/list", this.form);
      // console.log("User data : " + JSON.stringify(res));
      this.page.totalPage = await this.request("/restapi/user/count",this.form);
      console.log("카운트" + this.page.totalPage);
      this.form.length = String(this.page.perPage);

      var res = await this.request("/restapi/user/search", this.form);
      //seq 추가 (등차 수열 적용해야함)
      for (var i = 0; i < res.length; i++) {
        res[i]["seq"] = i+1; 
        console.log("User data : " + JSON.stringify(res[i]));
      }
      this.row.data = res;
    },
    async handle(page) {
      console.log("page : " + page);
      console.log("current Page : " + this.page.currentPage);
      this.form.start = String(page - 1);
      var response = await this.request("/restapi/user/search", this.form);
      //seq 추가 (등차 수열 적용해야함)
      for (var i = 0; i < response.length; i++) {
        response[i]["seq"] = (4*page)-3+i;
        console.log("User data : " + JSON.stringify(response[i]));
      }
      this.row.data = response;
      this.$session.set('page', this.page.currentPage );
    },
    async searchData(form) {
        this.$session.set('keyword', form.searchWord);
        this.$session.set('type', form.searchType);
        this.form.keyword = form.searchWord;
        this.form.type = form.searchType;
        this.form.start = "0";
        this.form.length = String(this.page.perPage);

        this.page.totalPage = await this.request("/restapi/user/count",this.form);
        
        //데이터 없음 화면 및 페이징 UI 변경
        if (this.page.totalPage !== 0) {
          this.row.default = true;
          this.row.noData = false;
        } else {
          this.row.default = false;
          this.row.noData = true;
        }
         var response = await this.request("/restapi/user/search", this.form);
          console.log("User Data : " + JSON.stringify(response));
          //seq 추가
          for (var i = 0; i < response.length; i++) {
            response[i]["seq"] = i+1;
            console.log("User data : " + JSON.stringify(response[i]));
          }
         this.row.data = response;
    },
    onRowSelected(items) {
      console.log("items " + JSON.stringify(items));
      this.$emit("rename", "Content");
      this.$router.push({
        name: "UserView",
        params: {
          id: items[0].userId,
          name: items[0].name,
          password: items[0].password,
          hpNo: items[0].hpNo,
          regDate: items[0].regDate,
        },
      });
      this.$session.set('page', this.page.currentPage );
    },
    movePage() {
      this.$emit("rename", "Content");
      this.$router.push("/service/userCreate");
    },
  },
};
</script>
<style>
</style>