<template>
  <div id="app">
    <img src="./assets/logo.png">

	<b-container fluid>
	  <b-row class="text-center">
      <b-col md="12" class="py-3">
         <b-button @click="showElements(1)" variant="success">Клиентская база</b-button>
		 <b-button @click="showElements(2)" variant="warning">Рассылка</b-button>
		 <b-button variant="primary">История</b-button>
	 </b-col>
    </b-row>

	<b-row>
		<b-col>
			<b-table v-if="showagents" hover bordered
			:items="users" :fields="fields" >
						
			 <template slot="isAdmin" slot-scope="row">
	            <b-checkbox v-model="row.item.admin" @input="saveuser(row)" ></b-checkbox>
			 </template>
			
			<template slot="isBlocked" slot-scope="row">
	            <b-checkbox v-model="row.item.blocked" @input="saveuser(row)" ></b-checkbox>
			 </template>
			
			</b-table>
		</b-col>
		</b-row>
		
		<b-row v-if="showNotification">
			<b-col sm="2">
				<b-form-textarea
				id="textarea"
				v-model="textMessage"
				placeholder="Текст сообщения"
				rows="3"
				max-rows="6"></b-form-textarea>
			</b-col>
		</b-row>

	</b-container>
	</div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'app',
  data () {
    return {
      msg: 'Welcome to Zebra service',
      selected: null,
	  showagents: false,
	  showNotification: false,
	  textMessage:"",
	  users:[],
	   fields: {
	   tcode: {
            key: 'telegramCode',
            label: 'Код Telegram',
            sortable: true
          },
		fname:{
			key: 'firstName',
			label: 'Имя',
			sortable: true
		},
		lname: {
			key: 'lastName',
			label: 'Фамилия',
			sortable: true
		},
		uname: {
			key: 'lastName',
			label: 'Пользователь',
			sortable: true
		},
		cname: {
			key: 'companyName',
			label: 'Компания',
			sortable: true
		},
		iadmin: {
			key: 'isAdmin',
			label: 'Администратор'
		},
		iblock: {
			key: 'isBlocked',
			label: 'Заблокирован'
		}  
	   }
    }
  },
  methods:{
	saveuser(prm){
		const usr = JSON.stringify(prm.item);

		var params = new URLSearchParams()
        params.append('usr', usr)
		
		axios.post('http://127.0.0.1:8000/rest/updateuser',usr,
		{
			headers: {
				'Content-Type': 'application/json'
			}
		})
		.then((response) => {
			//console.log(response);
		})
		.catch((error) => {
			console.log(error);
		});
	},
	loadagents(){
		axios.get('http://127.0.0.1:8000/rest/getusers')
		.then(response => {
			this.users = response.data
		}).catch(e=>{this.msg=e})
	},
	showElements(pageNumber){
		this.showagents = false;
		this.showNotification = false;

		if(pageNumber == 1){
			this.showagents = true;
		}
		
		if(pageNumber == 2){
			this.showNotification = true;
		}
	}

  }
  ,
  created(){
	this.loadagents();
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
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
</style>
