---
title: Flutter news
---

Flutter transforms the app development process. Build, test, and deploy beautiful mobile, web, desktop, and embedded apps from a single codebase.

---


 ### 项目简介
基于flutter框架搭建的新闻查询app
 ### 欢迎页
 <div align=center>
 <img src="1.png" width = "30%" height = "30%">

 </div>
 
#### 通过ElevatedButton组件跳转到登录页
 ``` bash
   child: ElevatedButton(
        style: ElevatedButton.styleFrom(
          shape: RoundedRectangleBorder(
            borderRadius: Radii.k6pxRadivs
          ),
          backgroundColor: AppColors.primaryElement,
          textStyle: TextStyle(
            color: AppColors.primaryText
          )
        ),
        child: Text(
          "开始浏览"
        ),
        onPressed: (){
          debugPrint("helloworld");
          Navigator.pushNamed(context, "./sign-up");
        },
      ),
```
ElevatedButton是Flutter中的一个按钮组件，通过onPressed点击事件的回调函数跳转到登录页
  ### 登陆页
 <div align=center>
 <img src="2.png" width = "30%" height = "30%">
 </div>

 #### 检测输入框实现登陆功能
``` bash
 final TextEditingController _accountController = TextEditingController();
  
 final TextEditingController _passwordController = TextEditingController();
``` 
 声明账号，密码输入框

``` bash
 
  Future<bool?> _handInSign() async {
   ...
  }
``` 
Future<bool?>表示一个返回bool类型或null的异步操作。

``` bash
    if(!duIsAccount(_accountController.value.text)){//如果输入的值和duIsAccount不匹配
      await Fluttertoast.showToast(msg: "账号不规范");
      return false;
    }

    if(!duCheckStingLength(_passwordController.value.text, 6)){
      await Fluttertoast.showToast(msg: "密码长度不够");
      return false;
    }

``` 

校验用户输入的值与在utlis.dart中定义的duIsAccount正则表达式、duCheckStingLength是否匹配



```bash
UserLoginResponseEntity userProfile =
await UserAPI.login(context: context, params: myparse);
```
调用apis中的登陆方法并传值context，myparse
``` bash
if(userProfile.accessToken.isNotEmpty){
      Navigator.pushNamed(context, "./app");
    }
```
判断accessToken是否不为空，如果不为空则通过Navigator跳转到首页

在Flutter中，accessToken是在某些情况下使用的身份验证令牌，例如在与后端服务器交互或使用某些第三方服务时。它通常用于证
明你的应用程序或用户的身份，并授予对特定资源的访问权限。 ————GPT

---

对用户输入的密码进行加密
``` bash
String duSha256(String inputPassword){
  String salt = "aslkjfalsfkjaslfkjslfkjlafsk"; //盐
  var bytes = utf8.encode(inputPassword + salt);
  return sha256.convert(bytes).toString();
}
```
将输入的字符串密码（inputPassword）与一个硬编码的盐值（salt）连接，然后转换为SHA-256

可改进的地方
1.盐值应该是一个随机生成的字符串，而不是一个硬编码的值。
2.在函数中添加一些异常处理。例如，如果inputPassword是null或者空字符串，或者如果utf8.encode或sha256.convert出现任何错误，函数应该能够处理这些情况。

### 主页页面切换

<div align=center>
 <img src="首页.png" width = "30%" height = "30%">
</div>

#### 顶部导航栏

``` bash
  PreferredSizeWidget _buildAppBar() {
    return transparentAppBar(
        context: context,
        title: Text(
          _tabTitles[_page],
          style: TextStyle(
              color: Colors.black,
              fontFamily: "Montserrat",
              fontSize: duSetFontSize(18),
              fontWeight: FontWeight.w600),
        ),
        leading: const Text(""), //首页没有返回按钮，所以传空值
        actions: <Widget>[
          IconButton(
              onPressed: () {
                debugPrint("正在搜索");
              },
              icon: const Icon(
                Icons.search,
                color: AppColors.primartyText2,
              ))
        ]);
  }
  ```
使用封装在Widget的导航栏通用组件
页面导航栏标题根据索引值来显示

