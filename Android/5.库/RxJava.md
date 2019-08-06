# 可观察者对象



# 创建操作符

## create()

```java
        Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) throws Exception {
                emitter.onNext("数据1");
            }
        }).subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                Log.e("HLP", s);
            }
        }, new Consumer<Throwable>() {
            @Override
            public void accept(Throwable throwable) throws Exception {
                Log.e("HLP", throwable.getMessage());
            }
        });
```

| 事件种类     | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| onNext()     | 发送该事件时，观察者会回调onNext()方法                       |
| onError()    | 发送该事件时，观察者会回调 onError() 方法，当发送该事件之后，其他事件将不会继续发送 |
| onComplete() | 发送该事件时，观察者会回调 onComplete() 方法，当发送该事件之后，其他事件将不会继续发送 |

## just

* **创建被观察者并发送多条数据，最多只能10条**

```
        Observable.just("数据1","数据2")
        .subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                Log.e("HLP", s);
            }
        }, new Consumer<Throwable>() {
            @Override
            public void accept(Throwable throwable) throws Exception {
                Log.e("HLP", throwable.getMessage());
            }
        });
```

## fromArray

* **发送多条数据，可以超过10条数据**
* **可以传入数组**

```
        String[] data = new String[]{"数据1", "数据2"};
        Observable.fromArray(data)
                .subscribe(new Consumer<String>() {
                    @Override
                    public void accept(String s) throws Exception {
                        Log.e("HLP", s);
                    }
                }, new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable throwable) throws Exception {
                        Log.e("HLP", throwable.getMessage());
                    }
                });
```

## fromCallable

* **这里的 Callable 是 java.util.concurrent 中的 Callable，Callable 和 Runnable 的用法基本一致，只是它会返回一个结果值，这个结果值就是发给观察者的**

```
        Observable.fromCallable(new Callable<String>() {
            @Override
            public String call() throws Exception {
                return "数据1";
            }
        }).subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                Log.e("HLP", s);
            }
        });
```

打印结果

```
E/HLP: 数据1
```

## fromFuture

* **参数中的 Future 是 java.util.concurrent 中的 Future，Future 的作用是增加了 cancel() 等方法操作 Callable，它可以通过 get() 方法来获取 Callable 返回的值**

```
        final FutureTask<String> task = new FutureTask<>(new Callable<String>() {
            @Override
            public String call() throws Exception {
                return "数据1";
            }
        });
        Observable.fromFuture(task).doOnSubscribe(new Consumer<Disposable>() {
            @Override
            public void accept(Disposable disposable) throws Exception {
                task.run();
            }
        }).subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                Log.e("HLP", s);
            }
        });
```

打印结果

```
E/HLP: 数据1
```

## fromIterable

* **发送多条数据，传入集合**

```
        List<String> data = new ArrayList<>();
        data.add("数据1");
        data.add("数据1");
        Observable.fromIterable(data)
                .subscribe(new Consumer<String>() {
                    @Override
                    public void accept(String s) throws Exception {
                        Log.e("HLP", s);
                    }
                }, new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable throwable) throws Exception {
                        Log.e("HLP", throwable.getMessage());
                    }
                });
```

## defer

* **这个方法的作用就是直到被观察者被订阅后才会创建被观察者**

```
    //第1次对i赋值
    Integer i = 10;
        // 2. 通过defer 定义被观察者对象
        // 注：此时被观察者对象还没创建
        Observable<Integer> observable = Observable.defer(new Callable<ObservableSource<? extends Integer>>() {
            @Override
            public ObservableSource<? extends Integer> call() throws Exception {
                return Observable.just(i);
            }
        });

        //第2次对i赋值
        i = 15;

        //观察者开始订阅
        // 注：此时，才会调用defer（）创建被观察者对象（Observable）
        observable.subscribe(new Observer<Integer>() {

            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(Integer value) {
                Log.d("HLP", "接收到的整数是" + value);
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
```

## timer

* **当到指定时间后就会发送一个0L值给观察者**
* **第一个参数：时间 第二个：时间单位**

```
        Observable.timer(2, TimeUnit.SECONDS)
                .subscribe(new Observer<Long>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(Long aLong) {
                        Log.e("HLP", aLong + "");
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
```

## interval

* **每隔一段时间就会发送一个事件，这个事件是从0开始，不断增1的数字**
* **有些类似秒表**

```
        Observable.interval(2, TimeUnit.SECONDS)
                .subscribe(new Consumer<Long>() {
                    @Override
                    public void accept(Long aLong) throws Exception {
                        Log.e("HLP", aLong + "");
                    }
                });
                
//打印
E/HLP: 0
E/HLP: 1
E/HLP: 2
...
```

## intervalRange()

* **可以指定发送事件的开始值和数量，其他与 interval() 的功能一样**

```
        Observable.intervalRange(0, 5, 0, 1, TimeUnit.SECONDS)
                .subscribe(new Consumer<Long>() {
                    @Override
                    public void accept(Long aLong) throws Exception {
                        Log.e("HLP", aLong + "");
                    }
                });

//打印
E/HLP: 0
E/HLP: 1
E/HLP: 2
E/HLP: 3
E/HLP: 4
```

## range

* **同时发送一定范围的事件序列**

```
        Observable.range(0, 5)
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        Log.e("HLP", integer + "");
                    }
                });

//打印
E/HLP: 0
E/HLP: 1
E/HLP: 2
E/HLP: 3
E/HLP: 4
```

## rangeLong

* **作用与 range() 一样，只是数据类型为 Long**

```
        Observable.rangeLong(0, 5)
                .subscribe(new Consumer<Long>() {
                    @Override
                    public void accept(Long integer) throws Exception {
                        Log.e("HLP", integer + "");
                    }
                });
                
//打印
E/HLP: 0
E/HLP: 1
E/HLP: 2
E/HLP: 3
E/HLP: 4
```

## 其它

* **never()该方法创建的被观察者对象发送事件的特点：不发送任何事件**
* **empty()该方法创建的被观察者对象发送事件的特点：仅发送Complete事件，直接通知完成**
* **error()该方法创建的被观察者对象发送事件的特点：仅发送Error事件，直接通知异常,可自定义异常**

---

# 转换操作符

## map()

* **map 可以将被观察者发送的数据类型转变成其他的类型**

```
        Observable.just(1, 2, 3)
               //泛型1是原始类型，泛型2是转换后类型，并输出到观察者中
                .map(new Function<Integer, String>() {
                    @Override
                    public String apply(Integer integer) throws Exception {
                        return "参数" + integer;
                    }
                })
                .subscribe(new Observer<String>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(String s) {
                        Log.e("HLP", s);
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
```

