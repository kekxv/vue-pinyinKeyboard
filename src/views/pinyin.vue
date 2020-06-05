<template>
    <div class="pinyin">
        <h1>拼音输入测试</h1>
        <label for="test">测试</label><input id="test" v-model="text"/>
        <PinYinKeyboard @on-delete="DeleteText" @on-push="PushText"></PinYinKeyboard>
    </div>
</template>

<script>
    // @ is an alias to /src

    import PinYinKeyboard from "../components/PinYinKeyboard";

    export default {
        name: 'pinyin',
        components: {PinYinKeyboard},
        data() {
            return {
                text: ""
            };
        },
        methods: {
            log: console.log,
            DeleteText() {
                this.text = this.text.substr(0, this.text.length - 1);
            },
            PushText(key) {
                this.text += (key);
            }
        }
    }
</script>


