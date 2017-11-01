---
layout: post
title:  "vue-validators 教程"
date:   2016-08-23 15:04:23
categories: [jekyll]
tags: [vue-validators]
---

本博客主要是介绍vue的验证器插件vue-validators的使用方法。

# 安装

## 直接下载

查看 [dist 目录](https://github.com/vuejs/vue-validator/tree/dev/dist)。 注意，dist 目录下的文件是最新稳定版，不会同步更新到 `dev` 分支上的最新代码。

## CDN

### UNPKG

```html
<script src="https://unpkg.com/vue-validator@2.1.7/dist/vue-validator.min.js"></script>
```

## NPM

### 稳定版

    $ npm install vue-validator

### 开发版

    $ git clone https://github.com/vuejs/vue-validator.git node_modules/vue-validator
    $ cd node_modules/vue-validator
    $ npm install
    $ npm run build

如果使用 CommonJS 模块规范, 需要显式的使用 `Vue.use()` 安装验证器组件：

> :提醒: 如果与 `vue-router` 同时使用，必须在调用 `router#map`, `router#start` 等实例方法前安装验证器。

```javascript
var Vue = require('vue')
var VueValidator = require('vue-validator')

Vue.use(VueValidator)
```

使用独立编译文件时不需要这样做，因为验证器组件会自动安装。



# 入门

```javascript
new Vue({
  el: '#app'
})
```

我们可以像下面这样使用 `validator` 元素指令和 `v-validate` 指令:

```html
<div id="app">
  <validator name="validation1">
    <form novalidate>
      <div class="username-field">
        <label for="username">username:</label>
        <input id="username" type="text" v-validate:username="['required']">
      </div>
      <div class="comment-field">
        <label for="comment">comment:</label>
        <input id="comment" type="text" v-validate:comment="{ maxlength: 256 }">
      </div>
      <div class="errors">
        <p v-if="$validation1.username.required">Required your name.</p>
        <p v-if="$validation1.comment.maxlength">Your comment is too long.</p>
      </div>
      <input type="submit" value="send" v-if="$validation1.valid">
    </form>
  </validator>
```

验证结果会关联到验证器元素上。在上例中，验证结果保存在 `$validation1` 下，`$validation1` 是由 `validator` 元素的 `name` 属性值加 `$` 前缀组成。

> :提醒: 验证器名称不要与 Vue.js 中的自带属性重复，如 `$event` 等。


# 验证结果结构

验证结果保存在如下结构中:

```
{
  // top-level validation properties
  valid: true,
  invalid: false,
  touched: false,
  undefined: true,
  dirty: false,
  pristine: true,
  modified: false,
  errors: [{
    field: 'field1', validator: 'required', message: 'required field1'
  }, ... {
    field: 'fieldX', validator: 'customValidator', message: 'invalid fieldX'
  }],

  // field1 validation
  field1: {
    required: false, // build-in validator, return `false` or `true`
    email: true, // custom validator
    url: 'invalid url format', // custom validator, if specify the error message in validation rule, set it
    ...
    customValidator1: false, // custom validator
    // field validation properties
    valid: false,
    invalid: true,
    touched: false,
    undefined: true,
    dirty: false,
    pristine: true,
    modified: false,
    errors: [{
      validator: 'required', message: 'required field1'
    }]
  },

  ...

  // fieldX validation
  fieldX: {
    min: false, // validator
    ...
    customValidator: true,

    // fieldX validation properties
    valid: false,
    invalid: true,
    touched: true,
    undefined: false,
    dirty: true,
    pristine: false,
    modified: true,
    errors: [{
      validator: 'customValidator', message: 'invalid fieldX'
    }]
  },
}
```

全局结果可以直接从验证结果中获取到，字段验证结果保存在以字段名命名的键下。

## 字段验证结果
- `valid`: 字段有效时返回 `true`,否则返回 `false`。
- `invalid`: `valid` 的逆.
- `touched`: 字段获得过焦点时返回 `true`,否则返回 `false`。
- `untouched`: `touched` 的逆.
- `modified`: 字段值与**初始**值不同时返回 `true`,否则返回 `false`。
- `dirty`: 字段值改变过至少**一次**时返回 `true`,否则返回 `false`。
- `pristine`: `dirty` 的逆.
- `errors`: 字段无效时返回存有错误信息的数据，否则返回 `undefined`。

## 全局结果
- `valid`: **所有**字段都有效时返回 `true`,否则返回 `false`。
- `invalid`: 只要存在无效字段就返回 `true`,否则返回 `false`。
- `touched`: 只要存在获得过焦点的字段就返回 `true`,否则返回 `false`。
- `untouched`: `touched` 的逆。
- `modified`: 只要存在与**初始**值不同的字段就返回 `true`,否则返回 `false`。
- `dirty`: 只要存在值改变过至少**一次**的字段就返回 `true`,否则返回 `false`。
- `pristine`: **所有**字段都没有发生过变化时返回 `true`,否则返回 `false`。
- `errors`: 有无效字段时返回所有无效字段的错误信息，否则返回 `undefined`。


# 验证器语法
`v-validate` 指令用法如下:

    v-validate[:field]="array literal | object literal | binding"

## 字段
2.0-alpha以前的版本中，验证器是依赖于 `v-model` 的。从2.0-alpha版本开始，`v-model` 是可选的。

~v1.4.4:
```html
<form novalidate>
  <input type="text" v-model="comment" v-validate="minLength: 16, maxLength: 128">
  <div>
    <span v-show="validation.comment.minLength">Your comment is too short.</span>
    <span v-show="validation.comment.maxLength">Your comment is too long.</span>
  </div>
  <input type="submit" value="send" v-if="valid">
</form>
```

v2.0-alpha后:
```html
<validator name="validation">
  <form novalidate>
    <input type="text" v-validate:comment="{ minlength: 16, maxlength: 128 }">
    <div>
      <span v-show="$validation.comment.minlength">Your comment is too short.</span>
      <span v-show="$validation.comment.maxlength">Your comment is too long.</span>
    </div>
    <input type="submit" value="send" v-if="valid">
  </form>
</validator>
```

### Caml-case 属性
同 [Vue.js](http://vuejs.org.cn/guide/components.html#camelCase-vs-kebab-case)一样, `v-validate` 指令中的字段名可以使用 kebab-case:

```html
<validator name="validation">
  <form novalidate>
    <input type="text" v-validate:user-name="{ minlength: 16 }">
    <div>
      <span v-if="$validation.userName.minlength">Your user name is too short.</span>
    </div>
  </form>
</validator>
```

### 属性
可以通过 `field` 属性来指定字段名。这在动态定义包含验证功能的表单时有用：

> 注意: 当使用 `field` 属性指定字段名时不需要在 `v-validate` 指令中再次指定。

```html
<div id="app">
  <validator name="validation">
    <form novalidate>
      <p class="validate-field" v-for="field in fields">
      <label :for="field.id">{{field.label}}</label>
      <input type="text" :id="field.id" :placeholder="field.placeholder" :field="field.name" v-validate="field.validate">
      </p>
      <pre>{{ $validation | json }}</pre>
    </form>
  </validator>
</div>
```

```javascript
new Vue({
  el: '#app',
  data: {
    fields: [{
      id: 'username',
      label: 'username',
      name: 'username',
      placeholder: 'input your username',
      validate: { required: true, maxlength: 16 }
    }, {
      id: 'message',
      label: 'message',
      name: 'message',
      placeholder: 'input your message',
      validate: { required: true, minlength: 8 }
    }]
  }
})
```
## 字面量

### 数组
下例中使用了数组型字面量：

```html
<validator name="validation">
  <form novalidate>
    Zip: <input type="text" v-validate:zip="['required']"><br />
    <div>
      <span v-if="$validation.zip.required">Zip code is required.</span>
    </div>
  </form>
</validator>
```

因为 `required` 验证器不要额外的参数，这样写更简洁。


### 对象
下例中使用了对象型字面量：

```html
<validator name="validation">
  <form novalidate>
    ID: <input type="text" v-validate:id="{ required: true, minlength: 3, maxlength: 16 }"><br />
    <div>
      <span v-if="$validation.id.required">ID is required</span>
      <span v-if="$validation.id.minlength">Your ID is too short.</span>
      <span v-if="$validation.id.maxlength">Your ID is too long.</span>
    </div>
  </form>
</validator>
```


使用对象型字面量允许你为验证器指定额外的参数。对于 `required`，因为它不需要参数，如上例中随便指定一个值即可。

或者可以像下例一样使用严苛模式对象：

```html
<validator name="validation">
  <form novalidate>
    ID: <input type="text" v-validate:id="{ minlength: { rule: 3 }, maxlength: { rule: 16 } }"><br />
    <div>
      <span v-if="$validation.id.minlength">Your ID is too short.</span>
      <span v-if="$validation.id.maxlength">Your ID is too long.</span>
    </div>
  </form>
```

## 绑定
下例中展现了动态绑定：

```javascript
new Vue({
  el: '#app',
  data: {
    rules: {
      minlength: 3,
      maxlength: 16
    }
  }
})

```html
<div id="app">
  <validator name="validation">
    <form novalidate>
      ID: <input type="text" v-validate:id="rules"><br />
      <div>
        <span v-if="$validation.id.minlength">Your ID is too short.</span>
        <span v-if="$validation.id.maxlength">Your ID is too long.</span>
      </div>
    </form>
  </validator>
</div>
```

除数据属性外，也可以使用计算属性或事例方法来指定验证规则。

## Terminal 指令问题
请注意，当你想要使用如 `v-if` 和 `v-for` 这些 terminal 指令时，应把可验证的目标元素包裹在 `<template>` 之类的不可见标签内。因为 `v-validate` 指令不能与这些 terminal 指令使用在同一元素上。

下例中使用了 `<div>` 标签：

```javascript
new Vue({
  el: '#app',
  data: {
    enable: true
  }
})
```

```html
<div id="app">
  <validator name="validation">
    <form novalidate>
      <div class="username">
        <label for="username">username:</label>
        <input id="username" type="text" 
               @valid="this.enable = true" 
               @invalid="this.enable = false" 
               v-validate:username="['required']">
      </div>
      <div v-if="enable" class="password">
        <label for="password">password:</label>
        <input id="password" type="password" v-validate:password="{
          required: { rule: true }, minlength: { rule: 8 }
        }"/>
      <div>
    </form>
  </validator>
</div>
```

# Build-in validators

You can be used build-in validators of below:

- `required`: whether the value has been specified
- `pattern`: whether the pattern of the regular expression
- `minlength`: whether the length of specified value is less than or equal minimum length
- `maxlength`: whether the length of specified value is less more or equal maximum length
- `min`: whether the specified numerical value is less than or equal minimum
- `max`: whether the specified numerical value is more than or equal maximum

See more about [API section](/api.html#buildin-validators).

# 结合 v-model
可以验证通过 v-model 指令进行数据绑定的字段：

```html
<div id="app">
  <validator name="validation1">
    <form novalidate>
      message: <input type="text" v-model="msg" v-validate:message="{ required: true, minlength: 8 }"><br />
      <div>
        <p v-if="$validation1.message.required">Required your message.</p>
        <p v-if="$validation1.message.minlength">Too short message.</p>
      </div>
    </form>
  </validator>
</div>
```

```javascript
var vm = new Vue({
  el: '#app',
  data: {
    msg: ''
  }
})

setTimeout(function () {
  vm.msg = 'hello world!!'
}, 2000)
```

# 重置验证结果

可以通过 Vue 实例的 `$resetValidation()` 方法重置验证结果，该方法是由验证器安装时动态定义的。使用方法见下例：

```html
<div id="app">
  <validator name="validation1">
    <form novalidate>
      <div class="username-field">
        <label for="username">username:</label>
        <input id="username" type="text" v-validate:username="['required']">
      </div>
      <div class="comment-field">
        <label for="comment">comment:</label>
        <input id="comment" type="text" v-validate:comment="{ maxlength: 256 }">
      </div>
      <div class="errors">
        <p v-if="$validation1.username.required">Required your name.</p>
        <p v-if="$validation1.comment.maxlength">Your comment is too long.</p>
      </div>
      <input type="submit" value="send" v-if="$validation1.valid">
      <button type="button" @click="onReset">Reset Validation</button>
    </form>
    <pre>{{ $validation1 | json }}</pre>
  </validator>
</div>
```

```javascript
new Vue({
  el: '#app',
  methods: {
    onReset: function () {
      this.$resetValidation()
    }
  }
})
```


# 可验证的表单元素

## 复选框

支持复选框验证:

```html
<div id="app">
  <validator name="validation1">
    <form novalidate>
      <h1>Survey</h1>
      <fieldset>
        <legend>Which do you like fruit ?</legend>
        <input id="apple" type="checkbox" value="apple" v-validate:fruits="{
          required: { rule: true, message: requiredErrorMsg },
          minlength: { rule: 1, message: minlengthErrorMsg },
          maxlength: { rule: 2, message: maxlengthErrorMsg }
        }">
        <label for="apple">Apple</label>
        <input id="orange" type="checkbox" value="orange" v-validate:fruits>
        <label for="orange">Orage</label>
        <input id="grape" type="checkbox" value="grape" v-validate:fruits>
        <label for="grape">Grape</label>
        <input id="banana" type="checkbox" value="banana" v-validate:fruits>
        <label for="banana">Banana</label>
        <ul class="errors">
          <li v-for="msg in $validation1.fruits.errors">
            <p>{{msg.message}}</p>
          </li>
        </ul>
      </fieldset>
    </form>
  </validator>
