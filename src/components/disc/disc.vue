<template>
  <div class="disc">
    <music-list :songs="songs" :bg-image="bgImage" :title="title"></music-list> 
  </div>
</template>

<script type="text/ecmascript-6">
  import MusicList from 'components/music-list/music-list'
  import {createSong} from 'common/js/song'
  import {mapGetters} from 'vuex'
  import {getSongList2} from 'api/recommend'

  export default {
    components: {
      MusicList
    },
    data() {
      return {
        songs: []
      }
    },
    computed: {
      title() {
        return this.disc.dissname
      },
      bgImage() {
        return this.disc.imgurl
      },
      ...mapGetters([
        'disc'
      ])
    },
    methods: {
      _getSongList() {
        if (!this.disc.dissid) {
          this.$router.push('/recommend')
          return
        }        
        getSongList2(this.disc.dissid).then((res) => {
          if (res.code === 0) {
            this.songs = this._normalizeSongs(res.cdlist[0].songlist)
          }
        })
      },
      _normalizeSongs(list) {
        let ret = []
        list.forEach((musicData) => {
          if (musicData.songid && musicData.albummid) {
            ret.push(createSong(musicData))
          }
        })
        return ret
      }
    },
    created() {
      this._getSongList()
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  .disc
    position: fixed
    top: 0
    bottom: 0
    left: 0
    right: 0
    overflow: hidden
    background-color: black
</style>