### 底部导航栏

 ``` bash
  Widget _buildBottomNavigationBar() {
    return BottomNavigationBar(
      items: _bottomTabs, //底部导航的按钮列表
      currentIndex: _page,
      type: BottomNavigationBarType.fixed, //底部导航 固定
      onTap: (int index) {
        // setState(() {
        //   _page = index;
        // });
        //animateToPage - 动画跳转到页面
        _pageController.animateToPage(
            index,
            duration:const Duration(milliseconds: 500),
            curve: Curves.ease
        );

      }, //绑定点击事件 ->传入变量 index
      showSelectedLabels: false, //是否展示选中的标签,
      showUnselectedLabels: false, //是否展示未选中的标签
    );
  }
  ```

_bottomTabs 是底部导航栏的按钮列表，每个按钮应该都是一个 BottomNavigationBarItem 对象。
currentIndex 是当前选中的页面的索引。
type 是底部导航栏的类型，这里是固定类型。
onTap 是每个按钮被点击时执行的操作。在这个例子中，它被用来更改页面索引并让页面动画跳转到这个新的索引。
animateToPage 是 PageController 类的一个方法，它可以让页面动画跳转到指定的索引。
duration 是动画跳转的时间长度，这里是0.5秒。
curve 是动画的速度曲线，这里是渐变。

#### 内容切换

``` bash
  Widget _buildPageView(){
    return PageView(

      //切换方式
      physics: const BouncingScrollPhysics(),

      //页面控制器
      controller: _pageController,

      //页面切换时调用的函数
      onPageChanged: (int index){
        setState(() {
          _page = index;
        });
      },

      //子页面视图空间
      children:  const[
        MainPage(),
        AccountPage()
      ],
    );
  }
  ```

physics 属性定义了滚动行为，这里使用的是BouncingScrollPhysics()，它模拟了物理上的弹性滚动效果。
controller 属性定义了页面控制器，这个控制器可以用来控制PageView的滚动和跳转。
onPageChanged 属性是一个回调函数，当页面切换时，它会被调用。这里它被用来更新当前的页面索引。
children 属性是一个Widget列表，每个元素代表一个子页面。

### 新闻中心

``` bash
  //编写网络的请求方法
  void _loadAllDate() async {
    //发送请求 收纳柜用缓存存存储数量
    _categories = await NewsAPI.categories(
      context: context,
    cacheDisk: true,
    );
    _selCategoryCode = _categories![0].code;
    debugPrint("分类是: ${_categories![0].toJson()}");
    //2 拉取推荐阅读
    _newsRecommend =
    await NewsAPI.newsRecommend(context: context,cacheDisk: true);

    //3 拉取频道数组
    _channels = await NewsAPI.channels(context: context,cacheDisk: true,);

    //4 新闻列表的请求
    _newsPageList =
    await NewsAPI.newsPageList(context: context, cacheDisk: true);

    if(mounted){
      setState(() {

      });
    }
  }
```

发送网络请求用于获取数据，以上所有的数据获取都是异步进行的，它们不会阻止程序的其它部分运行
所有的数据都存储在缓存中以便后续使用，这可以减少对网络的访问次数，提高程序的效率。
``` bash
  void initState() {
    super.initState();
    _loadAllDate();
  }
```
initState()。这个方法在Flutter的StatefulWidget生命周期的开始时调用，通常用于初始化组件的状态。

#### 在新闻中心调用分类组件
``` bash
  Widget _buildCateGories() {
    return _categories == null
        ? SizedBox()
        : newCategoriesWidget(
            categories: _categories!,//将categories赋值给_categories！
            selCategoryCode: _selCategoryCode,
            onTap: (CategoryResponseEntity item) {
              debugPrint(item.title);
              debugPrint(item.code);

              _categories![0].code = item.code;
              _categories![0].title = item.title;

              _selCategoryCode = _categories![0].code;

            });
  }
```
 如果频道分类变量为空那么就返回一个SizeBox否则它会返回一个具有新分类的newCategoriesWidget()
 #### 在新闻中心调用推荐组件

 ``` bash
   Widget _buildRecommend() {
    return _newsRecommend == null
        ? const SizedBox()
        : recommendWidget(_newsRecommend!);
  }
 ```
