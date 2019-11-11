<template>
  <div class="e-nuxt-container">
    <div class="e-nuxt-content">
      <div class="e-nuxt-logo">
        <img style="max-width: 100%">
      </div>
      <div class="e-nuxt-system-info">
        <system-information />
      </div>
    </div>
    <div class="e-nuxt-links">
      <div class="e-nuxt-button" @click="openURL('https://github.com/michalzaq12/electron-nuxt')">
        Github
      </div>
      <div class="e-nuxt-button" @click="openURL('https://nuxtjs.org/guide')">
        Nuxt.js
      </div>
      <div class="e-nuxt-button" @click="openURL('https://electronjs.org/docs')">
        Electron.js
      </div>
    </div>
    <div>
     <h1>Recorder.js simple WAV export example</h1>

  <p>Make sure you are using a recent version of Google Chrome.</p>
  <p>Also before you enable microphone input either plug in headphones or turn the volume down if you want to avoid ear splitting feedback!</p>

  <button onclick="startRecording(this);">record</button>
  <button onclick="stopRecording(this);" disabled>stop</button>
  
  <h2>Recordings</h2>
  <ul id="recordingslist"></ul>
  
  <h2>Log</h2>
  <pre id="log"></pre>

  <v-btn>dsadssadsadsas</v-btn>
    </div>
      <script src="../scripts/recorder.js"></script>
  </div>
  
</template>

<script>


