## 修改token认证的全局配置

```python
# settings.py 注释token全局认证,因为公开数据,不论用户是否登录,或者token过去都应该可以获取到数据,如果全局验证,就会将没有token的请求拦截下来,这样是不合适的
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        # 'rest_framework.authentication.TokenAuthentication',  # 全局token认证
    ]
}
# 将token认证配置到views.py,哪个接口需要认证,就配置哪个
from rest_framework.authentication import TokenAuthentication

class GoodsListViewSet(mixins.ListModelMixin, viewsets.GenericViewSet):
	......
	# authentication_classes = (TokenAuthentication, )  # token认证配置
	......

```