## flatMap

* **这个方法可以将事件序列中的元素进行整合加工，返回一个新的被观察者**

```
        Observable.just(1, 2, 3)
                .flatMap(new Function<Integer, ObservableSource<String>>() {
                    @Override
                    public ObservableSource<String> apply(Integer integer) throws Exception {
                        return Observable.just("数据" + integer);
                    }
                }).subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(String o) {
                Log.e("HLP", o);
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
```

## concatMap

* **与flatMap的使用相同，区别在于flatMap在变换之后会生成一组新的数据序列，顺序与发送的可能会不同，而ConcatMap产生的顺序与发送的顺序一致**

```
        Observable.just(1, 2, 3)
                .concatMap(new Function<Integer, ObservableSource<String>>() {
                    @Override
                    public ObservableSource<String> apply(Integer integer) throws Exception {
                        return Observable.just("数据" + integer);
                    }
                })
                .subscribe(new Observer<String>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(String o) {
                        Log.e("HLP", o);
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
```

## Buffer

* **从需要发送的事件当中获取一定数量的事件，并将这些事件放到缓冲区当中一并发出**

* **buffer 有两个参数，一个是 count，另一个 skip。count 缓冲区元素的数量，skip 就代表缓冲区满了之后，发送下一次事件序列的时候要跳过多少元素。这样说可能还是有点抽象**

```
        Observable.just(1, 2, 3, 4, 5)
                .buffer(2, 1)
                .subscribe(new Observer<List<Integer>>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(List<Integer> integers) {
                        Log.d(TAG, "================缓冲区大小： " + integers.size());
                        for (Integer i : integers) {
                            Log.d(TAG, "================元素： " + i);
                        }
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
                
//输出
D/HLP: ================缓冲区大小： 2
D/HLP: ================元素： 1
D/HLP: ================元素： 2
D/HLP: ================缓冲区大小： 2
D/HLP: ================元素： 2
D/HLP: ================元素： 3
D/HLP: ================缓冲区大小： 2
D/HLP: ================元素： 3
D/HLP: ================元素： 4
D/HLP: ================缓冲区大小： 2
D/HLP: ================元素： 4
D/HLP: ================元素： 5
D/HLP: ================缓冲区大小： 1
D/HLP: ================元素： 5
```

## groupBy

* **将发送的数据进行分组，每个分组都会返回一个被观察者**

```
有些复杂，需要参考多个文档
```

## scan

* **将数据以一定的逻辑聚合起来**

```
        Observable.just(1, 2, 3, 4, 5)
                .scan(new BiFunction<Integer, Integer, Integer>() {
                    @Override
                    public Integer apply(Integer integer, Integer integer2) throws Exception {
                        Log.d(TAG, "====================apply ");
                        Log.d(TAG, "====================integer " + integer);
                        Log.d(TAG, "====================integer2 " + integer2);
                        return integer + integer2;
                    }
                })
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        Log.d(TAG, "====================accept " + integer);
                    }
                });
                
//输出
D/HLP: ====================accept 1
D/HLP: ====================apply 
D/HLP: ====================integer 1
D/HLP: ====================integer2 2
D/HLP: ====================accept 3
D/HLP: ====================apply 
D/HLP: ====================integer 3
D/HLP: ====================integer2 3
D/HLP: ====================accept 6
D/HLP: ====================apply 
D/HLP: ====================integer 6
D/HLP: ====================integer2 4
D/HLP: ====================accept 10
D/HLP: ====================apply 
D/HLP: ====================integer 10
D/HLP: ====================integer2 5
D/HLP: ====================accept 15
```

## window

* **发送指定数量的事件时，就将这些事件分为一组。window 中的 count 的参数就是代表指定的数量，例如将 count 指定为2，那么每发2个数据就会将这2个数据分成一组**

```
        Observable.just(1, 2, 3, 4, 5)
                .window(2)
                .subscribe(new Observer < Observable < Integer >> () {
                    @Override
                    public void onSubscribe(Disposable d) {
                    }

                    @Override
                    public void onNext(Observable < Integer > integerObservable) {
                        integerObservable.subscribe(new Observer < Integer > () {
                            @Override
                            public void onSubscribe(Disposable d) {
                            }

                            @Override
                            public void onNext(Integer integer) {
                                Log.d(TAG, "=====================integerObservable onNext " + integer);
                            }

                            @Override
                            public void onError(Throwable e) {
                            }

                            @Override
                            public void onComplete() {
                            }
                        });
                    }

                    @Override
                    public void onError(Throwable e) {
                    }

                    @Override
                    public void onComplete() {
                    }
                });
                
//打印:5个数据被分为3组
D/HLP: =====================integerObservable onNext 1
D/HLP: =====================integerObservable onNext 2
D/HLP: =====================integerObservable onNext 3
D/HLP: =====================integerObservable onNext 4
D/HLP: =====================integerObservable onNext 5

```

---

# 组合操作符

## concat

* **可以将多个观察者组合在一起，然后按照之前发送顺序发送事件。需要注意的是，concat() 最多只可以发送4个事件**

```
        Observable.concat(Observable.just(1, 2),
                Observable.just(3, 4),
                Observable.just(5, 6),
                Observable.just(7, 8))
                .subscribe(new Observer < Integer > () {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(Integer integer) {
                        Log.d(TAG, "================onNext " + integer);
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
                
//输出
D/HLP: ================onNext 1
D/HLP: ================onNext 2
D/HLP: ================onNext 3
D/HLP: ================onNext 4
D/HLP: ================onNext 5
D/HLP: ================onNext 6
D/HLP: ================onNext 7
D/HLP: ================onNext 8
```

## concatArray

* **与 concat() 作用一样，不过 concatArray() 可以发送多于 4 个被观察者**

```
        Observable.concatArray(Observable.just(1, 2),
                Observable.just(3, 4),
                Observable.just(5, 6),
                Observable.just(7, 8),
                Observable.just(9, 10))
                .subscribe(new Observer < Integer > () {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(Integer integer) {
                        Log.d(TAG, "================onNext " + integer);
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
                
//输出
D/HLP: ================onNext 1
D/HLP: ================onNext 2
D/HLP: ================onNext 3
D/HLP: ================onNext 4
D/HLP: ================onNext 5
D/HLP: ================onNext 6
D/HLP: ================onNext 7
D/HLP: ================onNext 8
D/HLP: ================onNext 9
D/HLP: ================onNext 10

```

## merge

* **这个方法月 concat() 作用基本一样，知识 concat() 是串行发送事件，而 merge() 并行发送事件**
* **从结果可以知道，只有等到第一个被观察者发送完事件之后，第二个被观察者才会发送事件**
* **mergeArray()与merge()的作用是一样的，只是它可以发送4个以上的被观察者，这里就不再赘述了**

