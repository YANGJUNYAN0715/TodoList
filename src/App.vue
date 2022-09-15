<template>
  <div class="todoListBox">
    <div class="header-box">
      <div class="header-left">
        <div>+</div>
        <h2>TodoList😋</h2>
      </div>
      <div class="header-right">
        <button class="headerSelectAll" @click="handleSelectAll">全选</button>
        <button class="headerCancelSelectAll" @click="handleCancelSelectAll">
          取消全选
        </button>
        <button class="headerAdd" @click="handleAdd">添加</button>
        <button class="headerClear" @click="handleClear">清空</button>
        <button class="headerDeleteSelected" @input="handleDeleteSelected">
          删除已选
        </button>
      </div>
    </div>
    <div class="content">
      <div class="content-item" style="background-color: #4500a2">
        <span>🔍查找</span>
        <input
          type="text"
          class="content-input"
          @input="handleSearch($event)"
        />
      </div>
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
          <button @click="handleDelete(index, item.id)">删除</button>
        </div>
      </div>
      <div class="content-item" style="background-color: #4500a2">
        <span>我也是有底线的......🥺</span>
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
      todoList: [],
    };
  },
  created() {
    let getList = JSON.parse(window.localStorage.getItem("listTodo"));
    if (getList.length == 0) {
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
    },
    handleDelete(index, id) {
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
    handleSelectAll() {
      this.todoList.forEach((item) => {
        if (item.isCheck == true) {
          return;
        }
        item.isCheck = !item.isCheck;
      });
      this.storage();
    },
    handleCancelSelectAll() {
      this.todoList.forEach((item) => {
        if (item.isCheck == false) {
          return;
        }
        item.isCheck = !item.isCheck;
      });
      this.storage();
    },
    handleClear() {
      if (this.todoList.length != 0) {
        this.todoList = [];
        jsConfetti.addConfetti({
          emojis: ["🎮", "✨"],
          confettiNumber: 20,
        });
      }
      this.storage();
    },
    handleDeleteSelected() {
      if (
        this.todoList.length !=
        this.todoList.filter((item) => item.isCheck == false).length
      ) {
        this.todoList = this.todoList.filter((item) => item.isCheck == false);
        jsConfetti.addConfetti({
          emojis: ["👍", "📅"],
          confettiNumber: 20,
        });
      }
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
    handleSearch(event) {
      if (event.target.value != "") {
        this.search = this.todoList.filter((item) =>
          item.text.includes(event.target.value)
        );
      } else {
        this.search = [];
      }
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
  cursor: pointer;
}
.todoListBox {
  width: 800px;
  height: 600px;
  background-image: linear-gradient(135deg, #5151e6 40%, #7a057457 120%);
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
      button {
        background-color: #4500a2;
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
      background-color: rgb(127, 7, 143);
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
        font-size: 20px;
        font-family: "Microsoft YaHei";
        height: 20px;
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
