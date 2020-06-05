<template>
    <div class="PinYinKeyboard">
        <div class="CandidateBox">
            <span class="tip_box">{{isCN?"拼音输入":"英文输入"}} : {{pinyinData}}</span>
            <div class="chinese" v-if="isCN && CnData.length>0">
                <span @click="hanzi_index--"> < </span>
                <div class="chinese_box">
                    <span @click="PushHanzi(hanzi)" v-for="hanzi in show_hanzi" v-text="hanzi"></span>
                </div>
                <span @click="hanzi_index++"> > </span>
            </div>
        </div>
        <div class="keys">
            <div class="rows" v-for="item in keys">
                <span @click="input_key(key)" class="cols" v-for="key in item"
                      v-text="isCN?key.toLocaleUpperCase():key"></span>
            </div>
        </div>
    </div>
</template>

<script>
    import pinyinUtil from "./pinyin/pinyinUtil"

    export default {
        name: "PinYinKeyboard",
        data() {
            return {
                keys: [],
                UpperKeys: [
                    ["~", "!", "@", "#", "$", "%", "^", "&", "*", "(", ")", "_", "+", "DELETE"],
                    ["TAB", "Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P", "[", "]", "|"],
                    ["小写", "A", "S", "D", "F", "G", "H", "J", "K", "L", ":", "\"", "回车"],
                    ["中/英", "Z", "X", "C", "V", "B", "N", "M", "<", ">", "?", "空格"],
                ],
                LowerKeys: [
                    ["`", "1", "2", "3", "4", "5", "6", "7", "8", "9", "0", "-", "=", "DELETE"],
                    ["TAB", "q", "w", "e", "r", "t", "y", "u", "i", "o", "p", "[", "]", "\\"],
                    ["大写", "a", "s", "d", "f", "g", "h", "j", "k", "l", ";", "'", "回车"],
                    ["中/英", "z", "x", "c", "v", "b", "n", "m", ",", ".", "/", "空格"],
                ],
                isUpper: false,
                isCN: false,
                pinyinData: "",
                CnData: "",
                hanzi_index: 0,
                show_hanzi: ""
            };
        },
        methods: {
            pinYin(key) {
                if (this.pinyinData.length === 0 && "`qwertyuiopasdfghjklzxcvbnm".indexOf(key.toLocaleLowerCase()) === -1) {
                    this.$emit("on-push", key);
                    return;
                }
                this.pinyinData += key;
            },
            PushHanzi(key) {
                if (key === " ") return;
                this.$emit("on-push", key);
                this.pinyinData = "";
            },
            input_key(key) {
                switch (key) {
                    case "DELETE":
                    case "delete":
                        if (this.isCN && this.pinyinData.length > 0) {
                            this.pinyinData = this.pinyinData.substr(0, this.pinyinData.length - 1);
                            break;
                        }
                        this.$emit("on-delete", key);
                        break;
                    case "TAB":
                    case "tab":
                        break;
                    case "回车":
                        break;
                    case "大写":
                    case "小写": {
                        this.isUpper = !this.isUpper;
                    }
                        break;
                    case "中/英":
                        this.isCN = !this.isCN;
                        break;
                    default: {
                        if (this.isCN) {
                            this.pinYin(key);
                        } else {
                            this.$emit("on-push", key);
                        }
                    }
                        break;
                }
            },
            update_Hanzi() {
                let show_hanzi = this.CnData.substring(this.hanzi_index * 5, (this.hanzi_index + 1) * 5);
                for (let i = show_hanzi.length; i < 5; i++) {
                    show_hanzi += " ";
                }
                this.show_hanzi = show_hanzi;
            }
        },
        watch: {
            isUpper(n, o) {
                this.keys = n ? this.UpperKeys : this.LowerKeys;
            },
            pinyinData(n, o) {
                this.CnData = pinyinUtil.getHanzi(n);
            },
            CnData(n, o) {
                this.hanzi_index = 0;
                this.update_Hanzi();
            },
            isCN(n, o) {
                this.pinyinData = "";
            },
            hanzi_index(n, o) {
                if (n < 0) {
                    this.hanzi_index = 0;
                    return;
                }
                let m_index = parseInt(((this.CnData.length + 4) / 5).toString());
                if (n !== 0 && n >= m_index) {
                    this.hanzi_index = m_index - 1;
                    return;
                }
                this.update_Hanzi();
            }
        },
        created() {
            this.keys = this.LowerKeys;
        }
    }
</script>

<style scoped>
    .PinYinKeyboard {
        width: 100%;
        height: 100%;
        text-align: center;
        min-width: 46em;
    }

    .rows {
        display: flex;
    }

    .cols {
        flex: 1;
    }

    .CandidateBox,
    .cols {
        margin: 0.3em;
        padding: 0.5em;
        box-sizing: border-box;
        border-radius: 3px;
        border: 1px solid #CCCCCC;
        display: inline-block;
        min-width: 2.5em;
        height: 2.5em;
        line-height: 1.5em;
        cursor: pointer;
        user-select: none;
    }

    .CandidateBox {
        box-sizing: border-box;
        display: block;
        min-width: unset;
        cursor: default;
        padding: unset;
    }

    .rows:nth-child(2) > .cols:first-child {
        flex: auto;
        width: 4em;
    }

    .rows:nth-child(1) > .cols:last-child {
        flex: auto;
        width: 4em;
    }

    .rows:nth-child(3) > .cols:first-child {
        flex: auto;
        width: 5.5em;
    }

    .rows:nth-child(3) > .cols:last-child {
        flex: auto;
        width: 4em;
    }

    .rows:nth-child(4) > .cols:first-child {
        flex: auto;
        width: 6em;
    }

    .rows:nth-child(4) > .cols:last-child {
        flex: auto;
        width: 5em;
    }

    .tip_box {
        float: left;
        padding: 0.5em 0.3em;
    }

    .chinese span {
        cursor: pointer;
        border: 1px solid #CCCCCC;
        box-sizing: border-box;
        padding: 0.1em 0.3em;
        margin: 0.3em;
        border-radius: 4px;
    }

    .chinese {
        float: right;
    }

    .chinese_box {
        display: inline-flex;
        width: 12em;
    }

    .chinese_box > span {
        flex: 1;
    }
</style>