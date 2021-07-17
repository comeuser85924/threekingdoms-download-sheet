<template>
    <div v-show="popupStatus" class="modal-mask">
        <div class="modal-wrapper">
        <div class="modal-container">
            <div class="modal-header">
                <h3  v-if="!nextStatus">{{popupData.instructions.title}}</h3>
                <h3  v-if="nextStatus">{{popupData.note.title}}</h3>
            </div>

            <div v-if="!nextStatus" class="modal-body" v-html="popupData.instructions.content"> </div>
            <div v-if="nextStatus" class="modal-body" v-html="popupData.note.content"></div>
            <div class="modal-footer">
            <slot name="footer">
                <button v-if="nextStatus" class="btn btn-secondary" @click="nextPage(false)">
                上一頁
                </button>
                <button v-if="!nextStatus" class="btn btn-secondary" @click="nextPage(true)">
                下一頁
                </button>
                <button v-if="nextStatus" class="btn btn-secondary" @click="hidePanel">
                關閉
                </button>
            </slot>
            </div>
        </div>
        </div>
    </div>
</template>

<script>

export default {
    name: 'popup',
    data(){
        return{
            popupData:
            {
                instructions:{
                    title:'使用說明',
                    content:
                    "嗨！三國志的各位英雄豪傑們"+
                    "再請配將大神配將時，當你在製武將及戰法作表格時<br>"+
                    "是否會覺得三國中的人物名字很難輸入嗎？<br>"+
                    "是否會覺得三國中的戰法名稱很難輸入嗎？<br>"+
                    "是否會覺得一直要選字很麻煩嗎？<br>"+

                    "現在此網站的擁有【快速產表格功能】<br>"+
                    "只要透過鼠標勾選，就能快速將武將及戰法下載表格至你電腦中！趕快來使用吧！<br><br>"+
                    "<span class='text-danger font-weight-bold f22'>備註 : 若要精美的排版請再請自行調整</span>"
                },
                note:{
                    title:'注意事項',
                    content:
                    "由於本人是低成本製作此網站<br>"+
                    "所以有些不便的部分先與各位說明<br>"+
                    "此網站只要超過<span class='text-danger'> 30 分鐘 </span> 無人使用的話，網站會<span class='text-danger'> 自動進入休眠狀態</span><br>"+
                    "所以有可能會有進網站但載入很久的情況發生<br>"+
                    "<span class='text-danger'>此時請耐心等待約 30 秒，網站就會自動會喚醒</span><br>"+
                    "若造成不便，請耐心等候<br><br>"+
                    "<span class='text-danger font-weight-bold f22'>備註 : 網站將會視各位的使用情況進行更新</span>"
                }
            },
            popupStatus: true,
            nextStatus: false,
        }
    },
    components: {

    },
    methods:{
        nextPage(status){
            this.nextStatus = status
        },
        hidePanel() {
            this.popupStatus = false
        }
    }
}
</script>

<style>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 50%;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  text-align: left;
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

@media screen and (max-width: 768px){

  .modal-container {
      overflow:inherit;
      width:85%;
      padding: 10px 5px;
  }
}
@media screen and (max-width: 320px){
  .modal-header{
    padding: 0 1rem !important;
  }
  .modal-body{
    margin:0!important;
  }
}
</style>
