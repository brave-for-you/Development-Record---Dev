---
title: Eslint+Husky
date: 2024-09-01
tags: 前端工程
categories: 前端
---
> 前端项目工程配置，强制校验代码格式统一规范

## 依赖包
```javascript
{
    "devDependencies": {
        "@vitejs/plugin-vue": "^3.0.2",
        "eslint": "^8.57.0",
        "eslint-plugin-vue": "^9.28.0",
        "husky": "^8.0.3",
        "sass": "^1.78.0",
        "vite": "^3.0.6"
    }
}
```
## 命令
```javascript
{
    "scripts": {
        "eslint": "eslint --ext .js,.vue .",
        "husky-init": "husky install",
        "husky-add": "husky add .husky/pre-commit"
    },
}
```
## Eslint规则
```javascript
module.exports = {
    env: {
        browser: true,
        es2021: true
    },
    extends: [
        'eslint:recommended',
        'plugin:vue/vue3-essential'
    ],
    overrides: [
        {
            env: {
                node: true
            },
            files: [
                '.eslintrc.{js,cjs}'
            ],
            parserOptions: {
                sourceType: 'script'
            }
        }
    ],
    parserOptions: {
        ecmaVersion: 'latest',
        sourceType: 'module'
    },
    plugins: [
        'vue'
    ],
    rules: {
        'vue/multi-word-component-names': 'off',
        // 这条规则不允许在循环体中使用 await。
        'no-await-in-loop': 2,
        //禁止使用alert confirm prompt
        'no-alert': 0,
        //禁止使用数组构造器
        'no-array-constructor': 2,
        //禁止使用按位运算符
        'no-bitwise': 0,
        //禁止使用arguments.caller或arguments.callee
        'no-caller': 2,
        //禁止给class赋值
        'no-class-assign': 2,
        //禁止在条件表达式中使用赋值语句
        'no-cond-assign': 2,
        //禁止修改const声明的变量
        'no-const-assign': 2,
        //禁止在条件中使用常量表达式 if (true) if (1)
        'no-constant-condition': 2,
        //禁止使用continue
        'no-continue': 0,
        //禁止在正则表达式中使用控制字符
        'no-control-regex': 2,
        //不能对var声明的变量使用delete操作符
        'no-delete-var': 2,
        //不允许将 Math、JSON、Reflect、Atomics 和 Intl 对象作为函数调用  var math = Math();
        'no-obj-calls': 2,
        //不允许两边完全相同的赋值  foo = foo;
        'no-self-assign': 2,
        //不允许无意义的代码
        // var x = 10;
        // if (x === x) {x = 20}
        'no-self-compare': 2,
        //禁止稀疏数组， [1,,2]
        'no-sparse-arrays': 2,
        //在调用super()之前不能使用this或super
        'no-this-before-super': 2,
        //不能有未定义的变量
        'no-undef': 2,
        //不能有无法执行的代码  因为 return、throw、break 和 continue 语句无条件地退出一个代码块
        'no-unreachable': 2,
        //不能有声明后未被使用的变量或参数
        'no-unused-vars': [2, {'vars': 'all', 'args': 'after-used'}],
        //未定义前不能使用
        'no-use-before-define': 2,
        //禁止比较时使用NaN，只能用isNaN()
        'use-isnan': 2,
        //强制驼峰法命名myFavoriteColor 、_myFavoriteColor、MY_FAVORITE_COLOR
        'camelcase': 2,
        //return 后面是否允许省略
        'consistent-return': 0,
        //必须使用 if (){} 中的{}
        'curly': [2, 'all'],
        //switch语句最后必须有default
        'default-case': 1,
        //default 必须放在最后
        'default-case-last': 2,
        //避免不必要的方括号
        'dot-notation': [0, {'allowKeywords': true}],
        //必须使用全等   === and !==
        'eqeqeq': 2,
        //函数表达式必须有名字
        'func-names': 0,
        //函数风格，规定只能使用函数声明/函数表达式
        'func-style': [0, 'declaration'],
        //变量名长度
        'id-length': 0,
        //声明时必须赋初值
        'init-declarations': 0,
        //代码块的嵌套块深度
        'max-depth': [2, 5],
        //强制每个文件的最大行数
        'max-lines': [1, 9999],
        //回调嵌套深度
        'max-nested-callbacks': [0, 5],
        //函数最多只能有10个参数
        'max-params': [0, 10],
        //函数内最多有几个声明
        'max-statements': [0, 10],
        //函数名首行大写必须使用new方式调用，首行小写必须用不带new方式调用  var friend = new Person();
        'new-cap': 2,
        //不允许在正则表达式的开头显式使用除法运算符     /=foo/
        'no-div-regex': 1,
        //如果if语句里面有return,后面不能跟else语句
        'no-else-return': 2,
        //块语句中的内容不能为空
        'no-empty': 2,
        //禁止对null使用==或!=运算符
        'no-eq-null': 2,
        //禁止多余的冒号
        'no-extra-semi': 2,
        //小数点之前之后必须要有数字  不能省略0
        'no-floating-decimal': 2,
        //禁止隐式转换
        'no-implicit-coercion': 2,
        //禁止行内备注
        'no-inline-comments': 0,
        //禁止使用__iterator__ 属性
        'no-iterator': 2,
        //禁止不必要的嵌套块
        'no-lone-blocks': 2,
        //禁止在循环中使用函数（如果没有引用外部变量不形成闭包就可以）
        'no-loop-func': 0,
        //禁止链式赋值  var a = b = c = 5;
        'no-multi-assign': 0,
        //字符串不能用\换行
        'no-multi-str': 2,
        //禁止使用嵌套的三目运算
        'no-nested-ternary': 0,
        //禁止在使用new构造一个实例后不赋值
        'no-new': 2,
        //禁止使用new Function
        'no-new-func': 2,
        //禁止使用new Object()
        'no-new-object': 2,
        //禁止使用new创建包装实例，new String new Boolean new Number
        'no-new-wrappers': 2,
        //禁止使用八进制数字（零开头的数字）
        'no-octal': 2,
        //禁止使用++，--
        'no-plusplus': 0,
        //禁止使用__proto__属性
        'no-proto': 2,
        //禁止重复声明变量
        'no-redeclare': 2,
        //return 语句中不能有赋值表达式  return foo = bar + 2;
        'no-return-assign': 0,
        //禁止使用javascript:void(0)
        'no-script-url': 0,
        //外部作用域中的变量不能与它所包含的作用域中的变量或参数同名
        'no-shadow': 2,
        //严格模式中规定的限制标识符不能作为声明时的变量名使用  NaN、Infinity、undefined
        'no-shadow-restricted-names': 2,
        //禁止使用三目运算符
        'no-ternary': 0,
        //变量初始化时不能直接给它赋值为undefined
        'no-undef-init': 2,
        //标识符不能以_开头或结尾
        'no-underscore-dangle': 0,
        //禁止不必要的嵌套 var isYes = answer === 1 ? true : false;
        'no-unneeded-ternary': 1,
        //禁用var，用let和const代替
        'no-var': 2,
        //禁用with
        'no-with': 2,
        //强制对象字面量缩写语法
        'object-shorthand': 0,
        //连续声明  var bar; var baz; => var bar,baz;
        'one-var': 0,
        //赋值运算符 += -=什么的
        'operator-assignment': [0, 'always'],
        //首选const
        'prefer-const': 0,
        //对象字面量中的属性必须加上双引号
        'quote-props': 0,
        //注释风格要不要有空格什么的
        'spaced-comment': 0,
        //使用严格模式
        'strict': 0,
        //箭头函数用小括号括起来
        'arrow-parens': 0,
        //=>的前/后括号
        'arrow-spacing': 0,
        //逗号风格，换行时在行首还是行尾  last 在行尾
        'comma-style': [2, 'last'],
        //缩进风格  2个tab
        'indent': [2, 4],
        //对象字面量中冒号的前后空格
        'key-spacing': [0, {'beforeColon': false, 'afterColon': true}],
        //new时必须加小括号
        'new-parens': 2,
        //禁止非必要的括号
        'no-extra-parens': 1,
        //空行最多不能超过3行
        'no-multiple-empty-lines': [1, {'max': 3}],
        //一行结束后面不要有空格
        'no-trailing-spaces': 1,
        //换行时运算符在行尾还是行首
        // foo = 1 +
        //       2;
        'operator-linebreak': [1, 'after'],
        //块语句内行首行尾是否要空行
        'padded-blocks': 0,
        //引号类型 `` '' ''
        'quotes': [1, 'single'],
        //语句强制分号结尾
        'semi': [0, 'always'],
        //分号前后空格
        'semi-spacing': [0, {'before': false, 'after': true}],
        //函数定义时括号前面要不要有空格
        'space-before-function-paren': [0, 'always'],
        //中缀操作符周围要不要有空格   a + b
        'space-infix-ops': 0,
        //正则表达式字面量用小括号包起来
        'wrap-regex': 0,
    }
}
```
## .eslintignore
```txt
build/*.js
node_modules
public
src/config/*
dist
vite.config.js
```
## pre-commit
> 注意：husky使用前必须先初始化git仓库，然后再依次初始新增husky文件
```shell
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run eslint
```
