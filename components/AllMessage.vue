<template>
     <div class="chats-container">
         <Message :pageId="pageId" />
     </div>
</template>

<script>
import Message from '~/components/Message.vue'
export default {
    props:['pageId'],
    components: {
        Message
    }
}
</script>