</div>
```

```javascript
new Vue({
  el: '#app',
  computed: {
    requiredErrorMsg: function () {
      return 'Required fruit !!'
    },
    minlengthErrorMsg: function () {
      return 'Please chose at least 1 fruit !!'
    },
    maxlengthErrorMsg: function () {
      return 'Please chose at most 2 fruits !!'
    }
  }
})
```

## 单选按钮

支持单选按钮验证:

```html
<div id="app">
  <validator name="validation1">
    <form novalidate>
      <h1>Survey</h1>
      <fieldset>
        <legend>Which do you like fruit ?</legend>
        <input id="apple" type="radio" name="fruit" value="apple" v-validate:fruits="{
          required: { rule: true, message: requiredErrorMsg }
        }">
        <label for="apple">Apple</label>
        <input id="orange" type="radio" name="fruit" value="orange" v-validate:fruits>
        <label for="orange">Orage</label>
        <input id="grape" type="radio" name="fruit" value="grape" v-validate:fruits>
        <label for="grape">Grape</label>
        <input id="banana" type="radio" name="fruit" value="banana" v-validate:fruits>
        <label for="banana">Banana</label>
        <ul class="errors">
          <li v-for="msg in $validation1.fruits.errors">
            <p>{{msg.message}}</p>
          </li>
        </ul>
      </fieldset>
    </form>
  </validator>