```
Observable.merge(
Observable.interval(1, TimeUnit.SECONDS).map(new Function < Long, String > () {
    @Override
    public String apply(Long aLong) throws Exception {
        return "A" + aLong;
    }
}),
Observable.interval(1, TimeUnit.SECONDS).map(new Function < Long, String > () {
    @Override
    public String apply(Long aLong) throws Exception {
        return "B" + aLong;
    }
}))
    .subscribe(new Observer < String > () {
    @Override
    public void onSubscribe(Disposable d) {

    }

    @Override
    public void onNext(String s) {
        Log.d(TAG, "=====================onNext " + s);
    }

    @Override
    public void onError(Throwable e) {

    }

    @Override
    public void onComplete() {

    }
});

//concat打印
05-21 16:10:31.125 12801-12850/com.example.rxjavademo D/chan: =====================onNext B0
05-21 16:10:31.125 12801-12849/com.example.rxjavademo D/chan: =====================onNext A0
05-21 16:10:32.125 12801-12849/com.example.rxjavademo D/chan: =====================onNext A1
05-21 16:10:32.126 12801-12850/com.example.rxjavademo D/chan: =====================onNext B1
05-21 16:10:33.125 12801-12849/com.example.rxjavademo D/chan: =====================onNext A2
05-21 16:10:33.125 12801-12850/com.example.rxjavademo D/chan: =====================onNext B2
05-21 16:10:34.125 12801-12849/com.example.rxjavademo D/chan: =====================onNext A3
05-21 16:10:34.125 12801-12850/com.example.rxjavademo D/chan: =====================onNext B3
05-21 16:10:35.124 12801-12849/com.example.rxjavademo D/chan: =====================onNext A4
05-21 16:10:35.125 12801-12850/com.example.rxjavademo D/chan: =====================onNext B4
05-21 16:10:36.125 12801-12849/com.example.rxjavademo D/chan: =====================onNext A5
05-21 16:10:36.125 12801-12850/com.example.rxjavademo D/chan: =====================onNext B5
......

//merge打印
05-21 16:17:52.352 14597-14621/com.example.rxjavademo D/chan: =====================onNext A0
05-21 16:17:53.351 14597-14621/com.example.rxjavademo D/chan: =====================onNext A1
05-21 16:17:54.351 14597-14621/com.example.rxjavademo D/chan: =====================onNext A2
05-21 16:17:55.351 14597-14621/com.example.rxjavademo D/chan: =====================onNext A3
05-21 16:17:56.351 14597-14621/com.example.rxjavademo D/chan: =====================onNext A4
05-21 16:17:57.351 14597-14621/com.example.rxjavademo D/chan: =====================onNext A5
......
```

## concatArrayDelayError() & mergeArrayDelayError()

* **在 concatArray() 和 mergeArray() 两个方法当中，如果其中有一个被观察者发送了一个 Error 事件，那么就会停止发送事件，如果你想 onError() 事件延迟到所有被观察者都发送完事件后再执行的话，就可以使用  concatArrayDelayError() 和 mergeArrayDelayError()**

```java
        Observable.concatArray(Observable.create(new ObservableOnSubscribe<Integer>() {
            @Override
            public void subscribe(ObservableEmitter<Integer> e) throws Exception {
                e.onNext(1);
                e.onError(new NumberFormatException());
            }
        }), Observable.just(2, 3, 4))
                .subscribe(new Observer<Integer>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(Integer integer) {
                        Log.d(TAG, "===================onNext " + integer);
                    }

                    @Override
                    public void onError(Throwable e) {
                        Log.d(TAG, "===================onError ");
                    }

                    @Override
                    public void onComplete() {

                    }
                });

//输出
D/HLP: ===================onNext 1
D/HLP: ===================onError 
```

## zip()

* **会将多个被观察者合并，根据各个被观察者发送事件的顺序一个个结合起来，最终发送的事件数量会与源 Observable 中最少事件的数量一样**

```java
        Observable.zip(Observable.intervalRange(1, 5, 1, 1, TimeUnit.SECONDS)
                        .map(new Function<Long, String>() {
                            @Override
                            public String apply(Long aLong) throws Exception {
                                String s1 = "A" + aLong;
                                Log.d(TAG, "===================A 发送的事件 " + s1);
                                return s1;
                            }
                        }),
                Observable.intervalRange(1, 6, 1, 1, TimeUnit.SECONDS)
                        .map(new Function<Long, String>() {
                            @Override
                            public String apply(Long aLong) throws Exception {
                                String s2 = "B" + aLong;
                                Log.d(TAG, "===================B 发送的事件 " + s2);
                                return s2;
                            }
                        }),
                new BiFunction<String, String, String>() {
                    @Override
                    public String apply(String s, String s2) throws Exception {
                        String res = s + s2;
                        return res;
                    }
                })
                .subscribe(new Observer<String>() {
                    @Override
                    public void onSubscribe(Disposable d) {
                        Log.d(TAG, "===================onSubscribe ");
                    }

                    @Override
                    public void onNext(String s) {
                        Log.d(TAG, "===================onNext " + s);
                    }

                    @Override
                    public void onError(Throwable e) {
                        Log.d(TAG, "===================onError ");
                    }

                    @Override
                    public void onComplete() {
                        Log.d(TAG, "===================onComplete ");
                    }
                });

//输出
D/HLP: ===================onSubscribe 
D/HLP: ===================A 发送的事件 A1
D/HLP: ===================B 发送的事件 B1
D/HLP: ===================onNext A1B1
D/HLP: ===================A 发送的事件 A2
D/HLP: ===================B 发送的事件 B2
D/HLP: ===================onNext A2B2
D/HLP: ===================A 发送的事件 A3
D/HLP: ===================B 发送的事件 B3
D/HLP: ===================onNext A3B3
D/HLP: ===================A 发送的事件 A4
D/HLP: ===================B 发送的事件 B4
D/HLP: ===================onNext A4B4
D/HLP: ===================A 发送的事件 A5
D/HLP: ===================B 发送的事件 B5
D/HLP: ===================onNext A5B5
D/HLP: ===================onComplete 
```

## combineLatest() & combineLatestDelayError()

* **combineLatest() 的作用与 zip() 类似，但是 combineLatest() 发送事件的序列是与发送的时间线有关的，当 combineLatest() 中所有的 Observable 都发送了事件，只要其中有一个 Observable 发送事件，这个事件就会和其他 Observable 最近发送的事件结合起来发送，这样可能还是比较抽象，看看以下例子代码**

