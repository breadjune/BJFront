<template>
  <!-- Default form login -->
  <b-container class="login-container">
    <a href=# @click="movePage('Overview')" ref="home" style="display:none"></a>

    <b-row class="login-row justify-content-md-center" align-v="center">
      <div class="login-contents">
        <div class="login-image-box col-md-6">
          <h2 class="login-title" @click="backDoor">AirTalk</h2>
          <img class="login-image" src="../assets/img/laptop3.png">
        </div>
        <div class="login-form-box col-md-6">
           <b-form class="login-form" @submit="onSubmit" @reset="onReset" v-if="show">
            <p class="login-header h4 text-center mb-4" style="font-weight:bold">Sign in</p>
              <b-form-group id="input-group-1" label="Email:" label-for="input-email">
                <b-input-group class="md-2">
                  <b-input-group-prepend is-text>
                    <i class="nc-icon nc-single-02" style="font-weight:bold;"></i>
                  </b-input-group-prepend>
                  <b-form-input id="input-email" v-model="form.email" type="email" placeholder="Enter email" required :value="email"></b-form-input>
                </b-input-group>
              </b-form-group>
              <br/>
              <b-form-group id="input-group-2" label="Password:" label-for="input-pw">
                <b-input-group class="md-2">
                  <b-input-group-prepend is-text>
                    <i class="nc-icon nc-lock-circle-open" style="font-weight:bold;"></i>
                  </b-input-group-prepend>
                  <b-form-input id="input-pw" v-model="form.password" type="password" placeholder="Enter Password" required :value="password"></b-form-input>
                </b-input-group>
              </b-form-group>
              <div class="login-btn text-center mt-6">
                <b-button type="submit" variant="primary" class="login-submit">Login</b-button>
                <b-button type="reset" variant="danger" class="login-reset">Reset</b-button>
              </div>
          </b-form>
        </div>
      </div>
    </b-row>
  </b-container>
  <!-- Default form login -->
</template>

<script>
  import axios from 'axios'
  const loginStore = 'loginStore'

  export default {
    data() {
      return {
        form: {
          email: 'test@i-mobilepark.com',
          password: 'test123'
        },
        show: true
      }
    },
    mounted(){
      console.log('auth : ' + this.$session.get('auth'));
      if(this.$session.get('auth')) {
        var elem = this.$refs.home
        elem.click()
      }
    },
    methods: {
      onSubmit(evt) {
        evt.preventDefault();
        console.log(JSON.stringify(this.form));
        const res = axios.post(`/rest/auth/login.json`, this.form).then((result) => {
          console.log("response : " + JSON.stringify(result));
          console.log("status : " + result.status);
          if(result.status === 200 && result.data != null ) {
            var data = result.data;
            console.log("data : " + JSON.stringify(data));
            // this.$store.commit("loginStore/loginComplete", data)
            this.$session.start();
            this.$session.set('adminName', data.name);
            this.$session.set('adminId', data.id);
            this.$session.set('level', data.adminGroupSeq);
            console.log("authToken : " + result.headers.authorization);
            this.$cookie.set('auth', result.headers.authorization);
            if(data.errorCode === '0') {
              this.$session.set('auth', true);
            } else {
              this.$session.set('auth', false);
            }
            var elem = this.$refs.home
            elem.click()
          } else {
            this.$store.dispatch('loginStore/setAuth')
          }
          
        });  
      },
      onReset(evt) {
        evt.preventDefault()
        // Reset our form values
        this.form.email = ''
        this.form.password = ''
        // Trick to reset/clear native browser form validation state
        this.show = false
        this.$nextTick(() => {
          this.show = true
        })
      },
      movePage(urlName) {
        this.$router.push({
        name: urlName
        });
      },
      backDoor() {
        this.$session.set('auth', true);
        this.$router.push({
        name: 'Overview'
        });
      }
    }
  }
</script>