</div>
```

```javascript
new Vue({
  el: '#app',
  computed: {
    requiredErrorMsg: function () {
      return 'Required fruit !!'
    }
  }
})
```


## 下拉列表

支持下拉列表验证:

```html
<div id="app">
  <validator name="validation1">
    <form novalidate>
      <select v-validate:lang="{ required: true }">
        <option value="">----- select your favorite programming language -----</option>
        <option value="javascript">JavaScript</option>
        <option value="ruby">Ruby</option>
        <option value="python">Python</option>
        <option value="perl">Perl</option>
        <option value="lua">Lua</option>
        <option value="go">Go</option>
        <option value="rust">Rust</option>
        <option value="elixir">Elixir</option>
        <option value="c">C</option>
        <option value="none">Not a nothing here</option>
      </select>
      <div class="errors">
        <p v-if="$validation1.lang.required">Required !!</p>
      </div>
    </form>
  </validator>
</div>
```

```javascript
new Vue({ el: '#app' })
```

# 验证结果类

> 2.1+

有时，我们需要为不同验证结果显示不同的样式以达到更好的交互效果。vue-validator 在验证完表单元素后会自动插入相应的类来指示验证结果，如下例所示：

```html
<input id="username" type="text" v-validate:username="{
  required: { rule: true, message: 'required you name !!' }
}">
```

上例会输出如下 HTML：

```html
<input id="username" type="text" class="invalid untouched pristine">
```

## 验证结果类列表
| 验证类型 | 类名 (默认) | 描述 |
|:---:|---|---|
| `valid` | `valid` | 当目标元素变为 **valid** 时 |
| `invalid` | `invalid` | 当目标元素变为 **invalid** 时 |
| `touched` | `touched` | 当 **touched** 目标元素时 |
| `untouched` | `untouched` | 当目标元素还未被 **touched** 时 |
| `pristine` | `pristine` | 当目标元素还未 **dirty** 时 |
| `dirty` | `dirty` | 当目标元素 **dirty** 时 |
| `modified` | `modified` | 当目标元素 **modified** 时 |

## 使用自定义验证结果类
当默认的验证结果类名不方便使用时，你可以使用 `classes` 属性自定义相应的类名，如下所示：

```html
<validator name="validation1" 
           :classes="{ touched: 'touched-validator', dirty: 'dirty-validator' }">
  <label for="username">username:</label>
  <input id="username" 
         type="text" 
         :classes="{ valid: 'valid-username', invalid: 'invalid-username' }" 
         v-validate:username="{ required: { rule: true, message: 'required you name !!' } }">
</validator>
```

`classes` 属性需要使用在 `v-validate` 或 `validator` 指令上，值必须为对象。

## 在非目标元素上使用验证结果类

通常情况下验证结果类会插入到定义 `v-validate` 指令的元素上。然而有时候我们需要把这些类插入到其他元素上。这时我们可以使用 `v-validate-class` 来实现，如下所示：

```html
<validator name="validation1" 
           :classes="{ touched: 'touched-validator', dirty: 'dirty-validator' }">
  <div v-validate-class class="username">
    <label for="username">username:</label>
    <input id="username" 
           type="text" 
           :classes="{ valid: 'valid-username', invalid: 'invalid-username' }" 
           v-validate:username="{ required: { rule: true, message: 'required you name !!' }
    }">
  </div>
</validator>
```

上例会输出如下 HTML：

```html
<div class="username invalid-username untouched pristine">
  <label for="username">username:</label>
  <input id="username" type="text">
</div>
```

# 分组

支持把验证字段分组：

```html
<validator name="validation1" :groups="['user', 'password']">
  username: <input type="text" group="user" v-validate:username="['required']"><br />
  password: <input type="password" group="password" v-validate:password1="{ minlength: 8, required: true }"/><br />
  password (confirm): <input type="password" group="password" v-validate:password2="{ minlength: 8, required: true }"/>
  <div class="user">
    <span v-if="$validation1.user.invalid">Invalid yourname !!</span>
  </div>
  <div class="password">
    <span v-if="$validation1.password.invalid">Invalid password input !!</span>
  </div>
</validator>
```

# 错误消息

错误消息可以直接在验证规则中指定，同时可以在 `v-show` 和 `v-if` 中使用错误消息：
```html
<validator name="validation1">
  <div class="username">
    <label for="username">username:</label>
    <input id="username" type="text" v-validate:username="{
      required: { rule: true, message: 'required you name !!' }
    }">
    <span v-if="$validation1.username.required">{{ $validation1.username.required }}</span>
  </div>
  <div class="password">
    <label for="password">password:</label>
    <input id="password" type="password" v-validate:password="{
      required: { rule: true, message: 'required you password !!' },
      minlength: { rule: 8, message: 'your password short too !!' }
    }"/>
    <span v-if="$validation1.password.required">{{ $validation1.password.required }}</span>
    <span v-if="$validation1.password.minlength">{{ $validation1.password.minlength }}</span>
  </div>
</validator>
```

也可以在 `v-for` 指令中使用错误消息：

```html
<validator name="validation1">
  <div class="username">
    <label for="username">username:</label>
    <input id="username" type="text" v-validate:username="{
      required: { rule: true, message: 'required you name !!' }
    }">
  </div>
  <div class="password">
    <label for="password">password:</label>
    <input id="password" type="password" v-validate:password="{
      required: { rule: true, message: 'required you password !!' },
      minlength: { rule: 8, message: 'your password short too !!' }
    }"/>
  </div>
  <div class="errors">
    <ul>
      <li v-for="error in $validation1.errors">
        <p>{{error.field}}: {{error.message}}</p>
      </li>
    </ul>
  </div>
</validator>
```

使用数据属性或计算属性来指定验证规则比使用内联验证规则更简洁。

## 错误消息枚举组件

在上例中，我们使用 `v-for` 指令来枚举验证器的 `errors`。但是，我们可以让它更简单。本验证器提供了非常易用的 `validator-errors` 组件来枚举错误消息，如下例所示：

```html
<validator name="validation1">
  <div class="username">
    <label for="username">username:</label>
    <input id="username" type="text" v-validate:username="{
      required: { rule: true, message: 'required you name !!' }
    }">
  </div>
  <div class="password">
    <label for="password">password:</label>
    <input id="password" type="password" v-validate:password="{
      required: { rule: true, message: 'required you password !!' },
      minlength: { rule: 8, message: 'your password short too !!' }
    }"/>
  </div>
  <div class="errors">
    <validator-errors :validation="$validation1"></validator-errors>
  </div>
</validator>
```

上例的代码渲染出的界面如下：

```html
<div class="username">
  <label for="username">username:</label>
  <input id="username" type="text">
</div>
<div class="password">
  <label for="password">password:</label>
  <input id="password" type="password">
</div>
<div class="errors">
  <div>
    <p>password: your password short too !!</p>
  </div>
  <div>
    <p>password: required you password !!</p>
  </div>
  <div>
    <p>username: required you name !!</p>
  </div>
