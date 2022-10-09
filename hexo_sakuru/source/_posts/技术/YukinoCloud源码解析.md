---
title: 网盘源码解析
author: Yukino
avatar: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2021-1-31 16:00:00
comments: true #是否开启评论
tags: #文章标签
  - cloud
keywords: cloud #这个暂时没找到用户
description: 网盘源码解析 #首页文章简介
photos: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/banner/1.jpg #文章详情页的banner
---
# 0. 数据库

```sql
CREATE TABLE `file_folder` (
  `file_folder_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '文件夹ID',
  `file_folder_name` varchar(255) DEFAULT NULL COMMENT '文件夹名称',
  `parent_folder_id` int(11) DEFAULT '0' COMMENT '父文件夹ID',
  `file_store_id` int(11) DEFAULT NULL COMMENT '所属文件仓库ID',
  `time` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`file_folder_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8;
```

```Java
@AllArgsConstructor
@Data
@Builder
public class FileFolder implements Serializable {

    /**
    * 文件夹ID
    */
    private Integer fileFolderId;
    /**
    * 文件夹名称
    */
    private String fileFolderName;
    /**
    * 父文件夹ID
    */
    private Integer parentFolderId;
    /**
    * 所属文件仓库ID
    */
    private Integer fileStoreId;
    /**
    * 创建时间
    */
    private Date time;

}
```