```java
Observable.combineLatest(
Observable.intervalRange(1, 4, 1, 1, TimeUnit.SECONDS)
    .map(new Function < Long, String > () {@Override
    public String apply(Long aLong) throws Exception {
        String s1 = "A" + aLong;
        Log.d(TAG, "===================A 发送的事件 " + s1);
        return s1;
    }
}),
Observable.intervalRange(1, 5, 2, 2, TimeUnit.SECONDS)
    .map(new Function < Long, String > () {@Override
    public String apply(Long aLong) throws Exception {
        String s2 = "B" + aLong;
        Log.d(TAG, "===================B 发送的事件 " + s2);
        return s2;
    }
}),
new BiFunction < String, String, String > () {@Override
    public String apply(String s, String s2) throws Exception {
        String res = s + s2;
        return res;
    }
})
.subscribe(new Observer < String > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "===================onSubscribe ");
    }

    @Override
    public void onNext(String s) {
        Log.d(TAG, "===================最终接收到的事件 " + s);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "===================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "===================onComplete ");
    }
});

//输出
05-22 11:41:20.859 15104-15104/? D/chan: ===================onSubscribe 
05-22 11:41:21.859 15104-15128/com.example.rxjavademo D/chan: ===================A 发送的事件 A1
05-22 11:41:22.860 15104-15128/com.example.rxjavademo D/chan: ===================A 发送的事件 A2
05-22 11:41:22.861 15104-15129/com.example.rxjavademo D/chan: ===================B 发送的事件 B1
05-22 11:41:22.862 15104-15129/com.example.rxjavademo D/chan: ===================最终接收到的事件 A2B1
05-22 11:41:23.860 15104-15128/com.example.rxjavademo D/chan: ===================A 发送的事件 A3
===================最终接收到的事件 A3B1
05-22 11:41:24.860 15104-15128/com.example.rxjavademo D/chan: ===================A 发送的事件 A4
05-22 11:41:24.861 15104-15129/com.example.rxjavademo D/chan: ===================B 发送的事件 B2
05-22 11:41:24.861 15104-15128/com.example.rxjavademo D/chan: ===================最终接收到的事件 A4B1
05-22 11:41:24.861 15104-15129/com.example.rxjavademo D/chan: ===================最终接收到的事件 A4B2
05-22 11:41:26.860 15104-15129/com.example.rxjavademo D/chan: ===================B 发送的事件 B3
05-22 11:41:26.861 15104-15129/com.example.rxjavademo D/chan: ===================最终接收到的事件 A4B3
05-22 11:41:28.860 15104-15129/com.example.rxjavademo D/chan: ===================B 发送的事件 B4
05-22 11:41:28.861 15104-15129/com.example.rxjavademo D/chan: ===================最终接收到的事件 A4B4
05-22 11:41:30.860 15104-15129/com.example.rxjavademo D/chan: ===================B 发送的事件 B5
05-22 11:41:30.861 15104-15129/com.example.rxjavademo D/chan: ===================最终接收到的事件 A4B5
===================onComplete

分析上述结果可以知道，当发送 A1 事件之后，因为 B 并没有发送任何事件，所以根本不会发生结合。当 B 发送了 B1 事件之后，就会与 A 最近发送的事件 A2 结合成 A2B1，这样只有后面一有被观察者发送事件，这个事件就会与其他被观察者最近发送的事件结合起来了。
因为 combineLatestDelayError() 就是多了延迟发送 onError() 功能，这里就不再赘述了
```

## reduce()

* **与 scan() 操作符的作用也是将发送数据以一定逻辑聚合起来，这两个的区别在于 scan() 每处理一次数据就会将事件发送给观察者，而 reduce() 会将所有数据聚合在一起才会发送事件给观察者**

```java
Observable.just(0, 1, 2, 3)
.reduce(new BiFunction < Integer, Integer, Integer > () {
    @Override
    public Integer apply(Integer integer, Integer integer2) throws Exception {
        int res = integer + integer2;
        Log.d(TAG, "====================integer " + integer);
        Log.d(TAG, "====================integer2 " + integer2);
        Log.d(TAG, "====================res " + res);
        return res;
    }
})
.subscribe(new Consumer < Integer > () {
    @Override
    public void accept(Integer integer) throws Exception {
        Log.d(TAG, "==================accept " + integer);
    }
});

//输出
====================integer 0
====================integer2 1
====================res 1
====================integer 1
====================integer2 2
====================res 3
====================integer 3
====================integer2 3
====================res 6
==================accept 6
```

## collect()

* **将数据收集到数据结构当中**

```
Observable.just(1, 2, 3, 4)
.collect(new Callable < ArrayList < Integer >> () {
    @Override
    public ArrayList < Integer > call() throws Exception {
        return new ArrayList < > ();
    }
},
new BiConsumer < ArrayList < Integer > , Integer > () {
    @Override
    public void accept(ArrayList < Integer > integers, Integer integer) throws Exception {
        integers.add(integer);
    }
})
.subscribe(new Consumer < ArrayList < Integer >> () {
    @Override
    public void accept(ArrayList < Integer > integers) throws Exception {
        Log.d(TAG, "===============accept " + integers);
    }
});

//输出
===============accept [1, 2, 3, 4]
```

## startWith() & startWithArray()

* **在发送事件之前追加事件，startWith() 追加一个事件，startWithArray() 可以追加多个事件。追加的事件会先发出**

```java
Observable.just(5, 6, 7)
.startWithArray(2, 3, 4)
.startWith(1)
.subscribe(new Consumer < Integer > () {
    @Override
    public void accept(Integer integer) throws Exception {
        Log.d(TAG, "================accept " + integer);
    }
});

//输出
================accept 1
================accept 2
================accept 3
================accept 4
================accept 5
================accept 6
================accept 7

```

## count()

* **返回被观察者发送事件的数量**

```java
Observable.just(1, 2, 3)
.count()
.subscribe(new Consumer < Long > () {
    @Override
    public void accept(Long aLong) throws Exception {
        Log.d(TAG, "=======================aLong " + aLong);
    }
});

//输出
=======================aLong 3
```

# 功能操作符

## delay()

* **延迟一段时间发送事件**

```java
Observable.just(1, 2, 3)
.delay(2, TimeUnit.SECONDS)
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "=======================onSubscribe");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "=======================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {

    }

    @Override
    public void onComplete() {
        Log.d(TAG, "=======================onSubscribe");
    }
});

//输出 
=======================onSubscribe
=======================onNext 1
=======================onNext 2
=======================onNext 3
=======================onSubscribe

```

## doOnEach()

* **Observable 每发送一件事件之前都会先回调这个方法**

