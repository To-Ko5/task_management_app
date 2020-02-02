<template>
     <div class="chat-container">
       <div class="message-container">
         <div v-for="(message,index) in messages" :key="index" class="message-area" :class="importantCheck(message.importantTask)"  @mouseover="showWindow(index)" @mouseleave="leaveWindow">
             <p class="message-text" :class="completionCheck(message.completionTask)">{{message.text}}</p>
             <p class="create-task-day">{{ createTaskDayFormat(message.createdAt) }}</p>
             <div class="task-progress" v-show="showWindowFlag && index === mouseOverIndex">

                 <div class="task-block" @click="completionTask(message,index)" @mouseover="taskBlockMouseOverCompletion(message.completionTask)" @mouseleave="taskBlockMouseleave('completion')">
                     <span v-if="!message.completionTask"><i class="fas fa-check-square"></i></span>
                     <span v-else><i class="fas fa-window-close"></i></span>
                     <div v-if="hoverFlag.completionFlag" class="task-description">
                         <p >{{ hoverMessage.completionTaskHoverMessage }}</p>
                     </div>
                 </div>

                 <div class="task-block" @click="importantTask(message,index)" @mouseover="taskBlockMouseOverImportant(message.importantTask)" @mouseleave="taskBlockMouseleave('important')">
                     <span :class="importantCheck(message.importantTask)"><i class="fas fa-star"></i></span>
                     <div v-if="hoverFlag.importantFlag" class="task-description">
                         <p>{{ hoverMessage.importantTaskHoverMessage }}</p>
                     </div>
                 </div>

                 <div class="task-block" @click="deleteTask(message)" @mouseover="taskBlockMouseOverDelete" @mouseleave="taskBlockMouseleave('delete')">
                     <span><i class="fas fa-trash-alt"></i></span>
                     <div v-show="hoverFlag.deleteFlag" class="task-description">
                         <p>{{ hoverMessageContent.deleteTaskContent }}</p>
                     </div>
                 </div>
             </div>
         </div>
       </div>
     </div>
</template>

<script>
import { db } from '~/plugins/firebase'