```sql
CREATE TABLE `file_store` (
  `file_store_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '文件仓库ID',
  `user_id` int(11) DEFAULT NULL COMMENT '主人ID',
  `current_size` int(11) DEFAULT '0' COMMENT '当前容量（单位KB）',
  `max_size` int(11) DEFAULT '1048576' COMMENT '最大容量（单位KB）',
  `permission` int(11) DEFAULT '0' COMMENT '仓库权限，0可上传下载、1不允许上传可以下载、2不可以上传下载',
  PRIMARY KEY (`file_store_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8;
```

```Java
@AllArgsConstructor
@Data
@Builder
public class FileStore implements Serializable {

    /**
    * 文件仓库ID
    */
    private Integer fileStoreId;
    /**
    * 主人ID
    */
    private Integer userId;
    /**
    * 当前容量（单位KB）
    */
    private Integer currentSize;
    /**
    * 最大容量（单位KB）
    */
    private Integer maxSize;
    /**
     * 仓库权限：0可上传下载、1只允许下载、2禁止上传下载
     */
    private Integer permission;

}
```



```sql
CREATE TABLE `my_file` (
  `my_file_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '文件ID',
  `my_file_name` varchar(255) DEFAULT NULL COMMENT '文件名',
  `file_store_id` int(11) DEFAULT NULL COMMENT '文件仓库ID',
  `my_file_path` varchar(255) DEFAULT '/' COMMENT '文件存储路径',
  `download_time` int(11) DEFAULT '0' COMMENT '下载次数',
  `upload_time` datetime DEFAULT NULL COMMENT '上传时间',
  `parent_folder_id` int(11) DEFAULT NULL COMMENT '父文件夹ID',
  `size` int(11) DEFAULT NULL COMMENT '文件大小',
  `type` int(11) DEFAULT NULL COMMENT '文件类型',
  `postfix` varchar(255) DEFAULT NULL COMMENT '文件后缀',
  PRIMARY KEY (`my_file_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8;
```

```Java
@AllArgsConstructor
@Data
@Builder
public class MyFile implements Serializable {
    /**
    * 文件ID
    */
    private Integer myFileId;
    /**
    * 文件名
    */
    private String myFileName;
    /**
    * 文件仓库ID
    */
    private Integer fileStoreId;
    /**
    * 文件存储路径
    */
    private String myFilePath;
    /**
    * 下载次数
    */
    private Integer downloadTime;
    /**
    * 上传时间
    */
    private Date uploadTime;
    /**
    * 父文件夹ID
    */
    private Integer parentFolderId;
    /**
    * 文件大小
    */
    private Integer size;
    /**
    * 文件类型
    */
    private Integer type;
    /**
    * 文件后缀
    */
    private String postfix;

}
```

```sql
CREATE TABLE `temp_file` (
  `file_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '临时文件ID',
  `file_name` varchar(255) DEFAULT NULL COMMENT '文件名',
  `size` varchar(255) DEFAULT NULL COMMENT '文件大小',
  `upload_time` datetime DEFAULT NULL COMMENT '上传时间：4小时后删除',
  `file_path` varchar(255) DEFAULT NULL COMMENT '文件在FTP上的存放路径',
  PRIMARY KEY (`file_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8;
```

```java
@AllArgsConstructor
@Data
@Builder
public class TempFile implements Serializable {
    private static final long serialVersionUID = -90736141035866360L;
    /**
    * 临时文件ID
    */
    private Integer fileId;
    /**
    * 文件名
    */
    private String fileName;
    /**
    * 文件大小
    */
    private String size;
    /**
    * 上传时间：4小时后删除
    */
    private Date uploadTime;
    /**
    * 文件在FTP上的存放路径
    */
    private String filePath;

}
```

```sql
CREATE TABLE `user` (
  `user_id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '用户ID',
  `open_id` varchar(255) DEFAULT NULL COMMENT '用户的openid',
  `file_store_id` int(11) DEFAULT NULL COMMENT '文件仓库ID',
  `user_name` varchar(50) DEFAULT NULL COMMENT '用户名',
  `email` varchar(50) DEFAULT '	0000@qq.com' COMMENT '用户邮箱',
  `password` varchar(20) DEFAULT NULL COMMENT '密码',
  `register_time` datetime DEFAULT NULL COMMENT '注册时间',
  `image_path` varchar(255) DEFAULT '' COMMENT '头像地址',
  `role` int(11) DEFAULT '1' COMMENT '用户角色,0管理员，1普通用户',
  PRIMARY KEY (`user_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8;
```

```Java
@AllArgsConstructor
@Data
@Builder
public class User implements Serializable {
    /**
    * 用户ID
    */
    private Integer userId;
    /**
    * 用户的openid
    */
    private String openId;
    /**
    * 文件仓库ID
    */
    private Integer fileStoreId;
    /**
    * 用户名
    */
    private String userName;
    /**
     * 用户邮箱
     */
    private String email;
    /**
     * 用户密码
     */
    private String password;
    /**
    * 注册时间
    */
    private Date registerTime;
    /**
    * 头像地址
    */
    private String imagePath;
    /**
     * 用户角色，0管理员，1普通用户
     */
    private Integer role;

}
```

# 1.注册与登录

## 1.1 邮箱注册

**获取验证码**

```java
    @ResponseBody
    @RequestMapping("/sendCode")
    public String sendCode(String userName, String email, String password) {
        User userByEmail = userService.getUserByEmail(email);
        if (userByEmail != null) {
            logger.error("发送验证码失败！邮箱已被注册！");
            return "exitEmail";
        }
        logger.info("开始发送邮件.../n" + "获取的到邮件发送对象为:" + mailSender);
        mailUtils = new MailUtils(mailSender);
        String code = mailUtils.sendCode(email, userName, password);
        session.setAttribute(email + "_code", code);
        return "success";
    }
```

```java
    public String sendCode(String email,String userName,String password){
        int code = (int) ((Math.random() * 9 + 1) * 100000);
        logger.info("开始发送复杂邮件...");
        logger.info("mailSender对象为:"+mailSender);
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage);
        try {
            helper.setSubject("莫提网盘-邮箱验证");
            helper.setText("<h2 >莫提网盘-简洁、优雅、免费</h2>" +
                    "<h3>用户注册-邮箱验证<h3/>" +
                    "您现在正在注册莫提网盘账号<br>" +
                    "验证码: <span style='color : red'>"+code+"</span><br>" +
                    "用户名 :"+userName+
                    "<br>密码 :"+password+
                    "<hr>"+
                    "<h5 style='color : red'>如果并非本人操作,请忽略本邮件</h5>",true);
            //helper.setFrom("你需要修改此处为你的QQ邮箱");
            helper.setFrom("707772743@qq.com");
            helper.setTo(email);
        } catch (MessagingException e) {
            e.printStackTrace();
        }
        logger.info("mimeMessage对象为:"+mimeMessage);
        mailSender.send(mimeMessage);
        return String.valueOf(code);
    }
```

后端生成验证码，生成的验证码，通过session属性返回给页面，通过邮件服务发送给注册邮箱，前端进行验证码比对.

**注册**

```java
    @PostMapping("/register")
    public String register(User user, String code, Map<String, Object> map) {
        String uCode = (String) session.getAttribute(user.getEmail() + "_code");
        if (!code.equals(uCode)) {
            map.put("errorMsg", "验证码错误");
            return "index";
        }
        // 用户名去空格
        user.setUserName(user.getUserName().trim());
        user.setImagePath("https://p.qpic.cn/qqconnect/0/app_101851241_1582451550/100?max-age=2592000&t=0");
        user.setRegisterTime(new Date());
        user.setRole(1);
        if (userService.insert(user)) {
            FileStore store = FileStore.builder().userId(user.getUserId()).currentSize(0).build();
            fileStoreService.addFileStore(store);
            user.setFileStoreId(store.getFileStoreId());
            userService.update(user);
            logger.info("注册用户成功！当前注册用户" + user);
            logger.info("注册仓库成功！当前注册仓库" + store);
        } else {
            map.put("errorMsg", "服务器发生错误，注册失败");
            return "index";
        }
        session.removeAttribute(user.getEmail() + "_code");
        session.setAttribute("loginUser", user);
        return "redirect:/index";
    }
```

- 创建用户用户信息
- 创建该用户对应的仓库信息，并将用户信息与仓库信息关联起来。

## 1.2 登录

```java
    @PostMapping("/login")
    public String login(User user, Map<String, Object> map) {
        User userByEmail = userService.getUserByEmail(user.getEmail());
        if (userByEmail != null && userByEmail.getPassword().equals(user.getPassword())) {
            session.setAttribute("loginUser", userByEmail);
            logger.info("登录成功！"+userByEmail);
            return "redirect:/index";
        }else{
            User user1 = userService.getUserByEmail(user.getEmail());
            String errorMsg = user1 == null ? "该邮箱尚未注册" : "密码错误";
            logger.info("登录失败！请确认邮箱和密码是否正确！");
            //登录失败，将失败信息返回前端渲染
            map.put("errorMsg", errorMsg);
            return "index";
        }
```

# 2. 文件操作

## 2.1 文件上传

### 2.1.1 easyUploader.js 工具

**GitHub 地址**

 `https://github.com/funnyque/easyUpload.js`

**用法**

参照GitHub 仓库README.md

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>示例</title>
    <link rel="stylesheet" href="css/upload-file.css">
    <style>
        html * {
            margin: 0;
            padding: 0;
        }
    </style>
</head>
<body>
<div id="uploader"></div>

<script src="js/easyUploader.jq.js"></script>
<script>
    var uploader = easyUploader({
        id: "uploader",
        accept: '',//任何格式文件都支持
        action: 'uploadFile',
        dataFormat: 'formData',
        headers:{
            id:0 //这里是写的request的headers参数，后端可以获取这个数据
        },
        maxCount: 10,
        maxSize: 1024,
        multiple: true,
        data: null,
        beforeUpload: function(file, data, args) {
            /* dataFormat为formData时配置发送数据的方式 */
            data.append('token', '387126b0-7b3e-4a2a-86ad-ae5c5edd0ae6TT');
            data.append('otherKey', 'otherValue');

            /* dataFormat为base64时配置发送数据的方式 */
            // data.base = file.base;
            // data.token = '387126b0-7b3e-4a2a-86ad-ae5c5edd0ae6TT';
            // data.otherKey = 'otherValue';
        },
        onChange: function(fileList) {
            /* input选中时触发 */
            document.body.onbeforeunload = function() {
                window.event.returnValue = "确认离开？";
            }
        },
        onRemove: function(removedFiles, files) {
            console.log('onRemove', removedFiles);
        },
        onSuccess: function(res) {
            var code = res["code"];
            console.log(code);
            if (code == 499){
                alert("可能因为你的违规操作，您暂时无法上传文件！");
            }
            if (code == 501){
                alert("当前目录存在同名文件，请删除后再试！");
            }
            if (code == 502){
                alert("文件名不符合规范，支持【汉字,字符,数字,下划线,英文句号,横线】");
            }
            if (code == 503){
                alert("仓库容量不足，上传失败！");
            }
            if (code == 504){
                alert("服务器出错了！");
            }
            document.body.onbeforeunload = function () {
                window.event.returnValue = "确认离开？";
            }
        },
        onError: function(err) {
            console.log('onError', err);
        },
    });
</script>
</body>
</html>
```

### 2.1.2 后端接收数据

```Java
    @PostMapping("/uploadFile")
    @ResponseBody
    public Map<String, Object> uploadFile(@RequestParam("file") MultipartFile files) {
    //1. 判断当前用户的文件仓库权限是否满足
        Map<String, Object> map = new HashMap<>();
        if (fileStoreService.getFileStoreByUserId(loginUser.getUserId()).getPermission() != 0){
            logger.error("用户没有上传文件的权限!上传失败...");
            map.put("code", 499);
            return map;
        }

    //2. 判断当前上传文件夹是否有同名文件
        FileStore store = fileStoreService.getFileStoreByUserId(loginUser.getUserId());
        //前端传过来的folderId
        Integer folderId = Integer.valueOf(request.getHeader("id"));
        //file.getOriginalFilename()是上传时的文件名
        String name = files.getOriginalFilename().replaceAll(" ","");
        //获取当前目录下的所有文件，用来判断是否已经存在
        List<MyFile> myFiles = null;
        if (folderId == 0){
            //当前目录为根目录
            myFiles = myFileService.getRootFilesByFileStoreId(loginUser.getFileStoreId());
        }else {
            //当前目录为其他目录
            myFiles = myFileService.getFilesByParentFolderId(folderId);
        }
        for (int i = 0; i < myFiles.size(); i++) {
            if ((myFiles.get(i).getMyFileName()+myFiles.get(i).getPostfix()).equals(name)){
                logger.error("当前文件已存在!上传失败...");
                map.put("code", 501);
                return map;
            }
        }
    //3. 检查文件名并定义存储路径
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
        String dateStr = format.format(new Date());
        //  userId/date/folderId
        String path = loginUser.getUserId()+"/"+dateStr +"/"+folderId;
        if (!checkTarget(name)){
            logger.error("上传失败!文件名不符合规范...");
            map.put("code", 502);
            return map;
        }
    //4. 检查仓库size
        //toIntExact()方法从指定的long参数返回int值
        Integer sizeInt = Math.toIntExact(files.getSize() / 1024);
        //是否仓库放不下该文件
        if(store.getCurrentSize()+sizeInt > store.getMaxSize()){
            logger.error("上传失败!仓库已满。");
            map.put("code", 503);
            return map;
        }
    //5. 文件信息整理(名字，类型，大小)
        //处理文件大小
        String size = String.valueOf(files.getSize()/1024.0);
        int indexDot = size.lastIndexOf(".");
        size = size.substring(0,indexDot);
        int index = name.lastIndexOf(".");
        String tempName = name;
        String postfix = "";
        int type = 4;
        if (index!=-1){
            tempName = name.substring(index);
            name = name.substring(0,index);
            //获得文件类型
            type = getType(tempName.toLowerCase());
            postfix = tempName.toLowerCase();
        }
    //6. 提交到文件FTP服务器
        try {
            //提交到FTP服务器
            boolean b = FtpUtil.uploadFile("/"+path, name + postfix, files.getInputStream());
            if (b){
                //上传成功
                logger.info("文件上传成功!"+files.getOriginalFilename());
                //向数据库文件表写入数据
                myFileService.addFileByFileStoreId(
                        MyFile.builder()
                              .myFileName(name).fileStoreId(loginUser.getFileStoreId()).myFilePath(path)
                              .downloadTime(0).uploadTime(new Date()).parentFolderId(folderId).
                              size(Integer.valueOf(size)).type(type).postfix(postfix).build());
                //更新仓库表的当前大小
                fileStoreService.addSize(store.getFileStoreId(),Integer.valueOf(size));
                try {
                    //不懂为何要睡5s
                    Thread.sleep(5000);
                    map.put("code", 200);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }else{
                logger.error("文件上传失败!"+files.getOriginalFilename());
                map.put("code", 504);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return map;
    }
```

## 2.2 文件下载

通过前端传过来的文件id去查询文件信息，再通过ftp下载

```java
    @GetMapping("/downloadFile")
    public String downloadFile(@RequestParam Integer fId){
        if (fileStoreService.getFileStoreByUserId(loginUser.getUserId()).getPermission() == 2){
            logger.error("用户没有下载文件的权限!下载失败...");
            return "redirect:/error401Page";
        }
        //获取文件信息
        MyFile myFile = myFileService.getFileByFileId(fId);
        String remotePath = myFile.getMyFilePath();
        String fileName = myFile.getMyFileName()+myFile.getPostfix();
        try {
            //去FTP上拉取
            OutputStream os = new BufferedOutputStream(response.getOutputStream());
            response.setCharacterEncoding("utf-8");
            // 设置返回类型
            response.setContentType("multipart/form-data");
            // 文件名转码一下，不然会出现中文乱码//Content-Disposition客户端的弹出对话框，对应的文件名 (中文字符需要转一下编码)
            response.setHeader("Content-Disposition", "attachment;fileName=" + URLEncoder.encode(fileName, "UTF-8"));
            boolean flag = FtpUtil.downloadFile("/" + remotePath, fileName, os);
            if (flag) {
                myFileService.updateFile(
                        MyFile.builder().myFileId(myFile.getMyFileId()).downloadTime(myFile.getDownloadTime() + 1).build());
                os.flush();
                os.close();
                logger.info("文件下载成功!" + myFile);
            }
        } catch (Exception e) {
            logger.info("yukino文件下载失败!" + myFile);
            e.printStackTrace();
        }
        return "success";
    }
```

```java
    public static boolean downloadFile(String remotePath, String fileName, OutputStream outputStream) {
        boolean result = false;
        //add by yukino
        remotePath = BASEPATH + remotePath;
        try {
            remotePath = new String(remotePath.getBytes("GBK"),"iso-8859-1");
            fileName = new String(fileName.getBytes("GBK"),"iso-8859-1");
            //初始化FTP客户端
            if (!initFtpClient()){
                return result;
            };
            // 转移到FTP服务器目录
            //ftp.changeWorkingDirectory(remotePath);
            ftp.changeWorkingDirectory(remotePath);
            ftp.enterLocalPassiveMode();
            FTPFile[] fs = ftp.listFiles();
            for (FTPFile ff : fs) {
                if (ff.getName().equals(fileName)) {
                    ftp.enterLocalPassiveMode();
                    ftp.retrieveFile(remotePath+"/"+fileName,outputStream);
                    result = true;
                }
            }
            ftp.logout();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (ftp.isConnected()) {
                try {
                    ftp.disconnect();
                } catch (IOException ioe) {
                }
            }
        }
        return result;
    }
```

## 2.3 删除文件

```java
    @GetMapping("/deleteFile")
    public String deleteFile(@RequestParam Integer fId,Integer folder){
        //获得文件信息
        MyFile myFile = myFileService.getFileByFileId(fId);
        String remotePath = myFile.getMyFilePath();
        String fileName = myFile.getMyFileName()+myFile.getPostfix();
        //从FTP文件服务器上删除文件
        boolean b = FtpUtil.deleteFile("/"+remotePath, fileName);
        if (b){
            //删除成功,返回空间
            fileStoreService.subSize(myFile.getFileStoreId(),Integer.valueOf(myFile.getSize()));
            //删除文件表对应的数据
            myFileService.deleteByFileId(fId);
        }
        logger.info("删除文件成功!"+myFile);
        return "redirect:/files?fId="+folder;
    }
```

```java
    public static boolean deleteFile( String remotePath,String fileName){
        boolean flag = false;
        remotePath = BASEPATH + remotePath;
        try {
            remotePath = new String(remotePath.getBytes("GBK"),"iso-8859-1");
            fileName = new String(fileName.getBytes("GBK"),"iso-8859-1");
            if (!initFtpClient()){
                return flag;
            };
            // 转移到FTP服务器目录
            ftp.changeWorkingDirectory(remotePath);
            ftp.enterLocalPassiveMode();
            FTPFile[] fs = ftp.listFiles();
            for (FTPFile ff : fs) {
                if ("".equals(fileName)){
                    return flag;
                }
                if (ff.getName().equals(fileName)){
                    String filePath = remotePath + "/" +fileName;
                    ftp.deleteFile(filePath);
                    flag = true;
                }
            }
            ftp.logout();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (ftp.isConnected()) {
                try {
                    ftp.disconnect();
                } catch (IOException ioe) {
                }
            }
        }
        return flag;
    }
```

## 2.4 重命名文件

```java
    @PostMapping("/updateFileName")
    public String updateFileName(MyFile file,Map<String, Object> map) {
        MyFile myFile = myFileService.getFileByFileId(file.getMyFileId());
        if (myFile != null){
            String oldName = myFile.getMyFileName();
            String newName = file.getMyFileName();
            if (!oldName.equals(newName)){
                boolean b = FtpUtil.reNameFile(myFile.getMyFilePath() + "/" + oldName+myFile.getPostfix(), myFile.getMyFilePath() + "/" + newName+myFile.getPostfix());
                if (b){
                    Integer integer = myFileService.updateFile(
                            MyFile.builder().myFileId(myFile.getMyFileId()).myFileName(newName).build());
                    if (integer == 1){
                        logger.info("修改文件名成功!原文件名:"+oldName+"  新文件名:"+newName);
                    }else{
                        logger.error("修改文件名失败!原文件名:"+oldName+"  新文件名:"+newName);
                    }
                }
            }
        }
        return "redirect:/files?fId="+myFile.getParentFolderId();
    }
```

```Java
    public static boolean reNameFile( String oldAllName,String newAllName){
        boolean flag = false;
        try {
            oldAllName = new String(oldAllName.getBytes("GBK"),"iso-8859-1");
            newAllName = new String(newAllName.getBytes("GBK"),"iso-8859-1");
            if (!initFtpClient()){
                return flag;
            };
            ftp.enterLocalPassiveMode();
            ftp.rename(oldAllName,newAllName);
            flag = true;
            ftp.logout();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (ftp.isConnected()) {
                try {
                    ftp.disconnect();
                } catch (IOException ioe) {
                }
            }
        }
        return flag;
    }
}
```

## 2.5 生成二维码

```Java
    @GetMapping("getQrCode")
    @ResponseBody
    public Map<String,Object> getQrCode(@RequestParam Integer id,@RequestParam String url){
        Map<String,Object> map = new HashMap<>();
        map.put("imgPath","https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=2654852821,3851565636&fm=26&gp=0.jpg");
        if (id != null){
            MyFile file = myFileService.getFileByFileId(id);
            if (file != null){
                try {
                    //getRealPath是部署到Tomcat服务器上的项目文件夹下的路径，不是源代码的路径,是虚拟路径，但是依然可以访问
                    //private/var/folders/6p/73llqwqx1_xg39j8pm5fjd9m0000gn/T/tomcat-docbase.145091729584971266.8080/user_img/
                    String path = request.getSession().getServletContext().getRealPath("/user_img/");
                    url = url+"/file/share?t="+ UUID.randomUUID().toString().substring(0,10) +"&f="+file.getMyFileId()+"&p="+file.getUploadTime().getTime()+""+file.getSize()+"&flag=1";
                    File targetFile = new File(path, "");
                    if (!targetFile.exists()) {
                        targetFile.mkdirs();
                    }
                    File f = new File(path, id + ".jpg");
                    if (!f.exists()){
                        //文件不存在,开始生成二维码并保存文件
                        OutputStream os = new FileOutputStream(f);
                        QRCodeUtil.encode(url, "/static/img/logo.png", os, true);
                        os.close();
                    }
                    map.put("imgPath","user_img/"+id+".jpg");
                    map.put("url",url);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
        return map;
    }
```

## 2.6 分享文件

```java
    @GetMapping("/file/share")
    public String shareFile(Integer f,String p,String t,Integer flag){
        String fileNameTemp = "";
        String remotePath = "";
        String fileName = "";
        Integer times = 0;
        if (flag == null || f == null || p == null || t == null){
            logger.info("下载分享文件失败，参数错误");
            return "redirect:/error400Page";
        }
        if(flag == 1){
            //获取文件信息
            MyFile myFile = myFileService.getFileByFileId(f);
            if (myFile == null){
                return "redirect:/error404Page";
            }
            String pwd = myFile.getUploadTime().getTime()+""+myFile.getSize();
            if (!pwd.equals(p)){
                return "redirect:/error400Page";
            }
            remotePath = myFile.getMyFilePath();
            fileName = myFile.getMyFileName()+myFile.getPostfix();
        }else if(flag == 2){
            TempFile tempFile = tempFileService.queryById(f);
            if (tempFile == null){
                return "redirect:/error404Page";
            }
            Long test = tempFile.getUploadTime().getTime();

            String pwd = tempFile.getSize();
            if (!pwd.equals(p)){
                return "redirect:/error400Page";
            }
            remotePath = tempFile.getFilePath();
            fileName = tempFile.getFileName();
        }else {
            return "redirect:/error400Page";
        }
        fileNameTemp = fileName;
        try {
            //解决下载文件时 中文文件名乱码问题
            boolean isMSIE = isMSBrowser(request);
            if (isMSIE) {
                //IE浏览器的乱码问题解决
                fileNameTemp = URLEncoder.encode(fileNameTemp, "UTF-8");
            } else {
                //万能乱码问题解决
                fileNameTemp = new String(fileNameTemp.getBytes("UTF-8"), "ISO-8859-1");
            }
            //去FTP上拉取
            OutputStream os = new BufferedOutputStream(response.getOutputStream());
            response.setCharacterEncoding("utf-8");
            // 设置返回类型
            response.setContentType("multipart/form-data");
            // 文件名转码一下，不然会出现中文乱码
            response.setHeader("Content-Disposition", "attachment;fileName=" + fileNameTemp);
            if (FtpUtil.downloadFile("/" + remotePath, fileName, os)) {
                myFileService.updateFile(
                        MyFile.builder().myFileId(f).downloadTime(times + 1).build());
                os.flush();
                os.close();
                logger.info("文件下载成功!");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return "success";
    }
```



# 3. 文件夹操作

## 3.1 文件夹重命名

```java
    @PostMapping("/updateFolder")
    public String updateFolder(FileFolder folder,Map<String, Object> map) {
        //获得文件夹的数据库信息
        FileFolder fileFolder = fileFolderService.getFileFolderByFileFolderId(folder.getFileFolderId());
        fileFolder.setFileFolderName(folder.getFileFolderName());
        //获得当前目录下的所有文件夹,用于检查文件夹是否已经存在
        List<FileFolder> fileFolders = fileFolderService.getFileFolderByParentFolderId(fileFolder.getParentFolderId());
        for (int i = 0; i < fileFolders.size(); i++) {
            FileFolder folder1 = fileFolders.get(i);
            if (folder1.getFileFolderName().equals(folder.getFileFolderName()) && folder1.getFileFolderId() != folder.getFileFolderId()){
                logger.info("重命名文件夹失败!文件夹已存在...");
                return "redirect:/files?error=2&fId="+fileFolder.getParentFolderId();
            }
        }
        //向数据库写入数据
        Integer integer = fileFolderService.updateFileFolderById(fileFolder);
        logger.info("重命名文件夹成功!"+folder);
        return "redirect:/files?fId="+fileFolder.getParentFolderId();
    }
```



## 3.2 清空并删除文件夹

```java
    public void deleteFolderF(FileFolder folder){
        //获得当前文件夹下的所有子文件夹
        List<FileFolder> folders = fileFolderService.getFileFolderByParentFolderId(folder.getFileFolderId());
        //删除当前文件夹的所有的文件
        List<MyFile> files = myFileService.getFilesByParentFolderId(folder.getFileFolderId());
        if (files.size()!=0){
            for (int i = 0; i < files.size(); i++) {
                Integer fileId = files.get(i).getMyFileId();
                boolean b = FtpUtil.deleteFile("/"+files.get(i).getMyFilePath(), files.get(i).getMyFileName() + files.get(i).getPostfix());
                if (b){
                    myFileService.deleteByFileId(fileId);
                    fileStoreService.subSize(folder.getFileStoreId(),Integer.valueOf(files.get(i).getSize()));
                }
            }
        }
        if (folders.size()!=0){
            for (int i = 0; i < folders.size(); i++) {
                deleteFolderF(folders.get(i));
            }
        }
        fileFolderService.deleteFileFolderById(folder.getFileFolderId());
    }
```



## 3.3 添加文件夹

```java
    @PostMapping("/addFolder")
    public String addFolder(FileFolder folder,Map<String, Object> map) {
        //设置文件夹信息
        folder.setFileStoreId(loginUser.getFileStoreId());
        folder.setTime(new Date());
        //获得当前目录下的所有文件夹,检查当前文件夹是否已经存在
        List<FileFolder> fileFolders = null;
        if (folder.getParentFolderId() == 0){
            //向用户根目录添加文件夹
            fileFolders = fileFolderService.getRootFoldersByFileStoreId(loginUser.getFileStoreId());
        }else{
            //向用户的其他目录添加文件夹
            fileFolders = fileFolderService.getFileFolderByParentFolderId(folder.getParentFolderId());
        }
        for (int i = 0; i < fileFolders.size(); i++) {
            FileFolder fileFolder = fileFolders.get(i);
            if (fileFolder.getFileFolderName().equals(folder.getFileFolderName())){
                logger.info("添加文件夹失败!文件夹已存在...");
                return "redirect:/files?error=1&fId="+folder.getParentFolderId();
            }
        }
        //向数据库写入数据
        Integer integer = fileFolderService.addFileFolder(folder);
        logger.info("添加文件夹成功!"+folder);
        return "redirect:/files?fId="+folder.getParentFolderId();
    }
    
```



# 4. 管理员操作

## 4.1 前往用户管理界面

```java
    @GetMapping("/manages-users")
    public String manageUsers(Map<String,Object> map,Integer cur){
        if (loginUser.getRole() == 1){
            //用于无访问权限
            logger.error("当前登录用户："+loginUser.getUserName()+"无管理员权限！");
            return "redirect:/error401Page";
        }
        //获取全部的用户
        Integer usersCount = userService.getUsersCount();
        //获取当前查询的页数，如果为空，默认为0
        cur = (cur == null || cur<0)?0:cur;
        //获得统计信息
        FileStoreStatistics statistics = myFileService.getCountStatistics(loginUser.getFileStoreId());
        //分页获得20个用户信息
        Page<Object> page = PageHelper.startPage(cur, 20);
        List<UserToShow> users = userService.getUsers();
        map.put("statistics", statistics);
        map.put("users", users);
        map.put("page", page);
        map.put("usersCount", usersCount);
        logger.info("用户管理域的内容："+map);
        return "admin/manage-users";
    }
```

## 4.2 修改用户的权限和最大容量

```java
    @GetMapping("/updateStoreInfo")
    @ResponseBody
    public String updateStoreInfo(Integer uId,Integer pre,Integer size){
        Integer integer = fileStoreService.updatePermission(uId, pre, size*1024);
        if (integer == 1) {
            //更新成功，返回200状态码
            logger.info("修改用户"+userService.queryById(uId).getUserName()+"：的权限和仓库大小成功！");
            return "200";
        }else {
            //更新失败，返回500状态码
            logger.error("修改用户"+userService.queryById(uId).getUserName()+"：的权限和仓库大小失败！");
            return "500";
        }
    }
```

## 4.3 删除用户

```java
    @GetMapping("/deleteUser")
    public String deleteUser(Integer uId,Integer cur){
        cur = (cur == null || cur < 0)?1:cur;
        User user = userService.queryById(uId);
        FileStore fileStore = fileStoreService.getFileStoreByUserId(uId);
        List<FileFolder> folders = fileFolderService.getRootFoldersByFileStoreId(fileStore.getFileStoreId());
        //迭代删除文件夹
        for (FileFolder f:folders) {
            deleteFolderF(f);
        }
        List<MyFile> files = myFileService.getRootFilesByFileStoreId(fileStore.getFileStoreId());
        //删除该用户仓库根目录下的所有文件
        for (MyFile f:files) {
            String remotePath = f.getMyFilePath();
            String fileName = f.getMyFileName()+f.getPostfix();
            //从FTP文件服务器上删除文件
            boolean b = FtpUtil.deleteFile("/"+remotePath, fileName);
            if (b){
                //删除成功,返回空间
                fileStoreService.subSize(f.getFileStoreId(),Integer.valueOf(f.getSize()));
                //删除文件表对应的数据
                myFileService.deleteByFileId(f.getMyFileId());
            }
            logger.info("删除文件成功!"+f);
        }
        if (FtpUtil.deleteFolder("/" + uId)){
            logger.info("清空FTP上该用户的文件成功");
        }else {
            logger.error("清空FTP上该用户的文件失败");
        }
        userService.deleteById(uId);
        fileStoreService.deleteById(fileStore.getFileStoreId());
        return "redirect:/manages-users?cur="+cur;
    }
```

```java
    public void deleteFolderF(FileFolder folder){
        //获得当前文件夹下的所有子文件夹
        List<FileFolder> folders = fileFolderService.getFileFolderByParentFolderId(folder.getFileFolderId());
        //删除当前文件夹的所有的文件
        List<MyFile> files = myFileService.getFilesByParentFolderId(folder.getFileFolderId());
        if (files.size()!=0){
            for (int i = 0; i < files.size(); i++) {
                Integer fileId = files.get(i).getMyFileId();
                boolean b = FtpUtil.deleteFile("/"+files.get(i).getMyFilePath(), files.get(i).getMyFileName() + files.get(i).getPostfix());
                if (b){
                    myFileService.deleteByFileId(fileId);
                    fileStoreService.subSize(folder.getFileStoreId(),Integer.valueOf(files.get(i).getSize()));
                }
            }
        }
        if (folders.size()!=0){
            for (int i = 0; i < folders.size(); i++) {
                deleteFolderF(folders.get(i));
            }
        }
        fileFolderService.deleteFileFolderById(folder.getFileFolderId());
    }
}
```



# 6. 其他通用配置

## 6.1 注册视图控制器

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/temp-file").setViewName("temp-file");
        registry.addViewController("/error400Page").setViewName("error/400");
        registry.addViewController("/error401Page").setViewName("error/401");
        registry.addViewController("/error404Page").setViewName("error/404");
        registry.addViewController("/error500Page").setViewName("error/500");
    }
}
```



## 6.2 注册登录拦截器

```java
public class LoginHandlerInterceptor implements HandlerInterceptor {

    /**
     * 在目标方式执行之前执行
     * @param request
     * @param response
     * @param handler
     * @return
     * @throws Exception
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object loginUser = request.getSession().getAttribute("loginUser");
        if (loginUser==null){
            //未登录,返回登录页面
            response.sendRedirect("/moti-cloud/");
            return false;
        }else {
            //已登录,放行
            return true;
        }
    }
}
```

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
                .excludePathPatterns(
                        "/","/temp-file","/error400Page","/error401Page","/error404Page","/error500Page","/uploadTempFile","/admin","/sendCode","/loginByQQ","/login","/register","/file/share","/connection",
                        "/asserts/**","/**/*.css", "/**/*.js", "/**/*.png ", "/**/*.jpg"
                        ,"/**/*.jpeg","/**/*.gif", "/**/fonts/*", "/**/*.svg");
    }
}
```



## 6.3 配置错误界面

```java
@Configuration
public class MvcConfig implements ErrorPageRegistrar{
    @Override
    public void registerErrorPages(ErrorPageRegistry registry) {
        ErrorPage error400Page = new ErrorPage(HttpStatus.BAD_REQUEST, "/error400Page");
        ErrorPage error401Page = new ErrorPage(HttpStatus.UNAUTHORIZED, "/error401Page");
        ErrorPage error404Page = new ErrorPage(HttpStatus.NOT_FOUND, "/error404Page");
        ErrorPage error500Page = new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/error500Page");
        registry.addErrorPages(error400Page,error401Page,error404Page,error500Page);
    }
}
```

## 6.4 配置上传最大文件

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer, ErrorPageRegistrar {
    @Bean
    public MultipartConfigElement multipartConfigElement() {
        MultipartConfigFactory factory = new MultipartConfigFactory();
        //文件最大
        factory.setMaxFileSize(DataSize.parse("1024000KB"));
        /// 设置总上传数据总大小
        factory.setMaxRequestSize(DataSize.parse("1024000KB"));
        return factory.createMultipartConfig();
    }
}


//应该上面的设置对MultipartFile生效
public Map<String, Object> uploadFile(@RequestParam("file") MultipartFile files)
```

