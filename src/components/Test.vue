<template>
    <div class="testpage">
        <button @click="sendTest">click</button>
    </div>
</template>

<script>
    import io from "socket.io-client";

    export default {
        name: "Test",
        data() {
            return {
                socket: io("http://localhost:3001")
            }
        },
        methods: {
            sendTest() {
                this.socket.emit("sendTest", {msg: "test"})
            }

        },
        mounted() {
            this.socket.on("sendVideo", (data) => {
                console.log(data)

            })
            this.socket.on("recieved", (data)=> {
                console.log(data)
            })

        },
    }
</script>

<style lang="less" scoped>

</style>