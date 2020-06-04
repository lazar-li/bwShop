## goods.views.py

```python
from .serializers import GoodsSerializer
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

from .models import Goods

# Create your views here.


class GoodsListView(APIView):
    """
    List all goods
    """
    def get(self, request, format=None):
        goods = Goods.objects.all()[:10]
        goods_serializer = GoodsSerializer(goods, many=True)
        return Response(goods_serializer.data)
	# post实现添加商品,类似Django自带的表单功能
    def post(self, request, format=None):
        serializer = GoodsSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

## serializers.py和forms.py类似

**serializers继承的是rest_framework**

**forms继承的是django.forms.Form**

## forms.py

```python
class MessageBoardForm(forms.Form):    
    title = forms.CharField(max_length=3,min_length=2,label='标题')
    content = forms.CharField(widget=forms.Textarea,label='内容')
    email = forms.EmailField(label='邮箱')
    reply = forms.BooleanField(required=False(表示可传可不传),label='回复')
```



## serializers.py

```python
from rest_framework import serializers


class GoodsSerializer(serializers.Serializer):
    name = serializers.CharField(required=True, max_length=100)
    click_num = serializers.IntegerField(default=0)

```

**serializers.Serializer实现添加验证**

```python
class GoodsSerializer(serializers.Serializer):
    name = serializers.CharField(required=True, max_length=100)
    click_num = serializers.IntegerField(default=0)
    goods_front_image = serializers.ImageField()

    def create(self, validated_data):
        """
        Create and return a new `Snippet` instance, given the validated data.
        """
        return Goods.objects.create(**validated_data)

```

**serializers.ModelSerializer实现序列化和django的表单验证类似**

### serializers.py

```python
from rest_framework import serializers

from goods.models import Goods, GoodsCategory


class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = GoodsCategory
        fields = "__all__"


class GoodsSerializer(serializers.ModelSerializer):
    category = CategorySerializer()

    class Meta:
        model = Goods
        fields = "__all__"
```

### forms.py

```python
from django import forms
class BookForm(forms.ModelForm):    
    class Meta:        
    model = Book        
    fields = '__all__'(获取所有字段)
    #fields = [‘title’,‘page’](只获取title和page字段)
    #exclude = [‘price’](获取除了price之外的所有字段)
```

