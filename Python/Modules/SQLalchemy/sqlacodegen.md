``` bash
pip install sqlacodegen
```



### 导出整个数据库

``` bash
sqlacodegen --outfile=models.py mysql://root:guess@192.168.1.250:3306/test

# 账号：root
# 密码：guess
# 数据库服务器地址：192.168.1.250
# 数据库端口：3306
# 数据库名：test
```



### 导出某些表

``` bash
sqlacodegen --outfile=models.py mysql://root:guess@192.168.1.250:3306/test --tables teacher,student

要导出的表：teacher、student
```

