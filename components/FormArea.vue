<template>
    <div class="input-container">
        <textarea v-model="text"></textarea>
        <p class="register-area"><button @click="addMessage" class="register-btn">登録</button></p>
    </div>
</template>

<script>
import { db } from '~/plugins/firebase'
export default {
    data(){
        return {
            text: null
        }
    },
    methods: {
        addMessage(event){
            if(!this.text || !this.text.match(/\S/g)) {
                alert('何も入力されていません')
                return
            }

            const channelId = this.$route.params.id
            db.collection('channels').doc(channelId).collection('messages')
            .add({
                text: this.text,
                completionTask: false,
                importantTask: false,
                createdAt: new Date().getTime()
            })
            .then(()=>{
                this.text = null
            }).catch((error)=>{
                alert(error)
            })
        },
    }
}
</script>

<style lang="scss" scoped>

$btn-size: 150px;

.input-container {
    padding: 10px;
    box-sizing: border-box;
    height: 100%;
    display: flex;
    justify-content: space-between;
    textarea {
        width: calc(100% - (#{$btn-size} + 1%));
        height: 100%;
        border-radius: 4px;
        outline: none;
        resize: none;
    }
    .register-area {
        width: $btn-size;
        .register-btn {
            border: none;
	        outline: none;
            width: 100%;
            height: 100%;
            font-family: inherit;
            font-weight: 400;
            font-size: 1.6rem;
            letter-spacing: 2px;
            background: #dedede;
            cursor: pointer;
            &:hover {
                opacity: 0.7;
            }
        }
    }
}

</style>