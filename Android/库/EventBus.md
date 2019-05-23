# 简介

* **EventBus是一个Android端优化的publish/subscribe消息总线，简化了应用程序内组件间，组件与后台线程间的通讯**

# 发送普通事件

>事件源类

* **使用泛型，将数据源封装到EventData类中**
```
data class EventData<D>(var data: D)
```

>第一个页面

* **需要一开始就得注册EventBus**
* **自定义接收数据源的方法**
* **销毁页面需要解注册**
```java
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        EventBus.getDefault().register(this)

        btn.setOnClickListener {
            startActivity(Intent(this, SecondActivity::class.java))
        }
    }

    /**
     * 接收事件
     */
    @Subscribe(threadMode = ThreadMode.MAIN)
    fun recieve(data: EventData<*>) {
        val msg = data.data as String
        tv.text = msg
    }

    override fun onDestroy() {
        super.onDestroy()
        EventBus.getDefault().unregister(this)
    }
}
```

>第二个页面

* **发送事件，上个页面因为注册了EventBus，所以销毁这个页面，数据会返回到上个页面中**
```
class SecondActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_second)

        btn.setOnClickListener {
            //发送事件
            EventBus.getDefault().post(EventData<String>("前一页数据"))
            finish()
        }
    }
}
```

---

#<center>发送粘性事件

>第一个页面
```
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        EventBus.getDefault().register(this)

        btn.setOnClickListener {
            EventBus.getDefault().postSticky(EventData<String>("来自首页数据"))
            startActivity(Intent(this, ThirdActivity::class.java))
        }
    }

    /**
     * 接收事件
     */
    @Subscribe(threadMode = ThreadMode.MAIN, sticky = false)
    fun recieve(data: EventData<*>) {
        val msg = data.data as String
        tv.text = msg
    }

    override fun onDestroy() {
        super.onDestroy()
        EventBus.getDefault().unregister(this)
    }
}
```

>第二个页面

* **粘性事件允许先发送事件，后注册，但也需要解注册**
* **粘性事件是全局可接收到的数据，发送之前需要给数据添加唯一标识**
* **粘性事件接收方法需要新增`sticky`参数**
```
class ThirdActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_third)

        EventBus.getDefault().register(this)
    }

    @Subscribe(threadMode = ThreadMode.MAIN, sticky = true)
    fun getData(data: EventData<*>) {
        val msg = data.data as String
        tv.text = msg
    }

    override fun onDestroy() {
        super.onDestroy()
        EventBus.getDefault().unregister(this)
    }
}
```