</div>
```

## 自定义错误消息模版

如果你不喜欢 `validator-errors` 默认的错误消息格式，可以指定自定义的组件或 partial 作为消息模版。

### 组件模版

下例中展示了使用组件作为模版：

```html
<div id="app">
  <validator name="validation1">
    <div class="username">
      <label for="username">username:</label>
      <input id="username" type="text" v-validate:username="{
        required: { rule: true, message: 'required you name !!' }
      }">
    </div>
    <div class="password">
      <label for="password">password:</label>
      <input id="password" type="password" v-validate:password="{
        required: { rule: true, message: 'required you password !!' },
        minlength: { rule: 8, message: 'your password short too !!' }
      }"/>
    </div>
    <div class="errors">
      <validator-errors :component="'custom-error'" :validation="$validation1">
      </validator-errors>
    </div>
  </validator>
</div>
```

```javascript
// register the your component with Vue.component
Vue.component('custom-error', {
  props: ['field', 'validator', 'message'],
  template: '<p class="error-{{field}}-{{validator}}">{{message}}</p>'
})

new Vue({ el: '#app' })
```
  
### Partial 模版

下例中展示了使用 partial 作为模版：

```html
<div id="app">
  <validator name="validation1">
    <div class="username">
      <label for="username">username:</label>
      <input id="username" type="text" v-validate:username="{
        required: { rule: true, message: 'required you name !!' }
      }">
    </div>
    <div class="password">
      <label for="password">password:</label>
      <input id="password" type="password" v-validate:password="{
        required: { rule: true, message: 'required you password !!' },
        minlength: { rule: 8, message: 'your password short too !!' }
      }"/>
    </div>
    <div class="errors">
      <validator-errors partial="myErrorTemplate" :validation="$validation1">
      </validator-errors>
    </div>
  </validator>
</div>
```

```javascript
// register custom error template
Vue.partial('myErrorTemplate', '<p>{{field}}: {{validator}}: {{message}}</p>')
new Vue({ el: '#app' })
```

### 指定错误消息

有时候你只需要输出部分错误消息，此时你可以通过 `group` 或 `field` 属性来指定这部分验证结果。

- `group`: 指定组的错误消息 (例如 $validation.group1.errors)
- `field`: 指定字段的错误消息 (例如 $validation.field1.errors)

下例中展示了 `group` 属性的使用：

```html
<div id="app">
  <validator :groups="['profile', 'password']" name="validation1">
    <div class="username">
      <label for="username">username:</label>
      <input id="username" type="text" group="profile" v-validate:username="{
        required: { rule: true, message: 'required you name !!' }
      }">
    </div>
    <div class="url">
      <label for="url">url:</label>
      <input id="url" type="text" group="profile" v-validate:url="{
        required: { rule: true, message: 'required you name !!' },
        url: { rule: true, message: 'invalid url format' }
      }">
    </div>
     <div class="old">
     <label for="old">old password:</label>
      <input id="old" type="password" group="password" v-validate:old="{
        required: { rule: true, message: 'required you old password !!' },
        minlength: { rule: 8, message: 'your old password short too !!' }
      }"/>
    </div>
    <div class="new">
      <label for="new">new password:</label>
      <input id="new" type="password" group="password" v-validate:new="{
        required: { rule: true, message: 'required you new password !!' },
        minlength: { rule: 8, message: 'your new password short too !!' }
      }"/>
    </div>
    <div class="confirm">
      <label for="confirm">confirm password:</label>
      <input id="confirm" type="password" group="password" v-validate:confirm="{
        required: { rule: true, message: 'required you confirm password !!' },
        minlength: { rule: 8, message: 'your confirm password short too !!' }
      }"/>
    </div>
    <div class="errors">
      <validator-errors group="profile" :validation="$validation1">
      </validator-errors>
    </div>
  </validator>
</div>
```

```javascript
Vue.validator('url', function (val) {
  return /^(http\:\/\/|https\:\/\/)(.{4,})$/.test(val)
})
new Vue({ el: '#app' })
```

## 手动设置错误消息

有时候你需要手动设置验证错误的消息，如从服务器端得到的验证错误消息。这时你可以通过 `$setValidationErrors` 方法设置错误消息，如下例：

```html
<div id="app">
  <validator name="validation">
    <div class="username">
      <label for="username">username:</label>
      <input id="username" type="text" v-model="username" v-validate:username="{
        required: { rule: true, message: 'required you name !!' }
      }">
    </div>
    <div class="old">
      <label for="old">old password:</label>
      <input id="old" type="password" v-model="passowrd.old" v-validate:old="{
        required: { rule: true, message: 'required you old password !!' }
      }"/>
    </div>
    <div class="new">
      <label for="new">new password:</label>
      <input id="new" type="password" v-model="password.new" v-validate:new="{
        required: { rule: true, message: 'required you new password !!' },
        minlength: { rule: 8, message: 'your new password short too !!' }
      }"/>
    </div>
    <div class="confirm">
      <label for="confirm">confirm password:</label>
      <input id="confirm" type="password" v-validate:confirm="{
        required: { rule: true, message: 'required you confirm password !!' },
        confirm: { rule: passowd.new, message: 'your confirm password incorrect !!' }
      }"/>
    </div>
    <div class="errors">
      <validator-errors :validation="$validation"></validator-errors>
    </div>
    <button type="button" v-if="$validation.valid" @click.prevent="onSubmit">update</button>
  </validator>
</div>
```
```javascript
new Vue({
  el: '#app',
  data: {
    id: 1,
    username: '',
    password: {
      old: '',
      new: ''
    }
  },
  validators: {
    confirm: function (val, target) {
      return val === target
    }
  },
  methods: {
    onSubmit: function () {
      var self = this
      var resource = this.$resource('/user/:id')
      resource.save({ id: this.id }, {
        username: this.username,
        passowrd: this.new
      }, function (data, stat, req) {
        // something handle success ...
        // ...
      }).error(function (data, stat, req) {
        // handle server error
        self.$setValidationErrors([
          { field: data.field, message: data.message }
        ])
      })
    }
  }
})
```

# 事件

可以使用 vue 中的事件绑定方法绑定验证器产生的事件。

## 字段验证事件

对于每一个字段，你都可以监听如下事件：

- `valid`: 当字段验证结果变为有效时触发
- `invalid`: 当字段验证结果变为无效时触发
- `touched`: 当字段失去焦点时触发
- `dirty`: 当字段值首次变化时触发
- `modified`: 当字段值与初始值不同时或变回初始值时触发

```html
<div id="app">
  <validator name="validation1">
    <div class="comment-field">
      <label for="comment">comment:</label>
      <input type="text" 
             @valid="onValid" 
             @invalid="onInvalid" 
             @touched="onTouched" 
             @dirty="onDirty" 
             @modified="onModified"
             v-validate:comment="['required']"/>
    </div>
    <div>
      <p>{{occuredValid}}</p>
      <p>{{occuredInvalid}}</p>
      <p>{{occuredTouched}}</p>
      <p>{{occuredDirty}}</p>
      <p>{{occuredModified}}</p>
    </div>
  </validator>