```java
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        //      e.onError(new NumberFormatException());
        e.onComplete();
    }
})
.doOnEach(new Consumer < Notification < Integer >> () {
    @Override
    public void accept(Notification < Integer > integerNotification) throws Exception {
        Log.d(TAG, "==================doOnEach " + integerNotification.getValue());
    }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 09:07:05.547 19867-19867/? D/chan: ==================onSubscribe 
==================doOnEach 1
==================onNext 1
==================doOnEach 2
==================onNext 2
==================doOnEach 3
==================onNext 3
==================doOnEach null
==================onComplete 
```

## doOnNext()

* **Observable 每发送 onNext() 之前都会先回调这个方法**

```java
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.doOnNext(new Consumer < Integer > () {
    @Override
    public void accept(Integer integer) throws Exception {
        Log.d(TAG, "==================doOnNext " + integer);
    }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 09:09:36.769 20020-20020/com.example.rxjavademo D/chan: ==================onSubscribe 
==================doOnNext 1
==================onNext 1
==================doOnNext 2
==================onNext 2
==================doOnNext 3
==================onNext 3
==================onComplete 
```

## doAfterNext()

* **Observable 每发送 onNext() 之后都会回调这个方法**

```java
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.doAfterNext(new Consumer < Integer > () {
    @Override
    public void accept(Integer integer) throws Exception {
        Log.d(TAG, "==================doAfterNext " + integer);
    }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 09:15:49.215 20432-20432/com.example.rxjavademo D/chan: ==================onSubscribe 
==================onNext 1
==================doAfterNext 1
==================onNext 2
==================doAfterNext 2
==================onNext 3
==================doAfterNext 3
==================onComplete 
```

## doOnComplete()

* **Observable 每发送 onComplete() 之前都会回调这个方法**

```java
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.doOnComplete(new Action() {
    @Override
    public void run() throws Exception {
        Log.d(TAG, "==================doOnComplete ");
    }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
==================doOnComplete 
==================onComplete 
```

## doOnError()

* **Observable 每发送 onError() 之前都会回调这个方法**

```java
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onError(new NullPointerException());
    }
})
.doOnError(new Consumer < Throwable > () {
    @Override
    public void accept(Throwable throwable) throws Exception {
        Log.d(TAG, "==================doOnError " + throwable);
    }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 09:35:04.150 21051-21051/? D/chan: ==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
==================doOnError java.lang.NullPointerException
==================onError 
```

## doOnSubscribe()

* **Observable 每发送 onSubscribe() 之前都会回调这个方法**

```java
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.doOnSubscribe(new Consumer < Disposable > () {
    @Override
    public void accept(Disposable disposable) throws Exception {
        Log.d(TAG, "==================doOnSubscribe ");
    }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 09:39:25.778 21245-21245/? D/chan: ==================doOnSubscribe 
==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
==================onComplete 
```

## doOnDispose()

* **当调用 Disposable 的 dispose() 之后回调该方法**

```java
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.doOnDispose(new Action() {
    @Override
    public void run() throws Exception {
        Log.d(TAG, "==================doOnDispose ");
    }
})
.subscribe(new Observer < Integer > () {
    private Disposable d;
    
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
        this.d = d;
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
        d.dispose();
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 09:55:48.122 22023-22023/com.example.rxjavademo D/chan: ==================onSubscribe 
==================onNext 1
==================doOnDispose 

```

## doOnLifecycle()

* **在回调 onSubscribe 之前回调该方法的第一个参数的回调方法，可以使用该回调方法决定是否取消订阅**

```java
Observable.create(new ObservableOnSubscribe<Integer>() {
    @Override
    public void subscribe(ObservableEmitter<Integer> e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.doOnLifecycle(new Consumer<Disposable>() {
    @Override
    public void accept(Disposable disposable) throws Exception {
        Log.d(TAG, "==================doOnLifecycle accept");
    }
}, new Action() {
    @Override
    public void run() throws Exception {
        Log.d(TAG, "==================doOnLifecycle Action");
    }
})
.doOnDispose(
    new Action() {
        @Override
        public void run() throws Exception {
            Log.d(TAG, "==================doOnDispose Action");
        }
})
.subscribe(new Observer<Integer>() {
    private Disposable d;
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
        this.d = d;
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
        d.dispose();
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
    
});

//输出

05-23 10:20:36.345 23922-23922/? D/chan: ==================doOnLifecycle accept
==================onSubscribe 
==================onNext 1
==================doOnDispose Action
==================doOnLifecycle Action

```

## doOnTerminate() & doAfterTerminate()

* **doOnTerminate 是在 onError 或者 onComplete 发送之前回调，而 doAfterTerminate 则是 onError 或者 onComplete 发送之后回调**

```java
Observable.create(new ObservableOnSubscribe<Integer>() {
    @Override
    public void subscribe(ObservableEmitter<Integer> e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
//      e.onError(new NullPointerException());
        e.onComplete();
    }
})
.doOnTerminate(new Action() {
    @Override
    public void run() throws Exception {
        Log.d(TAG, "==================doOnTerminate ");
    }
})
.subscribe(new Observer<Integer>() {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
    
});

//输出
05-23 10:00:39.503 22398-22398/com.example.rxjavademo D/chan: ==================onSubscribe 
==================onNext 1
==================onNext 2
05-23 10:00:39.504 22398-22398/com.example.rxjavademo D/chan: ==================onNext 3
==================doOnTerminate 
==================onComplete 

```

## doFinally()

* **在所有事件发送完毕之后回调该方法。**

```java
Observable.create(new ObservableOnSubscribe<Integer>() {
    @Override
    public void subscribe(ObservableEmitter<Integer> e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.doFinally(new Action() {
    @Override
    public void run() throws Exception {
        Log.d(TAG, "==================doFinally ");
    }
})
.doOnDispose(new Action() {
    @Override
    public void run() throws Exception {
        Log.d(TAG, "==================doOnDispose ");
    }
})
.doAfterTerminate(new Action() {
    @Override
    public void run() throws Exception {
        Log.d(TAG, "==================doAfterTerminate ");
    }
})
.subscribe(new Observer<Integer>() {
    private Disposable d;
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
        this.d = d;
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
        d.dispose();
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 10:10:10.469 23196-23196/? D/chan: ==================onSubscribe 
05-23 10:10:10.470 23196-23196/? D/chan: ==================onNext 1
==================doOnDispose 
==================doFinally 

```

## onErrorReturn()

* **当接受到一个 onError() 事件之后回调，返回的值会回调 onNext() 方法，并正常结束该事件序列**

