---
title: node按行读取含分割线的多段log，提取重要信息并写入到另一文件输出
updated: 2022-11-17 14:24:23
date: 2022-11-17 14:24:23
tags: [JS]
categories: [前端,JS]
---

给定一个```.log```文件，含多段log，每段中包含用户id等信息。
从中提取id 时间 ip 响应码 api，项内通过```,```连接，项之间通过空格连接。时间也按形如```2000-10-10 10:34:55```的标准格式输出。在输出一整条完整信息之前不换行。
<!-- more -->

## log文件

origin.log文件。部分如下：
![origin.log](log.png)

## 输出文件
final.txt文件。部分输出如下：
![final.txt](final.png)

## 代码
analyze.js文件。整体代码如下：
```javascript
// id 时间 ip 响应码 api FROM【xxx，】  
const fs = require('fs')
const readline = require('readline')
const rs = fs.createReadStream('origin.log')
const ws = fs.createWriteStream('final.txt')
const rl = readline.createInterface({
    input: rs,
    terminal: false,
})

let reg = /^.*[/-]{28,}$/g  // 正则：是否为分割行
let totalLog = 0 // 计算log总数:通过计算分割线总数
let section = {} // 存放每段log
let countPerLog = 0 // section中的item数量
let tempJSONstr = ''

// 将对象类型的section转化为 JSON string 并写入文件
const sectionToString = function (section) {
    section = JSON.stringify(section)
    return section
}
// 获取id
const addId = function (obj) {
    obj = JSON.parse(obj)
    let length = Object.keys(obj).length;
    let arr = obj[length - 1]
    // console.log(arr)
    let regID = new RegExp("(^|[&?])" + 'user_id=' + "([0-9]*)(.*|$)");
    if (arr.includes('user_id')) {
        let strLine = arr.match(regID)
        idres = strLine[[2]]
        // return "id:" + idres
        return `${idres}`
    } else {
        return `n`
    }
}
// 获取时间和ip
const addTimeIp = function (obj) {
    obj = JSON.parse(obj)
    let arr = obj[0].split(' ') // arr是.log中的第一行
    let length = arr.length
    let time = `20${arr[1].slice(0, 2)}-${arr[1].slice(2, 4)}-${arr[1].slice(4, 6)} ${arr[2]}` // 访问时间
    let ip = arr[length - 1] // 用户ip
    // let strLine = `时间:${time} ip:${ip}`
    let strLine = `${time} ${ip}`
    return strLine
}
// 获取响应码和api
const addCodeApi = function (obj) {
    obj = JSON.parse(obj)
    let strLine = ''
    let length = Object.keys(obj).length;
    let arr = obj[length - 1].split(' ') // arr是.log中的最后一行
    let code = arr[4]
    let regApi = new RegExp("(^/.+)" + "(\\?)" + "(.*|$)");
    // console.log(arr[6])
    let api = arr[6].match(regApi)
    if (!api) {
        // strLine = `响应码:${code}  API:${arr[6]}`
        strLine = `${code} ${arr[6]}`
    } else {
        // strLine = `响应码:${code}  API:${api[1]}`
        strLine = `${code} ${api[1]}`
    }
    return strLine
}
// 获取访问的数据库
const addDB = function (obj) {
    obj = JSON.parse(obj)
    let DBSet = new Set()
    let length = Object.keys(obj).length;
    for (let i = 0; i < length; i++) {
        let arr = obj[i].split(' ') // arr是.log中的第i行
        // console.log(arr)
        if (arr.includes('FROM')) {
            let DBindex = arr.indexOf('FROM') + 1;
            // console.log(DBindex)
            DBSet.add(arr[DBindex])
            // console.log(arr[DBindex])
        }
    }
    if (!DBSet.size) {
        // return 'DB:n                '
        return 'n'
    } else {
        DBSet = [...DBSet].join(',')
        // return `DB:${DBSet}`
        return `${DBSet}`
    }
}
// 按行读原始log并以json形式存入tempJSONstr
rl.on('line', line => {
    if (!reg.test(line)) {  // 当前行不是"...----"的分割行
        section[countPerLog] = line
        countPerLog++
    } else {  // 当前行是"----"分割行
        totalLog++
        if (JSON.stringify(section) !== '{}') {
            section = sectionToString(section)
            // console.log(section)
            tempJSONstr = tempJSONstr + section + '@'
            section = {}
            countPerLog = 0
        }
        // console.log(line, "\n")
    }
})

// 由于最后一段log下没有分割线，在此处加入输出文件。
rl.on('close', () => {
    section = sectionToString(section)
    // console.log(section)
    tempJSONstr = tempJSONstr + section
    let obj = tempJSONstr.split('@')
    console.log('readline close...');
    console.log("@@@@@@@@@@@ totalLog:" + totalLog)
    console.log("@@@@@@@@@@@@@@obj.length = " + obj.length);
    for (let i = 0; i < obj.length; i++) {
        // if (obj[i].includes(id)) {
        let resline = addId(obj[i])
        resline = `${resline} ${addTimeIp(obj[i])}`
        resline = `${resline} ${addCodeApi(obj[i])}`
        resline = `${resline} ${addDB(obj[i])}`
        ws.write(resline + '\n')
    }
    // }
});
```

