<template>
  <div class="todoListBox">
    <div class="header-box">
      <div class="header-left">
        <div>+</div>
        <h2>TodoList 待办事件列表</h2>
      </div>
      <div class="header-right">
        <button class="headerallSelect" @click="handleallSelect">全选</button>
        <button class="headerAdd" @click="handleAdd">添加</button>
        <button class="headerClear" @click="handleClear">清空</button>
      </div>
    </div>
    <div class="content">
      <div
        class="content-item"
        v-for="(item, index) in todoList"
        :key="item.id"
      >
        <div class="content-left" @click="handleSelect(index, item.id)">
          <span :style="item.isCheck ? 'opacity : 1' : 'opacity : 0'"></span>
        </div>
        <input
          type="text"
          class="content-input"
          v-model="item.text"
          :disabled="item.isCheck"
          :class="item.isCheck ? 'line-through' : ''"
          @blur="handleBlur"
          ref="input"
        />
        <div class="content-right">
          <p>{{ item.time }}</p>
          <button @click="handleDelItem(index, item.id)">删除</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ts-nocheck
import dayjs from "dayjs";
import JSConfetti from "js-confetti";
const jsConfetti = new JSConfetti();

export default {
  name: "App",
  data() {
    return {
      todoList: [
        {
          id: "1",
          isCheck: false,
          text: "吃饭",
          time: "22-08-12 20:57",
        },
      ],
    };
  },
  created() {
    let getList = JSON.parse(window.localStorage.getItem("listTodo"));
    if (getList === null) {
      this.getList = [
        {
          id: this.randomID(),
          isCheck: false,
          text: "请点击上方的添加按钮添加事件",
          time: dayjs(new Date()).format("YY-MM-DD HH:mm"),
        },
      ];
    } else {
      this.todoList = getList;
    }
  },
  methods: {
    handleAdd() {
      this.todoList.unshift({
        id: this.randomID(),
        isCheck: false,
        text: "",
        time: dayjs(new Date()).format("YY-MM-DD HH:mm"),
      });
      const inputLength = this.todoList.length - 1;
      this.$nextTick(() => {
        this.$refs.input[inputLength].focus();
      });
    },
    handleDelItem(index, id) {
      if (this.todoList[index].id === id) {
        this.todoList.splice(index, 1);
      }
      jsConfetti.addConfetti({
        emojis: ["👍", "📅"],
        confettiNumber: 20,
      });
      this.storage();
    },
    handleSelect(index, id) {
      if (this.todoList[index].id === id) {
        this.todoList[index].isCheck = !this.todoList[index].isCheck;
      }
      this.storage();
    },
    handleallSelect() {
      this.todoList.forEach((item) => {
        if (item.isCheck == true) {
          return;
        }
        item.isCheck = !item.isCheck;
      });
      this.storage();
    },
    handleClear() {
      this.todoList = [];
      jsConfetti.addConfetti({
        emojis: ["🎮", "✨"],
        confettiNumber: 20,
      });
      this.storage();
    },
    randomID() {
      return Number(
        Math.random().toString().substr(2, 0) + Date.now()
      ).toString(10);
    },
    storage() {
      window.localStorage.setItem("listTodo", JSON.stringify(this.todoList));
    },
    handleBlur() {
      this.storage();
    },
  },
};
</script>

<style lang="less">
button {
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  color: #fff;
  font-size: 20px;
  margin-left: 10px;
}
.todoListBox {
  width: 800px;
  height: 600px;
  background-color: #3c3e4f;
  border-radius: 40px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 20px;
  box-sizing: border-box;
  color: #fff;
  .header-box {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: #ccc 1px solid;
    .header-left {
      display: flex;
      align-items: center;
      div {
        width: 50px;
        height: 50px;
        background-color: #9999e6;
        font-size: 30px;
        border-radius: 50%;
        text-align: center;
        margin-right: 15px;
      }
    }
    .header-right {
      .headerallSelect {
        background-color: rgb(167, 7, 12);
      }
      .headerAdd {
        background-color: rgb(19, 113, 19);
      }
      .headerClear {
        background-color: rgb(50, 113, 150);
      }
    }
  }
  .content {
    margin-top: 20px;
    height: 450px;
    overflow-y: scroll;
    .content-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      background-color: #696f70;
      border-radius: 10px;
      padding: 10px 20px;
      box-sizing: border-box;
      margin-top: 20px;
      .content-left {
        width: 30px;
        height: 30px;
        border: 1px solid #ccc;
        border-radius: 50%;
        position: relative;
        span {
          display: inline-block;
          width: 20px;
          height: 20px;
          background-color: #9999e6;
          border-radius: 50%;
          position: absolute;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
        }
      }
      .line-through {
        color: rgba(255, 255, 255, 0.5);
        text-decoration: line-through rgba(255, 255, 255, 0.8);
      }

      .content-input {
        flex: 1;
        margin: 0 10px;
        outline: none;
        background: transparent;
        border: none;
        border-bottom: 1px solid #ccc;
        padding: 5px 10px;
        color: #fff;
      }
      .content-right {
        display: flex;
        align-items: center;
        button {
          background-color: rgb(167, 7, 12);
        }
      }
    }
  }
}
</style>
