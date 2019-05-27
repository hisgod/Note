# 注解

## @Inject



## @Binds

* **都是提供对象的作用**
* **`@Provides`需要实现方法**
* **`@Binds`只需要知道谁来提供就行**

```
@Module
public interface ViewModelModule {
    @Binds
    ViewModelProvider.Factory bindViewModelFactory(ViewModelFactory factory);
}
```

#  全局单例ViewModelProvider.Factory

# 入门

> 实例化对象

* `@Inject`告知dagger可以实例化此对象

```java
public class Son {
    @Inject
    public Son() {
        Log.e("HLP", "儿子");
    }
}
```

>构建注入器

使用`@Component`注解，标识器件，可以理解为注射器，保存需要注入到某个类的信息
```java
@dagger.Component
public interface Component {
    //可以将@Inject生成的对象注入到MainActivity中
    void inject(MainActivity mainActivity);
}
```

>注入目标

```java
public class MainActivity extends AppCompatActivity {
    @Inject
    Son son;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    
        //创建注射器
        DaggerComponent.create().inject(this);

        Log.e("HLP", son.toString());
    }
}
```

# 解析

## `@Component`

* **生成以Dagger开头的DaggerXXX文件**

```kotlin
@Component
interface AppComponent {

}
```



# module

>概述

使用`@Module`标记的类，称为模块类，为了方便的提供注入对象，由于某些类的构造函数不易修改，所以提供这个注解是为了方便的提供注入的对象

>注入对象

注入对象没有使用任何注解
```
public class Son {
    public Son() {
        Log.e("HLP", "儿子");
    }
}
```

>注入模块

```
@dagger.Module
public class Module {
    //标记提供对象
    @Provides
    public Son provideSon() {
        return new Son();
    }
}
```

>注射器添加Module模块
```
//将module模块加入到注射器
@dagger.Component(modules = Module.class)
public interface Component {
    //可以将@Inject生成的对象注入到MainActivity中
    void inject(MainActivity mainActivity);
}
```

>注入对象

```
public class MainActivity extends AppCompatActivity {
    @Inject
    Son s;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //创建注射器，需要传入module需要注入的对象
        DaggerComponent.builder().module(new Module()).build().inject(this);

        Log.e("HLP", s.toString());
    }
}
```

#Activity

>@Module

提供第三方库对象的类
```
@Module
public class AppModule {
    @Singleton
    @Provides
    public Person getPerson() {
        return new Person();
    }
}
```

>ActivityModule模块

提供Activity的对象
```
@Module
public abstract class ActivityModule {
    @ContributesAndroidInjector
    public abstract MainActivity mainActivity();
}
```

>@Component

将多个module模块一并注入到指定的地方
```
/**
 * Singleton
 * <p>
 * 因为AppModule.class添加了单例模式对象，s所以Component也需要添加
 */
@Singleton
@dagger.Component(
        modules = {
                AndroidInjectionModule.class,
                AppModule.class,    //提供第三方库对象的类
                ActivityModule.class    //提供Activity对象的类
        }
)
public interface AppComponent {
    void inject(MyApplication application);
}
```

>Application中进行全局注入

既然全局注入了，每次新增添的Activity都需要提供对象，否则会报错
```
/**
 * 需要实现HasActivityInjector接口
 * 重写方法，把DispatchingAndroidInjector<Activity>对象返回给系统
 */
public class MyApplication extends Application implements HasActivityInjector {
    @Inject
    DispatchingAndroidInjector<Activity> injector;

    @Override
    public void onCreate() {
        super.onCreate();
        DaggerAppComponent.create().inject(this);//注入
        registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {
            @Override
            public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
                AndroidInjection.inject(activity);  //注入到Activity
            }

            @Override
            public void onActivityStarted(Activity activity) {

            }

            @Override
            public void onActivityResumed(Activity activity) {

            }

            @Override
            public void onActivityPaused(Activity activity) {

            }

            @Override
            public void onActivityStopped(Activity activity) {

            }

            @Override
            public void onActivitySaveInstanceState(Activity activity, Bundle outState) {

            }

            @Override
            public void onActivityDestroyed(Activity activity) {

            }
        });
    }

    @Override
    public AndroidInjector<Activity> activityInjector() {
        return injector;
    }
}

```

