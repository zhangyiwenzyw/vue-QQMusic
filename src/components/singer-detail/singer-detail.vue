<template>
    <transition name="slide">
        <music-list :title="title" :bg-image="bgImage"  :songs="songs"></music-list>
    </transition>
</template>

<script>

import {getSingerDetail} from 'api/singer'
import {ERR_OK} from 'api/config'
import {getMusic} from 'api/music'
import {createSong} from 'common/js/song'
import MusicList from 'components/music-list/music-list'
import {mapGetters} from 'vuex'
export default {
    data(){
        return{
            songs:[]
        }
    },
    computed: {
        title() {
            return this.singer.name
        },
        bgImage(){
            return this.singer.avatar
        },
        ...mapGetters([
            'singer'
        ])
    },
    created(){
        this._getDetail()
    },
    methods: {
        _getDetail(){
            if(!this.singer.id){
                this.$router.push('/singer')
                return
            }
            getSingerDetail(this.singer.id).then((res) => {
                if(res.code === ERR_OK){
                    console.log(res.data.list)
                    this.songs = this._normalizeSongs(res.data.list)
                    console.log(this.songs)
                }
            })
        },
        _normalizeSongs(list) {
            let ret = []
            list.forEach((item)=>{
                let {musicData} = item
                if(musicData.songid && musicData.albummid){
                    getMusic(musicData.songmid).then((res)=>{
                        if(res.code === ERR_OK){
                            const svkey = res.data.items
                            const songVkey = svkey[0].vkey
                            const newSong = createSong(musicData,songVkey)
                            ret.push(newSong)                           
                        }
                    })
                }
            })
            return ret
            console.log(ret)
        }
    },
    components:{
        MusicList
    }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
    @import "~common/stylus/variable"

    .singer-detail{
        position:fixed;
        z-index:100;
        top:0;
        left:0;
        right:0;
        bottom:0;
        background: $color-background
    }  
    .slide-enter-active, .slide-leave-active{
        transition:transform 0.3s
    }
    .slide-enter, .slide-leave-to{
        transform: translateX(100%)
    }
       
    
</style>

