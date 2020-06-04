### 第三方登录流程图

![img](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1578200791223&di=9ab662d363efd66471007084334492fe&imgtype=jpg&src=http%3A%2F%2Fimg0.imgtn.bdimg.com%2Fit%2Fu%3D3393543755%2C976104987%26fm%3D214%26gp%3D0.jpg)

## 微博登录

### 微博开放平台

https://open.weibo.com/apps/3067025662

### utils新建weibo_login

```python
"""
微博登录模块
"""


def get_auth_url():
    weibo_auth_url = "https://api.weibo.com/oauth2/authorize"
    redirect_url = "http://172.16.65.8:8081/index/"
    auth_url = weibo_auth_url + "?client_id={client_id}&redirect_uri={re_url}".format(client_id=3067025662,
                                                                                      re_url=redirect_url)
    print(auth_url)


def get_access_token(code="78e7144007c3580a77cc3298540a1e2f"):
    access_token_url = "https://api.weibo.com/oauth2/access_token"
    import requests
    re_dict = requests.post(access_token_url, data={
        "client_id": "3067025662",
        "client_secret": "95f74303bf95323494e8efd5f145bfdc",
        "grant_type": "authorization_code",
        "code": code,
        "redirect_uri": "http://172.16.65.8:8081/index/"
    })
    print(re_dict.text)  # {"access_token":"2.00lc9XLGqTvY2D297e69bfd5fWhxHE","remind_in":"157679999","expires_in":157679999,"uid":"5667311547","isRealName":"false"}


def get_user_info(access_token="", uid=""):
    user_url = "https://api.weibo.com/2/users/show.json?access_token={token}&uid={uid}".format(token=access_token,
                                                                                               uid=uid)
    print(user_url)


if __name__ == '__main__':
    # get_auth_url()
    # get_access_token(code="feb54794e037c476cfef94750b4b2d1e")
    get_user_info(access_token="2.00lc9XLGqTvY2D297e69bfd5fWhxHE", uid="5667311547")
```