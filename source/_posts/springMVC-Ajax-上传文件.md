title: springMVC ajax 上传文件
date: 2015-07-03 21:04:16
tags: tcp
categories: 计算机网络
description: springMVC中使用ajax上传单文件.
keywords: springMVC ajax fileupload 文件上传
---
* ###插件
    [AjaxfileUpload.js](https://github.com/hujiaxuan1995/Ajaxfileupload.js)
主要用来异步上传文件，基本原理还是拼接iframe，用iframe上传。
* ###前端JS代码

        function ImageUpload(){
            $.ajaxFileUpload({
                url : '/spring-taoxingqu/upload.do', //用于文件上传的服务器端请求地址
                secureuri : false, //一般设置为false
                fileElementId : 'file', //文件上传空间的id属性  <input type="file" id="file" name="file" />
                type : 'post',
                dataType : 'text', //返回值类型 一般设置为json
                success : function(data, status) //服务器成功响应处理函数
                {
                    alert("图片上传成功");
                },
                error : function(data, status, e)//服务器响应失败处理函数
                {
                    alert("图片上传失败");
                }
            });
        }
* ###后台控制器
            package com.springapp.mvc.controller;
            import com.springapp.mvc.service.UserServiceImpl;
            import org.springframework.stereotype.Controller;
            import org.springframework.ui.ModelMap;
            import org.springframework.web.bind.annotation.RequestMapping;
            import org.springframework.web.bind.annotation.RequestMethod;
            import org.springframework.web.bind.annotation.ResponseBody;
            import org.springframework.web.multipart.MultipartFile;
            import org.springframework.web.multipart.MultipartHttpServletRequest;
            import javax.servlet.http.HttpServletRequest;
            import javax.servlet.http.HttpServletResponse;
            import javax.servlet.http.HttpSession;
            import java.io.FileOutputStream;
            import java.io.IOException;
            import java.io.InputStream;
            import java.text.SimpleDateFormat;
            import java.util.Date;
            /**
             * Created by hujiaxuan on 2015/7/3.
             */
            @Controller
            public class ImageUpload{
                @RequestMapping(value = "upload.do", method = RequestMethod.POST)
                public @ResponseBody String upload(HttpServletRequest request,
                                   HttpServletResponse response, ModelMap model,HttpSession session) throws IOException {
                    MultipartHttpServletRequest multipartRequest = (MultipartHttpServletRequest) request;
                    MultipartFile mFile = multipartRequest.getFile("file");
                    String user_name = (String)session.getAttribute("user_name");
                    //String path = request.getSession().getServletContext().getRealPath("/");
                    String path = request.getSession().getServletContext().getRealPath("/resources/upload");
                    String filename = mFile.getOriginalFilename();
                    InputStream inputStream = mFile.getInputStream();
                    byte[] b = new byte[1048576];
                    int length = inputStream.read(b);
                    Date date = new Date();
                    SimpleDateFormat F = new SimpleDateFormat("yyyyMMddHHmmss");
                    String prefix=filename.substring(filename.lastIndexOf("."));
                    filename = "123" + F.format(date) + prefix;
                    String url =path +"/"+ filename;
                    System.out.println(url);
                    FileOutputStream outputStream = new FileOutputStream(url);
                    outputStream.write(b, 0, length);
                    inputStream.close();
                    outputStream.close();
                    String url2 = "/spring-taoxingqu/resources/upload/"+filename;
                    boolean res = new UserServiceImpl().UserFaceUpload(user_name,url2);
                    return "success";
                }
            }
