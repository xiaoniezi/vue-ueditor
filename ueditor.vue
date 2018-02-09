<template>
  <div class="ueditor" ref="rowUEditor">
    <script id="editor" type="text/plain"></script>
  </div>
</template>
<script>
export default {
    name: 'ueditor',
    data() {
        return {
            editor: null
        }
    },
    props: {
        defaultMsg: {
            type: String
        },
        config: {
            type: Object,
        }
    },
    computed: {
        DefaultConfig() {
            let obj = this.config
            let serverUrl = this.$store.state.baseURL + '/sys/ueditor/exec.act'
            return {
                autoFloatEnabled: false,
                serverUrl,
                ...obj
            }
        }
    },
    mounted() {
        this.initUEditor()
    },
    methods: {
        initUEditor() {
            const that = this;
            this.editor = UE.getEditor('editor', this.DefaultConfig); // 初始化UE
            this.editor.addListener("ready", function() {
                if (that.defaultMsg == null) {
                    that.editor.setContent('');
                } else {
                    that.editor.setContent(that.defaultMsg);
                }
            });
            this.editor.addListener("contentChange", function() { //监听内容变化
                that.getUEContent();
            })
        },
        getUEContent() { // 获取内容方法
            let content = this.editor.getContent();
            // console.log(content)
            content = content.replace(/<p([\s\S]*?)<\/p>/g, "<div$1</div>")
            this.$emit("getUEContent", content)
        }
    },
    destroyed() {
        this.editor.destroy();
    }
}
</script>
<style>
.ueditor {
  line-height: normal;
}
</style>