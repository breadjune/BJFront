<template>
  <div class="wrapper">
    <a href=# @click="movePage('Login')" ref="login" style="display:none"></a>
    <side-bar>
      <!-- 모바일 메뉴 우선 보이지 않게 숨김 -->
      <!-- <mobile-menu slot="content"></mobile-menu> -->
      <ul>
        <li>
          <a href="#" @click="changeCountents('Overview')" class="nav-link">
            <i class="nc-icon nc-chart-pie-35"></i>
            Dashboard
          </a>
        </li>
        <li>
          <a href="#" @click="changeCountents('about')" class="nav-link">
            <i class="nc-icon nc-badge"></i>
            ABOUT
          </a>
        </li>
        <li>
          <a href="#" @click="changeCountents('document')" class="nav-link">
            <i class="nc-icon nc-notes"></i>
            Document
          </a>
        </li>
        <li>
          <b-navbar-toggle target="navbar-toggle-collapse" @click="changeCountents('directory')" class="nav-link directory">
            <div href=#><i class="nc-icon nc-layers-3"></i>Board</div>
          </b-navbar-toggle>
        </li>
        <b-collapse id="navbar-toggle-collapse" class="board-toggle">
          <b-navbar-nav class="ml-auto">
            <b-nav-item href="#" @click="changeCountents('Notice')">
              <i class="nc-icon nc-single-copy-04"></i>
              공지 게시판
            </b-nav-item>
          </b-navbar-nav>
        </b-collapse>     
      </ul>
    </side-bar>
    <div class="main-panel">
      <top-navbar v-bind="title"></top-navbar>

      <component :is="this.name" v-on:rename="rename"></component>

      <footer></footer>
    </div>
  </div>
</template>
<style lang="scss">

</style>
<script>
  import TopNavbar from './TopNavbar.vue'
  import Footer from './ContentFooter.vue'
  import Content from './Content.vue'
  import MobileMenu from './MobileMenu.vue'
  import Overview from '../pages/Overview'
  import Notice from '../pages/admin/notice/Manage.vue'
  export default {
    data() {
      return {
        name: 'Overview',
        title: {
          name: 'Dashboard'
        }
      }
    },
    components: {
      TopNavbar,
      Footer,
      Content,
      Notice,
      MobileMenu,
      Overview
    },
    mounted(){
      console.log('auth : ' + this.$session.get('auth'));
      if(!this.$session.get('auth')) {
        const elem = this.$refs.login
        elem.click()
      }
    },
    methods: {
      oonReceive(text) {
            this.receiveText = text;
        },
      rename(name) {
        console.log('name : ' + name);
        this.name = name
      },
      changeCountents(name) {
        
        this.$session.remove('page');
        this.$session.remove('type');
        this.$session.remove('keyword');
        console.log("name is ... : " + name);

        var list = document.querySelectorAll('.nav-main__links li');

        if(name !== 'Login' && name !== 'directory' && name !== 'board') {
          this.active(list)
          this.name = name
        }

        if(name === 'directory') {
          for (var i = 0; i < list.length; i++) { 
            (function(idx) {
              list[idx].classList.remove("active");
            })(i);
          }
        }
        
        if(name === 'board') {
          var list2 = document.querySelectorAll('.navbar-nav li');
          this.active(list2)
        }
      },
      active(list) {
        //라우터 정보 초기화 
        this.$router.push("/admin/overview");
        
        for (var i = 0; i < list.length; i++) { 
          (function(idx) {
            list[idx].classList.remove("active");
            list[idx].onclick = function() {
              list[idx].classList.add("active");
            }
          })(i);
        }
      },
      movePage(urlName) {
        console.log('name : ' + this.name);
        if (this.name !== urlName) {
          this.name = urlName;
          this.$router.push({
            name: urlName
          });
        }
      }
    }
  }

</script>
<style>
</style>