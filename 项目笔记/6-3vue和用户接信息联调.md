### 修改前端api.js

```javascript
//获取用户信息
export const getUserDetail = () => { return axios.get(`${local_host}/users/1/`) }

```

