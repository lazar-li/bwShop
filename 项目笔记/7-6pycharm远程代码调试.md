![image-20191231143136444](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191231143136444.png)

![image-20191231143706233](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191231143706233.png)

![image-20191231143848139](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191231143848139.png)

![image-20191231144023363](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191231144023363.png)

## 服务器配置

### 安装python3

### 安装MySQL

### 安装uwsgi+Nginx

### 安装虚拟环境

` yum install python-setuptools python-devel`

` pip3 install virtualenvwrapper`

https://www.cnblogs.com/huanjoyous/p/11119676.html

### 创建并进入虚拟环境

mkv MxShop & workon MxShpo 

### 安装项目依赖包

` pip3 install mysqlclient`

` pip3 install -i https://pypi.douban.com/simple django==2.1`

` pip3 install -i https://pypi.douban.com/simple  `

` pip3 install -i https://pypi.douban.com/simple pillow`

`本地:pip3 freeze > requirements.txt`

` 服务器同级目录:pip3 install -r requirements.txt `

` pip install https://codeload.github.com/sshwsfc/xadmin/zip/django2`

### 远程启动项目

`python manage.py runserver 0.0.0.0:8000`

### 配置实现通过pycharm调试服务器代码

![image-20191231163913781](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191231163913781.png)

![image-20191231164525214](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191231164525214.png)

![image-20191231164637508](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191231164637508.png)

![image-20191231170147265](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191231170147265.png)