</div>
```

```javascript
new Vue({
  el: '#app',
  data: {
    occuredValid: '',
    occuredInvalid: '',
    occuredTouched: '',
    occuredDirty: '',
    occuredModified: ''
  },
  methods: {
    onValid: function () {
      this.occuredValid = 'occured valid event'
      this.occuredInvalid = ''
    },
    onInvalid: function () {
      this.occuredInvalid = 'occured invalid event'
      this.occuredValid = ''
    },
    onTouched: function () {
      this.occuredTouched = 'occured touched event'
    },
    onDirty: function () {
      this.occuredDirty = 'occured dirty event'
    },
    onModified: function (e) {
      this.occuredModified = 'occured modified event: ' + e.modified
    }
  }
})
```

## 顶级验证事件

可以监听如下顶级验证结果的变化事件：

- `valid`: 当全局验证结果变为有效时触发
- `invalid`: 当全局验证结果变为无效时触发
- `touched`: 当任意验证字段失去焦点时触发
- `dirty`: 当任意字段首次改变时触发
- `modified`: 当任意字段首次改变时或所有字段恢复初始值时触发

```html
<div id="app">
  <validator name="validation1"
             @valid="onValid"
             @invalid="onInvalid"
             @touched="onTouched"
             @dirty="onDirty"
             @modified="onModified">
    <div class="comment-field">
      <label for="username">username:</label>
      <input type="text" 
             v-validate:username="['required']"/>
    </div>
    <div class="password-field">
      <label for="password">password:</label>
      <input type="password" 
             v-validate:password="{ required: true, minlength: 8 }"/>
    </div>
    <div>
      <p>{{occuredValid}}</p>
      <p>{{occuredInvalid}}</p>
      <p>{{occuredTouched}}</p>
      <p>{{occuredDirty}}</p>
      <p>{{occuredModified}}</p>
    </div>
  </validator>
</div>
```

```javascript
new Vue({
  el: '#app',
  data: {
    occuredValid: '',
    occuredInvalid: '',
    occuredTouched: '',
    occuredDirty: '',
    occuredModified: ''
  },
  methods: {
    onValid: function () {
      this.occuredValid = 'occured valid event'
      this.occuredInvalid = ''
    },
    onInvalid: function () {
      this.occuredInvalid = 'occured invalid event'
      this.occuredValid = ''
    },
    onTouched: function () {
      this.occuredTouched = 'occured touched event'
    },
    onDirty: function () {
      this.occuredDirty = 'occured dirty event'
    },
    onModified: function (modified) {
      this.occuredModified = 'occured modified event: ' + modified
    }
  }
})
```

# 延迟初始化

如果在 `validator` 元素上设置了 `lazy` 属性，那么验证器直到 `$activateValidator()` 被调用时才会进行初始化。这在待验证的数据需要异步加载时有用，避免了在得到数据前出现错误提示。

下例中在得到评论内容后验证器才开始工作；如果不设置 `lazy` 属性，在得到评论内容前会显示错误提示。

```html
<!-- comment component -->
<div>
  <h1>Preview</h1>
  <p>{{comment}}</p>
  <validator lazy name="validation1">
    <input type="text" :value="comment" v-validate:comment="{ required: true, maxlength: 256 }"/>
    <span v-if="$validation1.comment.required">Required your comment</span>
    <span v-if="$validation1.comment.maxlength">Too long comment !!</span>
    <button type="button" value="save" @click="onSave" v-if="valid">
  </validator>
</div>
```

```javascript
Vue.component('comment', {
  props: {
    id: Number,
  },
  data: function () {
    return { comment: '' }
  },
  activate: function (done) {
    var resource = this.$resource('/comments/:id');
    resource.get({ id: this.id }, function (comment, stat, req) {
      this.comment =  comment.body

      // activate validator
      this.$activateValidator()
      done()

    }.bind(this)).error(function (data, stat, req) {
      // handle error ...
      done()
    })
  },
  methods: {
    onSave: function () {
      var resource = this.$resource('/comments/:id');
      resource.save({ id: this.id }, { body: this.comment }, function (data, stat, req) {
        // handle success
      }).error(function (data, sta, req) {
        // handle error
      })
    }
  }
})
```

# 自定义验证器

## 全局注册
可以使用 `Vue.validator` 方法注册自定义验证器。

> **提示:** `Vue.validator` asset 继承自 Vue.js 的 asset 管理系统.

通过下例中的 `email` 自定义验证器详细了解 `Vue.validator` 的使用方法：

```javascript
// Register email validator function. 
Vue.validator('email', function (val) {
  return /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(val)
})

new Vue({
  el: '#app'
  data: {
    email: ''
  }
})
```
```html
<div id="app">
  <validator name="validation1">
    address: <input type="text" v-validate:address="['email']"><br />
    <div>
      <p v-show="$validation1.address.email">Invalid your mail address format.</p>
    </div>
  <validator>
</div>
```

## 局部注册
可以通过组件的 `validators` 选项注册只能在组件内使用的自定义验证器。

自定义验证器是通过在组件的 `validators` 下定义验证通过返回真不通过返回假的回调函数来实现。

下例中注册了 `numeric` 和 `url` 两个自定义验证器：

```javascript
new Vue({
  el: '#app',
  validators: { // `numeric` and `url` custom validator is local registration
    numeric: function (val/*,rule*/) {
      return /^[-+]?[0-9]+$/.test(val)
    },
    url: function (val) {
      return /^(http\:\/\/|https\:\/\/)(.{4,})$/.test(val)
    }
  },
  data: {
    email: ''
  }
})
```

```html
<div id="app">
  <validator name="validation1">
    username: <input type="text" v-validate:username="['required']"><br />
    email: <input type="text" v-validate:address="['email']"><br />
    age: <input type="text" v-validate:age="['numeric']"><br />
    site: <input type="text" v-validate:site="['url']"><br />
    <div class="errors">
      <p v-if="$validation1.username.required">required username</p>
      <p v-if="$validation1.address.email">invalid email address</p>
      <p v-if="$validation1.age.numeric">invalid age value</p>
      <p v-if="$validation1.site.url">invalid site uril format</p>
    </div>
  <validator>
</div>
```

## 错误消息

可以为自定义验证器指定默认的错误消息：

```javascript
// `email` custom validator global registration
Vue.validator('email', {
  message: 'invalid email address', // error message with plain string
  check: function (val) { // define validator
    return /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(val)
  }
})

// build-in `required` validator customization
Vue.validator('required', {
  message: function (field) { // error message with function
    return 'required "' + field + '" field'
  },
  check: Vue.validator('required') // re-use validator logic
})

new Vue({
  el: '#app',
  validators: {
    numeric: { // `numeric` custom validator local registration
      message: 'invalid numeric value',
      check: function (val) {
        return /^[-+]?[0-9]+$/.test(val)
      }
    },
    url: { // `url` custom validator local registration
      message: function (field) {
        return 'invalid "' + field + '" url format field'
      },
      check: function (val) {
        return /^(http\:\/\/|https\:\/\/)(.{4,})$/.test(val)
      }
    }
  },
  data: {
    email: ''
  }
})
```

```html
<div id="app">
  <validator name="validation1">
    username: <input type="text" v-validate:username="['required']"><br />
    email: <input type="text" v-validate:address="['email']"><br />
    age: <input type="text" v-validate:age="['numeric']"><br />
    site: <input type="text" v-validate:site="['url']"><br />
    <div class="errors">
      <validator-errors :validation="$validation1"></validator-errors>
    </div>
  <validator>
