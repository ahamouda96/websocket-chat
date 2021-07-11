<template>
    <div class="row">
        <div class="col-md-8">
            <div class="card card-default">
                <div class="card-header">Messages</div>
                <div class="card-body p-0">
                    <ul class="list-unstyled" style="height:300px; overflow-y:scroll">
                        <li class="p-2" v-for="(message, index) in messages" :key="index">
                            <strong>{{ message.user.name }}</strong>
                            <span>{{ message.message }}</span>
                        </li>
                    </ul>
                </div>
                <input
                @keyup.enter="sendMessage"
                v-model="newMessage"
                type="text" 
                name="message" 
                placeholder="Enter your message ..." 
                class="form-control">
            </div>
            <span class="text-muted">user is typing ...</span>
        </div>
        <div class="col-md-4">
            <div class="card card-default">
                <div class="card-header">Active users</div>
                <div class="card-body">
                    <ul>
                        <li class="py-2">ahamouda</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props : ['user'],
        data(){
            return{
                messages: [],
                newMessage : '',
            };
        },
        created(){
            this.getMessages();
             Echo.join('chat')
                .listen('MessageSent', (event) => {
                    console.log(event)
                    this.messages.push(event.message);
                });
        },
        methods:{
            getMessages(){
                axios.get('messages').then(response => {
                    this.messages = response.data;
                })
            },
            sendMessage(){
                this.messages.push({
                    user : this.user,
                    message : this.newMessage
                });

                axios.post('messages', {message : this.newMessage});

                this.newMessage = '';
            },

            // listenToMessageSentEvent(){
            //     Echo.join('chat')
            //     .listen('MessageSent', (event) => {
            //         console.log(event)
            //         this.messages.push(event.message);
            //     });
            // },
        },
    }
</script>
