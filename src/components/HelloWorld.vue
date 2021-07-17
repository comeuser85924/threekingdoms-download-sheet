<template>
  <div style="max-width: 1280px;" class="mx-auto">
    <div class="my-2">
      <div class="mx-auto mb-4">
        <div class=" d-flex flex-wrap">
          <h2 class="w-100 m-2 title">武將區</h2>
          <button class="btn btn-primary text-white m-1 w-20" v-for="item of generalsCheckedTextList" :key="item.id" @click='skillsChecked(item)' >{{item.name}}</button>
          <button class="btn btn-outline-success m-1 w-10"  @click="generalExportExcel">匯出表格</button>
        </div>
      </div>
      <ul class="nav nav-tabs mx-auto d-flex justify-content-between">
        <div>
          <li class="nav-item" v-for="(item) in generalsTabList" :key="item.key">
            <a class="nav-link" style="cursor: pointer;" :class="{'active': item.tab  === generalsTab}" @click="handleGeneralsTab(item.tab)">{{item.tab}}</a>
          </li>
        </div>
        <div class="d-flex  w-20 my-2 align-items-center">
          <span class="w-40">品質篩選:</span>
          <select class="w-60 form-select form-select-sm mx-2"  @change="filterQuality($event)" v-model="generalsFilterText">
            <!--有value傳value，沒有value就傳包含的文字-->
            <option selected value="全部">全部</option>
            <option value="4"> 4星武將 </option>
            <option value="5"> 5星武將 </option>
          </select>
        </div>
      </ul>
      <div class="checkbox-form mx-auto mt-4">
        <div class="answers m-auto">
          <div v-for="(item,i) of generalsList" :key="i">
            <label v-if="item.country === generalsTab" class="item" >
              <span :for="item">{{ item.name }}</span>
              <input
                v-model="generalsExcelData"
                type="checkbox"
                :value="item"
              >
              <span class="checkmark" />
            </label>
            <label v-if="generalsTab === '所有武將'" class="item" >
              <span :for="item">{{ item.name }}</span>
              <input
                v-model="generalsExcelData"
                type="checkbox"
                :value="item"
              >
              <span class="checkmark" />
            </label>
          </div>
        </div>
      </div>
    </div>
    <div class="my-2">
      <div class="mx-auto mb-4">
        <div class=" d-flex flex-wrap">
          <h2 class="w-100 m-2 title">戰法區</h2>
          <button class="btn btn-primary text-white m-2 w-20"  v-for="item of skillsCheckedTextList" :key="item.id" @click='skillsChecked(item)' >{{item.name}}</button>
          <button class="btn btn-outline-success m-2 w-10"  @click="skillsExportExcel">匯出表格</button>
        </div>
      </div>
      <ul class="nav nav-tabs mx-auto">
        <li class="nav-item" v-for="(item) in skillsTabList" :key="item.key">
          <a class="nav-link" style="cursor: pointer;" :class="{'active': item.tab  === skillsCurrentTab}" @click="handleSkillsTab(item.tab)">{{item.tab}}</a>
        </li>
      </ul>
      <div class="checkbox-form mx-auto mt-4">
        <div class="answers m-auto ">
          <div v-for="(item,i) of skillsList" :key="i">
            <label v-if="item.quality === skillsCurrentTab" class="item">
              <span :for="item">{{ item.name }}</span>
              <input
                v-model="skillsExcelData"
                type="checkbox"
                :value="item"
              >
              <span class="checkmark" />
            </label>
            <label v-if="skillsCurrentTab === '所有戰法'" class="item">
              <span :for="item">{{ item.name }}</span>
              <input
                v-model="skillsExcelData"
                type="checkbox"
                :value="item"
              >
              <span class="checkmark" />
            </label>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import $ from 'jquery';

