---
title: Dev-Memos
date: 2024-03-26 16:30:00
tags:
---
一些感觉有意思的代码片段记录，持续更新记录...

## 加解密

### 加密
```javascript
compileStr(code) {
  let c = String.fromCharCode(code.charCodeAt(0) + code.length)
  for(let i=1;i<code.length;i++) {
    c += String.fromCharCode(code.charCodeAt(i) + code.charCodeAt(i-1))
  }
  return escape(c)
}
```
### 解密
```javascript
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
```javascript
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
	timer = setInterval(() => {
		s--
		s< 10 && (s = "0" + s)
		if (s.length >= 3) {
			s = 59
			m = (Number(m) - 1)
			m < 10 && (m = "0" + m)
		}
		if (m.length >= 3) {
			m = 59
			h = (Number(h) - 1)
			h < 10 && (h = "0" + h)
		}
		if (h.length >= 3) {
			h = "00"
			m = "00"
			s = "00"
			clearInterval(timer)
		}
		_thisTemp.timerStr = (h + ":" + m + ":" + s)
	}, 1000)
}
```
### 秒转换时分秒（10:00:00）
```javascript
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
```javascript
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
### 判断对比输入日期与当前日期
```javascript
_handleToCompareTime(dateString) {
  if (!dateString) {
    return false
  }
  const currentDate = new Date() // 获取当前日期
  const inputDate = new Date(dateString) // 将输入的日期字符串转换为Date对象
  if (inputDate > currentDate) {
    return false // 如果输入的日期大于当前日期
  }
  return true // 输入日期小于或等于当前日期
}
_handleToCompareTime('2024-05-01')
```
### 时间格式化
```javascript
dateFormat(date, format) {
    const zeroPadding = (i) => {
        return (i < 10 ? '0' : '') + i
    }
    return format.replace(/yyyy|MM|dd|HH|mm|ss/g, function (item) {
        switch (item) {
            case 'yyyy':
                return zeroPadding(date.getFullYear())
            case 'MM':
                return zeroPadding(date.getMonth() + 1)
            case 'dd':
                return zeroPadding(date.getDate())
            case 'HH':
                return zeroPadding(date.getHours())
            case 'mm':
                return zeroPadding(date.getMinutes())
            case 'ss':
                return zeroPadding(date.getSeconds())
        }
    })
}
dateFormat(new Date(), 'yyyy-MM-dd HH:mm:ss')
```
### 时间格式化 一年前的时间
```javascript
oldDateFormat(date, format) {
    const zeroPadding = (i) => {
        return (i < 10 ? '0' : '') + i
    }
    return format.replace(/yyyy|MM|dd|HH|mm|ss/g, function (item) {
        switch (item) {
            case 'yyyy':
                return zeroPadding(date.getFullYear() - 1)
            case 'MM':
                return zeroPadding(date.getMonth() + 1)
            case 'dd':
                return zeroPadding(date.getDate())
            case 'HH':
                return zeroPadding(date.getHours())
            case 'mm':
                return zeroPadding(date.getMinutes())
            case 'ss':
                return zeroPadding(date.getSeconds())
        }
    })
}
dateFormat(new Date(), 'yyyy-MM-dd HH:mm:ss')
```
### ElementUi日期组件
```javascript
    // ElementUi组件禁用当前时间前2天后5天以外的内容
    disabledDate: (date) => {
        return date.getTime() < Date.now() - 2 * 24 * 60 * 60 * 1000 || date.getTime() > Date.now() + 5 * 24 * 60 * 60 * 1000
    }
