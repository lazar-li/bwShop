

### 		JWT  的认证流程



JSON Web Token（JWT）是一个开放的标准（RFC 7519），它定义了一个紧凑且自包含的方式，用于在各方之间作为JSON对象安全地传输信息。由于此信息是经过数字签名的，因此可以被验证和信任。 

1首先 从用户使用账号和密码 发出 post 的请求

2 服务器使用私钥会创建一个jwt ；

3 服务器返回这个jwt 给浏览器；

4 浏览器将该jwt串在请求头中像服务器发送请求 

5  服务器验证该jwt 

6 返回响应的资源给浏览器

![img](https://img2018.cnblogs.com/blog/463242/201911/463242-20191105094118819-115186199.png) 



# 第三方登陆的流程

使用  social（搜收）_django 第三方库 第三方登陆

1  进入微博开放平台，首先要经过认证，然后才可以创建应用 

2 点开 微连接  点击 网战接入

3 写上 应用的名字  创建好  有一个 App key 这个 非常重要 

4 我们在应用信息 里面 添加一个 测试用户来登陆

5 之后我们设值 oauth授权设置  

​	5.1 写入回调网页   测试 使用 127.0.0.1:8000/login/weibo  上先之后我们使用 服务器的IP

​		取消授权回调页 

​	5.2 APP Sercet 非常重要 也需要记住 



# 第三方登陆 我们使用 social_django 这个库来实现

1   我们先安装 库pip install social-auth-app-django

2 .我们在 1INSTALL_APP中配置   ‘social_django’

3  .我们生成表   然后数据库就会 多了 5个表

4 我们在添加到`AUTHENTICATION_BACKENDS中`   配置  微信微博 qq 登陆的

​	

```
# 设置邮箱和用户名和手机号均可登录
AUTHENTICATION_BACKENDS = (
    'users.views.CustomBackend',
    'social_core.backends.weibo.WeiboOAuth2',
    'social_core.backends.qq.QQOAuth2',
    'social_core.backends.weixin.WeixinOAuth2',
    'django.contrib.auth.backends.ModelBackend',
)

```

5 # 第三方登录

​    

path('', include('social_django.urls', namespace='social'))

 

6 APP Secret和App key配置，settings里面  

```
# 第三方登录，里面的值是你的开放平台对应的值
SOCIAL_AUTH_WEIBO_KEY = 'xxxxxxx'
SOCIAL_AUTH_WEIBO_SECRET = 'xxxxxx'

SOCIAL_AUTH_QQ_KEY = 'xxxxxxx'
SOCIAL_AUTH_QQ_SECRET = 'xxxxxxx'

SOCIAL_AUTH_WEIXIN_KEY = 'xxxxxxx'
SOCIAL_AUTH_WEIXIN_SECRET = 'xxxxxxx'
```

7  修改源码

登录成功跳到首页，发现还处于未登录状态，我们需要对源码做修改

social_core/actions.py  修改drf

```
# 修改源码适配drf
    response = backend.strategy.redirect(url)
    payload = jwt_payload_handler(user)
    response.set_cookie("name",user.name if user.name else user.username, max_age=24*3600)
    response.set_cookie("token", jwt_encode_handler(payload), max_age=24*3600)
    return response
```



[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

现在就登录后就正常了。qq和微信的登录，一样的操作，只要去开放平台注册应用，其它跟微博登录一样设置就可以了



## 支付宝的登陆流程

首先我们需要在在电脑瞎咋  支付宝开放平台开发助手

1 . 进入蚂蚁金服开放平台  我登陆后进入管理中心   应用列表

2 . **创建应用** 

3  创建应用后会有一个appid 

4微信支付和支付宝支付都是要求企业认证才可以完成的，个人开发不可以，所以我们需要用

沙箱环境，它可以让我们不具备这些应用或者说应用审核还没通过的时候先开发调试

5 搜索沙箱环境    在信息配置 里面   记住我们的APPid ：   

6在 支付宝开发助手  生成**公钥和私钥的生成方法**   把他放在 沙箱环境 的密钥

7 把公钥 私钥 cp  到 trade /key 下面    在文件开头 结尾添加内容  

-----BEGIN PRIVATE KEY----------END PRIVATE KEY-----

8 我们需要安装的模块  

​	pip install pycrypotodeme

9 utils中新建alipay.py  写入代码  修改沙箱appID

10运行alipay.py,会生成一个支付链接url，点进去跳到支付页面 

 11 在手机下载支付宝 沙箱   我们使用 买家信息登陆   输入账号密码会消费成功

12 # 配置支付宝支付相关接口的

url path('alipay/return/', AlipayView.as_view()

13  在 alipay.py 里面修下  服务器的地址 

​	把return_url和notify_url都改成远程服务器的地址

```
return_url="http://47.93.198.159:8000/alipay/return/"

app_notify_url="http://47.93.198.159:8000/alipay/return/"
```

14  在 views ser alipay 里面i需改 沙箱的APPID

15  settings中配置公钥私钥路径  

16 我们在 xadmin 里面  环境里测试  生成 alipay_url :会写入东西我们 实现就可以了



### **12.6.vue静态文件放到django中**

​	vue有两种开发模式  npm dev build  用来生成静态文件

​		npm dev run 是来运行的

​	1 npm run build

​	2.把index.html拷贝到templates目录下 

​	3.django中创建static目录 

​	4.

- 把index.entry.js考到django的static目录下面

- 把dist/static下的两个文件夹拷贝到django static目录下

- 5（4）settings设置静态文件路径

  ```
  STATIC_URL = '/static/'
  STATICFILES_DIRS = (
      os.path.join(BASE_DIR, "static"),
  )
  ```

  6我们修改 index 里面的rul

  7添加商品到购物车-->>去结算-->>生成订单-->>跳到支付页面了 

AIPview 几种方式的对比

	# 1serializers.Serializer

```

from rest_framework import serializers


class GoodsSerializer(serializers.Serializer):
    name = serializers.CharField(required=True,max_length=100)
    click_num = serializers.IntegerField(default=0)
    goods_front_image = serializers.ImageField()
```

## 2serializers.ModelSerializer

上面是用Serializer实现的，需要自己手动添加字段，如果用Modelserializer，会更加的方便，直接用__all__就可以全部序列化 

```
class GoodsSerializer(serializers.ModelSerializer):
    class Meta:
        model = Goods
        fields = '__all__'
```

	###### 2.1 覆盖外键的字段 CategorySerializer()  之后可以看的等详细了

```
#ModelSerializer实现商品列表页
class GoodsSerializer(serializers.ModelSerializer):
    #覆盖外键字段
    category = CategorySerializer()
    class Meta:
        model = Goods
        fields = '__all__'
```

#3.APIview 封装

```

```

## 4 mixins    generics  mixins和generic一起用用 

mixins和generic一起用用 

```
from goods.serializers import GoodsSerializer
from .models import Goods
from rest_framework.response import Response
from rest_framework import mixins
from rest_framework import generics


class GoodsListView(mixins.ListModelMixin,generics.GenericAPIView):
    '商品列表页'
    queryset = Goods.objects.all()
    serializer_class = GoodsSerializer

    def get(self,request,*args,**kwargs):
        return self.list(request,*args,**kwargs)
```

​	4 . 1  generics.ListAPIView 直接获取了 商品列表页的信息

- ```
  class GoodsListView(generics.ListAPIView):
      '商品列表页'
      queryset = Goods.objects.all()
      serializer_class = GoodsSerializer
  ```

#### 5.8.drf的APIView、GenericView、viewsets和router的原理分析

GenericViewSet（viewsets）     ----drf

　　GenericAPIView                  ---drf

　　　　APIView                        ---drf

　　　　　　View　　　　        ----django

​	上面这些view功能不同 主要是有mixin 的存在  

	### mixin  的五种方法



1  CreateModelMixin：  创建数据类

2  ListModelMixin          返回所有数据列表

3 RetrieveModelMixin ：传入参数 返回某个数据对象

4UpdateModelMixin    传入参数 更新数据对象

5  DestroyModelMixin  删除数据对象



#数据 3分 流程

1（1）商品分类有两个接口： 

​	1一种是全部分类：一级二级三级 

​	2 一种是某一类的分类以及商品详细信息：

2  开始写商品分类的接口  给分类添加三级分类的serializer 

```
 #goods/serializers.py
from rest_framework import serializers
from .models import Goods,GoodsCategory


class CategorySerializer3(serializers.ModelSerializer):
    '''三级分类'''
    class Meta:
        model = GoodsCategory
        fields = "__all__"


class CategorySerializer2(serializers.ModelSerializer):
    '''
    二级分类
    '''
    #在parent_category字段中定义的related_name="sub_cat" 
    sub_cat = CategorySerializer3(many=True)
    class Meta:
        model = GoodsCategory
        fields = "__all__"


class CategorySerializer(serializers.ModelSerializer):
    """
    商品一级类别序列化
    """
    sub_cat = CategorySerializer2(many=True)
    class Meta:
        model = GoodsCategory
        fields = "__all__"

（3）views.py

class CategoryViewSet(mixins.ListModelMixin, mixins.RetrieveModelMixin, viewsets.GenericViewSet):
    '''
    list:
        商品分类列表数据
    '''

    queryset = GoodsCategory.objects.filter(category_type=1)
    serializer_class = CategorySerializer

```

（4）url配置 

​		

 

```
# 配置Category的url
router.register(r'categorys', CategoryViewSet, base_name="categorys")
```

# DRF 操作流程

1 首先我们先安装  pip install djangorestframework

2 setting app  里面配置 rest——framwork

3 首先我们创建序列化器   新建文件 serializers。py

​	

```
class UserDetailSerializer(serializers.ModelSerializer):
    """
    用户详情
    """
    class Meta:
        model = User   指明序列胡器处理的字段来自哪里的
        fields = ("name", "gender", "birthday", "email","mobile")  # API web 可视化显示的
        
```

4 我们编写视图   引入需要用用到的 导入 serializers   views

​	

```
class BannerViewset(mixins.ListModelMixin, viewsets.GenericViewSet):
    """
    首页轮播图
    """
    queryset = Banner.objects.all().order_by("index")
    serializer_class = BannerSerializer
```

5 	之后自定义 路由 生成路由的地址 

​	

```
from rest_framework import routers 
需要用到 routers
之后我们注册路由
router.register('indexgoods',IndexCategoryViewset,basename='indexgoods')
```



6 完成之后我们测试 运行

python  manage.py runserver

# 手机注册 登陆 验证码

1 我们需要注册 云片网  认证开发者信息认证 选择个人

​	 “控制台”-->>“国内短信”-->> 签名1  

​	我们需要 增加个签名  短信签名要写自己的

​	还需要在 设置里面关闭IP白名单

2之后我们在 apps 下面创建utils 文件夹  我们在建立个yunpian。py 吸入人代码 

​	我们需要修改 自己的YunPian 的APIKEY  设置自己  还需要设置自己的手机号

3 我们用 DRF实现发送短信验证码接口

​	手机号检验   

​		是不是已经合法  和已经注册了

​	3.1在setting.py 里面配置  手机号码的正则表达式

​	3.2 需要在 user  建立 序列化

​	3.3 我们还需要在 setting 里面配置 AIP key  自己的

​	3.4 在views 里面写入代码 

​	3.5 最后配置url

​	3.6 我们去 code 里面验证手机号 输入正确的手机号

4   我们完成注册的接口