#Fragment

>被注入对象

需要把Person对象创建好，然后通过Dagger2注入到Fragment，在Fragment里可以通过Dagger2拿到Person的实例
```
public class Person {

}
```

>提供第三方对象


使用@Module注解标记，表名这个类是，提供第三方库的对象，使用Dagger2注入到寄主对象中
```
@Module
public class AppModule {
    @Singleton
    @Provides
    public Person getPerson() {
        return new Person();
    }
}
```

>提供Activity对象

提供对应的Activity对象，每次新创建的Activity都需要在这以同样的格式进行添加，这样才能使用Dagger2往Activity里面进行注入对象
```
@Module
public abstract class ActivityModule {
    @ContributesAndroidInjector
    public abstract MainActivity mainActivity();
}
```

>提供Fragment对象

提供Fragment对象，每次新创建的Fragment都需要在这以同样的格式进行添加，提供Fragment对象，方便Dagger往Fragment中注入对象
```
@Module
public abstract class FragmentModule {
    @ContributesAndroidInjector
    public abstract HomeFragment homeFragment();
}
```

>注射器

将各个Module添加到注射器中，Dagger2会把它们一并注入到对应的Fragment
```
@Singleton
@dagger.Component(
        modules = {
                AndroidSupportInjectionModule.class,
                AppModule.class,    //提供第三方库对象的类
                ActivityModule.class,    //提供Activity对象的类
                FragmentModule.class
        }
)
public interface AppComponent {
    void inject(MyApplication application);
}
```

>Activity代码
```
public class MainActivity extends AppCompatActivity implements HasSupportFragmentInjector {
    @Inject
    DispatchingAndroidInjector<Fragment> injector;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        HomeFragment fragment = new HomeFragment();
        getSupportFragmentManager().beginTransaction().replace(R.id.fl, fragment).commit();
    }

    @Override
    public AndroidInjector<Fragment> supportFragmentInjector() {
        return injector;
    }
}
```

>Fragment代码
```
public class HomeFragment extends Fragment implements Injectable {
    @Inject
    Person person;

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        TextView tv = new TextView(getContext());
        tv.setText("碎片");
        return tv;
    }

    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);

        Log.e("HLP", person.toString());
    }
}
```

>全局注入
```
public class MyApplication extends Application implements HasActivityInjector {
    @Inject
    DispatchingAndroidInjector<Activity> injector;

    @Override
    public void onCreate() {
        super.onCreate();
        DaggerAppComponent.create().inject(this);//注入
        registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {
            @Override
            public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
                if(activity instanceof HasSupportFragmentInjector){
                    AndroidInjection.inject(activity);  //注入到Activity
                }

                if(activity instanceof FragmentActivity){
                    //注入Fragment
                    AppCompatActivity appCompatActivity = (AppCompatActivity) activity;
                    appCompatActivity.getSupportFragmentManager().registerFragmentLifecycleCallbacks(new FragmentManager.FragmentLifecycleCallbacks() {
                        @Override
                        public void onFragmentCreated(@NonNull FragmentManager fm, @NonNull Fragment f, @Nullable Bundle savedInstanceState) {
                            super.onFragmentCreated(fm, f, savedInstanceState);
                            if (f instanceof Injectable) {
                                AndroidSupportInjection.inject(f);
                            }
                        }
                    }, true);
                }
            }

            @Override
            public void onActivityStarted(Activity activity) {

            }

            @Override
            public void onActivityResumed(Activity activity) {

            }

            @Override
            public void onActivityPaused(Activity activity) {

            }

            @Override
            public void onActivityStopped(Activity activity) {

            }

            @Override
            public void onActivitySaveInstanceState(Activity activity, Bundle outState) {

            }

            @Override
            public void onActivityDestroyed(Activity activity) {

            }
        });


    }

    @Override
    public AndroidInjector<Activity> activityInjector() {
        return injector;
    }
}
```