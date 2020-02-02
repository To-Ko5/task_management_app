<template>
    <div>
    <h2 class="title">Channel<span class="sidebar-plus" @click="channelPlus"><i class="fas fa-plus"></i></span></h2>
    
    <div v-show="channelPlusFlag" class="channel-plus-section">
        <p><input class="channel-input-area" type="text" name="" v-model="channelTitle" autofocus @keydown.enter="channeInput"></p>
        <p><button class="channel-input-btn" @click="channeInput">追加</button></p>
    </div>

    <ul class="sidebar-title" >
        <li v-for="(channel,index) in channels" :key="index" class="sidebar-title-list">
            <nuxt-link :to="`/channels/${channel.id}`">{{channel.name}}</nuxt-link>
            <span class="channel-delete-area" @click="deleteChannel(channel.id)"><i class="far fa-times-circle"></i></span>
        </li>
    </ul>
    </div>
</template>


<script>
import { db } from '~/plugins/firebase'
export default {
    data(){
        return {
            channels: [],
            channelPlusFlag: false,
            channelTitle: null,
        }
    },
    mounted(){
            db.collection('channels').orderBy('createdAt')
            .onSnapshot((snapshot)=>{
                    snapshot.docChanges().forEach((change)=>{
                    const doc = change.doc
                    if(change.type === 'added') {
                        this.channels.push({
                        id:doc.id,
                        ...doc.data()
                        })
                    }
                })
            })
    },
    methods: {
        deleteChannel(channelId){
            const ans = confirm('チャンネルを削除してもよろしいでしょうか？')
            if(!ans){
                return
            }
            db.collection("channels").doc(channelId).delete()
            .then(()=> {
                const del = this.channels.filter((value)=>{
                    return value.id != channelId  
                })
                this.channels = del
                this.$router.push('/')
            }).catch((error)=> {
                alert(error)
            });
        },
        channelPlus(){
            this.channelPlusFlag = !this.channelPlusFlag
        },
        channeInput(){
            if(!this.channelTitle || !this.channelTitle.match(/\S/g)){
                alert('タイトルを入力してください')
                return
            }
            db.collection('channels').add({
                name: this.channelTitle,
                createdAt: new Date().getTime()
            }).then(()=>{
                this.channelTitle = null,
                this.channelPlusFlag = false
            }).catch((error)=>{
                alert(error)
            })
        }
    }
}
</script>


<style lang="scss" scoped>

.title {
    margin-bottom: 30px;
    overflow: hidden;
    .sidebar-plus {
        display: block;
        float: right;
        cursor: pointer;
    }
}

.channel-plus-section {
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px;
    .channel-input-area {
        line-height: 1.8;
        border: none;
	    outline: none;
        box-shadow: 0px 2px 2px rgba(0, 0, 0, 0.29);
        border-bottom: solid 3px #627295;
    }
    .channel-input-btn {
        line-height: 1.8;
        border: none;
	    outline: none;
        cursor: pointer;;
        background: #ffffff;
        color: #0f4c75;
        box-shadow: 0px 2px 2px rgba(0, 0, 0, 0.29);
        border-bottom: solid 3px #627295;
        text-shadow: 1px 1px 1px rgba(255, 255, 255, 0.5);
    }
}

.sidebar-title {
    margin-bottom: 18px;
    .sidebar-title-list {
      overflow: hidden;
      line-height: 1.6;
      font-size: 1.8rem;
      &:not(:last-of-type) {
          margin-bottom: 10px;
      }
      .channel-delete-area {
        display: block;
        float: right;
        text-align: right;
        cursor: pointer;
      }
    }
  }

</style>
