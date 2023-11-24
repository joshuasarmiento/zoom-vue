<script setup>
import { ZoomMtg } from '@zoomus/websdk';

ZoomMtg.setZoomJSLib('https://source.zoom.us/2.18.0/lib', '/av');
ZoomMtg.preLoadWasm();
ZoomMtg.prepareWebSDK();
// loads language files, also passes any error messages to the ui
ZoomMtg.i18n.load('en-US');
ZoomMtg.i18n.reload('en-US');


var authEndpoint = 'https://zoom-endpt.vercel.app/'
var sdkKey = 'x89uSWg9S_mbkO0QJmdMQ'
var meetingNumber = '96933602211'
var passWord = 'ML4gCD'
var role = 0
var userName = 'Joshua Sarmiento'
var userEmail = 'joshua_sarmiento@asterra.com.ph'
var registrantToken = ''
var zakToken = ''
var leaveUrl = 'https://zoom.us'

async function getSignature() {
  try {
    const response = await fetch(authEndpoint, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        meetingNumber: meetingNumber,
        role: role,
      }),
      mode: 'no-cors',
    });

    const data = await response.json();
    console.log(data);
    startMeeting(data.signature);
  } catch (error) {
    console.error(error);
  }
}


function startMeeting(signature) {
  document.getElementById('zmmtg-root').style.display = 'block';

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
        }
      });
    },
    error: (error) => {
      console.log(error);
    }
  });
}
</script>

<template>
  <h1>Zoom Meeting SDK Vue.js Sample</h1>

  <button @click='getSignature'>Join Meeting</button>
</template>

<style scoped>

</style>
