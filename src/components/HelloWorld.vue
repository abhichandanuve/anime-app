<template>
  <div>
    <div v-if="!showModal" class="container">
      <div>
        <img
          class="thumbnail"
          @click="openModal"
          height="100%"
          width="100%"
          :src="data.thumbNailImage"
        />
        <div class="footer">
          <div class="info" @click="openModal">
            <img height="50px" width="50px" :src="data.logo" />
            <div>
              <p class="title">{{ data.title }}</p>
              <p>{{ data.subTitle }}</p>
            </div>
          </div>
          <button @click="getData">refresh</button>
        </div>
      </div>
    </div>
    <div v-if="showModal" class="modal">
      <div class="modal-content">
        <div class="close" @click="closeModal">x</div>
        <img height="100%" width="100%" :src="data.mainImage" />
        <div class="footer">
          <div class="info">
            <img height="50px" width="50px" :src="data.logo" />
            <div>
              <p class="title">{{ data.title }}</p>
              <p>{{ data.subTitle }}</p>
            </div>
          </div>
          <button @click="getData">refresh</button>
        </div>
        <div class="content" v-html="data.text"></div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";

export default {
  name: "ModalComponent",
  async mounted() {
    this.getData();
  },
  setup() {
    const showModal = ref(false);
    const token = ref("");
    const data = ref("");
    const openModal = () => {
      showModal.value = true;
    };

    const getData = async () => {
      await axios
        .post(
          "https://swsut62sse.execute-api.ap-south-1.amazonaws.com/prod/generateToken",
          {
            email: "hemanth@lobb.in",
          }
        )
        .then((response) => {
          token.value = response.data;
        })
        .catch((error) => {
          console.error("Error fetching data:", error);
        });

      await axios
        .get(
          "https://tzab40im77.execute-api.ap-south-1.amazonaws.com/prod/getContent",
          {
            headers: {
              Authorization: `Bearer ${token.value.token}`,
            },
          }
        )
        .then((response) => {
          data.value = response.data.content;
        })
        .catch((error) => {
          console.error("Error fetching data:", error);
        });
    };

    const closeModal = () => {
      showModal.value = false;
    };

    return {
      showModal,
      openModal,
      closeModal,
      getData,
      data,
    };
  },
};
</script>

<style>
.title {
  font-weight: bold;
}
.content {
  border-top: 2px gray solid;
}
.thumbnail {
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
}
.container {
  padding: 0;
  border-radius: 8px;
  width: 50%;
  margin: auto;
  box-shadow: 10px 10px 5px lightblue;
}
.footer {
  padding: 20px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.content {
  padding: 20px;
}
.info {
  display: flex;
  gap: 10px;
}
p {
  margin: 0;
  width: 100%;
  display: flex;
  text-align: left;
}
.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.close {
  border-radius: 50%;
  background-color: white;
  top: 10px;
  width: 36px;
  position: absolute;
  right: 220px;
  color: black;
  font-size: 30px;
  cursor: pointer;
}

button {
  border-radius: 30px;
  padding-right: 20px;
  padding-left: 20px;
  height: 30px;
  background-color: gray;
  color: lightskyblue;
  font-weight: bold;
  border: 0;
}

.modal-content {
  background-color: #fefefe;
  margin: auto;
  border: 1px solid #888;
  width: 100%;
  max-width: 400px;
}
</style>
