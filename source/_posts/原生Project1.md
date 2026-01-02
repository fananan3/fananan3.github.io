---
title: 原生Project1
---
Android Studio is the official Integrated Development Environment (IDE) for Android app development. Based on the powerful code editor and developer tools from IntelliJ IDEA , Android Studio offers even more features that enhance your productivity when building Android apps,

---

 ### 项目简介
 使用Android studio搭建的备忘录

  ### 登陆页

  

  <div align=center>
  <img src="1.png" width = "30%" height = "30%">
  </div>

  登录功能实现逻辑：
  1、在两个EditText控件中分别输入用户名和密码；
  2、判断用户名和密码是否为空，若为空则通过Toast控件提醒用户输入用户名和密码；
  3、点击保存按钮，将用户名和密码保存到APP中；
  4、登录时输入用户名和密码，点击登录按钮同时从APP中读取存入的用户名和密码，进行比较，如果一致，则登录成功，否则提示用户，用户名或密码不正确，需要重新输入；登录成功跳转到备忘录列表界面。
#### 用户保存账号密码
  ``` bash
   public void saved(View view){
        //获取输入的用户名和密码
        String name = userName.getText().toString().trim();
        //String psw = passWord.getText().toString().trim();
        //对用户输入的密码进行加密 登陆也会对输入的密码加密
        String psw1 = passWord.getText().toString().trim();
        String psw = MD5Utils.md5(psw1);
        if (name.equals("") || name == "") {
            Toast.makeText(LoginActivity.this, "请输入用户名", Toast.LENGTH_SHORT).show();
        } else if (psw.equals("") || psw == "") {
            Toast.makeText(LoginActivity.this, "请输入密码", Toast.LENGTH_SHORT).show();
        } else{
            //调用SPSaveUserInfo类是的saveUserInfo()方法保存用户名和密码
            boolean isSaveSuccess = SPSaveUserInfo.saveUserInfo(this,name,psw);
            //判断是否保存成功
            if (isSaveSuccess){//如果isSaveSuccess为true那就显示保存成功反之失败
                Toast.makeText(LoginActivity.this, "保存成功", Toast.LENGTH_SHORT).show();
            }else {
                Toast.makeText(LoginActivity.this, "保存失败", Toast.LENGTH_SHORT).show();
            }
        }
        //保存后清空用户名或密码
        userName.setText("");
        passWord.setText("");

    }
```


``` bash
   public static boolean saveUserInfo(Context context, String account, String password){
                //获得SharedPreferences对象
                SharedPreferences sp = context.getSharedPreferences("data",Context.MODE_PRIVATE);
                //获取编辑器
                SharedPreferences.Editor editor = sp.edit();
                //将传递过来的用户名和密码写入到编辑器
                editor.putString("account",account);
                editor.putString("password",password);
                editor.commit();//提交编辑器
                return true;
        }
```
1.获取一个SharedPreferences对象，这个名字为"data"，并且其模式为MODE_PRIVATE，这意味着只有当前的应用程序可以访问这个SharedPreferences。
2.获取一个编辑器（SharedPreferences.Editor），这个编辑器可以让你添加和修改SharedPreferences中的数据。
3.通过editor.putString方法将用户名和密码写入SharedPreferences。
4.editor.commit()将编辑器的更改提交，这会保存你在编辑器中做的所有修改。

#### 密码加密

``` bash
public class MD5Utils {
    public static String md5(String input) {
        try {
            // 创建一个MessageDigest实例，用于MD5加密算法
            MessageDigest md = MessageDigest.getInstance("MD5");

            // 将输入字符串转换为字节数组
            byte[] byteArr = input.getBytes();

            // 使用MD5加密算法更新摘要
            md.update(byteArr);

            // 获取字节数组的MD5摘要值
            byte[] digest = md.digest();

            // 将字节数组转换为十六进制字符串
            StringBuilder sb = new StringBuilder();
            for (byte b : digest) {
                sb.append(String.format("%02x", b & 0xff));
            }

            // 返回十六进制字符串作为加密后的结果
            return sb.toString();
        } catch (NoSuchAlgorithmException e) {
            // 如果MD5算法不可用，则抛出异常
            e.printStackTrace();
        }
        return null;
    }
}
```
#### 登陆功能实现
``` bash
   public void login(View view) {             //ctrl+shift+/多行注释快捷键
        Map<String, String> userInfo = SPSaveUserInfo.getUserInfo(this);
        //获取输入的用户名和密码
        String name = userName.getText().toString().trim();
        String psw = passWord.getText().toString().trim();
        if (name.equals("") || name == "") {
            Toast.makeText(LoginActivity.this, "用户名不能为空", Toast.LENGTH_SHORT).show();
        } else if (psw.equals("") || psw == "") {
            Toast.makeText(LoginActivity.this, "密码不能为空", Toast.LENGTH_SHORT).show();
        } else if (name.equals(userInfo.get("account")) && psw.equals(userInfo.get("password"))) {
            //通过Toast控件显示登录信息
            Toast.makeText(LoginActivity.this, "登录成功", Toast.LENGTH_SHORT).show();
            //通过Intent意图对象来实现Activity之间的跳转 显示意图
            //实例化一个Intent对象
            Intent intent = new Intent(LoginActivity.this, MemoryActivity.class);
            intent.setAction("android.intent.action.VIEW");

            startActivity(intent); //开启Activity
        } else {
            Toast.makeText(LoginActivity.this, "用户名或密码不正确，请重新输入！", Toast.LENGTH_SHORT).show();
        }
    }
```
#### 以上代码所用到的知识点

1 Android Intent：用于在Android应用中启动新的Activity或者打开新的应用组件。
2 Toast控件：用于显示简短的文本通知，通常用于向用户显示消息或反馈。
3 用户输入获取：通过诸如userName.getText()和passWord.getText()这样的代码行，获取用户界面上的文本输入。
4 条件判断：使用if，else if和else语句来进行条件判断和执行相应的代码块。
5 字符串比较：使用equals和equalsIgnoreCase方法来比较字符串。
6 意图（Intent）动作设置：通过intent.setAction()方法设置Intent的动作。
7 Activity的启动：通过startActivity()方法启动新的Activity。




  
  ### 首页
  <div align=center>
  <img src="主页.png" width = "30%" height = "30%">

  </div>

  
  ### 添加记录
  <div align=center>
  <img src="添加记录.png" width = "30%" height = "30%">

  </div>

  
  ### 修改记录
  <div align=center>
  <img src="修改记录.png" width = "30%" height = "30%">

  </div>

  
  ### 删除记录
  <div align=center>
  <img src="删除记录.png" width = "30%" height = "30%">

  </div>



