---
layout: post
title: elementUI--input
category: root
summary: '需求：只能输入数字'
author: hjy
---

### 需求说明：

* 使用element-ui el-input组件，要求只能输入数字，最先使用的办法：

```javascript
  <el-input
    v-model='count'
    type='number'
  />
```

### 存在问题：

```javascript
  1.可以输入e|+等
  2.样式需要自己覆盖原生样式
```

### 目前解决的办法：
* 办法一：

```javascript
  <el-input
    v-model='count'
    @input="value=value.replace(/\D/g,'')"
    @blur="handleBlur"
  />

  ...

  handleBlur(e){
    this.count = e.target.value
  }
```

* 办法二：

```javascript
  <el-input
    v-model='count'
    @input="count = handleInput($event)"
  />

  ...

  handleInput(value){
    // 解决中文输入法下输入。问题
    value = value.replace('。', ".");
    //先把非数字的都替换掉，除了数字和.
    value = value.replace(/[^\d.]/g,"");
    //保证只有出现一个.而没有多个.
    value = value.replace(/\.{2,}/g,".");
    //必须保证第一个为数字而不是.
    value = value.replace(/^\./g,"");
    //保证.只出现一次，而不能出现两次以上
    value = value.replace(".","$#$").replace(/\./g,"").replace("$#$",".");
    //只能输入四个小数
    value =  value.replace(/^(\-)*(\d+)\.(\d{4}).*$/,'$1$2.$3');
    return value
  }
```

* 办法三：（自定义指令）

```javascript
  <el-input
    v-model='count'
    v-inputInt='count'
  />
  ...

  directives: {
    inputInt: {
      bind(el, binding, vnode) {
        let input = vnode.elm.getElementsByTagName('input')[0];
        input.addEventListener('compositionstart', () => {
          vnode.inputLocking = true
        })
        input.addEventListener('compositionend', () => {
          vnode.inputLocking = false
          input.dispatchEvent(new Event('input'))
        })
        input.addEventListener('input', () => {
          if(vnode.inputLocking) {
            return;
          }
          let oldValue = input.value;
          let newValue = input.value.replace(/[^\d]/g, '');
          if(newValue && binding.value !== 'zeroBefore') {
            newValue = newValue.replace(/^\b(0+)/gi, '') // 不指定可以以0开头的时候 去掉开头多余的0
          }
          // 判断是否需要更新，避免进入死循环
          if(newValue !== oldValue) {
            input.value = newValue
            // 通知v-model更新 vue底层双向绑定实现的原理是基于监听input事件
            input.dispatchEvent(new Event('input')) 
          }
        })
      }
    },
    inputFloat: {
      bind(el, binding, vnode) {
        let input = vnode.elm.getElementsByTagName('input')[0];
        input.addEventListener('compositionstart', () => {
          vnode.inputLocking = true
        })
        input.addEventListener('compositionend', () => {
          vnode.inputLocking = false
          input.dispatchEvent(new Event('input'))
        })
        input.addEventListener('input', () => {
          if(vnode.inputLocking) {
            return;
          }
          let oldValue = input.value;
          let newValue = input.value;
          newValue = newValue.replace(/[^\d.]/g, '');
          newValue = newValue.replace(/^\./g, '');
          newValue = newValue.replace('.', '$#$').replace(/\./g, '').replace('$#$', '.');
          newValue = newValue.replace(/^(\-)*(\d+)\.(\d\d).*$/, '$1$2.$3')
          if(newValue) {
            let arr = newValue.split('.')
            newValue = Number(arr[0]) + (arr[1] === undefined ? '' : '.' + arr[1]) // 去掉开头多余的0
          }
          // 判断是否需要更新，避免进入死循环
          if(newValue !== oldValue) {
            input.value = newValue
            input.dispatchEvent(new Event('input')) // 通知v-model更新
          }
        })
        // input 事件无法处理小数点后面全是零的情况 因为无法确定用户输入的0是否真的应该清除，如3.02。放在blur中去处理
        input.addEventListener('blur', () => {
          let oldValue = input.value;
          let newValue = input.value;
          if(newValue) {
            newValue = Number(newValue).toString()
          }
          // 判断是否需要更新，避免进入死循环
          if(newValue !== oldValue) {
            input.value = newValue
            input.dispatchEvent(new Event('input')) // 通知v-model更新
          }
        })
      }
    }
  },

```

