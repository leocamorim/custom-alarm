<template>
  <div id="app">
    
    <h1 v-on:load="console.log(curTime)">{{curTime}}</h1>

    <div class="input-box">
      <input class="url" type="text" v-model="$data._searchKey" placeholder="Digite o nome do vídeo do youtube...">

      <input class="single-time" type="number" v-model="$data._alarmHrs">
      <input class="single-time" type="number" v-model="$data._alarmMin">
      <input class="single-time" type="number" v-model="$data._alarmSec">
      <!-- <button style="margin-top: 10px;" v-on:click="alarmRings">SHOOT!</button> -->
    </div>

    <div v-html="$data._iframeHtml"></div>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  data () {
    return {
      curTime: 'CUSTOM ALARM !!',
      _alarmHrs: '8',
      _alarmMin: '0',
      _alarmSec: '0',
      _searchKey: '',
      _iframeHtml: ''
    }
  },
  mounted: function() {
    this.reloadCurTime();
  },
  methods: {
    reloadCurTime: function () {
      let refreshTime = setInterval(() => {
        let current_datetime = new Date()
        let _hrs = (current_datetime.getHours() < 10 ? '0'+current_datetime.getHours() : current_datetime.getHours());
        let _min = (current_datetime.getMinutes() < 10 ? '0' + current_datetime.getMinutes() : current_datetime.getMinutes());
        let _sec = (current_datetime.getSeconds() < 10 ? '0'+current_datetime.getSeconds() :current_datetime.getSeconds());
        let formatted_date = _hrs + ":" + _min + ":" + _sec;
        this.curTime = formatted_date;

        this.$data._alarmHrs = (this.$data._alarmHrs.length < 2 ? '0'+this.$data._alarmHrs : this.$data._alarmHrs);
        this.$data._alarmMin = (this.$data._alarmMin.length < 2 ? '0'+this.$data._alarmMin : this.$data._alarmMin);
        this.$data._alarmSec = (this.$data._alarmSec.length < 2 ? '0'+this.$data._alarmSec : this.$data._alarmSec);

        if(_hrs == this.$data._alarmHrs && _min == this.$data._alarmMin && _sec == this.$data._alarmSec) {
          this.alarmRings();
        }
        // console.warn('HeartBeat:', 'I AM ALIVE!')
      }, 1000)
    },
    alarmRings: function () {
      let _val = this.$data._searchKey;
      const key = 'AIzaSyBC127ZSBObfsxEMfGLXJjpY5nBGzVkeNc';
      const ytApiSimpleSearchURL = 'https://www.googleapis.com/youtube/v3/search';

      var options = {
        part: 'snippet',
        key: key,
        maxResults: 1,
        type: 'video',
        q: _val
      }

      axios.get(ytApiSimpleSearchURL, {params: options})
      .then(data => {
        let _url = 'https://www.youtube.com/watch?v=' + data.data.items[0].id.videoId;
        let _html = '<iframe src="https://www.youtube.com/embed/'+data.data.items[0].id.videoId+'?autoplay=1" allow="autoplay"></iframe>';
        this.$data._iframeHtml = _html;
      });
    }
  }
}
</script>

<style>
.input-box {
  margin-bottom: 15px;
}
input.url {
  width: 500px;
}
input.single-time {
  width: 30px
}
</style>