</div>
```

# 自定义验证时机

默认情况下，`vue-validator` 会根据 `validator` 和 `v-validate` 指令自动进行验证。然而有时候我们需要关闭自动验证，在有需要时手动触发验证。

## `initial`
当 `vue-validator` 完成初始编译后，会根据每一条 `v-validate` 指令自动进行验证。如果你不需要自动验证，可以通过 `initial` 属性或 `v-validate` 验证规则来关闭自动验证，如下所示：

```html
<div id="app">
  <validator name="validation1">
    <form novalidate>
      <div class="username-field">
        <label for="username">username:</label>
        <!-- 'inital' attribute is applied the all validators of target element (e.g. required, exist) -->
        <input id="username" type="text" initial="off" v-validate:username="['required', 'exist']">
      </div>
      <div class="password-field">
        <label for="password">password:</label>
        <!-- 'initial' optional is applied with `v-validate` validator (e.g. required only) -->
        <input id="password" type="password" v-validate:passowrd="{ required: { rule: true, initial: 'off' }, minlength: 8 }">
      </div>
      <input type="submit" value="send" v-if="$validation1.valid">
    </form>
  </validator>
</div>
```

这在使用服务器端验证等异步验证方式时有用，具体可见后文例子。

## `detect-blur` and `detect-change`
`vue-validator` 会在检测到表单元素(input, checkbox, select 等)上的 DOM 事件(`input`, `blur`, `change`)时自动验证。此时，可以使用 `detect-change` 和 `detect-blur` 属性：

```html
<div id="app">
  <validator name="validation">
    <form novalidate @submit="onSubmit">
      <h1>user registration</h1>
      <div class="username">
        <label for="username">username:</label>
        <input id="username" type="text" 
          detect-change="off" detect-blur="off" v-validate:username="{
          required: { rule: true, message: 'required you name !!' }
        }" />
      </div>
      <div class="password">
        <label for="password">password:</label>
        <input id="password" type="password" v-model="password" 
          detect-change="off" detect-blur="off" v-validate:password="{
          required: { rule: true, message: 'required you new password !!' },
          minlength: { rule: 8, message: 'your new password short too !!' }
        }" />
      </div>
      <div class="confirm">
        <label for="confirm">confirm password:</label>
        <input id="confirm" type="password" 
          detect-change="off" detect-blur="off" v-validate:confirm="{
          required: { rule: true, message: 'required you confirm password !!' },
          confirm: { rule: password, message: 'your confirm password incorrect !!' }
        }" />
      </div>
      <div class="errors" v-if="$validation.touched">
        <validator-errors :validation="$validation"></validator-errors>
      </div>
      <input type="submit" value="register" />
    </form>
  </validator>
</div>
```

```javascript
new Vue({
  el: '#app',
  data: {
    password: ''
  },
  validators: {
    confirm: function (val, target) {
      return val === target
    }
  },
  methods: {
    onSubmit: function (e) {
      // validate manually
      var self = this
      this.$validate(true, function () {
        if (self.$validation.invalid) {
          e.preventDefault()
        }
      })
    }
  }
})
```

# 异步验证

当在需要进行服务器端验证，可以使用异步验证，如下例：

```html
<template>
  <validator name="validation">
    <form novalidate>
      <h1>user registration</h1>
      <div class="username">
        <label for="username">username:</label>
        <input id="username" type="text" 
          detect-change="off" v-validate:username="{
          required: { rule: true, message: 'required your name !!' },
          exist: { rule: true, initial: 'off' }
        }" />
        <span v-if="checking">checking ...</span>
      </div>
      <div class="errors">
        <validator-errors :validation="$validation"></validator-errors>
      </div>
      <input type="submit" value="register" :disabled="!$validation.valid" />
    </form>
  </validator>
</template>
```

```javascript
function copyOwnFrom (target, source) {
  Object.getOwnPropertyNames(source).forEach(function (propName) {
    Object.defineProperty(target, propName, Object.getOwnPropertyDescriptor(source, propName))
  })
  return target
}

function ValidationError () {
  copyOwnFrom(this, Error.apply(null, arguments))
}
ValidationError.prototype = Object.create(Error.prototype)
ValidationError.prototype.constructor = ValidationError

