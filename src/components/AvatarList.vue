<template>
  <div>
    <div class="position-fixed top-0 left-0 right-0 bg-primary pa-3">
      <tag-input v-model="searchArray" :title="tagPlaceholder" />
    </div>
    <div class="d-flex flex-wrap" style="margin-top: 120px">
      <div v-for="avatar in listArray" :key="avatar.id">
        <AvatarDetail
          v-bind:name="avatar.name"
          v-bind:thumbnail-image-url="avatar.thumbnailImageUrl"
          @click="() => openDialog(avatar.id, avatar.name)"
        />
      </div>
      <v-btn @click="handleGetAvatar">Refresh</v-btn>
    </div>

    <AvatarChangeDialog
      v-model="dialog"
      :avatar-name="avatarName"
      :change-avatar="handelAvatarChange"
    ></AvatarChangeDialog>
    <LoginDialog v-model="openLoginDialog">
      <div
        class="position-fixed bottom-0 left-0 right-0 pa-3 w-100"
        style="background-color: white"
      >
        <template v-if="firstLogin">
          <v-text-field
            v-model="userName"
            placeholder="username"
          ></v-text-field>
          <v-text-field
            v-model="password"
            placeholder="password"
            type="password"
          ></v-text-field>
          <v-btn @click="handleLogin">Login</v-btn>
        </template>
        <template v-if="!firstLogin">
          <v-text-field
            v-model="auth2faNumber"
            placeholder="2FA"
          ></v-text-field>
          <v-btn @click="handleTwoFactorAuth">2FA</v-btn>
        </template>
        {{ authToken }}
      </div>
    </LoginDialog>
  </div>
</template>

<script setup>
// import list from "@/assets/json/avatar.json";
import { computed, ref } from "vue";
import AvatarDetail from "./AvatarDetail.vue";
import TagInput from "./TagInput.vue";
import { ipcRenderer } from "electron";
import AvatarChangeDialog from "./AvatarChangeDialog.vue";
import LoginDialog from "./LoginDialog.vue";
import * as fs from "fs";

const auth2faNumber = ref("");
const authToken = ref("");
const list = ref([]);
const dialog = ref(false);
const avatarId = ref("");
const avatarName = ref("");
const searchArray = ref([]);
const userName = ref("");
const password = ref("");
const openLoginDialog = ref(false);
const firstLogin = ref(true);

const init = async () => {
  fs.readFile("./resources/auth.txt", "utf8", (err, data) => {
    if (err) {
      console.log(err);
      openLoginDialog.value = true;
      return;
    }
    authToken.value = data;
    handleTokenCheck();
  });
  fs.readFile("./resources/avatarList.txt", "utf8", (err, data) => {
    if (err) {
      console.log(err);
      return;
    }
    list.value = JSON.parse(data);
  });
};

const openDialog = (id, name) => {
  avatarId.value = id;
  avatarName.value = name;
  dialog.value = true;
};

const handleLogin = async () => {
  const result = await ipcRenderer.invoke("VRChatLogin", {
    userName: userName.value,
    password: password.value,
    num: auth2faNumber.value,
  });

  authToken.value = result.authToken;

  const check = await ipcRenderer.invoke("TokenCheck", {
    authToken: authToken.value,
  });

  if (check) {
    openLoginDialog.value = false;
  } else {
    firstLogin.value = false;
  }

  fs.writeFileSync("./resource/auth.txt", result.authToken, (err) => {
    if (err) {
      alert(err);
    }
  });
};

const handleTwoFactorAuth = async () => {
  const result = await ipcRenderer.invoke("TwoFactorAuth", {
    authToken: authToken.value,
    num: auth2faNumber.value,
  });

  const check = await ipcRenderer.invoke("TokenCheck", {
    authToken: authToken.value,
  });

  if (check) {
    openLoginDialog.value = false;
    firstLogin.value = true;
  }
  console.info(result);
};

const handleTokenCheck = async () => {
  if (authToken.value === "") {
    openLoginDialog.value = true;
    return;
  }

  const result = await ipcRenderer.invoke("TokenCheck", {
    authToken: authToken.value,
  });

  if (result === false) {
    openLoginDialog.value = true;
  }
};

const handleGetAvatar = async () => {
  //結果が60未満になるまでループさせる。時間はすこしあける
  //最大でも10回まで
  list.value = [];

  for (let i = 0; i < 10; i++) {
    //
    const result = await ipcRenderer.invoke("GetAvatarList", {
      authToken: authToken.value,
      offsetMultiply: i,
    });

    if (result?.error) {
      console.info(result);
      return;
    }
    list.value = list.value.concat(result);

    if (result.length < 60) {
      break;
    }
  }

  fs.writeFileSync(
    "./resources/avatarList.txt",
    JSON.stringify(list.value),
    (err) => {
      if (err) {
        alert(err);
      }
    }
  );
};

const handelAvatarChange = async () => {
  await ipcRenderer.invoke("ChangeAvatar", {
    id: avatarId.value,
    authToken: authToken.value,
  });
};

const filter = (list) => {
  let temp = list.value;
  if (searchArray.value.length > 0) {
    for (const searchText of searchArray.value) {
      temp = temp.filter((i) => {
        if (
          i.description.toLowerCase().indexOf(searchText.toLowerCase()) > -1 ||
          i.name.toLowerCase().indexOf(searchText.toLowerCase()) > -1
        ) {
          return true;
        }
      });
    }
  }

  return temp.map((i) => {
    return {
      id: i.id,
      thumbnailImageUrl: i.thumbnailImageUrl,
      name: i.name,
      description: i.description,
    };
  });
};

const listArray = computed(() => filter(list));

const tagPlaceholder = computed(() => {
  //
  return (
    list.value.length + "件中のうち" + listArray.value.length + "件を表示中"
  );
});

init();
</script>

<style scope>
.avatar-grid {
  padding: 0;
}

.no-space {
  padding: 0;
  margin: 0;
}
</style>
