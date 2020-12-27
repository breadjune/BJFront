<template>
  <div class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12">
          <card>
            <template slot="header">
              <h4 class="card-title">{{title}}</h4>
              <hr>
            </template>
            <b-form>
              <div v-if="!createFlag">
                <label for="seq">NO.</label>
                <b-input id="seq" name="seq" type="text" v-model="form.seq" readonly></b-input>
              </div>
              <div v-if="!showFlag && !createFlag">
                <label for="title">제목</label>
                <b-input id="title" name="title" type="text" v-model="form.title" readonly maxlength="100"></b-input>
              </div>
              <div v-else-if="showFlag || createFlag">
                <label for="title">제목</label>
                <b-input id="title" name="title" type="text" v-model="form.title" maxlength="100"></b-input>
              </div>
              <div v-if="!createFlag">
                <label for="writer">작성자</label>
                <b-input id="writer" name="writer" type="text" v-model="form.writer" readonly maxlength="20"></b-input>
              </div>
              <div v-if="createFlag">
                <label for="writer">작성자</label>
                <b-input id="writer" name="writer" type="text" v-model="form.adminName" readonly maxlength="20"></b-input>
              </div>
              <div>
                <label for="contents">내용</label>
                <editor 
                @onEdit="edit"
                :showFlag="showFlag"
                :contents="form.contents"
                >
                </editor>
              </div>
              <div v-if="showFlag">
                <b-button class="btn-fill mb-2 mr-sm-2 mb-sm-1" variant="success" @click="upload">업로드</b-button>
                <div class="file-box">{{files ? files.name : ''}}</div>
              </div>
              <div v-else>
                <div class="btn btn-success btn-fill mb-2 mr-sm-2 mb-sm-1" @click="download">다운로드</div>
                <div class="file-box">{{form.fileName}}</div>
              </div>
              <div style="display:none">
                <label for="files" ref="upload">파일 첨부</label>
                <b-form-file id="files" name="files" type="file" v-model="files"></b-form-file>
              </div>
            </b-form>
          </card>
          <b-button class="btn-fill mb-2 mr-sm-2 mb-sm-1" variant="primary" style="float:left" @click="list">목록</b-button>
          <b-button v-if="createFlag" class="btn-fill mb-2 mr-sm-2 mb-sm-1" variant="primary" style="float:left" @click="save">저장</b-button>
          <b-button v-if="showFlag" class="btn-fill mb-2 mr-sm-2 mb-sm-1" variant="primary" style="float:left" @click="update">저장</b-button>
          <b-button v-if="!showFlag && !createFlag" class="btn-fill mb-2 mr-sm-2 mb-sm-1" variant="primary" style="float:left" @click="modify">수정</b-button>
          <b-button v-if="!createFlag" class="btn-fill mb-2 mr-sm-2 mb-sm-1" variant="danger" style="float:left" @click="del">삭제</b-button>
          <br><br>
          <hr>
          <card v-if="!btnControl" style="clear:both">
            <comment
             :seq="form.seq">
            </comment>
          </card>
          <modal
            :status="modal.status"
            :header="modal.header"
            :body="modal.body"
            @isCancel="toggle"
            @isOk="remove">
            <div>
              {{modalData}}
            </div> 
          </modal> 
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import card from 'src/components/Cards/Card.vue'
import comment from './Comment.vue'
import editor from './Editor.vue'
import modal from '@/layout/Confirm.vue'
export default {
  components: {
    card,
    comment,
    editor,
    modal
  },
  data() {
    return {
      files: null,
      showFlag: false,
      createFlag: false,
      modal: {
        status: false,
        header: "",
        body: "",
        headerMsg: {
          alert: "확인",
          create: "등록",
          modify: "수정",
          delete: "삭제"
        },
        bodyMsg:{
          delete: "정말 삭제 하시겠습니까?",
          fail: "저장 실패 하였습니다. 정보를 확인해주세요.",
          title: "제목을 입력해 주세요.",
          content: "내용을 입력해 주세요."
        }
      }
    }
  },
  props: {
    title: String,
    create: ['create'],
    form: Object
  },
  watch: {
    create(flag) {
      // this.showFlag = true;
      this.createFlag = true;
    },
    visible(){  //모달이 닫히면 false 체크
      if(this.visible==false && this.resultS=="S"){
          this.$emit("rename", "Content");
          this.$router.push("/service/userManage");
      }
    }
  },
  mounted() {
    // this.form= this.$route.params[0].row;
    // console.log("this form? : " + JSON.stringify(this.form));
  },
  methods : {
    list(){
      this.$emit('onList', true);
    },
    modify(){
      this.showFlag = true;
    },
    save(){
      if(this.form.title == undefined) {
        this.modalData = this.msg.title;
        this.visible = !this.visible;
        return false;
      }
      var formData = new FormData();
      if(this.files != null) formData.append('files', this.files);
      formData.append('title', this.form.title);
      formData.append('writer', this.form.writer);
      formData.append('contents', this.contents);
      this.$emit('onSave', formData);
    },
    update(){
      var formData = new FormData();
      formData.append('seq', this.form.seq);
      if(this.files != null) formData.append('files', this.files);
      formData.append('title', this.form.title);
      formData.append('writer', this.form.writer);
      formData.append('contents', this.contents);
      this.$emit('onSave', formData);
    },
    contentFunc(){
      this.editor.setContent(form.contents);
    },
    edit(edit){
      this.contents = edit;
    },
    down(){
      this.$emit('onDown',true);
    },
    del() {
      this.modal.header = this.modal.headerMsg.delete;
      this.modal.body = this.modal.bodyMsg.delete;
      this.toggle();
    },
    toggle() {
      this.modal.status = !this.modal.status;
      console.log("toggle invoke : " + this.modal.status);
    },
    remove(){
      console.log("remove invoked!");
      var formData = new FormData();
      formData.append('seq', this.form.seq);
      this.$emit('onRemove', formData);
    },
    upload(){
      const elem = this.$refs.upload
      elem.click()
    },
    download(){
      console.log("seq : " + this.form.seq);
      this.$emit('onDownload', this.form.seq);
    },
    isNotEmpty : function(_str){
      obj = String(_str);
      if(obj == null || obj == undefined || obj == 'null' || obj == 'undefined' || obj == '' ) return false;
      else return true;
	  }
  }
}

</script>
<style>
.file-box {
  height: 45px;
  vertical-align: middle;
  border: 1px solid #ccc;
  color: rgb(123, 123, 123);
  padding: 10px 0 10px 10px;
  margin: 10px 0 10px 0;
  border-radius: 5px;
}
</style>