<!-- 1. 理解setup属性 -->
<!-- 在没有setup属性的时候，我们需要setup()函数里面定义变量方法并使用return返回，其次我们我们写好的vue组件在导入根组件之后必须手动注册到component里面才能使用，等一系列可以被优化的繁杂语法和操作 -->
 <!-- 为了优化掉这些繁杂的重复性的操作，vue3就推出了setup属性，添加好这个属性后代码就更加简洁了，类似语法糖作用 -->
<script setup>
  // 2. import导入
  //    2.1 命名导入（外界使用）/命名导出（内部向外提供）必须使用{} ，使用了{}就是命名导入会寻找{}里面的名称
  //    2.2 默认导入 接收的变量名称可以自定义，接收时不需要使用{}，
  //    2.3 import和export机制的配合（就先不深入理解了）
  // 导入需要使用的函数
  import {ref,onMounted} from 'vue';
  // 导入Ajax
  import axios from 'axios';


  // 3. 定义响应式数据 ref返回对象引用类型的变量
  // 定义搜索表单中的响应式数据（在html的标签中通过v-model来进行动态绑定）【解决如何实现数据动态实时交互的问题】
  // 对于引用对象const表示不可改变绑定关系，对于基本类型const表示不可改变值
  const name = ref('');
  const job = ref('');
  const gender = ref('');
  const characterList = ref('');

  // 4. 定义查询函数
  // async和await搭配使用可以让方法变成一个同步方法，这样处理数据更加直观【解决方法异步问题】
  // async和await需要搭配使用
  // async 声明异步函数，只有在异步函数里面才可以使用await关键子，普通函数里面使用await关键字会有语法错误
  // await 第一个作用是让方法顺序执行，再axios调用时会等待返回结果，再继续执行
  // await 第二个作用是作为拆包器，axios调用的返回结果是Promise对象，这个对象里面有result数据，所以简化了模板代码
  async function search() {
    // 发送异步请求
    // 5. 模板字符串【解决URL路径需要动态拼接参数的问题】
    // 使用反引号包裹URL
    // 使用${响应式变量.value}获取值
    // 将响应式变量的值动态拼接到URL路径当中
    // 因为要传递响应式数据作为url拼接参数所以使用模板字符串
    const result = await axios.get(`http://localhost:8080/character?name=${name.value}&job=${job.value}&gender=${gender.value}`);
    characterList.value = result.data.data;
  }

</script>

<template>
  <div>
    <form>
      <label for="character">英雄：</label>
      <input type="text" id="character" v-model="name">

      <label for="gender">性别：</label>
      <select id="gender"  v-model="gender">
        <option value=""></option>
        <option value="1">男</option>
        <option value="2">女</option>
      </select>

      <label for="job">职位：</label>
      <select id="job" v-model="job">
        <option value=""></option>
        <option value="1">坦克</option>
        <option value="2">战士</option>
        <option value="3">刺客</option>
        <option value="4">法师</option>
        <option value="5">射手</option>
        <option value="6">辅助</option>
      </select>

      <button type="button">查询</button>
    </form>

    <table>
      <thead>
        <tr>
          <th>英雄</th>
          <th>名称</th>
          <th>职位</th>
          <th>性别</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="character in characterList">
          <!-- 插入表达式不能出现在标签内部，所以出现在标签内部的响应式数据使用v-bind命令 -->
          <td><img v-bind:src="character.img"></td> 
          <td>{{character.name}}</td>
          <td>
            <!-- v-if用来控制标签内容显示或者隐藏，v-show也有类似的作用 -->
            <span v-if="character.job == 1">坦克</span>
            <span v-else-if="character.job == 2">战士</span>
            <span v-else-if="character.job == 3">刺客</span>
            <span v-else-if="character.job == 4">法师</span>
            <span v-else-if="character.job == 5">射手</span>
            <span v-else="character.job == 6">辅助</span>
          </td>
          <td>{{character.gender == 1 ? '男' : '女'}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
</style>
