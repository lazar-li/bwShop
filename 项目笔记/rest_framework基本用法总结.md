# mixins

**mixin类提供用于提供基本视图行为的操作**

**mixins在drf中主要配合viewset共同使用，实现http方法与mixins的相关类与方法进行关联。**

```python
from rest_framework import mixins
mixins.CreateModelMixin()
mixins.ListModelMixin()
mixins.RetrieveModelMixin()
mixins.UpdateModelMixin()
mixins.DestroyModelMixin()

```

## ListModelMixin

提供一种`.list(request, *args, **kwargs)`方法，该方法实现列出查询集。

如果填充了查询集，则返回一个`200 OK`响应，并将查询集的序列化表示形式作为响应的主体。可以可选地对响应数据进行分页。

## CreateModelMixin

提供一种`.create(request, *args, **kwargs)`方法，该方法实现创建和保存新模型实例。

如果创建了对象，则返回一个`201 Created`响应，该对象的序列化表示作为响应的主体。如果表示形式包含名为的键`url`，则将`Location`使用该值填充响应的标头。

如果提供的用于创建对象的请求数据无效，`400 Bad Request`则将返回响应，并将错误详细信息作为响应的正文。

## RetrieveModelMixin

提供一种`.retrieve(request, *args, **kwargs)`方法，该方法实现在响应中返回现有模型实例。

如果可以检索到对象，则返回一个`200 OK`响应，该对象的序列化表示作为响应的主体。否则将返回`404 Not Found`。

## UpdateModelMixin

提供一种`.update(request, *args, **kwargs)`方法，该方法实现更新和保存现有模型实例。

还提供了一种`.partial_update(request, *args, **kwargs)`方法，该方法与该`update`方法类似，但是用于更新的所有字段都是可选的。这样可以支持HTTP `PATCH`请求。

如果对象被更新，它将返回一个`200 OK`响应，该对象的序列化表示作为响应的主体。

如果提供的用于更新对象的请求数据无效，`400 Bad Request`则将返回响应，并将错误详细信息作为响应的主体。

## DestroyModelMixin

提供一种`.destroy(request, *args, **kwargs)`方法，该方法实现删除现有模型实例。

如果删除对象，则返回`204 No Content`响应，否则将返回`404 Not Found`。

**以上是官网对mixins的介绍**

**源码解析:https://www.cnblogs.com/w-s-l123/p/9056244.html**

# viewsets

```python
from rest_framework import viewsets

viewsets.ViewSetMixin()
viewsets.ViewSet(ViewSetMixin, views.APIView)
viewsets.GenericViewSet(ViewSetMixin, generics.GenericAPIView)
viewsets.ReadOnlyModelViewSet(mixins.RetrieveModelMixin,
                              mixins.ListModelMixin,
                              GenericViewSet)
viewsets.ModelViewSet(mixins.CreateModelMixin,
                      mixins.RetrieveModelMixin,
                      mixins.UpdateModelMixin,
                      mixins.DestroyModelMixin,
                      mixins.ListModelMixin,
                      GenericViewSet)
```

## ViewSet

### 属性

- `basename` -用于创建的URL名称的基础。
- `action`-当前动作的名称（例如`list`，`create`）。
- `detail` -布尔值，指示是否为列表视图或详细信息视图配置了当前操作。
- `suffix`-视图集类型的显示后缀-镜像`detail`属性。
- `name`-视图集的显示名称。此参数与互斥`suffix`。
- `description` -视图集单个视图的显示描述。

**ViewSet 主要是用来解决 APIView 中，要求 函数名必须是请求名的问题**

**在 ViewSet 可以用任意的函数作为 请求的 函数，只需要在
定义路由的时候，在 as_view 中，指定 请求方法 和 函数 的对应关系即可
还解决了 在同一类中，只能定义 一个 get 请求方法**

```python
class  UserViewSet(ViewSet):
  
	def list(self, request):
		# 查询所有数据
		pass
		
	def create(self, request):
		# 新增数据
		pass
```

### 混合视图

```python
from rest_framework.mixins import ListModelMixin,CreateModelMixin,RetrieveModelMixin,UpdateModelMixin, DestroyModelMixin
```

- ListModelMixin
- CreateModelMixin
- RetrieveModelMixin
- UpdateModelMixin
- DestroyModelMixin

混合视图 内置了一系列的方法，例如 list, create, retrieve, update, destroy

混合适合 需要 和 GenericViewSet 配合使用

**例:**

```python
# views.py
class UserView(ListModelMixin, CreateModelMixin,
            RetrieveModelMixin, UpdateModelMixin,
			DestroyModelMixin,
               GenericViewSet):

    queryset = User.objects.all()
    serializer_class = UserSerializer
# urls.py  通过as_view内传参,来确定哪个函数执行哪个请求
path('users', views.UserView.as_view({"get":"list", "post":"create"}), name="user-list"),
url('users/(?P<pk>\d+$)', views.UserView.as_view({"get":"retrieve", "put": "update", "delete":"destroy"}), name="user-detail"),
```

**在 ViewSet 中， 可以定义一个router，用来替代传统的路由定义
新建一个 `routers.py` ,代码如下**

```python
from rest_framework.routers import DefaultRouter

router = DefaultRouter()

router.register('user',  views.UserView)
```

**在路由`urls.py` 中进行管理**

```python
from .routers import router 

urlpatterns = [
	url('^', include(router.urls))
	...
	...
]
```

ViewSet 在使用的时候

get : 默认对应 list , retrieve
post : 默认对应create
put : 默认对应update
delete :默认对应 destory