```

## 文件内容

### 文件流下载
```javascript
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
```javascript
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
### img转Base64
```javascript
convertBlobToBase64Url(file) {
    return new Promise((resolve, reject) => {
        let image = new Image()
        image.src = URL.createObjectURL(file)
        image.onload = function () {
            let canvas = document.createElement('canvas')
            let content = canvas.getContext('2d')
            canvas.width = image.width
            canvas.height = image.height
            content.drawImage(image, 0, 0, canvas.width, canvas.height)
            resolve(canvas.toDataURL(file.type))
            canvas.width = canvas.height = 0
            content.fillRect(0, 0, 0, 0)
            canvas = null
            content = null
            image = null
        }
        image.onerror = function (event, source, lineno, colno, error) {
            if (error) {
                console.error(event, source, lineno, colno, error)
                reject(error)
                image = null
            }
        }
    })
}
``` 
### 获取图片
```javascript
export function chooseImage(option) {
    try {
        const imageInput = document.createElement('input')
        imageInput.type = 'file'
        imageInput.accept = 'image/*'
        if (option.sourceType === 'camera') {
            imageInput.capture = 'environment'
        }
        imageInput.style.height = '0px'
        imageInput.style.width = '0px'
        imageInput.style.position = 'fixed'
        imageInput.style.left = '0px'
        imageInput.style.top = '0px'
        imageInput.style.visibility = 'hidden'
        document.getElementsByTagName('body')[0].appendChild(imageInput)
        imageInput.onchange = async function (e) {
            if (e.target.files.length) {
                let image = e.target.files[0]
                let gifFlag = image.type.includes('gif')
                // 设置了压缩但是图片为gif图片打印错误提示
                if (option.compress && gifFlag) {
                    console.log('\n')
                    console.warn('前端不具备压缩gif动图的能力，请服务端处理');
                    console.log('\n')
                }
                // 不是gif图片 设置了压缩 并且 大于1.8M才会触发压缩
                const compress = !gifFlag && option.compress && image.size > 1572864
                // 达到压缩条件开始压缩
                if (compress) {
                    // 大于1.8M才会触发压缩
                    image = await compressImageHandler(image)
                }
                const result = {
                    file: image,
                    name: image.name,
                    size: image.size,
                    path: !compress || gifFlag ? URL.createObjectURL(image) : await imageToBase64Handler(image),
                    type: image.type,
                }
                typeof option.success === 'function' && option.success(result)
                typeof option.complete === 'function' && option.complete(result)
                imageInput.remove()
            }
        }
        imageInput.click()
    } catch (e) {
        typeof option.fail === 'function' && option.fail(e)
        typeof option.complete === 'function' && option.complete(e)
    }
}
chooseImage({
  success: (data) => {}
})
``` 
### 选取视频
```javascript
export function chooseVideo(option = {}) {
    try {
        const fileInput = document.createElement('input')
        fileInput.type = 'file'
        fileInput.accept = 'video/*'
        if (option.sourceType === 'camera') {
            fileInput.capture = option.camera ? (option.camera === 'front' ? 'user' : 'environment') : 'user'
        }
        fileInput.style.height = '0px'
        fileInput.style.width = '0px'
        fileInput.style.position = 'fixed'
        fileInput.style.left = '0px'
        fileInput.style.top = '0px'
        fileInput.style.visibility = 'hidden'
        document.getElementsByTagName('body')[0].appendChild(fileInput)
        fileInput.onchange = function (e) {
            if (e.target.files.length) {
                const file = e.target.files[0]
                const result = {
                    file,
                    name: file.name,
                    size: file.size,
                    path: URL.createObjectURL(file),
                    type: file.type,
                }
                typeof option.success === 'function' && option.success(result)
                typeof option.complete === 'function' && option.complete(result)
                fileInput.remove()
            }
        }
        fileInput.click()
    } catch (e) {
        typeof option.fail === 'function' && option.fail(e)
        typeof option.complete === 'function' && option.complete(e)
    }
}
chooseVideo({
  success: (data) => {}
})
``` 

## 字符串内容

### 数字校验
```javascript
const a = /[0-9]/.exec(Str) != null ? 1 : 0
``` 
### 字母校验
```javascript
const a = /[a-zA-Z]/.exec(Str) != null ? 1 : 0
``` 
### 营业执照统一信用编码校验
```javascript
const reg = /[0-9A-HJ-NPQRTUWXY]{2}\d{6}[0-9A-HJ-NPQRTUWXY]{10}/.exec(Str)
``` 
### 字节统计
```javascript
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
```javascript
let str = 'abc'
str.toUpperCase()
console.log(str) // ABC
``` 
### 键盘输入限制只有大写字母
```javascript
//  @input="scope.row.accountNo = handleToCheckValFn(scope.row.accountNo)"
_handleToCheckValFn(val) {
  const regex = /^[A-Z]+$/ // 只填大写字母
  // const regex = /^[0-9]+$/ // 只填数字
  let tempStr = ""
  for(let i in val) {
    if(regex.test(val[i])) {
      tempStr += val[i]
    }
  }
  return tempStr
}
```
### 普通校验（手机号、邮箱、身份证号）
```javascript
const validatePhone = (rule, value, callback = () => {}) => {
    if(!value) {
        return callback(new Error('手机号码不能为空！'))
    }
    if(!/^1[3456789]\d{9}$/.test(value)) {
        return callback(new Error('手机号码不正确！'))
    }
    return callback()
}
const validateEmial = (rule, value, callback = () => {}) => {
    if(!value) {
        return callback(new Error('邮箱不能为空！'))
    }
    if(!/^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/.test(value)) {
        return callback(new Error('邮箱格式不正确！'))
    }
    return callback()
}
const validateIdCard = (rule, value, callback = () => {}) => {
    if(!value) {
        return callback(new Error('身份证号不能为空！'))
    }
    if(!/^[1-9]\d{5}(?:18|19|20|21|22)\d{2}(?:0[1-9]|10|11|12)(?:0[1-9]|[1-2]\d|30|31)\d{3}[$/.test(value)) {
        return callback(new Error('身份证号不正确！'))
    }
    return callback()
}
```
### 首字母大写
```javascript
export function firstChartToUpperCase(str) {
    if (typeof str === 'string') {
        return str.charAt(0).toUpperCase() + str.substr(1)
    }
    return str
}
```
### 字符串转驼峰
```javascript
export function stringTurnHump(str, symbol = '-') {
    if (typeof str === 'string') {
        return str.split(symbol).map((value, index) => (index > 0 ? firstChartToUpperCase(value) : value)).join('')
    }
    return str
}
```
### css字符串转JS对象
```javascript
export function stringTurnObjectForCSS(cssString) {
    if (cssString) {
        return Object.fromEntries(cssString.split(';').map((value) => value.split(':').map((val, index) => (index === 0 ? stringTurnHump(val) : val))))
    }
    return cssString
}
```
### 手机号脱敏
```javascript
export function formatPhone(phoneNumber) {
    const result = checkPhoneNumber(phoneNumber)
    if (result.success) {
        return phoneNumber.toString().replace(/(.{4})(.{4})(.{3})/, '$1****$3')
    }
    return result.message
}
```
### 脱敏银行卡号
```javascript
export function desensitizeBankCardNumber(cardNumber) {
    const str = String(cardNumber)
    return str.substring(str.length - 4)
}
```
### 金额转换千分位格式
```javascript
/**
 * 金额转换千分位格式
 * @param money 需转换的金额
 * @param fixedLength {number|null} 保留几位小数 false 不处理
 * @returns {string|*}
 */
export function convertThousandth(money, fixedLength = null) {
    if (isNaN(Number(money))) {
        return money
    }
    let _money = Number(money).toString()
    if (typeof fixedLength === 'number' && !isNaN(fixedLength)) {
        _money = toFixed(money, fixedLength)
    }
    if (_money.indexOf('.') === -1) {
        return _money.replace(/\B(?=(?:\d{3})+\b)/g, ',')
    }
    return _money.replace(/(\d)(?=(\d{3})+\.)/g, '$1,')
}
```
### 身份证号校验（号码+区号）
```javascript
const areaCode = '1100,1101,1102,1200,1201,1202,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1522,1525,1526,1529,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2200,2201,2202,2203,2204,2205,2206,2207,2208,2224,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2327,3100,3101,3102,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3400,3401,3402,3403,3404,3405,3406,3407,3408,3410,3411,3412,3413,3414,3415,3416,3417,3418,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4200,4201,4202,4203,4205,4206,4207,4208,4209,4210,4211,4212,4213,4228,4290,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4331,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4412,4413,4414,4415,4416,4417,4418,4419,4420,4451,4452,4453,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4600,4601,4602,4690,5000,5001,5002,5003,5100,5101,5103,5104,5105,5106,5107,5108,5109,5110,5111,5113,5114,5115,5116,5117,5118,5119,5120,5132,5133,5134,5200,5201,5202,5203,5204,5222,5223,5224,5226,5227,5300,5301,5303,5304,5305,5306,5307,5308,5309,5323,5325,5326,5328,5329,5331,5333,5334,5400,5401,5421,5422,5423,5424,5425,5426,6100,6101,6102,6103,6104,6105,6106,6107,6108,6109,6110,6200,6201,6202,6203,6204,6205,6206,6207,6208,6209,6210,6211,6226,6229,6230,6300,6301,6321,6322,6323,6325,6326,6327,6328,6400,6401,6402,6403,6404,6405,6500,6501,6502,6521,6522,6523,6527,6528,6529,6530,6531,6532,6540,6542,6543,6590,7100,8100,8200'
const validateIdCard = (rule, value, callback = () => {}) => {
  if(!value) {
    return callback(new Error('身份证号不能为空！'))
  }
  if(!/^[1-9]\d{5}(?:18|19|20|21|22)\d{2}(?:0[1-9]|10|11|12)(?:0[1-9]|[1-2]\d|30|31)\d{3}[\dX]$/.test(value)) {
      return callback(new Error('身份证号不正确！'))
  }
  // 前4位区号有效验证
  if(!areaCode.includes(value.slice(0, 3))) {
    return callback(new Error('身份证号不正确！'))
  }
  return callback()
}
```
### 根据身份证号计算内容
```javascript
computeIdCardFn(IdCard, type) {
  if(!IdCard) {
    return ''
  }
  if (type === 1) {
    //获取出生日期
    let birthday = IdCard.substring(6, 10) + '-' + IdCard.substring(10, 12) + '-' + IdCard.substring(12, 14)
    return birthday
  }
  if (type === 2) {
    let sex = ''
    //获取性别
    if (parseInt(IdCard.substr(16, 1)) % 2 === 1) {
      sex = '男'
    }else {
      sex = '女'
    }
    return sex
  }
  if (type === 3) {
    //获取年龄
    let ageDate = new Date()
    let month = ageDate.getMonth() + 1
    let day = ageDate.getDate()
    let age = ageDate.getFullYear() - IdCard.substring(6, 10) - 1
    if (IdCard.substring(10, 12) < month || IdCard.substring(10, 12) === month && IdCard.substring(12, 14) <= day) {
      age++
    }
    if (age <= 0) {
      age = 1
    }
    return age
  }
}
```


