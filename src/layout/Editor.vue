<template>
<div>
  <card>
    <div class="editor">
      <editor-menu-bar :editor="editor" v-if="showFlag" v-slot="{ commands, isActive }">
        <div class="menubar">
          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.bold() }"
            @click="commands.bold"
          >
            <!-- <img class="icon" src="@/assets/img/icons/bold.svg" alt="" /> -->
            <img class="icon" src="../assets/img/icons/bold.svg" alt="" />
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.italic() }"
            @click="commands.italic"
          >
            <!-- <icon name="italic" /> -->
            <img class="icon" src="@/assets/img/icons/italic.svg" alt="" />
          </button>

          <button 
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.strike() }"
            @click="commands.strike"
          >
            <img class="icon" src="@/assets/img/icons/strike.svg" alt="" />
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.underline() }"
            @click="commands.underline"
          >
            <img
              class="icon"
              src="@/assets/img/icons/underline.svg"
              alt=""
            />
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.code() }"
            @click="commands.code"
          >
            <img class="icon" src="@/assets/img/icons/code.svg" alt="" />
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.paragraph() }"
            @click="commands.paragraph"
          >
            <img
              class="icon"
              src="@/assets/img/icons/paragraph.svg"
              alt=""
            />
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.heading({ level: 1 }) }"
            @click="commands.heading({ level: 1 })"
          >
            H1
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.heading({ level: 2 }) }"
            @click="commands.heading({ level: 2 })"
          >
            H2
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.heading({ level: 3 }) }"
            @click="commands.heading({ level: 3 })"
          >
            H3
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.bullet_list() }"
            @click="commands.bullet_list"
          >
            <img class="icon" src="@/assets/img/icons/ul.svg" alt="" />
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.ordered_list() }"
            @click="commands.ordered_list"
          >
            <img class="icon" src="@/assets/img/icons/ol.svg" alt="" />
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.blockquote() }"
            @click="commands.blockquote"
          >
            <img class="icon" src="@/assets/img/icons/quote.svg" alt="" />
          </button>

          <button
            type="button"
            class="menubar__button"
            :class="{ 'is-active': isActive.code_block() }"
            @click="commands.code_block"
          >
            <img class="icon" src="@/assets/img/icons/code.svg" alt="" />
          </button>

          <button class="menubar__button" @click="commands.horizontal_rule">
            <img class="icon" src="@/assets/img/icons/hr.svg" alt="" />
          </button>

          <button class="menubar__button" @click="commands.undo">
            <img class="icon" src="@/assets/img/icons/undo.svg" alt="" />
          </button>

          <button class="menubar__button" @click="commands.redo">
            <img class="icon" src="@/assets/img/icons/redo.svg" alt="" />
          </button>

            <button class="menubar__button" @click="showImagePrompt(commands.image)">
            <img class="icon" src="@/assets/img/icons/image.svg" alt="" />
          </button>
        </div>
      </editor-menu-bar>

      <editor-content class="editor__content" :editor="editor" />
    </div>
  </card>
  <!-- <div class="col">
        <h2>HTML</h2>
        <pre>
        {{ html }}
      </pre>
      </div> -->
  </div>
</template>

<script>
import Icon from "src/components/Icon/index.vue";
import '@/assets/sass/main.scss';
import { Editor, EditorContent, EditorMenuBar } from "tiptap";
import {
  Blockquote,
  CodeBlock,
  HardBreak,
  Heading,
  HorizontalRule,
  OrderedList,
  BulletList,
  ListItem,
  TodoItem,
  TodoList,
  Bold,
  Code,
  Italic,
  Link,
  Strike,
  Underline,
  History,
  Image,
} from "tiptap-extensions";
import svgSpriteLoader from "src/helpers/svg-sprite-loader";

const __svg__ = {
  path: "src/assets/img/icons/*.svg",
  name: "assets/img/[hash].sprite.svg",
};
svgSpriteLoader(__svg__.filename);

