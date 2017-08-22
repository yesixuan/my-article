---
title: JS常用方法（三）
tags: ["js","常用方法"]
date: 2017-08-22 09:59:00
categories:
- 前端
- JS
- 常用函数
---
> 整理一下从别处看到的js常用函数[参考](https://segmentfault.com/a/1190000010527982)...

<!-- more -->
## 字符串相关
### 字符串过滤
```js
//过滤字符串(html标签，表情，特殊字符，)
//字符串，替换内容（special-特殊字符,html-html标签,emjoy-emjoy表情,word-小写字母，WORD-大写字母，number-数字,chinese-中文），要替换成什么，默认'',保留哪些特殊字符
//如果需要过滤多种字符，type参数使用','分割
//如下栗子,意思就是过滤字符串的html标签，大写字母，中文，特殊字符，全部替换成*,但是保留特殊字符'%'，'?'，除了这两个，其他特殊字符全部清除
//var str='asd    654a大蠢sasdasdASDQWEXZC6d5#%^*^&*^%^&*$\\"\'#@!()*/-())_\'":"{}?<div></div><img src=""/>啊实打实大蠢猪自行车这些课程';
// ecDo.filterStr(str,'html,WORD,chinese,special','*','%?')
//"asd    654a**sasdasd*********6d5#%^*^&*^%^&*$\"'#@!()*/-())_'":"{}?*****************"
function filterStr(str, type, restr, spstr) {
  var typeArr = type.split(','),
    _str = str;
  for (var i = 0, len = typeArr.length; i < len; i++) {
    if (typeArr[i] === 'special') {
      var pattern, regText = '$()[]{}?\|^*+./\"\'+';
      if (spstr) {
        var _spstr = spstr.split(""),
          _regText = "[^0-9A-Za-z\\s";
        for (var i = 0, len = _spstr.length; i < len; i++) {
          if (regText.indexOf(_spstr[i]) === -1) {
            _regText += _spstr[i];
          } else {
            _regText += '\\' + _spstr[i];
          }
        }
        _regText += ']'
        pattern = new RegExp(_regText, 'g');
      } else {
        pattern = new RegExp("[^0-9A-Za-z\\s]", 'g')
      }
    }
    var _restr = restr || '';
    switch (typeArr[i]) {
      case 'special':
        _str = _str.replace(pattern, _restr);
        break;
      case 'html':
        _str = _str.replace(/<\/?[^>]*>/g, _restr);
        break;
      case 'emjoy':
        _str = _str.replace(/[^\u4e00-\u9fa5|\u0000-\u00ff|\u3002|\uFF1F|\uFF01|\uff0c|\u3001|\uff1b|\uff1a|\u3008-\u300f|\u2018|\u2019|\u201c|\u201d|\uff08|\uff09|\u2014|\u2026|\u2013|\uff0e]/g, _restr);
        break;
      case 'word':
        _str = _str.replace(/[a-z]/g, _restr);
        break;
      case 'WORD':
        _str = _str.replace(/[A-Z]/g, _restr);
        break;
      case 'number':
        _str = _str.replace(/[0-9]/g, _restr);
        break;
      case 'chinese':
        _str = _str.replace(/[\u4E00-\u9FA5]/g, _restr);
        break;
    }
  }
  return _str;
}
```

### 创建正则字符
```js
//创建正则字符,一般是为搜索或者高亮操作
//createKeyExp(['我','谁'])
//'(我|谁)'
function createKeyExp(strArr) {
  var str = "";
  for (var i = 0; i < strArr.length; i++) {
    if (i != strArr.length - 1) {
      str = str + strArr[i] + "|";
    } else {
      str = str + strArr[i];
    }
  }
  return "(" + str + ")";
}  
```
### 关键字加标签
```js
//简单关键字加标签（多个关键词用空格隔开）
//ecDo.findKey('守侯我oaks接到了来自下次你离开快乐吉祥留在开城侯','守侯 开','i')
//"<i>守侯</i>我oaks接到了来自下次你离<i>开</i>快乐吉祥留在<i>开</i>城侯"
//加完了标签，对i怎么设置样式就靠大家了！
function findKey(str, key, el) {
  var arr = null,
    regStr = null,
    content = null,
    Reg = null,
    _el = el || 'span';
  arr = key.split(/\s+/);
  //alert(regStr); //    如：(前端|过来)
  regStr = this.createKeyExp(arr);
  content = str;
  //alert(Reg);//        /如：(前端|过来)/g
  Reg = new RegExp(regStr, "g");
  content = content;
  //过滤html标签 替换标签，往关键字前后加上标签
  return content.replace(/<\/?[^>]*>/g, '').replace(Reg, "<" + _el + ">$1</" + _el + ">");
}
```

## 数组相关
### 对象数组排序
```js
//对象数组的排序
//var arr=[{a:1,b:2,c:9},{a:2,b:3,c:5},{a:5,b:9},{a:4,b:2,c:5},{a:4,b:5,c:7}]
//arraySort(arr2,'a,b')  a是第一排序条件，b是第二排序条件
//[{a:1,b:2,c:9},{a:2,b:3,c:5},{a:4,b:2,c:5},{a:4,b:5,c:7},{a:5,b:9}]
function arraySort(arr, sortText) {
  if (!sortText) {
    return arr
  }
  var _sortText = sortText.split(',').reverse(),
    _arr = arr.slice(0);
  for (var i = 0, len = _sortText.length; i < len; i++) {
    _arr.sort(function(n1, n2) {
      return n1[_sortText[i]] - n2[_sortText[i]]
    })
  }
  return _arr;
}
```

## DOM操作
### 预加载图片
```js
//图片没加载出来时用一张图片（loading图片）代替，一般和图片懒加载一起使用
function aftLoadImg(obj, url, cb) {
  var oImg = new Image(), _this = this;
  oImg.src = url;
  oImg.onload = function() {
    obj.src = oImg.src;
    if (cb && _this.istype(cb, 'function')) {
      cb(obj);
    }
  }
}
```
### 图片滚动懒加载
```js
//图片滚动懒加载
//@className {string} 要遍历图片的类名
//@num {number} 距离多少的时候开始加载 默认 0
//比如，一张图片距离文档顶部3000，num参数设置200，那么在页面滚动到2800的时候，图片加载。不传num参数就滚动，num默认是0，页面滚动到3000就加载

//html代码
//<p><img data-src="lawyerOtherImg.jpg" class="load-img" width='528' height='304' /></p>
//<p><img data-src="lawyerOtherImg.jpg" class="load-img" width='528' height='304' /></p>
//<p><img data-src="lawyerOtherImg.jpg" class="load-img" width='528' height='304' /></p>....
//data-src储存src的数据，到需要加载的时候把data-src的值赋值给src属性，图片就会加载。
//详细可以查看testLoadImg.html

//window.onload = function() {
//    ecDo.loadImg('load-img',100);
//    window.onscroll = function() {
//        ecDo.loadImg('load-img',100);
//        }
//}
function loadImg(className, num) {
  var _className = className || 'ec-load-img',
    _num = num || 0,
    _this = this;
  var oImgLoad = document.getElementsByClassName(_className);
  for (var i = 0, len = oImgLoad.length; i < len; i++) {
    if (document.documentElement.clientHeight + document.body.scrollTop > oImgLoad[i].offsetTop - _num && !oImgLoad[i].isLoad) {
      //记录图片是否已经加载
      oImgLoad[i].isLoad = true;
      //设置过渡，当图片下来的时候有一个图片透明度变化
      oImgLoad[i].style.cssText = "transition: ''; opacity: 0;"
      if (oImgLoad[i].dataset) {
        this.aftLoadImg(oImgLoad[i], oImgLoad[i].dataset.src, function(o) {
          setTimeout(function() {
            if (o.isLoad) {
              _this.removeClass(o, _className);
              o.style.cssText = "";
            }
          }, 1000)
        });
      } else {
        this.aftLoadImg(oImgLoad[i], oImgLoad[i].getAttribute("data-src"), function(o) {
          setTimeout(function() {
            if (o.isLoad) {
              _this.removeClass(o, _className);
              o.style.cssText = "";
            }
          }, 1000)
        });
      }
      (function(i) {
        setTimeout(function() {
          oImgLoad[i].style.cssText = "transition:all 1s; opacity: 1;";
        }, 16)
      })(i);
    }
  }
}
```

## 其它做操
### 封装AJAX
```js
/*
 * @param {string}obj.type http连接的方式，包括POST和GET两种方式
 * @param {string}obj.url 发送请求的url
 * @param {boolean}obj.async 是否为异步请求，true为异步的，false为同步的
 * @param {object}obj.data 发送的参数，格式为对象类型
 * @param {function}obj.success ajax发送并接收成功调用的回调函数
 * @param {function}obj.error ajax发送失败或者接收失败调用的回调函数
 */
//  ecDo.ajax({
//      type:'get',
//      url:'xxx',
//      data:{
//          id:'111'
//      },
//      success:function(res){
//          console.log(res)
//      }
//  })
function ajax(obj) {
  obj = obj || {};
  obj.type = obj.type.toUpperCase() || 'POST';
  obj.url = obj.url || '';
  obj.async = obj.async || true;
  obj.data = obj.data || null;
  obj.success = obj.success || function() {};
  obj.error = obj.error || function() {};
  var xmlHttp = null;
  if (XMLHttpRequest) {
    xmlHttp = new XMLHttpRequest();
  } else {
    xmlHttp = new ActiveXObject('Microsoft.XMLHTTP');
  }
  var params = [];
  for (var key in obj.data) {
    params.push(key + '=' + obj.data[key]);
  }
  var postData = params.join('&');
  if (obj.type.toUpperCase() === 'POST') {
    xmlHttp.open(obj.type, obj.url, obj.async);
    xmlHttp.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded;charset=utf-8');
    xmlHttp.send(postData);
  } else if (obj.type.toUpperCase() === 'GET') {
    xmlHttp.open(obj.type, obj.url + '?' + postData, obj.async);
    xmlHttp.send(null);
  }
  xmlHttp.onreadystatechange = function() {
    if (xmlHttp.readyState == 4 && xmlHttp.status == 200) {
      obj.success(xmlHttp.responseText);
    } else {
      obj.error(xmlHttp.responseText);
    }
  };
}
```
### 手机类型判断
```js
//手机类型判断
//browserInfo('android')
//false（在浏览器iphone6模拟器的调试）
function browserInfo(type) {
  switch (type) {
    case 'android':
      return navigator.userAgent.toLowerCase().indexOf('android') !== -1
    case 'iphone':
      return navigator.userAgent.toLowerCase().indexOf('iphone') !== -1
    case 'ipad':
      return navigator.userAgent.toLowerCase().indexOf('ipad') !== -1
    case 'weixin':
      return navigator.userAgent.toLowerCase().indexOf('MicroMessenger') !== -1
    default:
      return navigator.userAgent.toLowerCase()
  }
}
```
