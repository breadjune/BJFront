<template>
  <board
    :title="title"
    :fields="fields"
    :data="data"
    :pageSet="pageSet"
    :options="options"
    @onPageSelected="pageSelected"
    @onKeywordSearched="keywordSearched"
    @onRowSelected="rowSelected"
    @onCreated="created"
  ></board>
</template>
<script>
  import board from "@/layout/Board.vue"
  import axioMixin from "@/components/axioMixin"

  export default {
    mixins: [axioMixin],
    components: {
      board
    },
    data () {
      return {
        title: '공략 게시판',
        fields: [
          { key: "seq", label: "NO.", sortable: true},
          { key: "title", label: "제목", sortable: true},
          { key: "language", label: "언어", sortable: true},
          { key: "regDate", label: "등록일", sortable: true},
        ],
        data: [],
        pageSet: { 
          currentPage: 1,
          pageRows: 7, 
          totalRows: 999999
        },
        options: [
          { value: "title", text: "제목"},
          { value: "language", text: "언어"}
        ],
        form: {
          type: "",
          keyword: ""
        }
      }
    },
    mounted() {
      this.init();
    },
    methods: {
      init: async function () {

        var page = this.$session.get('page');
        var type = this.$session.get('type');
        var keyword = this.$session.get('keyword');
        var totalPage = this.$session.get('totalPage');

        this.form.type = type !== undefined ? type : "";
        this.form.keyword = keyword !== undefined ? keyword : "";
        
        var formData = this.form;
        formData.start = page !== undefined ? page-1 : 0;
        formData.length = String(this.pageSet.pageRows);
        formData.bcode = "0001";
        var response = null;
        if(this.form.type !== "" && this.form.keyword !== "") {
          response = await this.request("/restapi/board/search", formData);
        } else {
          response = await this.request("/restapi/board/list", formData);
        }
        this.data = response.result;
        this.pageSet.totalRows = response.total_cnt;

        this.$session.set('totalPage', response.total_cnt);
        this.pageSet.currentPage = page !== undefined ? page : 1;

        console.log("session page param : ["+page+"]["+type+"]["+keyword+"]["+totalPage+"]");

      },
      async pageSelected(page) {
        if(this.form.type !== "") {

          this.$session.set('page', (String)(page));
          console.log("session page param : ["+this.$session.get('page')+"]["+this.$session.get('type')+"]["+this.$session.get('keyword')+"]["+this.$session.get('totalPage')+"]");

          var formData = this.form;
          formData.start = page-1;
          formData.length = String(this.pageSet.pageRows);
          formData.bcode = "0001";
          
          var response = await this.request("/restapi/board/search", formData);
          console.log("response data : " + JSON.stringify(response));

          this.data = response.result;

        } else {
          var formData = Object();

          this.$session.set('page', (String)(page));
          console.log("session page param : ["+this.$session.get('page')+"]["+this.$session.get('type')+"]["+this.$session.get('keyword')+"]["+this.$session.get('totalPage')+"]");

          formData.start = page-1;
          formData.length = String(this.pageSet.pageRows);
          formData.bcode = "0001";
          var response = await this.request("/restapi/board/list", formData);
          console.log("board data : " + JSON.stringify(response));

          this.data = response.result;
        }
      },
      async rowSelected(items) {

        console.log("items "+JSON.stringify(items));
        var formData = new FormData();
        var seq = items[0].seq;
        formData.append('seq', seq);
        var response = await this.request("/restapi/board/fileNameInfo", formData);
        var fileName = response.fileName
        console.log("response fileName1 : " + response.fileName);
        console.log("response fileName2 : " + fileName);
        items[0].fileName = fileName;

        console.log("session page param : ["+this.$session.get('page')+"]["+this.$session.get('type')+"]["+this.$session.get('keyword')+"]["+this.$session.get('totalPage')+"]");

        this.$emit('rename', 'Content');
        this.$router.push({
          name:"NoticeView",
          params: {
            row: items,
            // fileName: fileName
          }
        })
      },
      created() {
        this.$emit('rename', 'Content');
        this.$router.push({
          name:"NoticeCreate",
          params: {
            created: true
          }
        });
      },
      async keywordSearched(form) {
        console.log("form params : [type:"+form.searchType+"][keyword:"+form.searchWord+"]");
        var url = "";

        if(form.searchWord !== "") {
          this.form.type = form.searchType;
          this.form.keyword = form.searchWord;
          url = "search"
        } else {
          this.form.type = "";
          this.form.keyword = "";
          url = "list"
        }
        
        this.$session.set('type', form.searchType);
        this.$session.set('keyword',form.searchWord);

        this.pageSet.currentPage = 1;

        var formData = this.form;
        formData.start = this.pageSet.currentPage-1;
        formData.length = String(this.pageSet.pageRows);
        formData.bcode = "0001";

        var response = await this.request("/restapi/board/"+url, formData);
        console.log("response data : " + JSON.stringify(formData));

        this.data = response.result;
        this.pageSet.totalRows = response.total_cnt;

        this.$session.set('totalPage', response.total_cnt);

        console.log("session page param : ["+this.$session.get('page')+"]["+this.$session.get('type')+"]["+this.$session.get('keyword')+"]["+this.$session.get('totalPage')+"]");
      }
    }
  }
</script>
<style>
</style>