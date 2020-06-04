### users.views.py

```python
from random import choice
from rest_framework.mixins import CreateModelMixin
from rest_framework import viewsets, status
from .serializers import SmsSerializer, UserRegSerializer
from utils.yuntongxun.SendTemplateSMS import ccp  # 发送短信验证码的api
from MxShop.settings import SMS_CODE_EXPIRES_SECONDS  # 验证码过期时间


class SmsCodeViewSet(CreateModelMixin, viewsets.GenericViewSet):
    """
    发送短信验证码
    """
    serializer_class = SmsSerializer

    def generate_code(self):
        """
        生成6位数字的验证码
        :return:
        """

        seeds = "1234567890"
        random_str = []
        for i in range(6):
            random_str.append(choice(seeds))
        return "".join(random_str)

    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        mobile = serializer.validated_data["mobile"]
        # 生成短信验证码
        # sms_code = "%06d" % random.randint(1, 1000000)
        sms_code = self.generate_code()
        print(sms_code)
        sms_status = ccp.sendTemplateSMS(mobile, [sms_code, SMS_CODE_EXPIRES_SECONDS / 60], 1)
        print("sms_status-type:%s" % type(sms_status))
        if not sms_status:
            return Response({
                "mobile": "发送失败"
            }, status=status.HTTP_400_BAD_REQUEST)
        else:
            code_record = VerifyCode(code=sms_code, mobile=mobile)
            code_record.save()
            return Response({
                "mobile": mobile
            }, status=status.HTTP_201_CREATED)
```

### users.serializers.py

```python
import re
from rest_framework import serializers
from django.contrib.auth import get_user_model
from rest_framework.validators import UniqueValidator

from MxShop.settings import REGEX_MOBILE
from datetime import datetime, timedelta
from .models import VerifyCode

User = get_user_model()


class SmsSerializer(serializers.Serializer):
    mobile = serializers.CharField(max_length=11)

    def validate_mobile(self, mobile):
        """
        验证手机号码
        :param data:
        :return:
        """

        # 手机是否注册
        if User.objects.filter(mobile=mobile).count():
            raise serializers.ValidationError("用户已存在")

        # 验证手机号码是否合法
        if not re.match(REGEX_MOBILE, mobile):
            raise serializers.ValidationError("手机号码非法")

        # 验证发送频率
        one_mintes_ago = datetime.now() - timedelta(hours=0, minutes=1, seconds=0)
        if VerifyCode.objects.filter(add_time__gt=one_mintes_ago, mobile=mobile).count():
            raise serializers.ValidationError("距离上一次发送未超过60s")

        return mobile

```

