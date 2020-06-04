```python
from django.views.generic.base import View
# from django.views.generic import ListView

from apps.goods.models import Goods


class GoodsListView(View):
    def get(self, request):
        """
        通过django的view实现商品列表页
        :param request:
        :return:
        """
        json_list = []
        goods = Goods.objects.all()[:10]
        # for good in goods:
        #     json_dict = {}
        #     json_dict["name"] = good.name
        #     json_dict["category"] = good.category.name
        #     json_dict["market_price"] = good.market_price
        #     json_list.append(json_dict)
        # from django.forms.models import model_to_dict
        # for good in goods:
        #     # 将所有字段提取出来,并转换成字典
        #     json_dict = model_to_dict(good)
        #     json_list.append(json_dict)

        import json
        from django.core import serializers
        # serializers的作用是将models的数据序列化成指定的数据格式
        json_data = serializers.serialize("json", goods)
        json_data = json.loads(json_data)  # loads是将json格式字符串转换为python格式字
        from django.http import HttpResponse, JsonResponse
        # import json
        return JsonResponse(json_data, safe=False)
```