export default {
    props: ['pageId'],
    data(){
        return {
            messages: [],
            showWindowFlag: false,
            mouseOverIndex: null,
            completionTaskClass: 'completion-task-class',
            importantTaskClass: 'important-task-class',
            hoverFlag: {
                completionFlag: false,
                importantFlag: false,
                deleteFlag: false,
            },
            hoverMessageContent: {
                completionTaskFinish: 'タスク完了',
                completionTaskUnfinished: '未完に戻す',
                importantTaskChecked: '重要タスクにする',
                importantTaskReturnChecked: '重要タスクから外す',
                deleteTaskContent: 'タスクを削除します'
            },
            hoverMessage: {
                completionTaskHoverMessage: '',
                importantTaskHoverMessage: ''
            }
        }
    },
      mounted(){
          this.dbcon()
    },
    methods: {
        dbcon() {
             db.collection('channels').doc(this.pageId).collection('messages').orderBy('createdAt')
            .onSnapshot((snapshot)=>{
                    snapshot.docChanges().forEach((change)=>{
                    const doc = change.doc
                    if(change.type === 'added') {
                        this.messages.push({
                        id:doc.id,
                        ...doc.data()
                        })
                    }
                })
            })
        },
        showWindow(index){
            this.mouseOverIndex = index
            this.showWindowFlag = true
        },
        leaveWindow() {
            this.showWindowFlag = false
            this.index = null
        },
        completionCheck(completionTask) {
            if(completionTask) {
                return this.completionTaskClass
            }
        },
        importantCheck(importantTask) {
            if(importantTask) {
                return this.importantTaskClass
            }
        },
        completionTask(message,index){
            const completion = db.collection('channels').doc(this.pageId).collection('messages').doc(message.id)
            return completion.update({
                completionTask: !message.completionTask
            }).then(()=>{
                this.messages[index].completionTask = !message.completionTask
                this.hoverFlag.completionFlag = false;
                this.taskBlockMouseOverCompletion(message.completionTask)
            }).catch((error)=>{
                alert(error)
            })
        },
        importantTask(message,index){
            const completion = db.collection('channels').doc(this.pageId).collection('messages').doc(message.id)
            return completion.update({
                importantTask: !message.importantTask
            }).then(()=>{
                this.messages[index].importantTask = !message.importantTask
                this.hoverFlag.importantFlag = false;
                this.taskBlockMouseOverImportant(message.importantTask)
            }).catch(()=>{
                 alert(error)
            })
        },
        deleteTask(message){
            const completion = db.collection('channels').doc(this.pageId).collection('messages').doc(message.id)
            completion.delete()
            .then(()=>{
                 const del = this.messages.filter((value)=>{
                     return value.id != message.id
                 })
                 this.messages = del
            }).catch((error)=>{
                alert(error)
            })
        },
        createTaskDayFormat(createDay) {
            const createTime = new Date(createDay)
            const year = createTime.getFullYear()
            const month = ("0"+(createTime.getMonth() + 1)).slice(-2)
            const date = ("0"+createTime.getDate()).slice(-2)
            const hour = ("0"+createTime.getHours()).slice(-2)
            const min = createTime.getMinutes()
            return `${year}年${month}月${date}日${hour}時${min}分`
        },
        taskBlockMouseOverCompletion(completionTask){
            if(completionTask) {
                this.hoverMessage.completionTaskHoverMessage = this.hoverMessageContent.completionTaskUnfinished
            }else {
                this.hoverMessage.completionTaskHoverMessage = this.hoverMessageContent.completionTaskFinish
            }
            this.hoverFlag.completionFlag = true
        },
        taskBlockMouseOverImportant(importantTask){
            if(importantTask) {
                this.hoverMessage.importantTaskHoverMessage = this.hoverMessageContent.importantTaskReturnChecked
            }else {
                this.hoverMessage.importantTaskHoverMessage = this.hoverMessageContent.importantTaskChecked
            }
            this.hoverFlag.importantFlag = true
        },
        taskBlockMouseOverDelete(){
            this.hoverFlag.deleteFlag= true
        },
        taskBlockMouseleave(task) {
            this.hoverFlag.completionFlag = false
            this.hoverFlag.importantFlag = false
            this.hoverFlag.deleteFlag = false
        }
    },
}

</script>

<style lang="scss" scoped>

.message-area {
    margin-bottom: 30px;
    padding: 18px;
    border: 1px solid #333;
    border-radius: 4px;
    position: relative;
    cursor: pointer;
    &:hover {
        background: #b9e1fc;
        transition: .3s;
    }
    .message-text {
        line-height: 1.6;
        &.completion-task-class {
                text-decoration: line-through;
                text-decoration-style: double;
            }
    }
    .create-task-day {
        position: absolute;
        bottom: 4%;
        right: 0.5%;
        font-size: 1.2rem;
    }
    &.important-task-class {
        background: #C79681;
    }
    .task-progress {
        background: rgba(225, 225, 225, 0.897);
        border-radius: 4px;
        padding: 10px;
        box-sizing: border-box;
        display: flex;
        justify-content: space-around;
        width: 30%;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        .task-block {
            border: 1px solid #333;
            border-radius: 8px;
            width: 10%;
            padding: 8px 0;
            box-sizing: border-box;
            text-align: center;
            position: relative;
            & .important-task-class {
                color: rgb(255, 21, 21);
            }
            &i {
                display: block;
            }
            &:hover {
                background: #FBB57D;
            }
            .task-description {
                position: absolute;
                left: 50%;
                top: -80%;
                width: 200px;
                transform: translate(-50%, -80%);
                background: #0f4c75;
                padding: 10px;
                box-sizing: border-box;
                color: #fff;
                border-radius: 4px;
                &::after {
                    content: "";
                    position: absolute;
                    top: 99%;
                    left: 50%;
                    margin-left: -15px;
                    border: 15px solid transparent;
                    border-top: 15px solid #0f4c75;
                }
            }
        }
    }
}

</style>