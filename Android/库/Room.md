# 组成

>DataBase(@Database)

* **包含数据库持有者，作为应用程序持久关系数据库的基础连接的主要访问点**
* **继承抽象类RoomDatabase**
* **包括数据库关联，实体类列表**
* **包含一个具有0个参数的抽象方法，并返回带标注@Dao**
* **在运行时，Database通过调用Room.databaseBuilder()或获取实例Room.inMemoryDatabaseBuilder()**

>Entity(@Entity)

* **表示数据库的表**

>Dao(@Dao)

* **包含访问数据库的方法**

---

#<center>Entity类

##<center>实体类常用形式

>标记主键

* **每个Entity都必须包含一个主键**
```
@Entity
data class Student(@PrimaryKey var id: Int, var name: String, var age: Int)
```

>设置主键自增长

* **`autoGenerate`可以设置主键自动增长**
```
@Entity
data class Student(@PrimaryKey(autoGenerate = true) var id: Int, var name: String, var age: Int)
```

>复合主键

* **复合主键使用`primaryKeys`参数**
* **复合主键：考虑到name也许有重复的，所以将id和name作为表的复合主键**
```
@Entity(primaryKeys = ["id", "name"])
data class Student(var id: Int, var name: String, var age: Int)
```

>重命名表名

* **`tableName`参数重命名表名**
```
@Entity(tableName = "users")
data class Student(@PrimaryKey(autoGenerate = true) var id: Int, var name: String, var age: Int)
```

>重命名列名

* **`@ColumnInfo`用于重命名列名**
```
@Entity(tableName = "users")
data class Student(
    @PrimaryKey(autoGenerate = true)
    @ColumnInfo(name = "uid")
    var id: Int,
    var name: String,
    var age: Int
)
```

>忽略字段

* **`@Ignore`忽略字段，被忽略的字段不会在表中出现**
```
@Entity(tableName = "users")
data class Student(
    @PrimaryKey(autoGenerate = true)
    @ColumnInfo(name = "uid")
    var id: Int,
    var name: String,
    @Ignore
    var age: Int
)
```

>忽略字段集

* **`ignoredColumns`参数用于忽略多个字段**
```
@Entity(ignoredColumns = ["age"])
data class Student(
    @PrimaryKey(autoGenerate = true)
    @ColumnInfo(name = "uid")
    var id: Int,
    var name: String,
    var age: Int
)
```

##<center>提供表搜索功能

>没深入研究，待定

##<center>AutoValue对象
>没深入研究，待定

##<center>设置外键约束

* **`foreignKeys`参数设置外键约束**
* **`entity`指定关联的表**
* **`parentColumns`主表的主键**
* **`childColumns`从表的主键**
```
@Entity(foreignKeys = arrayOf(ForeignKey(
            entity = User::class,
            parentColumns = arrayOf("id"),
            childColumns = arrayOf("user_id"))
       )
)
data class Book(
    @PrimaryKey var bookId: Int,
    var title: String?,
    @ColumnInfo(name = "user_id") var userId: Int
)
```

##<center>嵌套对象

* **`@Embedded`会将对象的每个属性进行分解，并创建列名**
* **每个对象不能包含重复属性名，否则编译器不能编译通过**
```
data class Address(
    var street: String?,
    var state: String?,
    var city: String?,
    @ColumnInfo(name = "post_code") var postCode: Int
)

@Entity
data class User(
    @PrimaryKey var id: Int,
    var firstName: String?,
    @Embedded var address: Address?
)
```

---

#<center>数据库视图

>没深入了解，2.1.0后的特性了

---

#<center>DAO类

>概述

* **DAO可以是接口，也可以是抽象类，如果是抽象类，可以选择使用一个构造函数`RoomDatabase`作为唯一参数，Room在编译时创建每个DAO实现类**

##<center>插入数据

>插入数据

* **如果方法接收参数可以返回单个`long`类型，如果返回数组则使用`long[]`，如果返回集合则使用`List<Long>`**
```
@Dao
interface StudentDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    fun add(student: Student)
}
```

>发生冲突处理(onConflict参数)

* **`OnConflictStrategy.ABORT`默认方式，在冲突时回滚事务**
* **`OnConflictStrategy.REPLACE`新数据替换旧数据**
* **`OnConflictStrategy.IGNORE`保持现有数据**

##<center>更新数据

* **方法可返回`int`类型，表示更新多少条数据**
```
@Dao
interface MyDao {
    @Update
    fun updateUsers(vararg users: User)
}
```

##<center>删除数据

* **方法可返回`int`类型，表示删除多少条数据**
```
@Dao
interface MyDao {
    @Delete
    fun deleteUsers(vararg users: User)
}
```

##<center>查询数据

>简单查询
```
@Dao
interface MyDao {
    @Query("SELECT * FROM user")
    fun loadAllUsers(): Array<User>
}
```

###<center>方法参数作为查询条件参数

* **使用`:`引用方法的参数作为查询条件**
```
@Dao
interface MyDao {
    @Query("SELECT * FROM user WHERE age > :minAge")
    fun loadAllUsersOlderThan(minAge: Int): Array<User>
}
```

>还有很多没做笔记

#<center>迁移数据库