// exmpale with ES2015
export default {
  data () {
    return { checking: false }
  },
  validators: {
    exist (val) {
      this.vm.checking = true // spinner on
      return fetch('/validations/exist', {
        method: 'post',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          username: val
        })
      }).then((res) => {
        this.vm.checking = false // spinner off
        return res.json()
      }).then((json) => {
        return Object.keys(json).length > 0 
          ? Promise.reject(new ValidationError(json.message))
          : Promise.resolve()
      }).catch((error) => {
        if (error instanceof ValidationError) {
          return Promise.reject(error.message)
        } else {
          return Promise.reject('unexpected error')
        }
      })
    }
  }
}
```

## 异步验证接口

在异步验证时，可以使用如下两类接口：

### 1. 函数

需要实现一个返回签名为 `function (resolve, reject)` 如同 `promise` 一样的函数的自定义验证器。函数参数解释如下：

- 验证结果
  - 成功时: `resolve`
  - 失败时: `reject`

### 2. promise
需要实现一个返回 `promise` 的自定义验证器。根据验证结果来 `resolve` 或 `reject`。

## 使用错误消息
如上例所示，在服务器端验证错误发生时，可以使用服务器端返回的错误消息。


# 验证器函数 context
验证器函数 context 是绑定到 Validation 对象上的。Validation 对象提供了一些属性，这些属性在实现特定的验证器时有用。

## `vm` 属性
暴露了当前验证所在的 vue 实例。

the following ES2015 example:
```javascript
new Vue({
  data () { return { checking: false } },
  validators: {
    exist (val) {
      this.vm.checking = true // spinner on
      return fetch('/validations/exist', {
        // ...
      }).then((res) => { // done
        this.vm.checking = false // spinner off
        return res.json()
      }).then((json) => {
        return Promise.resolve()
      }).catch((error) => {
        return Promise.reject(error.message)
      })
    }
  }
})
```

## `el` 属性
暴露了当前验证器的目标 DOM 元素。下面展示了结合 [International Telephone Input](https://github.com/jackocnr/intl-tel-input) jQuery 插件使用的例子：

```javascript
new Vue({
  validators: {
    phone: function (val) {
      return $(this.el).intlTelInput('isValidNumber')
    }
  }
})
```

# API 手册

## Build-in Validators

### required

- **Elements:**
    - `input[type="text"]`
    - `input[type="radio"]`
    - `input[type="checkbox"]`
    - `input[type="number"]`
    - `input[type="password"]`
    - `input[type="email"]`
    - `input[type="tel"]`
    - `input[type="url"]`
    - `select`
    - `textarea`

- **Usage:**

    Check whether the value has been specified.

- **Example:**

    ```html
    <!-- array syntax -->
    <input type="text" v-validate:zip="['required']">

    <!-- object syntax -->
    <!-- NOTE: 'rule' need the dummy value -->
    <input type="text" v-validate:zip="{ required: { rule: true } }">

    <!-- radio -->
    <fieldset>
      <legend>Which do you like fruit ?</legend>
      <input id="apple" type="radio" name="fruit" value="apple" v-validate:fruits="{ required: { rule: true } }">
      <label for="apple">Apple</label>
      <input id="orange" type="radio" name="fruit" value="orange" v-validate:fruits>
      <label for="orange">Orage</label>
      <input id="grape" type="radio" name="fruit" value="grage" v-validate:fruits>
      <label for="grape">Grape</label>
      <input id="banana" type="radio" name="fruit" value="banana" v-validate:fruits>
      <label for="banana">Banana</label>
    </fieldset>

    <!-- checkbox -->
    <fieldset>
      <legend>Which do you like fruit ?</legend>
      <input id="apple" type="checkbox" value="apple" v-validate:fruits="{ required: { rule: true } }">
      <label for="apple">Apple</label>
      <input id="orange" type="checkbox" value="orange" v-validate:fruits>
      <label for="orange">Orage</label>
      <input id="grape" type="checkbox" value="grage" v-validate:fruits>
      <label for="grape">Grape</label>
      <input id="banana" type="checkbox" value="banana" v-validate:fruits>
      <label for="banana">Banana</label>
    </fieldset>

    <!-- select -->
    <select v-validate:lang="{ required: true }">
      <option value="">----- select your favorite programming language -----</option>
      <option value="javascript">JavaScript</option>
      <option value="ruby">Ruby</option>
      <option value="python">Python</option>
      <option value="perl">Perl</option>
      <option value="lua">Lua</option>
      <option value="go">Go</option>
      <option value="rust">Rust</option>
      <option value="elixir">Elixir</option>
      <option value="c">C</option>
      <option value="none">Not a nothing here</option>
    </select>
    ```

### pattern

- **Elements:**
    - `input[type="text"]`
    - `input[type="number"]`
    - `input[type="password"]`
    - `input[type="email"]`
    - `input[type="tel"]`
    - `input[type="url"]`
    - `textarea`

- **Usage:**

    Check whether the pattern of the regular expression.

- **Example:**

    ```html
    <input type="text" v-validate:zip="{ pattern: '/^\d{3}-\d{4}$/' }">
    ```

### minlength

- **Elements:**
    - `input[type="text"]`
    - `input[type="checkbox"]`
    - `input[type="number"]`
    - `input[type="password"]`
    - `input[type="email"]`
    - `input[type="tel"]`
    - `input[type="url"]`
    - `select`
    - `textarea`

- **Usage:**

    Check whether the length of specified value is less than or equal minimum length

- **Example:**

    ```html
    <input type="password" v-validate:password="{ minlength: 8 }"/>

    <!-- checkbox (multiple) -->
    <fieldset>
      <legend>Which do you like fruit ?</legend>
      <input id="apple" type="checkbox" value="apple" v-validate:fruits="{ minlength: 1 }">
      <label for="apple">Apple</label>
      <input id="orange" type="checkbox" value="orange" v-validate:fruits>
      <label for="orange">Orage</label>
      <input id="grape" type="checkbox" value="grage" v-validate:fruits>
      <label for="grape">Grape</label>
      <input id="banana" type="checkbox" value="banana" v-validate:fruits>
      <label for="banana">Banana</label>
    </fieldset>

    <!-- select (multiple) -->
    <select multiple size="10" v-validate:lang="{ minlength: 3 }">
      <option value="javascript">JavaScript</option>
      <option value="ruby">Ruby</option>
      <option value="python">Python</option>
      <option value="perl">Perl</option>
      <option value="lua">Lua</option>
      <option value="go">Go</option>
      <option value="rust">Rust</option>
      <option value="elixir">Elixir</option>
      <option value="c">C</option>
      <option value="none">Not a nothing here</option>
    </select>
    ```

### maxlength

- **Elements:**
    - `input[type="text"]`
    - `input[type="checkbox"]`
    - `input[type="number"]`
    - `input[type="password"]`
    - `input[type="email"]`
    - `input[type="tel"]`
    - `input[type="url"]`
    - `select`
    - `textarea`

- **Usage:**

    Check whether the length of specified value is less more or equal maximum length

- **Example:**

    ```html
    <input type="text" v-validate:comment="{ maxlength: 256 }"/>
    ```

### min

- **Elements:**
    - `input[type="text"]`
    - `input[type="number"]`
    - `textarea`

- **Usage:**

    Check whether the specified numerical value is less than or equal minimum

- **Example:**

    ```html
    <input type="text" v-validate:age="{ min: 18 }"/>

    <!-- checkbox (multiple) -->
    <fieldset>
      <legend>Which do you like fruit ?</legend>
      <input id="apple" type="checkbox" value="apple" v-validate:fruits="{ maxlength: 3 }">
      <label for="apple">Apple</label>
      <input id="orange" type="checkbox" value="orange" v-validate:fruits>
      <label for="orange">Orage</label>
      <input id="grape" type="checkbox" value="grage" v-validate:fruits>
      <label for="grape">Grape</label>
      <input id="banana" type="checkbox" value="banana" v-validate:fruits>
      <label for="banana">Banana</label>
    </fieldset>

    <!-- you can use the `select` -->
    <select multiple size="10" v-validate:lang="{ maxlength: 3 }">
      <option value="javascript">JavaScript</option>
      <option value="ruby">Ruby</option>
      <option value="python">Python</option>
      <option value="perl">Perl</option>
      <option value="lua">Lua</option>
      <option value="go">Go</option>
      <option value="rust">Rust</option>
      <option value="elixir">Elixir</option>
      <option value="c">C</option>
      <option value="none">Not a nothing here</option>
    </select>
    ```

### max

- **Elements:**
    - `input[type="text"]`
    - `input[type="number"]`
    - `textarea`

- **Usage:**

    Check whether the specified numerical value is more than or equal maximum

- **Example:**

    ```html
    <input type="text" v-validate:limit="{ max: 100 }"/>
    ```

## 全局 API

### Vue.validator( id, [definition] )

- **参数:**
    - `{String} id`
    - `{Function | Object} [definition]`
- **返回:**
    - validator definition function or object

- **用法:**

  注册或获取全局验证器。

  ```javascript
  /*
   * Register custom validator 
   *
   * Arguments:
   *   - first argument: field value
   *   - second argument: rule value (optional). this argument is being passed from specified validator rule with v-validate
   * Return:
   *   `true` if valid, else return `false`
   */
  Vue.validator('zip', function (val, rule) {
    return /^\d{3}-\d{4}$/.test(val)
  })

  /*
   * Register custom validator for async
   * 
   * You can use the `Promise` or promise like `function (resolve, reject)`
   */
  Vue.validator('exist', function (val) {
    return fetch('/validations/exist', {
      method: 'post',
      // ...
    }).then(function (json) {
      return Promise.resolve() // valid
    }).catch(function (error) {
      return Promise.reject(error.message) // invalid
    })
  })

  /*
   * Register validator definition object
   *
   * You need to specify the `check` custom validator function.
   * If you need to error message, you can specify the `message` string or function together.
   */
  Vue.validator('email', {
    message: 'invalid email address', // error message
    check: function (val) { // custome validator
      return /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(val)
    }
  })
  ```

- **另见:**
  - [自定义验证器](custom.html)

## 构造器选项

### validators

- **类型:** `Object`

- **详细:**

  一个只对当前 Vue 实例可见的验证器定义对象。

- **另见:**
  - [Vue.validator()](#vuevalidator-id-definition-)

## 实例元方法

### $activateValidator()

- **参数:**
  无

- **用法:**

  激活使用 `validator` 元素的 `lazy` 属性延迟初始化的验证器

- **示例:**

  ```javascript
  Vue.component('comment', {
    props: {
      id: Number,
    },
    data: function () {
      return { comment: '' }
    },
    activate: function (done) {
      var resource = this.$resource('/comments/:id');
      resource.get({ id: this.id }, function (comment, stat, req) {
        this.comment =  comment.body
  
        // activate validator
        this.$activateValidator()
        done()
  
      }.bind(this)).error(function (data, stat, req) {
        // handle error ...
        done()
      })
    },
    methods: {
      onSave: function () {
        var resource = this.$resource('/comments/:id');
        resource.save({ id: this.id }, { body: this.comment }, function (data, stat, req) {
          // handle success
        }).error(function (data, sta, req) {
          // handle error
        })
      }
    }
  })
  ```
  
- **另见:**
  - [延迟初始化](lazy.html)

### $resetValidation( [cb] )

- **参数:**
  - `{Function} [cb]`

- **用法:**

  重置验证结果。

- **示例:**

  ```javascript
  new Vue({
    el: '#app',
    methods: {
      onClickReset: function () {
        this.$resetValidation(function () {
          console.log('reset done')
        })
      }
    }
  })
  ```

- **另见:**
  - [重置验证结果](reset.html)

### $setValidationErrors( errors )

- **参数:**
  - `Array<Object>` errors
    - `{String}` field
    - `{String}` message
    - `{String}` validator [optional]

- **参数: field**

  指定错误字段名。

- **参数: message**

  指定错误消息。

- **参数: validator**

  指定错误所在的验证器。

- **用法:**

  用来设置验证错误结果。这在手动设置服务器端验证产生的错误时有用。

- **示例:**

  ```javascript
  new Vue({
    el: '#app',
    data: {
      id: 1,
      username: '',
      password: {
        old: '',
        new: ''
      }
    },
    validators: {
      confirm: function (val, target) {
        return val === target
      }
    },
    methods: {
      onSubmit: function () {
        var self = this
        var resource = this.$resource('/user/:id')
        resource.save({ id: this.id }, {
          username: this.username,
          passowrd: this.new
        }, function (data, stat, req) {
          // something handle success ...
          // ...
        }).error(function (data, stat, req) {
          // handle server error
          self.$setValidationErrors([
            { field: data.field, message: data.message }
          ])
        })
      }
    }
  })
  ```

- **另见:**
  - [错误消息](errors.html)

### $validate( [field], [touched], [cb] )

- **参数:**
  - `{String} [field]`
  - `{Boolean} [touched]`
  - `{Function} [cb]`

- **用法:**

  Validate the target formalable element fields. 
  验证目标表单元素。

  - 如果未设置 `field` 参数，验证所有字段；

  - 如果 `touched` 参数为 `true`，那么验证结果的 `touched` 值会被设置为 `true`；

- **示例:**

  ```javascript
  new Vue({
    el: '#app',
    data: { password: '' },
    validators: {
      confirm: function (val, target) {
        return val === target
      }
    },
    methods: {
      onSubmit: function (e) {
        // validate the all fields manually with touched
        var self = this
        this.$validate(true, function () {
          console.log('validate done !!')
          if (self.$validation.invalid) {
            e.preventDefault()
          }
        })
      }
    }
  })
  ```

- **另见:**
  - [自定义验证时机](timing.html)

## 指令

### v-validate

- **类型:** `Array | Object`

- **Param Attributes:**
  - `group`
  - `field`
  - `detect-blur`
  - `detect-change`
  - `initial`

- **用法:**

  自定需要验证的表单元素。可参见下面的示例。

- **示例:**

  ```html
  <!-- array syntax -->
  <input type="text" v-validate:username="['required']">

  <!-- object syntax -->
  <input type="text" v-validate:zip="{ required: true, pattern: { rule: '/^\d{3}-\d{4}$/', message: 'invalid zip pattern' }}">

  <!-- binding -->
  <input type="text" v-validate:zip="zipRule">

  <!-- grouping -->
  <input type="text" group="profile" v-validate:user="['required']">

  <!-- field -->
  <input type="text" field="field1" v-validate="['required']">

  <!-- disable validation with DOM event -->
  <input type="password" detect-blur="off" detect-change="off" v-validate:password="['required']">

  <!-- disable initial auto-validation -->
  <input type="text" initial="off" v-validate:message="['required']">
  ```

- **另见:**
  - [验证器语法](syntax.html)
  - [分组](grouping.html)
  - [事件](events.html)
  - [结合 v-model](model.html)
  - [自定义验证时机](timing.html)

## Special Elements

### validator

- **属性:**
  - `name` (required)
  - `groups`
  - `lazy`
 
- **用法:**

  `<validator>` 元素用来在表单元素(input, select, textarea等)上引入验证器。`<validator>` 元素本身会被替换。

  验证结果会关联到验证器元素上，字段名是由 `validator` 元素的 `name` 属性值加 `$` 前缀组成。
  
> :小心: 验证器名称不要与 Vue.js 中的自带属性重复，如 `$event` 等。

- **示例:**

  ```html
  <!-- basic -->
  <validator name="validation">
    <input type="text" v-validate:username="['required']">
    <p v-if="$validation.invalid">invalid !!<p>
  </validator>

  <!-- validation grouping -->
  <validator name="validation" :groups="['user', 'password']">
    <label for="username">username:</label>
    <input type="text" group="user" v-validate:username="['required']">
    <label for="password">password:</label>
    <input type="password" group="password" v-validate:password1="{ minlength: 8, required: true }"/>
    <label for="confirm">password (confirm):</label>
    <input type="password" group="password" v-validate:password2="{ minlength: 8, required: true }"/>
    <p v-if="$validation.user.invalid">Invalid yourname !!</p>
    <p v-if="$validation.password.invalid">Invalid password input !!</p>
  </validator>

  <!-- lazy initialization -->
  <validator lazy name="validation">
    <input type="text" :value="comment" v-validate:comment="{ required: true, maxlength: 256 }"/>
    <span v-if="$validation.comment.required">Required your comment</span>
    <span v-if="$validation.comment.maxlength">Too long comment !!</span>
    <button type="button" value="save" @click="onSave" v-if="valid">
  </validator>
  ```

- **另见:**
  - [验证结果结构](structure.html)
  - [分组](grouping.html)
  - [延迟初始化](lazy.html)
  - [自定义验证时机](timing.html)
  - [异步验证](async.html)

### validator-errors

- **属性:**
  - `validation` (required with v-bind)
  - `component`
  - `partial`
  - `group`
  - `field`

- **用法:**

  `<validator-errors>` 可以作为错误消息的出口。`<validator-errors>` 元素会被替换成默认的错误消息模板。可以通过 `component` 和 `partial` 属性来自定义错误消息的显示方式。

- **示例:**

  ```html
  <!-- basic -->
  <validator name="validation">
    ...
    <div class="errors">
      <validator-errors :validation="$validation"></validator-errors>
    </div>
  </validator>

  <!-- render validation error message with component -->
  <validator name="validation">
    ...
    <div class="errors">
      <validator-errors :component="'custom-error'" :validation="$validation">
      </validator-errors>
    </div>
  </validator>

  <!-- render validation error message with partial -->
  <validator name="validation">
    ...
    <div class="errors">
      <validator-errors partial="myErrorTemplate" :validation="$validation">
      </validator-errors>
    </div>
  </validator>

  <!-- error message filter with group -->  
  <validator :groups="['profile', 'password']" name="validation1">
    ...
    <input id="username" type="text" group="profile" v-validate:username="{
      required: { rule: true, message: 'required you name !!' }
    }">
    ...
    <input id="old" type="password" group="password" v-validate:old="{
      required: { rule: true, message: 'required you old password !!' },
      minlength: { rule: 8, message: 'your old password short too !!' }
    }"/>
    ...
    <div class="errors">
      <validator-errors group="profile" :validation="$validation1">
      </validator-errors>
    </div>
  </validator>
  ```

- **另见:**
  - [错误](errors.html)


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