## 优化：处理含不同格式的log信息，如404 500
异常log如下：
![含异常的origin.log](abnormal.png)

analyze.js文件。整体代码如下：
```javascript
// id 时间 ip 响应码 api FROM【xxx，】  
const fs = require('fs')
const readline = require('readline')
const rs = fs.createReadStream('./log/origin.log')
const ws = fs.createWriteStream('./output/final.txt')
const rl = readline.createInterface({
    input: rs,
    terminal: false,
})

let reg = /^.*[/-]{28,}$/g  // 正则：是否为分割行
let totalLog = 0 // 计算log总数:通过计算分割线总数 + 404 + 500
let section = {} // 存放每段log
let countPerLog = 0 // section中的item数量
let tempJSONstr = ''

// 将对象类型的section转化为 JSON string 并写入文件
const sectionToString = function (section) {
    section = JSON.stringify(section)
    return section
}

// 获取id
const addId = function (obj) {
    obj = JSON.parse(obj)
    let length = Object.keys(obj).length;
    let arr = obj[length - 1]
    // console.log(arr)
    let regID = new RegExp("(^|[&?])" + 'user_id=' + "([0-9]*)(.*|$)");
    if (arr.includes('user_id')) {
        let strLine = arr.match(regID)
        idres = strLine[[2]]
        // return "id:" + idres
        return `${idres}`
    } else {
        return `n`
    }
}
// 获取时间和ip(状态码正常)
const addTimeIp = function (obj) {
    obj = JSON.parse(obj)
    let arr = obj[0].split(' ') // arr是.log中的第一行
    let length = arr.length
    let time = `20${arr[1].slice(0, 2)}-${arr[1].slice(2, 4)}-${arr[1].slice(4, 6)} ${arr[2]}` // 访问时间
    let ip = arr[length - 1] // 用户ip
    // let strLine = `时间:${time} ip:${ip}`
    let strLine = `${time} ${ip}`
    return strLine
}
// 获取时间和ip(状态码异常)
const addTimeIpErr = function (obj) {
    let arr = obj.split(' ') // arr是.log中的第一行
    let time = `20${arr[1].slice(0, 2)}-${arr[1].slice(2, 4)}-${arr[1].slice(4, 6)} ${arr[2]}` // 访问时间
    let regIp = /\((.+?)\)/g
    let tempip = obj.match(regIp) // 带括号的用户ip
    let ip = tempip[0].substring(1, tempip[0].length - 1);
    // console.log(ip)
    // let strLine = `时间:${time} ip:${ip}`
    let strLine = `${time} ${ip}`
    return strLine
}
// 获取响应码和api(状态码正常)
const addCodeApi = function (obj) {
    obj = JSON.parse(obj)
    let strLine = ''
    let length = Object.keys(obj).length;
    let arr = obj[length - 1].split(' ') // arr是.log中的最后一行
    let code = arr[4]
    let regApi = new RegExp("(^/.+)" + "(\\?)" + "(.*|$)");
    // console.log(arr[6])
    let api = arr[6].match(regApi)
    if (!api) {
        // strLine = `响应码:${code}  API:${arr[6]}`
        strLine = `${code} ${arr[6]}`
    } else {
        // strLine = `响应码:${code}  API:${api[1]}`
        strLine = `${code} ${api[1]}`
    }
    return strLine
}
// 获取响应码和api(状态码异常)
const addCodeApiErr = function (obj) {
    let strLine = ''
    let arr = obj.split(' ') // arr是.log中的第一行
    let code = arr[4]
    let regApi = new RegExp("(^/.+)" + "(\\?)" + "(.*|$)");
    // console.log(arr[6])
    let api = arr[6].match(regApi)
    if (!api) {
        // strLine = `响应码:${code}  API:${arr[6]}`
        strLine = `${code} ${arr[6]}`
    } else {
        // strLine = `响应码:${code}  API:${api[1]}`
        strLine = `${code} ${api[1]}`
    }
    return strLine
}
// 获取访问的数据库
const addDB = function (obj) {
    obj = JSON.parse(obj)
    let DBSet = new Set()
    let length = Object.keys(obj).length;
    for (let i = 0; i < length; i++) {
        let arr = obj[i].split(' ') // arr是.log中的第i行
        // console.log(arr)
        if (arr.includes('FROM')) {
            let DBindex = arr.indexOf('FROM') + 1;
            // console.log(DBindex)
            DBSet.add(arr[DBindex])
            // console.log(arr[DBindex])
        }
    }
    if (!DBSet.size) {
        // return 'DB:n                '
        return 'n'
    } else {
        DBSet = [...DBSet].join(',')
        // return `DB:${DBSet}`
        return `${DBSet}`
    }
}

// 按行读原始log并以json形式存入tempJSONstr
rl.on('line', line => {
    // 不是重启行
    if (line.includes('my uuid')) { }
    // 当前行不是"...----"的分割行 且状态码不是404或500 且不是出错后的描述行
    else if (!reg.test(line) && line[1] == 'I') {
        section[countPerLog] = line
        countPerLog++
        // 当前行状态码是404或500。情况有二，1:单独一群错误行 2:分割线里的正确行包着一群错误行
    } else if (!reg.test(line) && line[1] != 'I') {
        // 下面的if为了无视打印错误信息的几行
        if (line.includes(' 404 ') || line.includes(' 500 ')) {
            // 处理上一行是正确log && 正确log和err分属两段log
            if (JSON.stringify(section) !== '{}' && countPerLog != 1) {
                totalLog++
                section = sectionToString(section)
                tempJSONstr = tempJSONstr + section + '@'
                section = {}
                countPerLog = 0
                tempJSONstr = tempJSONstr + line + '@'
            } else if (JSON.stringify(section) !== '{}' && countPerLog == 1) {
                // 处理上一行是正确log && 正确log和err同属一段log
                section[countPerLog] = line
                countPerLog++
                section = sectionToString(section)
                tempJSONstr = tempJSONstr + section + '@'
                section = {}
                countPerLog = 0
            } else {
                // 处理上一行是错误log 当前行和上一行按分属两段log处理
                totalLog++
                tempJSONstr = tempJSONstr + line + '@'
            }
        }
    } else {  // 当前行是"----"分割行
        totalLog++
        if (JSON.stringify(section) !== '{}') {
            section = sectionToString(section)
            // console.log(section)
            tempJSONstr = tempJSONstr + section + '@'
            section = {}
            countPerLog = 0
        }
    }
})

// 由于最后一段log下没有分割线，在此处加入输出文件。
rl.on('close', () => {
    if (JSON.stringify(section) !== '{}') {
        section = sectionToString(section)
        // console.log(section)
        tempJSONstr = tempJSONstr + section
    }
    let obj = tempJSONstr.split('@')
    if (obj[obj.length - 1] == '') {
        obj.pop()
    }
    console.log(obj)
    console.log('readline close...');
    console.log("@@@@@@@@@@@ totalLog:" + totalLog)
    console.log("@@@@@@@@@@@@@@obj.length = " + obj.length);

    for (let i = 0; i < obj.length; i++) {
        if (obj[i][1] == 'E' || obj[i][1] == 'W') {
            // 处理出错行
            let resline = `n ${addTimeIpErr(obj[i])}`
            resline = `${resline} ${addCodeApiErr(obj[i])} n`
            ws.write(resline + '\n')
        } else {
            // 处理正确行
            if (JSON.parse(obj[i])[1][1] == 'E' || JSON.parse(obj[i])[1][1] == 'W') {
                // 首行正确，下一行出现错误
                obj[i] = JSON.parse(obj[i])
                obj[i] = obj[i][1]
                let resline = `n ${addTimeIpErr(obj[i])}`
                resline = `${resline} ${addCodeApiErr(obj[i])} n`
                ws.write(resline + '\n')
            } else {
                let resline = addId(obj[i])
                resline = `${resline} ${addTimeIp(obj[i])}`
                resline = `${resline} ${addCodeApi(obj[i])}`
                resline = `${resline} ${addDB(obj[i])}`
                ws.write(resline + '\n')
            }
        }
    }
});
```
