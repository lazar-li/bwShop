## django-cors-headers 跨域访问配置

### 安装

```
pip install django-cors-headers
```

### 配置settings

```python
INSTALLED_APPS = [
    ......
	'corsheaders',
    .....
]

MIDDLEWARE  = [   ＃或Django <1.10上的MIDDLEWARE_CLASSES 
    ... 
    ' corsheaders.middleware.CorsMiddleware '，
     ' django.middleware.common.CommonMiddleware '，
     ... 
]

CORS_ORIGIN_ALLOW_ALL = True

```

### 修改前端项目中src.api.api.js

```javascript
// 添加一个后端项目url变量
let local_host = 'http://127.0.0.1:8081';

//获取商品类别信息
export const getCategory = params => {
  if('id' in params){
    return axios.get(`${local_host}/categorys/`+params.id+'/');
  }
  else {
    return axios.get(`${local_host}/categorys/`, params);
  }
};
```