```java
Observable.create(new ObservableOnSubscribe<Integer>() {
    @Override
    public void subscribe(ObservableEmitter<Integer> e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onError(new NullPointerException());
    }
})
.onErrorReturn(new Function<Throwable, Integer>() {
    @Override
    public Integer apply(Throwable throwable) throws Exception {
        Log.d(TAG, "==================onErrorReturn " + throwable);
        return 404;
    }
})
.subscribe(new Observer<Integer>() {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 18:35:18.175 19239-19239/? D/chan: ==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
==================onErrorReturn java.lang.NullPointerException
==================onNext 404
==================onComplete 

```

## onErrorResumeNext()

* **当接收到 onError() 事件时，返回一个新的 Observable，并正常结束事件序列**

```java
Observable.create(new ObservableOnSubscribe<Integer>() {
    @Override
    public void subscribe(ObservableEmitter<Integer> e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onError(new NullPointerException());
    }
})
.onErrorResumeNext(new Function<Throwable, ObservableSource<? extends Integer>>() {
    @Override
    public ObservableSource<? extends Integer> apply(Throwable throwable) throws Exception {
        Log.d(TAG, "==================onErrorResumeNext " + throwable);
        return Observable.just(4, 5, 6);
    }
})
.subscribe(new Observer<Integer>() {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 18:43:10.910 26469-26469/? D/chan: ==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
==================onErrorResumeNext java.lang.NullPointerException
==================onNext 4
==================onNext 5
==================onNext 6
==================onComplete 

```

## onExceptionResumeNext()

* **与 onErrorResumeNext() 作用基本一致，但是这个方法只能捕捉 Exception**

```java
Observable.create(new ObservableOnSubscribe<Integer>() {
    @Override
    public void subscribe(ObservableEmitter<Integer> e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onError(new Error("404"));
    }
})
.onExceptionResumeNext(new Observable<Integer>() {
    @Override
    protected void subscribeActual(Observer<? super Integer> observer) {
        observer.onNext(333);
        observer.onComplete();
    }
})
.subscribe(new Observer<Integer>() {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 22:23:08.873 1062-1062/com.example.louder.rxjavademo D/chan: ==================onSubscribe 
05-23 22:23:08.874 1062-1062/com.example.louder.rxjavademo D/chan: ==================onNext 1
==================onNext 2
==================onNext 3
==================onError 

```

## retry()

* **如果出现错误事件，则会重新发送所有事件序列。times 是代表重新发的次数**

```
Observable.create(new ObservableOnSubscribe<Integer>() {
    @Override
    public void subscribe(ObservableEmitter<Integer> e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onError(new Exception("404"));
    }
})
.retry(2)
.subscribe(new Observer<Integer>() {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 22:46:18.537 22239-22239/com.example.louder.rxjavademo D/chan: ==================onSubscribe 
05-23 22:46:18.538 22239-22239/com.example.louder.rxjavademo D/chan: ==================onNext 1
==================onNext 2
==================onNext 3
==================onNext 1
==================onNext 2
==================onNext 3
==================onNext 1
==================onNext 2
==================onNext 3
==================onError 

```

## retryUntil()

* **出现错误事件之后，可以通过此方法判断是否继续发送事件**

```
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onError(new Exception("404"));
    }
})
.retryUntil(new BooleanSupplier() {
    @Override
    public boolean getAsBoolean() throws Exception {
        if (i == 6) {
            return true;
        }
        return false;
    }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        i += integer;
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-23 22:57:32.905 23063-23063/com.example.louder.rxjavademo D/chan: ==================onSubscribe 
05-23 22:57:32.906 23063-23063/com.example.louder.rxjavademo D/chan: ==================onNext 1
==================onNext 2
==================onNext 3
==================onError 


```

## retryWhen()

* **当被观察者接收到异常或者错误事件时会回调该方法，这个方法会返回一个新的被观察者。如果返回的被观察者发送 Error 事件则之前的被观察者不会继续发送事件，如果发送正常事件则之前的被观察者会继续不断重试发送事件**

```
Observable.create(new ObservableOnSubscribe < String > () {
    @Override
    public void subscribe(ObservableEmitter < String > e) throws Exception {
        e.onNext("chan");
        e.onNext("ze");
        e.onNext("de");
        e.onError(new Exception("404"));
        e.onNext("haha");
    }
})
.retryWhen(new Function < Observable < Throwable > , ObservableSource <? >> () {
    @Override
    public ObservableSource <? > apply(Observable < Throwable > throwableObservable) throws Exception {
        return throwableObservable.flatMap(new Function < Throwable, ObservableSource <? >> () {
            @Override
            public ObservableSource <? > apply(Throwable throwable) throws Exception {
                if(!throwable.toString().equals("java.lang.Exception: 404")) {
                    return Observable.just("可以忽略的异常");
                } else {
                    return Observable.error(new Throwable("终止啦"));
                }
            }
        });
    }
})
.subscribe(new Observer < String > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(String s) {
        Log.d(TAG, "==================onNext " + s);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError " + e.toString());
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-24 09:13:25.622 28372-28372/com.example.rxjavademo D/chan: ==================onSubscribe 
05-24 09:13:25.623 28372-28372/com.example.rxjavademo D/chan: ==================onNext chan
==================onNext ze
==================onNext de
05-24 09:13:25.624 28372-28372/com.example.rxjavademo D/chan: ==================onError java.lang.Throwable: 终止啦

```

## repeat()

* **重复发送被观察者的事件，times 为发送次数**

```
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.repeat(2)
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "===================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "===================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {

    }

    @Override
    public void onComplete() {
        Log.d(TAG, "===================onComplete ");
    }
});

//输出
05-24 11:33:29.565 8544-8544/com.example.rxjavademo D/chan: ===================onSubscribe 
===================onNext 1
===================onNext 2
===================onNext 3
===================onNext 1
===================onNext 2
===================onNext 3
05-24 11:33:29.565 8544-8544/com.example.rxjavademo D/chan: ===================onComplete 

```

## repeatWhen()

* **这个方法可以会返回一个新的被观察者设定一定逻辑来决定是否重复发送事件**

```
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
.repeatWhen(new Function < Observable < Object > , ObservableSource <? >> () {
    @Override
    public ObservableSource <? > apply(Observable < Object > objectObservable) throws Exception {
        return Observable.empty();
    //  return Observable.error(new Exception("404"));
    //  return Observable.just(4); null;
    }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "===================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "===================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "===================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "===================onComplete ");
    }
});

//输出
05-24 11:44:33.486 9379-9379/com.example.rxjavademo D/chan: ===================onSubscribe 
05-24 11:44:33.487 9379-9379/com.example.rxjavademo D/chan: ===================onComplete 

```

## subscribeOn()