#### 在新闻中心调用新闻列表组件
``` bash
//文章列表组件
  Widget _buildNewsList() {
    return _newsPageList == null
        ? Container()
        : Column(
            children: _newsPageList!.items.map((item) {//map去遍历_newsPageList!的内容将遍历的每个新闻信息通过toList渲染成一个新闻列表
              //声明文章卡片组件内容
              List<Widget> _haha = <Widget>[
                newsItem(item, context),
                const Divider(//一个分割线
                  height: 1,
                )
              ];
              //插入广告
              int index = _newsPageList!.items.indexOf(item);
              //每五个插入一条广告组件
              if ((index + 1) % 5 == 0) {
                _haha.addAll([//通过addAll方法将广告组件放进_haha中
                  adWidget(),
                  const Divider(
                    height: 1,
                  )
                ]);
              }
              return Column(//返回一个垂直布局的_haha
                children: _haha,
              );
            }).toList(),
          );
  }
```

### 分类组件

``` bash
 return SingleChildScrollView(
    //SingleChildScrollView是一个用于创建一个单个子项可滚动视图的Widget
    //滚动方向 - 水平滚动
    scrollDirection: Axis.horizontal,
    //子组件
    child: Row(//水平布局
      children: categories.map<Widget>((item) {
        //categories是一个字符串列表。map函数被用来遍历这个列表，然后对每个元素创建一个Widget。.toList()方法将映射后的Widget列表转换为一个列表。

        //返回一个包含分类的容器
        return Container(//返回一个容器
          alignment: Alignment.center,//对齐方式为居中对齐
          height: duSetHeight(52),//高度
          padding: const EdgeInsets.symmetric(horizontal: 8),//内边距
          child: GestureDetector(
            //GestureDetector是Flutter中的一个组件，用于识别各种手势，如点击、双击、长按、拖动、缩放等。
            child: Text(//文本
              item.title,//文本内容为标题
              style: TextStyle(//文本样式
                  color: selCategoryCode == item.code//一个三元表达式，当选中的分类和当前分类的值相等，那么颜色就为primaryText,否则为第二个
                      ? AppColors.primaryText
                      : AppColors.primartyText2,
                  fontSize: duSetFontSize(18),
                  fontFamily: "Montserrat",
                  fontWeight: FontWeight.w600),
            ),
            //手势识别点击事件，触发传递过来的ontap 事件
            onTap: () => onTap(item),
          ),
        );
      }).toList(),
    ),
  );
}
```
### 频道组件
``` bash
  return SizedBox(，//SizedBox是一个用于指定小部件大小的布局部件。它具有固定的大小，不会随着屏幕大小或父部件大小的变化而变化。
    height: duSetHeight(137),
    child: SingleChildScrollView(//单向滚动视图
      scrollDirection: Axis.horizontal,//水平轴滚动
      child: Row(//水平
        children: channels.map<Widget>((item){//同上
          return Container(//返回一个容器
            width: duSetWidth(70),
            height: duSetHeight(97),
            margin: EdgeInsets.symmetric(horizontal: duSetWidth(10)),//上外边距
            child: GestureDetector(
              //手势检测器
              child: Column(//垂直布局
                crossAxisAlignment: CrossAxisAlignment.center,
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                //crossAxisAlignment: CrossAxisAlignment.center, 是设置交叉轴（垂直于主轴）的对齐方式为居中。
                //mainAxisAlignment: MainAxisAlignment.spaceBetween, 是设置主轴的对齐方式为两端对齐，即元素之间有等量的空间。
                children: [
                  Container(
                    width: duSetWidth(60),
                    height: duSetHeight(64),
                    //阴影
                    decoration: BoxDecoration(
                      borderRadius: BorderRadius.circular(duSetWidth(32)),//圆角
                      color: AppColors.primaryBackground,
                      boxShadow: const [
                        BoxShadow(
                          color: Color.fromARGB(38, 27, 27, 29),
                          offset: Offset(0, 5),//Offset(0, 5)定义了一个在y轴方向偏移5个单位的点。
                          blurRadius: 40//blurRadius是定义了阴影边缘的模糊程度，值越大阴影边缘越模糊。
                        ),
                      ]
                    ),
                    child: Center(//居中
                      child: Image.asset(
                        "assets/images/channel-${item.title}.png",
                        //你正在使用这个方法从一个叫做channel-${item.title}.png的资产中加载图片
                      ),
                    ),
                  ),
                  Text(
                    item.title,
                  textAlign: TextAlign.center,
                    overflow: TextOverflow.clip,
                    maxLines: 1,
                    style: TextStyle(
                      color: AppColors.thirdElementText,
                      fontFamily: "Avenir",
                      fontSize: duSetFontSize(14),
                      fontWeight: FontWeight.w400,
                      height: 1
                    ),
                  )
                ],
              ),
              onTap: () => onTap(item),
            ),
          );
        }).toList(),
      ),//滚动元素横向排列

    ),
  );
```
### 推荐组件
``` bash
Widget ImageCached(
  String url, {
  double width = 48,
  double height = 48,
  EdgeInsetsGeometry? margin,
}) {
  return CachedNetworkImage( //CachedNetworkImage 是 Flutter用于异步加载和缓存网络图片。
    imageUrl: "https://cdn.pixabay.com/photo/2023/07/20/19/42/flower-8140215_1280.jpg",
    //地址
    //用于构造一个完成的图片显示
    imageBuilder: (context, imageProvider) => Container(//imageBuilder一个用于构建自定义图像的类 ImageProvider来提供要显示的图像，
      height: duSetHeight(height),
      width: duSetWidth(width),
      margin: margin,
      decoration: BoxDecoration(//容器修饰
          borderRadius: Radii.k6pxRadivs,//圆角
          image: DecorationImage(image: imageProvider, fit: BoxFit.cover)),//然后使用DecorationImage来指定如何渲染这个图像。
    ),

    //图片还没回来的 占位
    placeholder: (context, url) {
      return Container(
        alignment: Alignment.center,//居中对齐
        child: CircularProgressIndicator(), //旋转加载提示
      );
    },

    //如果图片加载失败
    errorWidget: (context, url, error) => Image.network(
        "https://cdn.pixabay.com/photo/2023/07/20/19/42/flower-8140215_1280.jpg"),
  );
  //封装图片组件到新闻首页展示
}


Widget recommendWidget(NewsItem item) {
  return Container(
    margin: EdgeInsets.all(duSetWidth(20)),//外边距
    child: Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: <Widget>[
        //顶部海报图片
        ImageCached(
          item.thumbnail!,
            width: duSetWidth(335), height: duSetHeight(290)),

        //作者
        Container(
          margin: EdgeInsets.only(top: duSetHeight(14)),
          child: Text(
            item.author!,
            style: TextStyle(
                fontFamily: "Avenir",
                fontWeight: FontWeight.normal,
                color: AppColors.primartyText2,
                fontSize: duSetFontSize(14)),
          ),
        ),
        //文章标题
        Container(
          margin: EdgeInsets.only(top: duSetHeight(10)),
          child: Text(
            item.title!,
            style: TextStyle(
                fontFamily: "Montserrat",
                fontWeight: FontWeight.w600,
                color: AppColors.primartyText2,
                fontSize: duSetFontSize(24),
                height: 1),
          ),
        ),

        //文章相关操作
        Container(
          margin: EdgeInsets.only(top: duSetWidth(10)),
          child: Row(
            crossAxisAlignment: CrossAxisAlignment.center,
            children: <Widget>[
              //约束盒子 ConstrainedBox是Flutter中的一个Box widget，用于限制子Widget在特定方向上的大小。它能够约束子Widget在某个方向上的大小，使其不超过指定的最大或最小值。
              ConstrainedBox(
                constraints: BoxConstraints(maxWidth: 120),//约束盒子的最大宽度
                child: Text(
                  item.category!, //分类
                  style: TextStyle(
                      fontFamily: "Avenir",
                      fontWeight: FontWeight.w400,
                      fontSize: duSetFontSize(14),
                      color: AppColors.secondaryElementText),
                  overflow: TextOverflow.clip, //将溢出的文字裁剪
                  maxLines: 1,
                ),
              ),
              //间距 - 增加分割和事件的宽度
              Container(
                width: duSetWidth(15),
              ),
              //时间
              ConstrainedBox(
                constraints: BoxConstraints(maxWidth: 120),
                child: Text(
                  item.addtime.toString(), //分类
                  style: TextStyle(
                      fontFamily: "Avenir",
                      fontWeight: FontWeight.w400,
                      fontSize: duSetFontSize(12),
                      color: AppColors.thirdElementText),
                  overflow: TextOverflow.clip,
                  maxLines: 1,
                ),
              ),
              const Spacer(),
              const Icon(
                Icons.more_horiz,
                color: AppColors.primartyText2,
                size: 24,
              )
            ],
          ),
        ),
      ],
    ),
  );
}
```
### 新闻列表组件
``` bash
/新闻列表组件的编写
Widget newsItem(NewsItem item, BuildContext context) {
  return Container(//返回一个容器
    height: duSetHeight(161),
    padding: EdgeInsets.all(duSetWidth(20)),//内边距
    child: Row(
      mainAxisAlignment: MainAxisAlignment.spaceBetween,
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        ImageCached(item.thumbnail!,
            width: duSetWidth(121), height: duSetHeight(121)),
        SizedBox(
          width: duSetWidth(194),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              Text(
                item.author!,
                style: TextStyle(
                    fontFamily: "Avenir",
                    fontWeight: FontWeight.normal,
                    color: AppColors.thirdElementText,
                    fontSize: duSetFontSize(14),
                    height: 1),
              ),
              Container(
                margin: EdgeInsets.only(top: duSetHeight(10)),
                child: Text(
                  item.title!,
                  style: TextStyle(
                    fontFamily: "Nontserrat",
                    fontWeight: FontWeight.w500,
                    color: AppColors.primartyText2,
                    fontSize: duSetFontSize(16),
                    height: 1,
                  ),
                  overflow: TextOverflow.ellipsis,//文本超出的处理方式 TextOverflow.ellipsis 会将超出容器的文本截断，并在末尾显示省略号 "..." 以表示被截断的文本。
                  maxLines: 3,//最大行数
                ),
              ),
              const Spacer(),
              Row(
                crossAxisAlignment: CrossAxisAlignment.center,
                children: [
                  ConstrainedBox(
                    constraints: BoxConstraints(maxWidth: duSetWidth(60)),
                    child: Text(
                      item.category!,
                      style: TextStyle(
                          fontFamily: "Avenir",
                          fontWeight: FontWeight.normal,
                          color: AppColors.secondaryElementText,
                          fontSize: duSetFontSize(14),
                          height: 1),
                      overflow: TextOverflow.clip,
                      maxLines: 1,
                    ),
                  ),
                  SizedBox(
                    width: duSetWidth(15),
                  ),
                  ConstrainedBox(
                    constraints: BoxConstraints(maxWidth: duSetWidth(100)),
                    child: Text(
                      duTimeLineFormat(item.addtime!),//duTimeLineFormat 是处理时间轴格式的组件，该组件封装在valuse中
                      style: TextStyle(
                          fontFamily: "Avenir",
                          fontWeight: FontWeight.normal,
                          color: AppColors.secondaryElementText,
                          fontSize: duSetFontSize(14),
                          height: 1),
                      overflow: TextOverflow.clip,//超出的处理方式为裁剪
                      maxLines: 1,
                    ),
                  ),
                  const Spacer(),
                  InkWell(//用于实现水波纹点击效果。
                    child: Icon(
                      Icons.more_horiz,
                      color: AppColors.primartyText2,
                      size: duSetFontSize(24),
                    ),
                    onTap: () {
                      Navigator.pushNamed(context, "./detail",
                          arguments: ScreenArguments(
                            item.author!,
                            item.addtime!,
                            item.thumbnail!,
                            item.title!,
                            "暂无内容"
                          ));
                    },
                  )
                ],
              )
            ],
          ),
        )
      ],
    ),
  );
}
```








