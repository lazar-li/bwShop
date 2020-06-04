

##### 四个app下面都新建文件adminx.py，然后分别注册到后台 

users/adminx.py 

```
import xadmin
from xadmin import views

class BaseSetting(object):
    enable_themes = True
    use_bootswatch = True


class GlobalSettings(object):
    site_title = "后台管理系统"
    site_footer = "北京网络职业技术学院"
    menu_style = "accordion"


xadmin.site.register(views.BaseAdminView, BaseSetting)
xadmin.site.register(views.CommAdminView, GlobalSettings)




```

###### ##我们需要在 xadmin  后台修改一下 

​	

```
# users/apps.py

from django.apps import AppConfig


class UsersConfig(AppConfig):
    name = 'users'
    #app名字后台显示中文
    verbose_name = "用户管理"
    
 -------------------------------------
 # users/__init__.py

default_app_config = 'users.apps.UsersConfig'
    
```



goods/adminx.py 

```
# encoding: utf-8
import xadmin
from .models import Goods, GoodsCategory, GoodsImage, Banner, HotSearchWords
from .models import IndexAd

class GoodsAdmin(object):
    list_display = ["name", "click_num", "sold_num", "fav_num", "goods_num", "market_price",
                    "shop_price", "goods_brief", "goods_desc", "is_new", "is_hot", "add_time"]
    search_fields = ['name', ]
    list_editable = ["is_hot", ]
    list_filter = ["name", "click_num", "sold_num", "fav_num", "goods_num", "market_price",
                   "shop_price", "is_new", "is_hot", "add_time", "category__name"]
    style_fields = {"goods_desc": "ueditor"}

    class GoodsImagesInline(object):
        model = GoodsImage
        exclude = ["add_time"]
        extra = 1
        style = 'tab'

    inlines = [GoodsImagesInline]


class GoodsCategoryAdmin(object):
    list_display = ["name", "category_type", "parent_category", "add_time"]
    list_filter = ["category_type", "parent_category", "name"]
    search_fields = ['name', ]


class GoodsBrandAdmin(object):
    list_display = ["category", "image", "name", "desc"]

    def get_context(self):
        context = super(GoodsBrandAdmin, self).get_context()
        if 'form' in context:
            context['form'].fields['category'].queryset = GoodsCategory.objects.filter(category_type=1)
        return context


class BannerGoodsAdmin(object):
    list_display = ["goods", "image", "index"]


class HotSearchAdmin(object):
    list_display = ["keywords", "index", "add_time"]


class IndexAdAdmin(object):
    list_display = ["category", "goods"]


xadmin.site.register(Goods, GoodsAdmin)
xadmin.site.register(GoodsCategory, GoodsCategoryAdmin)
xadmin.site.register(Banner, BannerGoodsAdmin)

xadmin.site.register(HotSearchWords, HotSearchAdmin)
xadmin.site.register(IndexAd, IndexAdAdmin)


```



######之后我们在 同样的位置修改 xadmin 里面的后台





（3）trade/adminx.py 

```
# __author__ = 'bobby'

import xadmin
from .models import ShoppingCart, OrderInfo, OrderGoods

class ShoppingCartAdmin(object):
    list_display = ["user", "goods", "nums", ]


class OrderInfoAdmin(object):
    list_display = ["user", "order_sn",  "trade_no", "pay_status", "post_script", "order_mount",
                    "order_mount", "pay_time", "add_time"]

    class OrderGoodsInline(object):
        model = OrderGoods
        exclude = ['add_time', ]
        extra = 1
        style = 'tab'

    inlines = [OrderGoodsInline, ]


xadmin.site.register(ShoppingCart, ShoppingCartAdmin)
xadmin.site.register(OrderInfo, OrderInfoAdmin)

```



（4）user_operation/adminx.py 

```
import xadmin
from .models import UserFav, UserLeavingMessage, UserAddress


class UserFavAdmin(object):
    list_display = ['user', 'goods', "add_time"]


class UserLeavingMessageAdmin(object):
    list_display = ['user', 'message_type', "message", "add_time"]


class UserAddressAdmin(object):
    list_display = ["signer_name", "signer_mobile", "district", "address"]

xadmin.site.register(UserFav, UserFavAdmin)
xadmin.site.register(UserAddress, UserAddressAdmin)
xadmin.site.register(UserLeavingMessage, UserLeavingMessageAdmin)
```

最后 生成一下数据库表

```
python manage.py makemigrations

python manage.py migrate
```



### 我们在settings 中配置下  图片 的路径

```
# 设置上传文件的路径
MEDIA_URL="/media/"
MEDIA_ROOT=os.path.join(BASE_DIR,"media")
```





之后我们配置一下 urls.py

```
from django.urls import path,include
import xadmin
from django.views.static import serve
from MxShop.settings import MEDIA_ROOT

urlpatterns = [
    path('xadmin/', xadmin.site.urls),
    path('ueditor/',include('DjangoUeditor.urls' )),
    #文件
    path('media/<path:path>',serve,{'document_root':MEDIA_ROOT}),
]
```

## 在这之后我们需要在 django views 里面实现商品的列表页 

goods/view_base.py 

```
from django.views.generic import View
from goods.models import Goods

class GoodsListView(View):
    def get(self,request):
        #通过django的view实现商品列表页
        json_list = []
        #获取所有商品
        goods = Goods.objects.all()
        for good in goods:
            json_dict = {}
            #获取商品的每个字段，键值对形式
            json_dict['name'] = good.name
            json_dict['category'] = good.category.name
            json_dict['market_price'] = good.market_price
            json_list.append(json_dict)

        from django.http import HttpResponse
        import json
        #返回json，一定要指定类型content_type='application/json'
        return HttpResponse(json.dumps(json_list),content_type='application/json')
```



之后我们在 bw shop /urls 。py  里面  写入 商品 列表页

​	

```
from goods.view_base import GoodsListView

urlpatterns = [
   #商品列表页
    path('goods/',GoodsListView.as_view(),name='goods-list')
]
```