export default {
  components: {
    EditorContent,
    EditorMenuBar,
    Icon,
  },
  data() {
    return {
      editorSeq: "",
      title: "",
      adminId: "",
      regDate: "",
      btnModify: true,                // 수정 버튼 표시 or 숨김
      // btnSave: false,                 // 저장, 삭제 버튼 표시 or 숨김
      // createChk: true,
      editor: new Editor({
        editable: false,
        extensions: [
          new Blockquote(),
          new BulletList(),
          new CodeBlock(),
          new HardBreak(),
          new Heading({ levels: [1, 2, 3] }),
          new HorizontalRule(),
          new ListItem(),
          new OrderedList(),
          new TodoItem(),
          new TodoList(),
          new Link(),
          new Bold(),
          new Code(),
          new Italic(),
          new Strike(),
          new Underline(),
          new History(),
          new Image(),
        ],
        content:
        `
          
        `,
        
      }),
    }; 
  },
  props: {
    showFlag: ['showFlag'],
    createFlag: ['createFlag'],
    contents: ['contents']
  },
  beforeDestroy() {
    this.editor.destroy();
  },
  mounted() {
  },
  computed: {
      html() {
        if (!this.editor) return "";
        return this.editor.getHTML();
    }
  },
  watch: {
    // title(){
    //     console.log(this.title);
    //     this.$emit('childs-event', this.title, this.editor.getHTML(),this.adminId);
    //     },
    showFlag(flag) {
      console.log("show flag : " + flag);
        if(this.showFlag==true) {
          this.editor.setOptions({
          editable: true,
        })
      }
    },
    html(){
      console.log(this.editor.getHTML());
      this.$emit('onEdit', this.editor.getHTML());
    },
    contents(newData){
      console.log("contents : " + newData);
      this.editor.setContent(this.contents);
    },
    // modifyFlag(){
    //   console.log("modifly flag invoked!");
    //   if(this.modifyFlag==true) {
    //     this.editor.setOptions({
    //       editable: true,
    //     })
    //     this.btnSave = true;
    //   }
    // },
    // createFlag(){
    //   console.log("create flag invoked!");
    //   if(this.createFlag==true) {
    //     this.editor.setOptions({
    //       editable: true,
    //     })
    //     this.btnSave = true;
    //   }
    // }
    // modifyChk(){
    //   console.log(this.modifyFlag);
    //   if(this.modifyFlag==true) {
    //     this.modify();
    //     this.editor.setContent(this.contents);
    //   }
    // },
    // createChk(){
    //   console.log(this.createFlag);
    //   if(this.createFlag==true)
    //   this.editor.setOptions({
    //     editable: true,
    //   })
    // }
  },
   methods: {
      showImagePrompt(command) {
        const src = prompt('이미지 URL을 입력하여 주십시오.');
        if (src !== null) {
          command({ src })
        }
      },
      modify() {
        this.editor.setOptions({
          editable: true,
        })
      },
        // save(){
        //     console.log(' save()');
        //     this.$emit('childs-event',this.editorSeq, this.title, this.editor.getHTML(),this.adminId ,);
        // }
        // del(){
        //     console.log(' del()');
        //     this.$emit('childs-eventDel', this.editorSeq);
        // },
        //   cancel(){
        //     console.log(' cancel()');
        //     this.$emit('rename','Content');
        //     this.$router.push('/admin/editorMain');
        // },
   },
};
</script>

<style lang="scss" scope>
.icon {
  position: relative;
  display: inline-block;
  vertical-align: middle;
  width: 0.8rem;
  height: 0.8rem;
  margin: 0 0.3rem;
  top: -0.05rem;
  fill: currentColor;

  &.has-align-fix {
  	top: -.1rem;
  }

  &__svg {
    display: inline-block;
    vertical-align: top;
    width: 100%;
    height: 100%;
  }

  &:first-child {
    margin-left: 0;
  }

  &:last-child {
    margin-right: 0;
  }
}

// svg sprite
body > svg,
.icon use > svg,
symbol {
  path,
  rect,
  circle,
  g {
    fill: currentColor;
    stroke: none;
  }

  *[d="M0 0h24v24H0z"] {
    display: none;
  }
}
</style>