<template>
  <div style="margin: 40px;">
    <!-- {{ typeof(tData) }} {{ tData }} -->
    <h1>
      Таблица
    </h1>
    <div class="tbl-header">
      <table cellpadding="0" cellspacing="0" border="0">
        <thead>
          <tr>
              <div class="form__group field">
                <input type="input" class="form__field" placeholder="Поиск" required v-model="filterRow"/>
                <label for="name" class="form__label">Поиск</label>
              </div>
            <!-- <input type="text" v-model="filterRow"> -->
          </tr>
          <tr>
            <th v-for="head in theaders" :key="head" style="min-width: 200px;">
              {{ head }}
              <button class="btn" v-if="head == 'id'" 
                style="min-width: 20px; min-height: 20px;" 
                @click="sorting(head)">
                {{ desc.id && head == 'id'  ? 'убыв..' : 'возр..' }}
                
              </button>
              <button class="btn" v-if="head == 'Дата'" 
                style="min-width: 20px; min-height: 20px;" 
                @click="sorting(head)">
                {{ desc.date ? 'убыв..' : 'возр..' }}
              </button>
  
            </th>
          </tr>
        </thead>
      </table>
      <div style="overflow: auto; max-height: 200px;">
        <table cellpadding="0" cellspacing="0" border="0">
          <tbody style="overflow: auto; height: 200px;">
            <tr v-for="row in tData" :key="row">
              <td v-for="(col, index) in row" :key="index">
                {{ index == 'date' ? returnDay(col) : col }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment';
export default {
  name: 'MTable',
  props: {
    msg: String
  },
  data() {
    return {
      theaders: ['id', 'Имя', 'Описание', 'Дата'],
      tData: [],
      constData: [],
      filterRow: '',
      desc: {
        date: true,
        id: true,
      },
    }
  },
  created(){
    this.getData()
  },
  watch: {
    filterRow(nw, ow) {
      if(nw == '' && ow != ''){ // ow != '' чтоб eslint не ругался
        this.tData = this.constData
      }else{
        this.tData  = this.constData.filter(f=>{
          if(
            f.name.toLowerCase().includes(this.filterRow.toLowerCase()) || 
            f.description.toLowerCase().includes(this.filterRow.toLowerCase())){
            return f
          }
        })
      }
    },
  },  
  methods: {
    //for date and id
    sorting(type){
      console.log('type', type);
      switch (type) {
        case 'id':
          if(!this.desc.id){
            this.tData.sort((a,b)=>{
              if (a.id > b.id) return 1;
              if (a.id == b.id) return 0;
              if (a.id < b.id) return -1;
            })

          }else{
            this.tData.sort((a,b)=>{
              if (a.id < b.id) return 1;
              if (a.id == b.id) return 0;
              if (a.id > b.id) return -1;
            })
          }
          // console.log('this.tData', this.tData);
          this.desc.id = !this.desc.id
          break;
        
        case 'Дата':
          if(!this.desc.date){
            this.tData.sort((a,b)=>{
              if (moment(a.date).isBefore(moment(b.date))) return 1;
              if (moment(a.date).isSame(moment(b.date))) return 0;
              if (moment(a.date).isAfter(moment(b.date))) return -1;
            })

          }else{
            this.tData.sort((a,b)=>{
              if (moment(b.date).isBefore(moment(a.date))) return 1;
              if (moment(b.date).isSame(moment(a.date))) return 0;
              if (moment(b.date).isAfter(moment(a.date))) return -1;
            })
          }
          this.desc.date = !this.desc.date
          break;

      }
    },

    // data
    async getData(){
      await fetch('data.json')
      .then(response => response.json())
      .then(dat => {
        this.tData = dat
        this.constData = dat
      })
    },
    // utils
    returnDay(col){
      return moment(col).format('DD.MM.YYYY')
    },
  },
}
</script>

<style type="scss">
.btn {
	border: none;
	font-family: 'Lato';
	font-size: 12px;
	color: inherit;
	cursor: pointer;
  width: 80px;
	padding: 5px 8px;
	display: inline-block;
	margin: 15px 30px;
	text-transform: uppercase;
	letter-spacing: 1px;
	font-weight: 700;
}

/* input */
.form__group {
  position: relative;
  padding: 15px 0 0;
  margin-top: 10px;
  margin-bottom: 20px;

  width: 50%;
}

.form__field {
  font-family: inherit;
  width: 100%;
  border: 0;
  border-bottom: 2px solid #9b9b9b;
  outline: 0;
  font-size: 1.3rem;
  color: #092635;
  padding: 7px 0;
  
  background: transparent;
  transition: border-color 0.2s;

  &::placeholder {
    color: transparent;
  }

  &:placeholder-shown ~ .form__label {
    font-size: 1.3rem;
    cursor: text;
    top: 20px;
  }
}

.form__label {
  position: absolute;
  top: 0;
  display: block;
  transition: 0.2s;
  font-size: 1rem;
  color: #9b9b9b;
}

.form__field:focus {
  ~ .form__label {
    position: absolute;
    top: 0;
    display: block;
    transition: 0.2s;
    font-size: 1rem;
    color: #11998e;
    font-weight:700;    
  }
  padding-bottom: 6px;  
  font-weight: 700;
  border-width: 3px;
  border-image-slice: 1;
}


/* table and others */
h1{
  font-size: 30px;
  color: #fff;
  text-transform: uppercase;
  font-weight: 300;
  text-align: center;
  margin-bottom: 15px;
}
table{
  width:100%;
  table-layout: fixed;
}
th{
  background-color: #9EC8B9;
  padding: 20px 15px;
  text-align: left;
  font-weight: 500;
  font-size: 22px;
  color: #092635;
  text-transform: uppercase;
}
td{
  padding: 15px;
  background-color: #F6B17A;
  text-align: left;
  vertical-align:middle;
  font-weight: 300;
  font-size: 16px;
  color: #1B4242;
  border-bottom: solid 1px rgba(255,255,255,0.1);
}

</style>