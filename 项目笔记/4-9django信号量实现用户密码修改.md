### users.views.py

```python
class UserViewSet(CreateModelMixin, viewsets.GenericViewSet):
    """
    用户
    """
    serializer_class = UserRegSerializer
    queryset = User.objects.all()

    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        user = self.perform_create(serializer)
        re_dict = serializer.data
        payload = jwt_payload_handler(user)
        re_dict["token"] = jwt_encode_handler(payload)
        re_dict["name"] = user.name if user.name else user.username

        headers = self.get_success_headers(serializer.data)
        return Response(re_dict, status=status.HTTP_201_CREATED, headers=headers)

    def perform_create(self, serializer):
        return serializer.save()
```

### users.serializers.py

```python
class UserRegSerializer(serializers.ModelSerializer):
    code = serializers.CharField(max_length=6, write_only=True, min_length=6,
                                 label="验证码",
                                 error_messages={
                                    "blank": "请输入验证码",
                                    "required": "请输入验证码",
                                    "max_length": "验证码格式错误",
                                    "min_length": "验证码格式错误"
                                }, help_text="验证码")

    username = serializers.CharField(label="用户名", required=True, allow_blank=False,
                                     validators=[UniqueValidator(queryset=User.objects.all(), message="用户已经存在")])
    password = serializers.CharField(
        style={'input_type': 'password'},
        label="密码",
        write_only=True
    )

    # def create(self, validated_data):
    #     user = super(UserRegSerializer, self).create(validated_data=validated_data)
    #     user.set_password(validated_data["password"])
    #     user.save()
    #     return user

    def validate_code(self, code):
        verify_records = VerifyCode.objects.filter(mobile=self.initial_data["username"]).order_by("add_time")
        if verify_records:
            last_recodrds = verify_records[0]
            print(last_recodrds.add_time)
            five_mintes_ago = datetime.now() - timedelta(hours=0, minutes=5, seconds=0)
            if five_mintes_ago > last_recodrds.add_time:
                raise serializers.ValidationError("验证码过期")

            if last_recodrds.code != code:
                raise serializers.ValidationError("验证码错误")

        else:
            raise serializers.ValidationError("验证码错误")

    def validate(self, attrs):
        attrs["mobile"] = attrs["username"]
        del attrs["code"]
        return attrs

    class Meta:
        model = User
        fields = ("username", "code", "mobile", "password")
```

### users.signals.py

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from rest_framework.authtoken.models import Token
from django.contrib.auth import get_user_model

User = get_user_model()


@receiver(post_save, sender=User)
def create_auth_token(sender, instance=None, created=False, **kwargs):
    if created:
        password = instance.password
        instance.set_password(password)
        instance.save()

```

### users.apps.py

```python
from django.apps import AppConfig


class UsersConfig(AppConfig):
    name = 'users'
    verbose_name = '用户'

    def ready(self):
        import users.signals
```