<template>
  <div id="app">
    输入JSON
    <el-input
      type="textarea"
      :autosize="{ minRows: 2, maxRows: 10 }"
      placeholder="请输入内容"
      v-model="textarea"
      @change="changeTextarea"
    >
    </el-input>
    <div>{{errorText || '如果有图片链接将会在这显示'}}</div>
    <img v-if="imgs.length > 0" v-for="item in imgs" :src="item" alt="item" :key="item" width="400" style="margin-right: 10px"/>
  </div>
</template>

<script>
const TYPES = [".jpg", ".png", ".gif", ".bmp"];

export default {
  name: "app",
  components: {},
  data() {
    return {
      textarea: "",
      imgs: [],
      errorText: ''
    };
  },
  methods: {
    changeTextarea() {
      if (!this.textarea) return
      this.imgs = [];
      let object
      try {
        try {
          object = JSON.parse(this.textarea)
        } catch (e) {
          object = eval('(' + this.textarea + ')')
        }
        this.itrator(object);
      } catch (e) {
        this.errorText = 'json格式错误'
        this.$message.error('json格式错误')
      }
    },
    //遍历json对象的所有属性，找出所有的图片链接
    itrator(obj) {
      for (var item in obj) {
        if (obj[item] instanceof Object) {
          this.itrator(obj[item]);
        } else {
          if (
            (typeof obj[item]).toLowerCase() == "string" &&
            new RegExp(TYPES.join("|")).test(obj[item])
          ) {
            this.imgs.push(obj[item]);
          }
        }
      }
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