export default {
  data () {
    return {
      externalContent: '',
      audio_stream: null,
      audio_context: null,
      recorder: null
    }
  },
  methods: {
    openURL (url) {
      remote.shell.openExternal(url)
    },
    startAudio(){
    },
  //   __log(e, data) {
  //   log.innerHTML += "\n" + e + " " + (data || '');
  // },
  startUserMedia(stream)  {
 let input = this.audio_context.createMediaStreamSource(stream);
    console.log('Media stream created.');
    // Uncomment if you want the audio to feedback directly
    //input.connect(audio_context.destination);
    //__log('Input connected to audio context destination.');
    
    this.recorder = new Recorder(input);
    console.log('Recorder initialised.');
  },
    startRecording(button) {
    this.recorder && this.recorder.record();
    button.disabled = true;
    button.nextElementSibling.disabled = false;
    console.log('Recording...');
  },
    stopRecording(button) {
    this.recorder && this.recorder.stop();
    button.disabled = true;
    button.previousElementSibling.disabled = false;
    console.log('Stopped recording.');
    
    // create WAV download link using audio data blob
    this.createDownloadLink();
    
    this.recorder.clear();
  },
     createDownloadLink() {
    this.recorder && this.recorder.exportWAV(function(blob) {
      console.log(blob);
      var url = URL.createObjectURL(blob);
      var li = document.createElement('li');
      var au = document.createElement('audio');
      var hf = document.createElement('a');
      
      au.controls = true;
      au.src = url;
      hf.href = url;
      hf.download = new Date().toISOString() + '.wav';
      hf.innerHTML = hf.download;
      li.appendChild(au);
      li.appendChild(hf);
      recordingslist.appendChild(li);
    });
  },
    speech(){
    window.SpeechRecognition = window.webkitSpeechRecognition || window.SpeechRecognition;
    let finalTranscript = '';
    let recognition = new window.SpeechRecognition();
    recognition.interimResults = true;
    recognition.maxAlternatives = 10;
    recognition.continuous = true;
    recognition.onresult = (event) => {
      console.log("event?")
      let interimTranscript = '';
      for (let i = event.resultIndex, len = event.results.length; i < len; i++) {
        let transcript = event.results[i][0].transcript;
        if (event.results[i].isFinal) {
          finalTranscript += transcript;
        } else {
          interimTranscript += transcript;
        }
      }
      console.log(interimTranscript)
      document.querySelector('body').innerHTML = finalTranscript + '<i style="color:#ddd;">' + interimTranscript + '</>';
    }
    recognition.start();
    },
    handleClickStart(){
        let shouldStop = false;
  let stopped = false;
  // const downloadLink = document.getElementById('download');
  // const stopButton = document.getElementById('stop');

  // stopButton.addEventListener('click', function() {
  //   shouldStop = true;
  // })
 var handleSuccess = function(stream) {  
    const options = {mimeType: 'video/webm;codecs=vp9'};
    const recordedChunks = [];
    const mediaRecorder = new MediaRecorder(stream, options);  
    console.log("dat1")
    console.log(mediaRecorder)
    mediaRecorder.addEventListener('dataavailable', function(e) {
          console.log("dat2")
      if (e.data.size > 0) {
        recordedChunks.push(e.data);
        console.log("data")
      }

      if(shouldStop === true && stopped === false) {
        console.log("pare")
        mediaRecorder.stop();
        stopped = true;
      }
    });

    mediaRecorder.addEventListener('stop', function() {
      console.log("pare")
      // downloadLink.href = URL.createObjectURL(new Blob(recordedChunks));
      // downloadLink.download = 'acetest.wav';
    });

    mediaRecorder.start();
  };
      navigator.mediaDevices.getUserMedia({ audio: true, video: false })
      .then(handleSuccess);
      // navigator.mediaDevices.getUserMedia({ audio: true }).then((mediaStreamObject)=>{
      //     this.audio_stream = mediaStreamObject
      //     const input = this.audio_context.createMediaStreamSource(mediaStreamObject)
      //     console.log("listening,", input)
      // })
    }
  },
  async mounted(){

 try {
      // webkit shim
      window.AudioContext = window.AudioContext || window.webkitAudioContext;
      navigator.getUserMedia = navigator.getUserMedia || navigator.webkitGetUserMedia;
      window.URL = window.URL || window.webkitURL;
      
      audio_context = new AudioContext;
      console.log('Audio context set up.');
      console.log('navigator.getUserMedia ' + (navigator.getUserMedia ? 'available.' : 'not present!'));
    } catch (e) {
      alert('No web audio support in this browser!',e);
    }
    
    navigator.getUserMedia({audio: true}, this.startUserMedia, function(e) {
      console.log('No live audio input: ' + e);
    });
  //         window.SpeechRecognition = window.webkitSpeechRecognition || window.SpeechRecognition;
  //   let finalTranscript = '';
  //   let recognition = new window.SpeechRecognition();
  //   recognition.interimResults = true;
  //   recognition.maxAlternatives = 10;
  //   recognition.continuous = true;
    
  //   recognition.onresult = (event) => {
  //     console.log("event?")
  //     let interimTranscript = '';
  //     for (let i = event.resultIndex, len = event.results.length; i < len; i++) {
  //       let transcript = event.results[i][0].transcript;
  //       if (event.results[i].isFinal) {
  //         finalTranscript += transcript;
  //       } else {
  //         interimTranscript += transcript;
  //       }
  //     }
  //     console.log(interimTranscript)
  //     document.querySelector('body').innerHTML = finalTranscript + '<i style="color:#ddd;">' + interimTranscript + '</>';
  //   }
  //   recognition.start();
  //  try {
  //       window.AudioContext = window.AudioContext || window.webkitAudioContext
  //       window.URL = window.URL || window.webkitURL
  //       this.audio_context = new AudioContext()
  //       console.log("this", this.audio_context)
  //       // this.handleClickStart()
  //       this.speech()
  //     } catch (e) {
  //       alert('No web audio support in this browser!')
  //     }
  
  }
}
</script>

<style>
.e-nuxt-container {
  min-height: calc(100vh - 50px);
  background: linear-gradient(to right, #ece9e6, #ffffff);
  font-family: Helvetica, sans-serif;
}

.e-nuxt-content {
  display: flex;
  justify-content: space-around;
  padding-top: 100px;
  align-items: flex-start;
  flex-wrap: wrap;
}

.e-nuxt-logo{
  width: 400px;
}

.e-nuxt-system-info {
  padding: 20px;
  border-top: 1px solid #397c6d;
  border-bottom: 1px solid #397c6d;
}

.e-nuxt-links {
  padding: 100px 0;
  display: flex;
  justify-content: center;
}

.e-nuxt-button {
  color: #364758;
  padding: 5px 20px;
  border: 1px solid #397c6d;
  margin: 0 20px;
  border-radius: 15px;
  font-size: 1rem;
}

.e-nuxt-button:hover{
  cursor: pointer;
  color: white;
  background-color: #397c6d;
}
</style>
