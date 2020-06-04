1. ![image-20191222162438858](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191222162438858.png)

2. ![image-20191222162606722](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191222162606722.png)

3. makemigrations不会操作数据库,只是生成了操作数据库的.py文件![image-20191222162814710](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191222162814710.png)

4. migrate将创建好的migrations文件生成数据表![image-20191222163005856](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191222163005856.png)

   ![image-20191222163218336](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191222163218336.png)

   **如果修改了数据表的结构,再执行了makemigrations这条命令后,新生成的migrations文件有时候会遇到执行migrate命令,数据结构并没有发生改变,这是django_migrations表内已经将生成的migrations文件的执行记录添加到了表内,所以执行migrate没有效果**

   1. 首先将修改的表删除
   2. 再将django_migrations表中和修改的表有关的信息删除
   3. 然后再执行migrate命令就可以了