* **指定被观察者的线程，要注意的时，如果多次调用此方法，只有第一次有效**
* **不指定，默认在程序执行的线程执行**

```
Observable.create(new ObservableOnSubscribe < Integer > () {
    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        Log.d(TAG, "=========================currentThread name: " + Thread.currentThread().getName());
        e.onNext(1);
        e.onNext(2);
        e.onNext(3);
        e.onComplete();
    }
})
//.subscribeOn(Schedulers.newThread())
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "======================onSubscribe");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "======================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "======================onError");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "======================onComplete");
    }
});

//输出
05-26 10:40:42.246 21466-21466/? D/chan: ======================onSubscribe
05-26 10:40:42.247 21466-21466/? D/chan: =========================currentThread name: main
======================onNext 1
======================onNext 2
======================onNext 3
======================onComplete

```

## observeOn()

* **指定观察者的线程，每指定一次就会生效一次**

```java
Observable.just(1, 2, 3)
.observeOn(Schedulers.newThread())
.flatMap(new Function < Integer, ObservableSource < String >> () {
    @Override
    public ObservableSource < String > apply(Integer integer) throws Exception {
        Log.d(TAG, "======================flatMap Thread name " + Thread.currentThread().getName());
        return Observable.just("chan" + integer);
    }
})
.observeOn(AndroidSchedulers.mainThread())
.subscribe(new Observer < String > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "======================onSubscribe");
    }

    @Override
    public void onNext(String s) {
        Log.d(TAG, "======================onNext Thread name " + Thread.currentThread().getName());
        Log.d(TAG, "======================onNext " + s);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "======================onError");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "======================onComplete");
    }
});

//输出
======================onSubscribe
======================flatMap Thread name RxNewThreadScheduler-1
======================flatMap Thread name RxNewThreadScheduler-1
======================flatMap Thread name RxNewThreadScheduler-1
======================onNext Thread name main
======================onNext chan1
======================onNext Thread name main
======================onNext chan2
======================onNext Thread name main
======================onNext chan3
======================onComplete
```

## RxJava调度器

| 调度器                         | 作用                                               |
| ------------------------------ | -------------------------------------------------- |
| Schedulers.computation( )      | 用于使用计算任务，如事件循环和回调处理             |
| Schedulers.immediate( )        | 当前线程                                           |
| Schedulers.io( )               | 用于 IO 密集型任务，如果异步阻塞 IO 操作           |
| Schedulers.newThread( )        | 创建一个新的线程                                   |
| AndroidSchedulers.mainThread() | Android 的 UI 线程，用于操作 UI（在RxAndroid包中） |

## observeOn&subscribeOn区别

* **subscribeOn：指定被观察者自身在哪个调度器上执行**
* **observeOn：指定观察者在哪个调度器观察被观察者**

# 过滤操作符

## filter()

* **通过一定逻辑来过滤被观察者发送的事件，如果返回 true 则会发送事件，否则不会发送**

```java
 Observable.just(1, 2, 3)
    .filter(new Predicate < Integer > () {
        @Override
        public boolean test(Integer integer) throws Exception {
            return integer < 2;
        }
})
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        i += integer;
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
==================onSubscribe 
==================onNext 1
==================onComplete 

```

## ofType()

* **可以过滤不符合该类型事件**

```
Observable.just(1, 2, 3, "chan", "zhide")
.ofType(Integer.class)
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        i += integer;
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-24 23:04:24.752 13229-13229/? D/chan: ==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
05-24 23:04:24.753 13229-13229/? D/chan: ==================onComplete 

```

## skip()

* **跳过正序某些事件，count 代表跳过事件的数量**

```
Observable.just(1, 2, 3)
.skip(2)
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        i += integer;
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-24 23:13:50.448 13831-13831/? D/chan: ==================onSubscribe 
05-24 23:13:50.449 13831-13831/? D/chan: ==================onNext 3
==================onComplete 

```

## distinct()

* **过滤事件序列中的重复事件。**

```
Observable.just(1, 2, 3, 3, 2, 1)
.distinct()
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        i += integer;
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-24 23:19:44.334 14206-14206/com.example.louder.rxjavademo D/chan: ==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
==================onComplete 

```

## distinctUntilChanged()

* **过滤掉连续重复的事件**

```
Observable.just(1, 2, 3, 3, 2, 1)
.distinctUntilChanged()
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        i += integer;
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-24 23:22:35.985 14424-14424/com.example.louder.rxjavademo D/chan: ==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
==================onNext 2
==================onNext 1
==================onComplete 

```

## take()

* **控制观察者接收的事件的数量。**

```
Observable.just(1, 2, 3, 4, 5)
.take(3)
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "==================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        i += integer;
        Log.d(TAG, "==================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "==================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "==================onComplete ");
    }
});

//输出
05-24 23:28:32.899 14704-14704/? D/chan: ==================onSubscribe 
==================onNext 1
==================onNext 2
==================onNext 3
==================onComplete 

```

## debounce()

* **如果两件事件发送的时间间隔小于设定的时间间隔则前一件事件就不会发送给观察者**

```
Observable.create(new ObservableOnSubscribe < Integer > () {

    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onNext(1);
        Thread.sleep(900);
        e.onNext(2);
    }
})
.debounce(1, TimeUnit.SECONDS)
.subscribe(new Observer < Integer > () {
    @Override
    public void onSubscribe(Disposable d) {
        Log.d(TAG, "===================onSubscribe ");
    }

    @Override
    public void onNext(Integer integer) {
        Log.d(TAG, "===================onNext " + integer);
    }

    @Override
    public void onError(Throwable e) {
        Log.d(TAG, "===================onError ");
    }

    @Override
    public void onComplete() {
        Log.d(TAG, "===================onComplete ");
    }
});

//输出
05-25 20:39:10.512 17441-17441/com.example.rxjavademo D/chan: ===================onSubscribe 
05-25 20:39:12.413 17441-17478/com.example.rxjavademo D/chan: ===================onNext 2

```

## firstElement() && lastElement()

* **firstElement() 取事件序列的第一个元素，lastElement() 取事件序列的最后一个元素**

```
Observable.just(1, 2, 3, 4)
.firstElement()
.subscribe(new Consumer < Integer > () {
    @Override
    public void accept(Integer integer) throws Exception {
        Log.d(TAG, "====================firstElement " + integer);
    }
});

Observable.just(1, 2, 3, 4)
.lastElement()
.subscribe(new Consumer < Integer > () {
    @Override
    public void accept(Integer integer) throws Exception {
        Log.d(TAG, "====================lastElement " + integer);
    }
});

//输出
05-25 20:47:22.189 19909-19909/? D/chan: ====================firstElement 1
====================lastElement 4

```

## elementAt() & elementAtOrError()

