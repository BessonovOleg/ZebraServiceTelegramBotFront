<template>
  <div id="app">
    <img src="./assets/logo.png">

	<b-container fluid>
	  <b-row class="text-center">
		<b-col md="12" class="py-3">
			<b-button @click="showElements(1)" variant="success">Клиентская база</b-button>
			<b-button @click="showElements(2)" variant="warning">Рассылка</b-button>
			<b-button @click="showElements(3)" variant="primary">История</b-button>
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
			
			<template slot="companyName" slot-scope="row">
	            <b-form-input v-model="row.item.companyName" @input="saveuser(row)" ></b-form-input>
			</template>
				
			</b-table>
		</b-col>
	</b-row>
		
	<b-row v-if="showNotification" >
		<b-col md="6" offset-md="3">
				<b-form-textarea 
					id="textarea"
					v-model="textMessage"
					placeholder="Текст сообщения"
					rows="3"
					max-rows="6"></b-form-textarea>
				<b-button class="m-2" @click="sendmsgall" variant="success" >Отправить</b-button>
		</b-col>
	</b-row>

	<b-row v-if="showMessages">
		<b-col sm="1">
			<b-button @click="clearHistory" variant="danger">Очистить историю</b-button>
		</b-col>
	</b-row>
	
	<b-row v-if="showMessages" >
		<b-col>
			<b-table hover bordered
			:items="messages" :fields="fieldsmessages" >
			</b-table>
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
	  showMessages: false,
	  textMessage:"",
	  users:[],
	  messages:[],
	  fieldsmessages:{
		devent:{
			key: 'dateEvent',
			label: 'Дата'
		},
		uname:{
			key: 'telegramUser.companyName',
			label: 'Пользователь'
		},
		msg:{
			key: 'messgaBody',
			label: 'Сообщение'
		}
	  },
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
	clearHistory(){
		axios.post('http://127.0.0.1:8000/rest/clearhistory')
		.then((response) => {
			this.messages = [];
		})
		.catch((error) => {
			alert(error);
		});
	},
	sendmsgall(){
		var params = new URLSearchParams()
        params.append('msg', this.textMessage);
		axios.post('http://127.0.0.1:8000/rest/sendmessagetoall',params)
		.then((response) => {
			alert("Сообщение отправлено!");
			this.textMessage = "";
		})
		.catch((error) => {
			alert(error);
		});
	},
	loadagents(){
		axios.get('http://127.0.0.1:8000/rest/getusers')
		.then(response => {
			this.users = response.data
		}).catch(e=>{this.msg=e})
	},
	loadMessages(){
		axios.get('http://127.0.0.1:8000/rest/getmessages')
		.then(response => {
			this.messages = response.data
		}).catch(e=>{this.msg=e})
	},
	showElements(pageNumber){
		this.showagents = false;
		this.showNotification = false;
	    this.showMessages = false;
		
		if(pageNumber == 1){
			this.loadagents();
			this.showagents = true;
		}
		
		if(pageNumber == 2){
			this.showNotification = true;
		}

		if(pageNumber == 3){
			this.loadMessages();
			this.showMessages = true;
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