## VUE加载自动触发元素点击事件

### SubTemplate
```javascript
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
```javascript
// 过滤
let numArr = [0, 1, 2, 3, 4, 5, 6, 7]
let res = numArr.filter(num => num >= 5)
console.log(res) // [5, 6, 7]
``` 
### 重复判断
```javascript
// 判断数组中是否有重复内容
let tempArr = [1, 2, 2, 3]
Array.from(new Set(tempArr)).length < tempArr.length
``` 
### 数组内容移动
```javascript
// 上移
this.tempArr[index] = this.tempArr.splice(index - 1, 1, this.tempArr[index])[0]
// 下移
this.tempArr[index] = this.tempArr.splice(index + 1, 1, this.tempArr[index])[0]
``` 

## 页面事件监听

### 监听离开
```javascript
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
```css
/* css线性渐变背景颜色 */
background: linear-gradient(to right , #ffce7b, #ff6609);
``` 
### 右上角三角标
```css
/* css右上角三角标 */
background-image: linear-gradient(225deg, #f44336 20%, #00dd00 20%);
``` 

## 移动端Debug
```html
<script src="https://unpkg.com/vconsole@latest/dist/vconsole.min.js"></script>
<script>
	 // VConsole 默认会挂载到 `window.VConsole` 上
	var vConsole = new window.VConsole();
</script>
```

## ssh
```sh
# 生成公钥私钥配对
$ ssh-keygen -t ed25519 -C "邮箱"
# git基础信息设置
$ git config --global user.name ""
$ git config --global user.email ""
```