* **elementAt() 可以指定取出事件序列中事件，但是输入的 index 超出事件序列的总数的话就不会出现任何结果。这种情况下，你想发出异常信息的话就用 elementAtOrError() **

```
Observable.just(1, 2, 3, 4)
.elementAt(0)
.subscribe(new Consumer < Integer > () {
    @Override
    public void accept(Integer integer) throws Exception {
        Log.d(TAG, "====================accept " + integer);
    }
});

//输出
05-25 20:56:22.266 23346-23346/com.example.rxjavademo D/chan: ====================accept 1

```



# 条件操作符

## all()

* **判断事件序列是否全部满足某个事件，如果都满足则返回 true，反之则返回 false**

```java
        Observable.just(1, 2, 3, 4)
                .all(new Predicate<Integer>() {
                    @Override
                    public boolean test(Integer integer) throws Exception {
                        return integer < 5;
                    }
                })
                .subscribe(new Consumer<Boolean>() {
                    @Override
                    public void accept(Boolean aBoolean) throws Exception {
                        Log.d(TAG, "==================aBoolean " + aBoolean);
                    }
                });

//输出
D/HLP: ==================aBoolean true
```

## takeWhile()

* **可以设置条件，当某个数据满足条件时就会发送该数据，反之则不发送**

```java
        Observable.just(1, 2, 3, 4)
                .takeWhile(new Predicate<Integer>() {
                    @Override
                    public boolean test(Integer integer) throws Exception {
                        return integer < 3;
                    }
                })
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        Log.d(TAG, "========================integer " + integer);
                    }
                });

//输出
D/HLP: ========================integer 1
D/HLP: ========================integer 2
```

## skipWhile()

* **可以设置条件，当某个数据满足条件时不发送该数据，反之则发送**

```java
        Observable.just(1, 2, 3, 4)
                .skipWhile(new Predicate<Integer>() {
                    @Override
                    public boolean test(Integer integer) throws Exception {
                        return integer < 3;
                    }
                })
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        Log.d(TAG, "========================integer " + integer);
                    }
                });

//输出
D/HLP: ========================integer 3
D/HLP: ========================integer 4
```

## takeUntil()

* **可以设置条件，当事件满足此条件时，下一次的事件就不会被发送了**

```java
        Observable.just(1, 2, 3, 4, 5, 6)
                .takeUntil(new Predicate<Integer>() {
                    @Override
                    public boolean test(Integer integer) throws Exception {
                        return integer > 3;
                    }
                })
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        Log.d(TAG, "========================integer " + integer);
                    }
                });

//输出
D/HLP: ========================integer 1
D/HLP: ========================integer 2
D/HLP: ========================integer 3
D/HLP: ========================integer 4
```

## skipUntil()

* **当 skipUntil() 中的 Observable 发送事件了，原来的 Observable 才会发送事件给观察者**

```java
        Observable.intervalRange(1, 5, 0, 1, TimeUnit.SECONDS)
                .skipUntil(Observable.intervalRange(6, 5, 3, 1, TimeUnit.SECONDS))
                .subscribe(new Observer<Long>() {
                    @Override
                    public void onSubscribe(Disposable d) {
                        Log.d(TAG, "========================onSubscribe ");
                    }

                    @Override
                    public void onNext(Long along) {
                        Log.d(TAG, "========================onNext " + along);
                    }

                    @Override
                    public void onError(Throwable e) {
                        Log.d(TAG, "========================onError ");
                    }

                    @Override
                    public void onComplete() {
                        Log.d(TAG, "========================onComplete ");
                    }
                });

//输出
D/HLP: ========================onSubscribe 
D/HLP: ========================onNext 5
D/HLP: ========================onComplete 
```

## sequenceEqual()

* **判断两个 Observable 发送的事件是否相同**

```java
        Observable.sequenceEqual(Observable.just(1, 2, 3),
                Observable.just(1, 2, 3))
                .subscribe(new Consumer<Boolean>() {
                    @Override
                    public void accept(Boolean aBoolean) throws Exception {
                        Log.d(TAG, "========================onNext " + aBoolean);
                    }
                });

//输出
D/HLP: ========================onNext true
```

## contains()

* **判断事件序列中是否含有某个元素，如果有则返回 true，如果没有则返回 false**

```java
        Observable.just(1, 2, 3)
                .contains(3)
                .subscribe(new Consumer<Boolean>() {
                    @Override
                    public void accept(Boolean aBoolean) throws Exception {
                        Log.d(TAG, "========================onNext " + aBoolean);
                    }
                });

//输出
D/HLP: ========================onNext true
```

## isEmpty()

* **判断事件序列是否为空**

```java
Observable.create(new ObservableOnSubscribe < Integer > () {

    @Override
    public void subscribe(ObservableEmitter < Integer > e) throws Exception {
        e.onComplete();
    }
})
.isEmpty()
.subscribe(new Consumer < Boolean > () {
    @Override
    public void accept(Boolean aBoolean) throws Exception {
        Log.d(TAG, "========================onNext " + aBoolean);
    }
});

//输出
========================onNext true
```

## amb()

* **amb() 要传入一个 Observable 集合，但是只会发送最先发送事件的 Observable 中的事件，其余 Observable 将会被丢弃**

```java
        ArrayList<Observable<Long>> list = new ArrayList<>();

        list.add(Observable.intervalRange(1, 5, 2, 1, TimeUnit.SECONDS));
        list.add(Observable.intervalRange(6, 5, 0, 1, TimeUnit.SECONDS));

        Observable.amb(list)
                .subscribe(new Consumer<Long>() {
                    @Override
                    public void accept(Long aLong) throws Exception {
                        Log.d(TAG, "========================aLong " + aLong);
                    }
                });

//输出
D/HLP: ========================aLong 6
D/HLP: ========================aLong 7
D/HLP: ========================aLong 8
D/HLP: ========================aLong 9
D/HLP: ========================aLong 10
```

## defaultIfEmpty()

* **如果观察者只发送一个 onComplete() 事件，则可以利用这个方法发送一个值**

```java
        Observable.create(new ObservableOnSubscribe<Integer>() {

            @Override
            public void subscribe(ObservableEmitter<Integer> e) throws Exception {
                e.onComplete();
            }
        })
                .defaultIfEmpty(666)
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        Log.d(TAG, "========================onNext " + integer);
                    }
                });

//输出
D/HLP: ========================onNext 666
```

# 背压

> 概述

被观察者 发送事件速度太快，而观察者 来不及接收所有事件，从而导致**观察者无法及时响应 / 处理所有发送过来事件的问题，最终导致缓存区溢出、事件丢失 & OOM**

## FLowable

