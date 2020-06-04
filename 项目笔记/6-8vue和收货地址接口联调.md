```javascript
//添加收货地址
export const addAddress = params => {return axios.post(`${local_host}/address/`, params)}

//删除收货地址
export const delAddress = addressId => {return axios.delete(`${local_host}/address/`+addressId+'/')}

//修改收货地址
export const updateAddress = (addressId, params) => {return axios.patch(`${local_host}/address/`+addressId+'/', params)}

//获取收货地址
export const getAddress = () => {return axios.get(`${local_host}/address/`)}
```