export default {
  name: 'HelloWorld',
  components: {
  },
  data(){
    return{
      allData:[],

      generalsFilterText:'全部',
      generalsFilterList:[],
      generalsList:[],
      generalsExcelData:[],
      generalsTab:'所有武將',
      generalsTabList:[
        {tab:'所有武將'},
        {tab:'魏'},
        {tab:'蜀'},
        {tab:'吳'},
        {tab:'群'}
      ],
      generalsCheckedTextList:[
        { id: '魏', name: '魏國全選', status: false},
        { id: '蜀', name: '蜀國全選', status: false},
        { id: '吳', name: '吳國全選', status: false},
        { id: '群', name: '群全選', status: false},
      ],

      skillsList:[],
      skillsExcelData:[],
      skillsCurrentTab: '所有戰法',
      skillsTabList:[
        {tab: '所有戰法'},
        {tab: 'S'}, 
        {tab: 'A'}
      ],
      skillsCheckedTextList:[
        { id: 'S', name: 'S級全選', status: false},
        { id: 'A', name: 'A級全選', status: false}
      ],

    }
  },
  async beforeMount() {
    await axios.get("https://lai-api-server.herokuapp.com/threekingdoms/generals")
      .then(res => {
        this.generalsList = res.data.data
        this.allData = res.data.data
      })
      .catch(error => {
        console.log(error)
      })
    await axios.get("https://lai-api-server.herokuapp.com/threekingdoms/skills")
      .then(res => {
        this.skillsList = res.data.data
      })
      .catch(error => {
        console.log(error)
      })
  },
  methods: {
    filterQuality(event){
      this.generalsList = this.allData.filter((item)=>{
        return (event.target.value !== '全部') ? item.quality === event.target.value: this.allData
      })
    },
    generalExportExcel() {
      if(this.generalsExcelData.length === 0) return alert('請選擇武將');
      require.ensure([], () => {
        const { export_json_to_excel } = require('../excel/Export2Excel');
        const tHeader = ['陣營', '品質', '姓名'];
        // const tHeader = ['魏', '蜀', '吳'];
        // 上面设置Excel的表格第一行的标题
        const filterVal = ['country','quality', 'name'];
        // 上面的index、nickName、name是tableData里对象的属性
        // const list = this.tableData;  //把data里的tableData存到list
        const list = this.generalsExcelData.sort((a,b)=> {
          if(b.country < a.country){
            return -1;
          }
        })
        const data = this.formatJson(filterVal, list);
        export_json_to_excel(tHeader, data, '三國志戰略版 武將');
      })
    },
    skillsExportExcel() {
      if(this.skillsExcelData.length === 0) return alert('請選擇戰法');
      require.ensure([], () => {
        const { export_json_to_excel } = require('../excel/Export2Excel');
        const tHeader = ['品質', '陣法'];
        const filterVal = ['quality','name'];
        const list = this.skillsExcelData.sort((a,b)=> {
          if(b.quality < a.quality){
            return -1;
          }
        })
        const data = this.formatJson(filterVal, list);
        export_json_to_excel(tHeader, data, '三國志戰略版 戰法');
      })
    },
    formatJson(filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => {
        if(j === 'quality' && v[j] === '4'  ){
          return '紫'
        }else if(j === 'quality' && v[j] === '5'  ){
          return '橙'
        }
        return v[j]
      }))
    },
    skillsChecked(skillsItem){
      switch (skillsItem.id) {
        case 'S':
          this.checkedAll(skillsItem, 'skillsCheckedTextList', 'skillsExcelData', 'skillsList', [{ selectAll: 'S級全選' },{ selectCancel: 'S級取消全選' }])
          break;
        case 'A':
          this.checkedAll(skillsItem, 'skillsCheckedTextList', 'skillsExcelData', 'skillsList', [{ selectAll: 'A級全選' },{ selectCancel: 'A級取消全選' }])
          break;
        case '魏':
          this.checkedAll(skillsItem, 'generalsCheckedTextList', 'generalsExcelData', 'generalsList', [{ selectAll: '魏國全選' },{ selectCancel: '魏國取消全選' }])
          break;
        case '蜀':
          this.checkedAll(skillsItem, 'generalsCheckedTextList', 'generalsExcelData', 'generalsList', [{ selectAll: '蜀國全選' },{ selectCancel: '蜀國取消全選' }])
          break;
        case '吳':
          this.checkedAll(skillsItem, 'generalsCheckedTextList', 'generalsExcelData', 'generalsList', [{ selectAll: '吳國全選' },{ selectCancel: '吳國取消全選' }])
          break;
        case '群':
          this.checkedAll(skillsItem, 'generalsCheckedTextList', 'generalsExcelData', 'generalsList', [{ selectAll: '群全選' },{ selectCancel: '群取消全選' }])
          break;
      }
    },
    handleGeneralsTab(tab){
      this.generalsTab = tab
    },
    handleSkillsTab(tab){
      this.skillsCurrentTab = tab
    },
    checkedAll(skillsItem, checkedTextList, excelData, allDataList, selectList){
      if(skillsItem.status){
        this[checkedTextList].map((item)=>{ 
          if(item.id == skillsItem.id) {
            item.name = selectList[0].selectAll
            item.status = false
          }
        })
        let catchList = []
        if( allDataList === 'generalsList' ){
          this[excelData].forEach((general) => { 
            if(general.country === skillsItem.id) catchList.push(general)
          });
        }else{
          this[excelData].forEach((skill) => { 
            if(skill.quality === skillsItem.id) catchList.push(skill)
          });
        }
        this[excelData] = this[excelData].filter((e)=>{ return catchList.indexOf(e) === -1 })
      }else{
        this[checkedTextList].map((item)=>{ 
          if(item.id == skillsItem.id) {
            item.name = selectList[1].selectCancel
            item.status = true
          }
        })
        let catchList = []
        if( allDataList === 'generalsList' ){
          catchList = this[allDataList].filter((general)=>{
            if( general.country === skillsItem.id ) return general
          })
        }else{
          catchList = this[allDataList].filter((skill)=>{
            if( skill.quality === skillsItem.id ) return skill
          })
        }
        catchList.forEach((item) => { this[excelData].push(item); });
      }
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.title{
 text-align: left;
}
.checkbox-form {
	display: flex;
	flex-direction: column;
  align-items: center;
  height: 100%;
}

.checkbox-form .answers {
  display: flex;
  flex-wrap: wrap;
	align-items: left;
}

.checkbox-form label {
	margin-left: 1em;
}

.checkbox-form .item {
  display: block;
  width: 125px;
	position: relative;
	padding-left: 35px;
	margin-bottom: 12px;
	cursor: pointer;
	font-size: 1em;
	height: 25px;
	display: flex;
	align-items: center;
}

.checkbox-form .item input {
	position: absolute;
	opacity: 0;
	cursor: pointer;
	height: 0;
	width: 0;
}

.checkbox-form .checkmark {
	position: absolute;
	top: 0;
	left: 0;
	height: 25px;
	width: 25px;
	background-color: #c2c2c2;
}

.checkbox-form .item:hover input ~ .checkmark {
	background-color: #949494;
}

.checkbox-form .item input:checked ~ .checkmark {
	background-color: #D8A22E;
}

.checkbox-form .checkmark:after {
	content: "";
	position: absolute;
	display: none;
}

.checkbox-form .item input:checked ~ .checkmark:after {
	display: block;
}

.checkbox-form .item .checkmark:after {
	left: 9px;
	top: 5px;
	width: 5px;
	height: 10px;
	border: solid white;
	border-width: 0 3px 3px 0;
	-webkit-transform: rotate(45deg);
	-ms-transform: rotate(45deg);
	transform: rotate(45deg);
}

@media screen and (max-width: 768px){
  .title{ text-align: center; }
  .checkbox-form .answers {
    justify-content: center;
  }
}
</style>