### 在 ViewSet 中， 自定义一个函数、如何让 DefaultRouter 识别

**在 ViewSet 中， 通过 一个 @action 装饰器，装饰某个函数，就可以让 DefaultRouter识别**

**@action 装饰中，常见的属性有：**

- methods : 代表请求方式、是一个列表，可以设置多个
- detail : True/False : True在路由上会设置一个 pk,要求根据 主键进行数据查询，False则和主键没有关系
- url_path : 设置请求路径 , 相当于 path函数中 router
- url_name : 设置请求路由对应的名字，相当于 path函数中 name

```
detail=False
<URLPattern '^user/login/$' [name='user-login']>,
detail=True
<URLPattern '^user/(?P<pk>[^/.]+)/login/$' [name='user-login']>, 
```

## DRF View APIView ViewSet

https://www.jianshu.com/p/664ea682c21b

# filters

```python
from rest_framework import filters

filters.BaseFilterBackend()
filters.SearchFilter(BaseFilterBackend)
filters.OrderingFilter(BaseFilterBackend)

```

## BaseFilterBackend

**该[`django-filter`](https://django-filter.readthedocs.io/en/latest/index.html)库包含一个`DjangoFilterBackend`类，该类支持针对REST框架的高度可自定义的字段筛选。**

**要使用`DjangoFilterBackend`，请先安装`django-filter`。然后添加`django_filters`到Django的`INSTALLED_APPS`**

```python
pip install django-filter
# 配置settings
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS':['django_filters.rest_framework.DjangoFilterBackend']
}
# 或将过滤器后端添加到单个View或ViewSet。
from django_filters.rest_framework import DjangoFilterBackend

class UserListView(generics.ListAPIView):
    ...
    filter_backends = [DjangoFilterBackend]
# 如果只需要简单的基于等式的过滤，则可以filterset_fields在视图或视图集上设置一个属性，列出要过滤的字段集。
class ProductList(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    filter_backends = [DjangoFilterBackend]
    filterset_fields = ['category', 'in_stock']
# 这将自动FilterSet为给定的字段创建一个类，并允许您发出如下请求：
http://example.com/api/products?category=clothing&in_stock=True
```

## [SearchFilter](https://www.django-rest-framework.org/api-guide/filtering/#searchfilter)

**该`SearchFilter`级支持简单单的查询参数基于搜索和基于该[admin界面的搜索功能](https://docs.djangoproject.com/en/stable/ref/contrib/admin/#django.contrib.admin.ModelAdmin.search_fields)。**

**使用时，可浏览的API将包含一个`SearchFilter`控件：**

![搜索过滤器](https://www.django-rest-framework.org/img/search-filter.png)

```python
# 所述SearchFilter如果视图有一类将只应用于search_fields属性集。该search_fields属性应该是模型上文本类型字段名称的列表，例如CharField或TextField。
from rest_framework import filters

class UserListView(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    filter_backends = [filters.SearchFilter]
    search_fields = ['username', 'email']
# 这将允许客户端通过执行以下查询来过滤列表中的项目：
http://example.com/api/users?search=russell
# 您还可以使用查找API双下划线表示法在ForeignKey或ManyToManyField上执行相关查找：
search_fields = ['username', 'email', 'profile__profession']
```

默认情况下，搜索将使用不区分大小写的部分匹配。搜索参数可以包含多个搜索词，应将其用空格和/或逗号分隔。如果使用多个搜索词，则仅当所有提供的词都匹配时，对象才会在列表中返回。

可以通过在前面加上各种字符来限制搜索行为`search_fields`。

- '^'开始搜索。
- '='完全匹配。
- '@'全文搜索。（当前仅支持Django的MySQL后端。）
- '$'正则表达式搜索。

例如：search_fields = ['=username', '=email']

默认情况下，搜索参数名为`'search'`，但是此参数可能会被`SEARCH_PARAM`设置覆盖。

要根据请求内容动态更改搜索字段，可以对进行子类化`SearchFilter`并覆盖该`get_search_fields()`函数。例如，以下子类仅`title`在查询参数`title_only`在请求中时才搜索：

```python
from rest_framework import filters

class CustomSearchFilter(filters.SearchFilter):
    def get_search_fields(self, view, request):
        if request.query_params.get('title_only'):
            return ['title']
        return super(CustomSearchFilter, self).get_search_fields(view, request)
```

## [OrderingFilter](https://www.django-rest-framework.org/api-guide/filtering/#orderingfilter)

**ctrl+鼠标左键访问**

# generics

```python
from rest_framework import generics

GenericAPIView(views.APIView)
CreateAPIView(mixins.CreateModelMixin,GenericAPIView)
ListAPIView(mixins.ListModelMixin,GenericAPIView)
RetrieveAPIView(mixins.RetrieveModelMixin,GenericAPIView)
DestroyAPIView(mixins.DestroyModelMixin,GenericAPIView)
UpdateAPIView(mixins.UpdateModelMixin,GenericAPIView)
ListCreateAPIView(mixins.ListModelMixin,
                  mixins.CreateModelMixin,
                  GenericAPIView)
RetrieveUpdateAPIView(mixins.RetrieveModelMixin,
                      mixins.UpdateModelMixin,
                      GenericAPIView)
RetrieveDestroyAPIView(mixins.RetrieveModelMixin,
                       mixins.DestroyModelMixin,
                       GenericAPIView)
RetrieveUpdateDestroyAPIView(mixins.RetrieveModelMixin,
                             mixins.UpdateModelMixin,
                             mixins.DestroyModelMixin,
                             GenericAPIView)
```

**https://www.django-rest-framework.org/api-guide/generic-views/**

