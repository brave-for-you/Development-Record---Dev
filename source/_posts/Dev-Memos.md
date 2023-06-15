---
title: Dev-Memos
date: 2023-06-15 10:29:07
tags:
---
一些代码片段记录。

## 加解密

### 加密
``` javascript
compileStr(code) {
  let c = String.fromCharCode(code.charCodeAt(0) + code.length)
  for(let i=1;i<code.length;i++) {
    c += String.fromCharCode(code.charCodeAt(i) + code.charCodeAt(i-1))
  }
  return escape(c)
}
```
### 解密
``` javascript
unCompileStr(str) {
  const code = unescape(str)
  let c = String.fromCharCode(code.charCodeAt(0) - code.length)
  for(let i=1;i<code.length;i++) {
    c += String.fromCharCode(code.charCodeAt(i) - c.charCodeAt(i-1))
  }
  return c
}
```

## 时间内容

### 时分秒倒计时（09:59:20）
``` javascript
// timeStamp为毫秒
handleToSetTimerFn(timeStamp) {
	let timer = null
	let t = timeStamp
	let h = 0
	let m = 0
	let s = 0
	h = Math.floor(t/(60*60))
	h<10&&(h="0"+h)
	m = Math.floor(t/60%60)
	m<10&&(m="0"+m)
	s = Math.floor(t%60)
	let _thisTemp = this
	timer = setInterval(function() {
		s--
		s<10&&(s="0"+s)
		if(s.length>=3) {
			s = 59
			m = (Number(m)-1)
			m<10&&(m="0"+m)
		}
		if(m.length>=3) {
			m = 59
			h = (Number(h)-1)
			h<10&&(h="0"+h)
		}
		if(h.length>=3) {
			h = "00"
			m = "00"
			s = "00"
			clearInterval(timer)
		}
		_thisTemp.timerStr = (h + ":" + m + ":" + s)
	},1000)
}
```
### 秒转换时分秒（10:00:00）
``` javascript
handleformatSeconds(value) {
  if(!value) {
    return 0
  }
  let second = parseInt(value)
  let minute = 0
  let hour = 0
  if (second > 60) {
    minute = parseInt(second / 60)
    second = parseInt(second % 60)
    if (minute > 60) {
      hour = parseInt(minute / 60)
      minute = parseInt(minute % 60)
    }
  }
  const result = (parseInt(hour) >= 10 ? parseInt(hour) : '0' + parseInt(hour)) + ':' + (parseInt(minute) >= 10 ? parseInt(minute) : '0' + parseInt(minute)) +':'+ (parseInt(second) >= 10 ? parseInt(second) : '0' + parseInt(second))
  return result
}
```
### 时间范围与当前时间前后对比
``` javascript
_handleToCompareTime(tempStartTimeStr, tempEndTimeStr) {
	const tempStartTime = new Date(tempStartTimeStr).getTime()
	const tempEndTime = new Date(tempEndTimeStr).getTime()
	const thisTempTime = new Date().getTime()
	if (thisTempTime < tempStartTime) {
		console.log(Math.round((tempStartTime - thisTempTime) / 1000 / 60)) // 距离开始剩余时间（minute）
		return -1 // 范围前
	} else if (thisTempTime > tempStartTime && thisTempTime < tempEndTime) {
		console.log(Math.round((tempEndTime - thisTempTime) / 1000 / 60)) // 距离结束剩余时间（minute）
		return 0 // 范围中
	} else {
		return 1 // 范围后
	}
}
```

## 文件内容

### 文件流下载
``` javascript
handleReturnDownloadFile(content,filename) {
  let eleLink = document.createElement('a')
  eleLink.download = filename
  eleLink.style.display = 'none'
  // 字符内容转变成blob地址 
  let url= window.URL.createObjectURL(new Blob([data], { type: 'application/octet-stream' }))
  eleLink.href = url 
  // 自动触发点击 
  document.body.appendChild(eleLink)
  eleLink.click()
  // 然后移除 
  document.body.removeChild(eleLink)
	//释放
	window.URL.revokeObjectURL(url)
}
``` 
### Base64转img
``` javascript
convertBase64UrlToBlob(urlData) {
  let bytes = window.atob(urlData)
  // 处理异常,将ascii码小于0的转换为大于0
  let ab = new ArrayBuffer(bytes.length)
  let ia = new Uint8Array(ab)
  for (let i = 0; i < bytes.length; i++) {
    ia[i] = bytes.charCodeAt(i)
  }
  return new Blob([ab], {type: 'image/jpg'})
}
``` 

## 字符串内容

### 数字校验
``` javascript
const a = /[0-9]/.exec(Str) != null ? 1 : 0
``` 
### 字母校验
``` javascript
const b = /[a-zA-Z]/.exec(Str) != null ? 1 : 0
``` 
### 字节统计
``` javascript
handleToCountStrLength(str) {
  let len = 0
  for (let i = 0; i < str.length; i++) { 
    let c = str.charAt(i)
    if (/^[\u0000-\u00ffA-Za-z1-9]+$/.test(c)) { 
      len += 1
    } else { 
      len += 2
    } 
  }
  return len
}
``` 
### 字符串转大写
``` javascript
str.toUpperCase()
``` 
### 键盘输入限制只有大写字母
``` javascript
_handleToCheckValFn(val) {
  const regex = /^[A-Z]+$/
  let tempStr = ""
  for(let i in val) {
    if(regex.test(val[i])) {
      tempStr += val[i]
    }
  }
  return tempStr
}
```

## VUE加载自动触发元素点击事件

### SubTemplate
``` javascript
directives: {
  trigger: {
    triggerFlag: false,
    inserted(el,binging) {
      if(binging.def.triggerFlag) {
        return
      }
      el.click()
      binging.def.triggerFlag = true
    }
  }
},
methods: {},
``` 

## Javascript数组内容

### filter()
``` javascript
// 过滤
let numArr = [0, 1, 2, 3, 4, 5, 6, 7]
let res = numArr.filter(num => num >= 5)
console.log(res) // [5, 6, 7]
``` 
### 重复判断
``` javascript
// 判断数组中是否有重复内容
Array.from(new Set(tempArr)).length < tempArr.length
``` 
### 数组内容移动
``` javascript
// 上移
this.tempArr[index] = this.tempArr.splice(index - 1, 1, this.tempArr[index])[0]
// 下移
this.tempArr[index] = this.tempArr.splice(index + 1, 1, this.tempArr[index])[0]
``` 

## 页面事件监听

### 监听离开
``` javascript
fn() {
	// 离开时监听触发
}
// PC端
window.addEventListener('blur', this.fn)
window.removeEventListener('blur', this.fn)
// 移动端
window.addEventListener('visibilitychange', this.fn)
window.removeEventListener('visibilitychange', this.fn)
```

## Css内容

### 线性渐变背景颜色
``` css
/* css线性渐变背景颜色 */
background: linear-gradient(to right , #ffce7b, #ff6609);
``` 
### 右上角三角标
``` css
/* css右上角三角标 */
background-image: linear-gradient(225deg, #f44336 20%, #00dd00 20%);
``` 

## 移动端Debug
``` html
<script src="https://unpkg.com/vconsole@latest/dist/vconsole.min.js"></script>
<script>
	 // VConsole 默认会挂载到 `window.VConsole` 上
	var vConsole = new window.VConsole();
</script>
```

## ssh生成公钥私钥配对
``` sh
$ ssh-keygen -t ed25519 -C "邮箱"
```


