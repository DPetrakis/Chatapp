<template>
   <div class="row">

       <div class="col-8">
           <div class="card card-default">
               <div class="card-header">Messages</div>
               <div class="card-body p-0">
                   <ul class="list-unstyled" style="height:300px; overflow-y:scroll">
                       <li class="p-2" v-for="message in messages" v-bind:key="message.id">
                           <div v-if="user.name == message.user.name">
                                <strong>Me</strong>
                                {{message.message}}
                           </div>
                           <div v-else>
                            <strong>{{message.user.name}}</strong>
                            {{message.message}}
                           </div>
                       </li>
                   </ul>
               </div>

               <input
                    @keyup.enter="sendMessage()"
                    v-model="newMessage"
                    type="text"
                    name="message"
                    placeholder="Enter your message..."
                    class="form-control">
           </div>
              <span class="text-muted">user is typing</span>
       </div>
    
        <div class="col-4">
            <div class="card card-default">
                <div class="card-header">Active Users</div>
                <div class="card-body">
                    <ul>
                        <li class="py-2">
                            Harris
                        </li>
                    </ul>
                </div>
            </div>
        </div>

   </div>
</template>

<script>
    export default {
       
       props: ['user'],

       data(){
           
        return {
           messages: [],
           newMessage: ''
        } 

        },

        created(){

            this.fetchMessages();
            Echo.join('chat').listen('MessageSent', (event) =>{
                 this.messages.push(event.message);
            });

        },

        methods: {
            fetchMessages(){
                axios.get('messages').then(response => {
                    this.messages = response.data;
                })
            },

            sendMessage() {
                this.messages.push({
                    user: this.user ,
                    message: this.newMessage
                });

                axios.post('messages',{message: this.newMessage});

                this.newMessage = '';
            }
        }
    }
</script>
