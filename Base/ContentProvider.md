# ContentProvider的完全解析
ContentProvider，内容提供者，可以类比于网站， 我们可以从其获得数据。通过ContentProvider，我们提供了一个通用的获取数据的方式。而这些数据可以是存储在文件中， 数据库中，可以是SharedPreferences，可以是多个文件。
![](http://img.blog.csdn.net/20160503151431791)
## 如何使用
在创建ContentProvider前，首先要实现底层的数据源，数据源包括数据库、文件系统或网络等，然后继承ContentProvider类中实现基本数据操作的接口函数。调用者不能直接调用ContentProvider的接口函数，需要通过ContentResolver对象，通过URI间接调用ContentProvider。
先以一个demo来了解ContentProvider如何使用。
```

```
## 使用步骤总结
创建ContentProvider， 实现5个接口的代码逻辑。通过类似于网站连接地址的东西Uri，我们使用ContentResolver来获取数据。

## ContentProvider的屏蔽性
使用ContentProvider屏蔽了数据的实际来源。我们只需要通过ContentResolver，传一个URI，Android系统根据传入的uri，系统替我们处理这个查询的ContentProvider， 然后初始化该ContentProvider所需要的资源，这些都是由Android系统完成的，我们无需要处理。

## URI
形式如下：
```content://<authority>/<data_path>/<id>```
- content:// 是通用前缀，表示该UIR用于ContentProvider定位资源。
- authority 是授权者名称，通常决定具体由哪一个ContentProvider来提供。可与网络访问中的域名做类比理解
- data_path 指明数据集， 如果该ContentProvider只有1个数据集，则可以省略。
- id 是数据编号，用来唯一确定数据集中哪一条数据。

## 涉及到的方法
- onCreate
一般用来初始化底层数据集和建立数据连接操作
- getType
用来返回指定URI的MIME类型。分两种1. vnd.android.cursor.dir开头，表示返回多条记录，是一个集合
2. vnd.android.cursor.item开头,表示返回单挑记录。
- insert
- delete
- update
- query