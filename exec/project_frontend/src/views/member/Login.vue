<template>
  <v-container>
    <section class="section-container">
      <v-row class="signin">
        <v-col cols="8" class="left"> </v-col>
        <v-col cols="4" class="right">
          <div class="loginFormBox">
            <h1 class="loginTitle">LOGIN</h1>
            <v-text-field
              class="inputField"
              label="ID (Email)"
              placeholder="ex) 내투왕@kingofinvestment.shop"
              dark
              type="string"
              v-model="user.id"
            ></v-text-field>
            <form>
              <v-text-field
                class="inputField"
                label="PW (대소문자 특수문자 조합 8자 이상)"
                placeholder="kingofinvest1!"
                dark
                type="password"
                v-model="user.password"
                autocomplete="off"
              ></v-text-field>
            </form>
            <v-row>
              <v-spacer></v-spacer>
              <button class="loginBtn" @click="confirm()">로그인</button>
            </v-row>
          </div>
        </v-col>
      </v-row>
    </section>
  </v-container>
</template>
<script>
import { login } from "@/api/user.js";
import http from "../../api/axios.js";
import { moveMain } from "@/api/move.js";
import swal from "sweetalert";
export default {
  data() {
    return {
      user: {
        id: "",
        password: "",
        isLoginError: false,
      },
    };
  },
  methods: {
    confirm() {
      sessionStorage.setItem("X-AUTH-TOKEN", "");
      login(
        this.user,
        (response) => {
          if (response.data.message === "success") {
            let token = response.data["X-AUTH-TOKEN"];
            this.$store.commit("setIsLogined", true); // vuex의 로그인 상태를 true로 변경
            sessionStorage.setItem("X-AUTH-TOKEN", token); // session스토리지에 token을 저장.
            // eslint-disable-next-line prettier/prettier
            http.defaults.headers["X-AUTH-TOKEN"] =
              // eslint-disable-next-line prettier/prettier
              window.sessionStorage.getItem("X-AUTH-TOKEN");
            moveMain();
          }
        },
        (error) => {
          if (this.user.id == "") {
            swal("아이디를 입력해 주세요!", {
              icon: "error",
            });
          } else if (this.user.pw == "") {
            swal("비밀번호를 입력해 주세요!", {
              icon: "error",
            });
          } else {
            swal("아이디 또는 비밀번호가 일치하지 않습니다.", {
              icon: "error",
            });
          }
          console.log(error);
        }
      );
    },
  },
};
</script>
<style scoped>
@import "./../../assets/css/login.css";
</style>
