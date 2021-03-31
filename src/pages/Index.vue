<template>
  <div
    class="full-height flex column q-py-md justify-center items-center q-px-xl"
  >
    <q-card class="flex column q-py-lg q-px-xl" style="width: 50%">
      <div v-show="!showVoting" class="">
        <h6 class="flex flex-center">
          Please login with your existing VOOT credentials
        </h6>
        <q-input
          v-model="email"
          label="Email"
          :type="isPwd ? 'password' : 'text'"
          ><template v-slot:append>
            <q-icon
              :name="isPwd ? 'visibility_off' : 'visibility'"
              class="cursor-pointer"
              @click="isPwd = !isPwd"
            ></q-icon> </template
        ></q-input>
        <q-input
          v-model="password"
          label="Password"
          :type="isPwd ? 'password' : 'text'"
        >
          <template v-slot:append>
            <q-icon
              :name="isPwd ? 'visibility_off' : 'visibility'"
              class="cursor-pointer"
              @click="isPwd = !isPwd"
            ></q-icon>
          </template>
        </q-input>
        <div class="q-pt-lg">
          <q-btn
            style="background: #ff0080; color: white"
            label="Login"
            @click="getAccessToken"
          ></q-btn>
        </div>
      </div>
      <div v-show="showVoting">
        <div class="q-pt-lg full-width flex flex-center">
          <q-img
            style="height: 140px; max-width: 150px"
            src="https://voot-interactivity.s3-ap-south-1.amazonaws.com/voting/contestant/image/Colors%20Kannada/BBK-Contestant-Shamanth_260x210.jpeg"
          >
          </q-img>
        </div>
        <div class="q-pt-lg full-width flex flex-center">
          <q-btn
            style="background: #ff0080; color: white"
            label="Voting 99  Times to Shamanth"
            @click="voteForShamanth"
          ></q-btn>
        </div>
      </div>
    </q-card>
  </div>
</template>

<script>
import axios from "axios";
import randomIp from "random-ip";

export default {
  name: "PageIndex",
  data() {
    return {
      count: 100,
      showVoting: false,
      email: "",
      password: "",
      isPwd: true,
      userDetails: {
        id: "",
        name: "",
        email: "",
        token: "",
      },
    };
  },
  methods: {
    getAccessToken() {
      const data = {
        type: "traditional",
        deviceId: "Macintosh",
        deviceBrand: "PC/MAC",
        data: { email: this.email, password: this.password },
      };
      const headers = {
        Accept: "application/json",
        "Access-Control-Allow-Origin": "*",
        "Content-Type": "application/json",
      };
      axios
        .post("https://userauth.voot.com/usersV3/v3/login", data, {
          headers,
        })
        .then(({ data }) => {
          console.log(data);
          const {
            uId,
            email,
            firstName,
            authToken: { accessToken: token },
          } = data.data;
          this.userDetails.email = email;
          this.userDetails.id = uId;
          this.userDetails.token = token;
          this.userDetails.name = firstName;
          console.log(this.userDetails);
          this.$q.notify({
            position: "top",
            type: "positive",
            message: `Successfully Fetched the user details. Please click on the voting button.`,
          });
          this.showVoting = true;
        })
        .catch((error) => {
          this.$q.notify({
            position: "top",
            type: "negative",
            message: `Could not vote shamanth`,
          });
          console.log(error);
        });
    },
    voteForShamanth() {
      const IP = randomIp("103.98.78.103", 16, 24);
      console.log(IP);
      const headers = {
        Accept: "application/json",
        "Access-Control-Allow-Origin": "*",
        "Content-Type": "application/json",
      };

      const data = {
        showId: "6b3d31e0-7b1e-11eb-a1af-d93c47ce2886",
        showName: "BBK Voting",
        categoryId: "",
        categoryName: "",
        contestantId: "c0d4b460-7b1f-11eb-a1af-d93c47ce2886",
        contestantName: "Shamanth",
        cycleId: "9c792960-9141-11eb-a1af-d93c47ce2886",
        userId: this.userDetails.id,
        userName: this.userDetails.name,
        userEmail: this.userDetails.email,
        region: "in",
        ip: IP,
        loginProvider: "Traditional",
        userFormDetails: "",
        accessToken: this.userDetails.token,
      };

      for (let j = 0; j < this.count; j++) {
        let randomTime = Math.random() * 12321312321;
        setTimeout(
          () => this.voteRandomly(data, randomTime * j),
          randomTime * j
        );
        setTimeout(() => this.voteRandomly(data, randomTime), randomTime);
      }
    },
    voteRandomly(data, randomTime) {
      console.log(data, randomTime);
      const contentType = {
        json: "application/json",
        form: "multipart/form-data",
      };

      axios
        .post("https://voting-api.voot.com/v1/addvote", data, {
          headers: {
            Accept: contentType.json,
            "Content-Type": contentType.json,
          },
        })
        .then(({ data }) => {
          console.log(data.id);
          this.$q.notify({
            position: "top",
            type: "positive",
            message: `${data.message} record ID ${data.id}`,
          });
        })
        .catch((error) => {
          this.$q.notify({
            position: "top",
            type: "negative",
            message: `Could not vote shamanth ${j}`,
          });
          console.log(error);
        });
    },
  },
};
</script>
