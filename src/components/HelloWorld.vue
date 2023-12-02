<script setup>
import { ref, onMounted } from "vue";
import { ZoomMtg } from "@zoomus/websdk";

ZoomMtg.setZoomJSLib("https://source.zoom.us/2.18.0/lib", "/av");
ZoomMtg.preLoadWasm();
ZoomMtg.prepareWebSDK();
// loads language files, also passes any error messages to the ui
ZoomMtg.i18n.load("en-US");
ZoomMtg.i18n.reload("en-US");

// var authEndpoint = 'https://zoom-endpt.mandalay.com.ph/'
var authEndpoint = "https://zoom-endpt.vercel.app/";
var sdkKey = "x89uSWg9S_mbkO0QJmdMQ";
var meetingNumber = "96933602211";
var passWord = "ML4gCD";
var role = 0;
var userName = "Joshua Sarmiento";
var userEmail = "joshua_sarmiento@asterra.com.ph";
var registrantToken = "";
var zakToken = "";
var leaveUrl = "https://zoom.us";

async function getSignature() {
  try {
    const response = await fetch(authEndpoint, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        meetingNumber: meetingNumber,
        role: role,
      }),
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    } else {
      const data = await response.json();
      console.log(data);
      startMeeting(data.signature);
    }
  } catch (error) {
    console.error(`Fetch Error: ${error}`);
    let message = document.createElement("p");
    message.setAttribute("role", "alert");
    message.innerText = `Oops! Something went wrong while trying to join the meeting. ${error.message}`;
    document.body.appendChild(message);
  }
}

function startMeeting(signature) {
  document.getElementById("zmmtg-root").style.display = "block";

  ZoomMtg.init({
    leaveUrl: leaveUrl,
    success: (success) => {
      console.log(success);
      ZoomMtg.join({
        signature: signature,
        sdkKey: sdkKey,
        meetingNumber: meetingNumber,
        passWord: passWord,
        userName: userName,
        userEmail: userEmail,
        tk: registrantToken,
        zak: zakToken,
        success: (success) => {
          console.log(success);
        },
        error: (error) => {
          console.log(error);
        },
      });
    },
    error: (error) => {
      console.log(error);
    },
  });
}

const joinMeeting = async () => {
  await getSignature();
  joined.value = true;
}

const joined = ref(false);

const meetingStatus = ref("Zoom will be available for the scheduled meeting on");
const meetingDate = ref("December 12th at 4:00 PM.");

onMounted(() => {
  let currentDateTime = new Date();
  let meetingDateTime = new Date("2023-12-12T16:00:00");
  
  if (currentDateTime >= meetingDateTime) {
    meetingStatus.value = 'Zoom is currently available for the scheduled meeting.';
  }
});

</script>

<template>
  <div v-if="!joined" class="container">
    <div class="wrapper" role="region" aria-labelledby="meeting-status">
      <h1 id="meeting-status" 
        tabindex="0"
        aria-live="polite">{{ meetingStatus }}</h1>
      <p id="date">{{ meetingDate }}</p>
      <button
        @click="joinMeeting"
        @keydown.enter.prevent="getSignature"
        aria-describedby="meeting-status"
        aria-label="Join Meeting Button"
      >
        Join Meeting
      </button>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  flex-direction: column;
}

.wrapper {
  position: relative;
  text-align: center;
  z-index: 11;
  padding: 40px;
  background-color: rgba(5, 33, 78, 0.384);
  border-radius: 30px;
  margin-top: 50px;
}

#meeting-status {
  font-family: "Inter", sans-serif;
  color: #fff;
  letter-spacing: 2px;
  font-weight: 400;
  text-align: center;
  width: 90%;
  margin: 0 auto 15px auto;
  line-height: 40px;
}

#date {
  font-family: "Inter", sans-serif;
  color: #fff;
  letter-spacing: 2px;
  font-weight: 400;
  text-align: center;
  width: 90%;
  margin: 0 auto 15px auto;
  line-height: 25px;
  font-size: 24px;
}
</style>
