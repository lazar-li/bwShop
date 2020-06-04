### 设计goods.models.商品类别表

```python
class GoodsCategory(models.Model):
    """
    商品类别
    """
    CATEGORY_TYPE = (
        (1, "一级类目"),
        (2, "二级类目"),
        (3, "三级类目"),
    )

    name = models.CharField(default="", max_length=30, verbose_name="类别名", help_text="类别名")
    code = models.CharField(default="", max_length=30, verbose_name="类别code", help_text="类别code")
    desc = models.TextField(default="", verbose_name="类别描述", help_text="类别描述")
    category_type = models.IntegerField(choices=CATEGORY_TYPE, verbose_name="类目级别", help_text="类目级别")  # 级别控制
    parent_category = models.ForeignKey("self", null=True, blank=True, verbose_name="父类别", help_text="父类级别",
                                        related_name="sub_cat", on_delete=models.CASCADE)
    is_tab = models.BooleanField(default=False, verbose_name="是否导航", help_text="是否导航")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "商品类别"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name
```

### 设计goods.models.品牌表

```python
class GoodsCategoryBrand(models.Model):
    """
    品牌名
    """
    cagetory = models.ForeignKey(GoodsCategory, null=True, blank=True, verbose_name="商品类目", on_delete=models.CASCADE)
    name = models.CharField(default="", max_length=30, verbose_name="品牌名", help_text="品牌名")
    desc = models.TextField(default="", max_length=200, verbose_name="品牌描述", help_text="品牌描述")
    image = models.ImageField(max_length=200, upload_to="brand/images/")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "品牌"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name
```

### 设计goods.models.商品表

**DjangoUeditor**富文本编辑器，Rich Text Editor, 简称 RTE, 是一种可内嵌于浏览器，所见即所得的文本编辑器。富文本编辑器不同于文本编辑器，程序员可到网上下载免费的富文本编辑器内嵌于自己的网站或程序里（当然付费的功能会更强大些），方便用户编辑文章或信息。

教程地址:https://blog.csdn.net/qq_39865665/article/details/83449677

下载地址:https://github.com/twz915/DjangoUeditor3/

下载完成后将DjangoUeditor app放到extra_apps/下

在settings中添加app

```python
INSTALLED_APPS = [
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'apps.users.apps.UsersConfig',
    'goods',
    'trade',
    'user_operation',
    'DjangoUeditor',
]
```



```python
class Goods(models.Model):
    """
    商品
    """
    category = models.ForeignKey(GoodsCategory, verbose_name="商品类目", on_delete=models.CASCADE)
    goods_sn = models.CharField(max_length=50, default="", verbose_name="商品唯一货号")
    name = models.CharField(max_length=300, verbose_name="商品名")
    click_num = models.IntegerField(default=0, verbose_name="点击数")
    sold_num = models.IntegerField(default=0, verbose_name="商品销售量")
    fav_num = models.IntegerField(default=0, verbose_name="收藏数")
    goods_num = models.IntegerField(default=0, verbose_name="库存数")
    market_price = models.FloatField(default=0, verbose_name="市场价格")
    shop_price = models.FloatField(default=0, verbose_name="本店价格")
    goods_brief = models.TextField(max_length=500, verbose_name="商品简短描述")
    goods_desc = UEditorField(verbose_name=u"内容", imagePath="goods/images/", width=1000, height=300,
                              filePath="goods/files/", default="")
    ship_free = models.BooleanField(default=True, verbose_name="是否承担运费")
    goods_front_image = models.ImageField(upload_to="", null=True, blank=True, verbose_name="封面图")
    is_new = models.BooleanField(default=False, verbose_name="是否新品")
    is_hot = models.BooleanField(default=False, verbose_name="是否热销")

    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "商品"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name
```

### 设计goods.models.商品轮播图表

```python
class GoodsImage(models.Model):
    """
    商品轮播图
    """
    goods = models.ForeignKey(Goods, verbose_name="商品", related_name="images", on_delete=models.DO_NOTHING)
    image = models.ImageField(upload_to="", verbose_name="图片", null=True, blank=True)
    image_url = models.CharField(max_length=300, null=True, blank=True, verbose_name="图片url")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "商品轮播图"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.goods.name
```

### 设计goods.models.首页轮播商品表

```python
class Banner(models.Model):
    """
    轮播的商品
    """
    goods = models.ForeignKey(Goods, verbose_name="商品", on_delete=models.CASCADE)
    image = models.ImageField(upload_to="banner", verbose_name="轮播图片")
    index = models.IntegerField(default=0, verbose_name="轮播顺序")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "轮播商品"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.goods.name
```

