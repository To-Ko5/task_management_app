<template>
  <div class="container">
    <div class="chats-layout">
        <h2 class="channel-title">{{ nowChannel }}</h2>
        <all-Message :pageId="pageId" />
    </div>

    <div class="input-layout">
      <formArea />
    </div>
  </div>
</template>

<script>
import allMessage from '~/components/allMessage.vue'
import formArea from '~/components/formArea.vue'
import {db} from '~/plugins/firebase'
export default {
  data(){
      return {
          nowChannel: '',
          pageId: this.$route.params.id
      }
  },
  components: {
    allMessage,
    formArea
  },
  mounted(){
    db.collection('channels').doc(this.pageId).get()
    .then((doc)=>{
        this.nowChannel = doc.data().name
    }).catch((error)=>{
        alert(error)
    })
  }
}
</script>



<style lang="scss" scoped>

.container {
    height: 100%;
    .chats-layout {
        overflow: auto;
        height: 90%;
        padding: 20px;
        box-sizing: border-box;
        .channel-title {
          margin-bottom: 30px;
        }
    }
    .input-layout {
         height: 10%;
    }
}

</style>