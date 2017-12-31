---
layout: post
title: Ajax原理和实现以及Json的使用
date: 2017-12-31
categories: blog
tags: [Ajax,JSON]
description: Asynchronous JavaScript and XML。
---

# 1. 什么是ajax？

   Asynchronous JavaScript and XML（异步的JS和XML）

# 2. 实现ajax的对象——XMLHttpRequest

## 一般使用流程
1. 运用HTML和CSS来实现页面
2. 运用XMLHttpRequest和web服务器进行数据的异步交换
3. 运用JS操作DOM，实现动态局部刷新

## 使用XMLHttpRequest的方法

可以写个函数来调用XMLHttpRequest：

    function getXhr(){
		var xhr=null;
		if(window.XMLHttpRequest){
			//非ie浏览器
			xhr=new XMLHttpRequest();
		}else{
			xhr=new ActiveXObject('Microsoft.XMLHttp');
		}
		return xhr;
    }

# 3. 如何实现异步交换
   实现异步交换，首先客户端要发送HTTP请求；
   
   服务器在收到请求后进行处理；

   服务器将处理结果返回给客户端。

## 1） HTTP请求
   HTTP请求由4部分组成：

1. 请求方法：GET、POST：GET一般用来获取信息，一般在2000字符；POST一般用来发送数据，数量无限制
2. 请求路径：url
3. 请求头
4. 请求体

客户端将要发送的信息放在open方法内：
send方法：

## 2） HTTP响应
   HTTP响应由3部分组成：

1. 数字和文字组成的状态码 HTTP/1.1 200 OK
2. 响应头
3. 响应体

responseText

## 3)客户端代码实现
    document.getElementById("search").onclick=function(){
	  //发送Ajax查询请求并处理
      var request=new XMLHttpRequest();
      //调用open方法拼成传递的数据
	  request.open("GET","service.php?number="+document.getElementById("keyword").value);
      //发送数据
	  request.send();
	  //监听readystate的变化（0→1→2→3→4）
      request.onreadystatechange=function(){
	  	  if(request.readyState==4){
              //200表示请求成功
			  if(request.status==200){
				  //将responseText转成JSON对象
				  //parse方法比eval方法好，在于eval可能会执行JSON中嵌入的恶意代码
				  var data =JSON.parse(request.responseText);
				  if(data.success){
					  document.getElementById("searchResult").innerHTML=data.msg;
				  }else{
					  document.getElementById("searchResult").innerHTML="出现错误:"+data.msg;
				  }
			  }else{
				  alert("发生错误："+request.status);
			  }
		  }
	  }
    }

## 4） 利用jQuery来实现ajax
    $("#search").click(function(){
		$.ajax({
			type:"GET",
			url:"http://localhost:8080/ajaxdemo/service.php?number="+$("#keyword").val(),
			dataType:"json",
			success: function(data){
				if(data.success){
					$("#searchResult").html(data.msg);
				}else{
					$("#searchResult").html("出现错误:"+data.msg);
				}
			},
			//jquery中的jqXHR就是responseText
			error: function(jqXHR){
				alert("发生错误："+jqXHR.status);
			}
		});
	});

## 5）跨域调用
### ① 只能处理GET请求

    $("#search").click(function(){
		$.ajax({
			type:"GET",
			**//将localhost改成127.0.0.1就不是同一个域了**
			url:"http://127.0.0.1:8080/ajaxdemo/service.php?number="+$("#keyword").val(),
			dataType:"jsonp",**//dataType设置成jsonp**
			jsonp:"callback",**//添加jsonp参数，**
			success: function(data){
				if(data.success){
					$("#searchResult").html(data.msg);
				}else{
					$("#searchResult").html("出现错误:"+data.msg);
				}
			},
			error: function(jqXHR){
				alert("发生错误："+jqXHR.status);
			}
		});
	});

### ②服务端做相关修改，如php服务端

    function search(){
	    $jsonp=$_GET["callback"];
        //其他处理逻辑
    }

将返回值进行修改
    
	原来是
    $result = '{"success": false,"msg": "没有找到员工"}';
    拼上括号，改造成
    $result = $jsonp.'({"success": false,"msg": "没有找到员工"})';

### ③处理跨域调用的其他方法

由HTML5提供的XHR2，IE10以下不支持。无需改动客户端，只需要对服务的进行修改。
在php服务端加上如下代码：

    header("Access-Control-Allow-Origin:*"); 
    header("Access-Control-Allow-Method:POST,GET"); 