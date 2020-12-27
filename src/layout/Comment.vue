<template>
<b-form>
  <div>
    <label for="contents">댓글 작성</label>
    <div class="text-box">
      <b-form-textarea id="contents" rows="3" name="contents" type="text" style="width:100%; border: 0;" no-resize v-model="comment"></b-form-textarea>
      <hr style="margin:5px">
      <b-row align-h="end" style="margin-right:0">
        <b-button class="btn-fill mb-2 mr-sm-2 mb-sm-1" variant="primary" @click="create" style="padding:5px">등록</b-button>
      </b-row>
    </div>
    <hr>
    <div v-for="(item, index) in data" :key="index">
      <slot :row="item">
          <p class="uname"><b>{{item.adminId}}</b> | <span class="mdate">{{item.modDate}}</span></p>
          <p class="comment" v-html="item.comment">{{item.comment}}</p>
          <hr>
        <!-- {{item.userId}} -->
      </slot>
    </div>
  </div>
</b-form>
</template>d
<script>
  import axioMixin from "@/components/axioMixin"

  export default {
    mixins: [axioMixin],
    data() {
      return {
        data: [],
        comment: "",
        columns: ['adminId','modDate','comment'],
      }
    },
    props: {
      seq: ['seq']
    },
    watch: {
      seq() {
        console.log("seq : " + this.seq);
        let formData = Object();
        formData.seq = this.seq;
        this.list(formData);
      }
    },
    methods: {
      list: async function (form) {
        var response = await this.request("/restapi/board/comment/search", form);
        console.log("result : " + JSON.stringify(response));
        this.data = response.result;
      },
      create: async function () {
        let formData = Object();
        formData.seq = this.seq;
        formData.adminId = this.$session.get("adminId");
        formData.comment = this.comment;
        var response = await this.request("/restapi/board/comment/create", formData);
        if(response !== undefined || response !== "") {
          this.list(formData);
        }
      }
    }
  }
</script>
<style>
.text-box {
  border: 1.5px solid;
  border-radius: 5px;
  border-color: rgb(153,153,153);
  padding: 10px;
}
.uname, .mdate {
  margin-bottom : 0;
}
.mdate {
  font-size: 13px;
  color: rgb(153,153,153);
}
.comment {
  /* border: 1.5px solid; */
  height: 100px;
  /* border-radius: 5px;
  border-color: rgb(153,153,153); */
  padding: 10px;
  overflow: scroll;
}
</style>
