### 创建app

![image-20191221171604938](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191221171604938.png)

```
python manage.py startapp appname
```

### 设计users.models

Django内置的`User`对象，已经包含了一些主要的属性，如username、password、email等，但实际情况可能还需要昵称、头像等其他属性，仅仅使用内置的User属性是不够的。

通过使用`AbstractUser`可以对User进行扩展使用，添加用户自定义的属性。

User模型源码如下。

```python
class User(AbstractUser):
    class Meta(AbstractUser.Meta):
        swappable = 'AUTH_USER_MODEL'
```


由此可见，User对AbstractUser仅仅是继承，没有进行任何的扩展。所以我们继承AbstractUser可以获得User的所有特性。

```python
# users.models.py
from datetime import datetime
from django.db import models
from django.contrib.auth.models import AbstractUser

# Create your models here.


class UserProfile(AbstractUser):
    """
    用户
    """
    name = models.CharField(max_length=30, null=True, blank=True, verbose_name="姓名")  # 姓名
    birthday = models.DateField(null=True, blank=True, verbose_name="出生年月")  # 生日
    mobile = models.CharField(max_length=11, verbose_name="电话")  # 手机号
    gender = models.CharField(max_length=6, choices=(("male", u"男"), ("female", u"女")), default="female", verbose_name="性别")  # 性别
    email = models.CharField(max_length=100, null=True, blank=True, verbose_name="邮箱")  # 邮箱

    class Meta:
        verbose_name = "用户"
        verbose_name_plural = "用户"

    def __str__(self):
        return self.username


class VerifyCode(models.Model):
    """
    短信验证码
    """
    code = models.CharField(max_length=10, verbose_name="验证码")
    mobile = models.CharField(max_length=11, verbose_name="电话")  # 手机号
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "短信验证码"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.code
```

```python
# settings.py添加
AUTH_USER_MODEL = 'users.UserProfile'
```

