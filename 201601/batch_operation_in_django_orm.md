###Django ORM 中的批量操作###

在Hibenate中，通过批量提交SQL操作，部分地实现了数据库的批量操作。但在Django的ORM中的批量操作却要完美得多，真是一个惊喜。

####数据模型定义####
首先，定义一个实例使用的django数据库模型Product，只是象征性地定义了两个字段name和price。

    from django.db import models
    
    class Product(models.Model):
        name = models.CharField(max_length=200)
        price = models.DecimalField(max_digits=10, decimal_places=2)

####批量插入数据####
批量插入数据时，只需先生成个一要传入的Product数据的列表，然后调用`bulk_create`方法一次性将列表中的数据插入数据库。

    product_list_to_insert = list()
    for x in range(10):
        product_list_to_insert.append(Product(name='product name ' + str(x), price=x))
    Product.objects.bulk_create(product_list_to_insert)

####批量更新数据####
批量更新数据时，先进行数据过滤，然后再调用`update`方法进行一次性地更新。下面的语句将生成类似`update...where...`的SQL语句。

    Product.objects.filter(name__contains='name').update(name='new name')

####批量删除数据####
批量更新数据时，先是进行数据过滤，然后再调用`delete`方法进行一次性地删除。下面的语句将生成类似`delete from...where...`的SQL语句。

    Product.objects.filter(name__contains='name query').delete()

如果是通过运行普通Python脚本的方式而不是在view中调用上述的代码的，别忘了先在脚本中进行django的初始化：

    import os
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "testproject.settings")

    import django
    django.setup()

Hibernate的所谓“批量操作”中，对每一个实体的更新操作，都会生成一条update语句，然后只是把好几个update语句一次性提交给数据库服务器而已。对实体的删除操作也是一样。

Django ORM中的批量操作的实现更接近于SQL操作的体验，运行效率也会比Hibernate中的实现更加高效。