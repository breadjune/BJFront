<template>
<b-fr
</template>

<script>
    import axios from "axios"
    
    export default {
        data() {
            return {
            }
        },

        methods: {
            save() {
            },
            list() {
                this.$emit('rename', 'Content');
                this.$router.push({
                    name: "MemberManage"
                });
            }
        }
    }
</script>