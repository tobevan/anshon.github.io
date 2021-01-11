## 创建型操作符

1.create  产生一个ObserverbalEmitter，手动发射

2.just  内部发射

3.fromArray  内部发射，数集对象

4.empty  不发射有值事件，只发射onComplete

5.range  内部发射，从 start 累加 count个

6.interval 

注意：rxjava2.0以后不能发射null



## 变换型操作符

1.map  把一种类型转为另外一种类型

2.flatmap  把一种类型转为另一种可以多次发射的类型 ObservableSource , 但是不保证顺序

3.contactmap 把一种类型转为另一种可以多次发射的类型 ObservableSource , 可保证顺序

4.groupby  把上游数据分组成一个被观察者 GroupedObservable

5.buffer 很多数据不想一次性发射出去，先缓存一部分



## 过滤型操作符

1.filter   new Predicate  true全返回

2.take  只发射前N个，忽略

3.distinct 过滤重复

4.elementAt  输出指定index元素，可指定默认值



## 条件操作符

1.all 全部满足条件输出true

2.contains 只要有一个满足输出true

3.any 同 contains



## 合并操作符

1.startWith，

```java
        Observable.create((ObservableOnSubscribe<String>) emitter -> {
            emitter.onNext("1");
            emitter.onNext("2");
            emitter.onComplete();
        }).startWith(Observable.create(emitter -> {
            emitter.onNext("111");
            emitter.onNext("222");
            emitter.onComplete();
        })).subscribe(s -> {
            Log.e(TAG, "testRXJava: " + s);
        });

2020-12-29 20:25:24.924 4752-4752/? E/MainActivity: testRXJava: 111
2020-12-29 20:25:24.925 4752-4752/? E/MainActivity: testRXJava: 222
2020-12-29 20:25:24.925 4752-4752/? E/MainActivity: testRXJava: 1
2020-12-29 20:25:24.925 4752-4752/? E/MainActivity: testRXJava: 2
```



2.concatWith，追加合并

3.concat  最多合并四个

```java
        Observable.concat(
                Observable.just("1", "2"),
                Observable.just("3", "4"),
                Observable.just("5"),
                Observable.create(emitter -> {
                    emitter.onNext("111");
                    emitter.onNext("222");
                    emitter.onComplete();
                })
        ).subscribe(s -> Log.e(TAG, "accept: " + s));
    }

2020-12-29 23:32:54.885 11477-11477/? E/MainActivity: accept: 1
2020-12-29 23:32:54.885 11477-11477/? E/MainActivity: accept: 2
2020-12-29 23:32:54.886 11477-11477/? E/MainActivity: accept: 3
2020-12-29 23:32:54.886 11477-11477/? E/MainActivity: accept: 4
2020-12-29 23:32:54.886 11477-11477/? E/MainActivity: accept: 5
2020-12-29 23:32:54.886 11477-11477/? E/MainActivity: accept: 111
2020-12-29 23:32:54.886 11477-11477/? E/MainActivity: accept: 222
```

4.merge 并发合并

```java
        Observable ob1 = Observable.intervalRange(0, 5, 2, 2, TimeUnit.SECONDS);
        Observable ob2 = Observable.intervalRange(10, 5, 3, 1, TimeUnit.SECONDS);
        Observable.merge(ob1, ob2)
                .subscribe(s -> Log.e(TAG, "accept: " + s));

2020-12-29 23:47:55.425 13262-13291/? E/MainActivity: accept: 0
2020-12-29 23:47:56.425 13262-13292/? E/MainActivity: accept: 10
2020-12-29 23:47:57.425 13262-13291/? E/MainActivity: accept: 1
2020-12-29 23:47:57.430 13262-13292/? E/MainActivity: accept: 11
2020-12-29 23:47:58.425 13262-13292/? E/MainActivity: accept: 12
2020-12-29 23:47:59.425 13262-13291/? E/MainActivity: accept: 2
2020-12-29 23:47:59.426 13262-13291/? E/MainActivity: accept: 13
2020-12-29 23:48:00.425 13262-13292/? E/MainActivity: accept: 14
2020-12-29 23:48:01.425 13262-13291/? E/MainActivity: accept: 3
2020-12-29 23:48:03.425 13262-13291/? E/MainActivity: accept: 4
```

5.zip 组合合并

```java
        Observable ob1 = Observable.just("语文", "数学");
        Observable ob2 = Observable.just(95, 100, 200);
        Observable.zip(ob1, ob2, new BiFunction<String, Integer, String>() {
            @Override
            public String apply(String s, Integer integer) throws Throwable {
                return s + "得分：" + integer;
            }
        }).subscribe(s -> Log.e(TAG, "accept: " + s));

2020-12-30 00:01:35.683 14598-14598/com.anshon.myapplication E/MainActivity: accept: 语文得分：95
2020-12-30 00:01:35.683 14598-14598/com.anshon.myapplication E/MainActivity: accept: 数学得分：100
```



## 异常操作符

1.onErrorReturn  拦截上游onError，返回自定义数据

```java
        Observable.create((ObservableOnSubscribe<Integer>) emitter -> {
            emitter.onNext(1);
            emitter.onNext(2);
            emitter.onError(new IOException("access denied!"));
            emitter.onNext(3);
            emitter.onComplete();
        }).subscribe(new Observer<Integer>() {
                    @Override
                    public void onSubscribe(@NonNull Disposable d) {

                    }

                    @Override
                    public void onNext(@NonNull Integer integer) {
                        Log.e(TAG, "consumer onNext: " + integer);
                    }

                    @Override
                    public void onError(@NonNull Throwable e) {
                        Log.e(TAG, "consumer onError: " + e.getMessage());
                    }

                    @Override
                    public void onComplete() {

                    }
                });
2020-12-30 00:18:42.731 15971-15971/com.anshon.myapplication E/MainActivity: consumer onNext: 1
2020-12-30 00:18:42.732 15971-15971/com.anshon.myapplication E/MainActivity: consumer onNext: 2
2020-12-30 00:18:42.732 15971-15971/com.anshon.myapplication E/MainActivity: consumer onError: access denied!


Observable.create((ObservableOnSubscribe<Integer>) emitter -> {
            emitter.onNext(1);
            emitter.onNext(2);
            emitter.onError(new IOException("access denied!"));
            emitter.onNext(3);
            emitter.onComplete();
        }).onErrorReturn(throwable -> {
            Log.e(TAG, "onErrorReturn: " + throwable.getMessage());
            return 400;
        }).subscribe(new Observer<Integer>() {
                    @Override
                    public void onSubscribe(@NonNull Disposable d) {

                    }

                    @Override
                    public void onNext(@NonNull Integer integer) {
                        Log.e(TAG, "consumer onNext: " + integer);
                    }

                    @Override
                    public void onError(@NonNull Throwable e) {
                        Log.e(TAG, "consumer onError: " + e.getMessage());
                    }

                    @Override
                    public void onComplete() {

                    }
                });
    }

2020-12-30 00:16:04.892 15641-15641/com.anshon.myapplication E/MainActivity: consumer onNext: 1
2020-12-30 00:16:04.892 15641-15641/com.anshon.myapplication E/MainActivity: consumer onNext: 2
2020-12-30 00:16:04.892 15641-15641/com.anshon.myapplication E/MainActivity: onErrorReturn: access denied!
2020-12-30 00:16:04.892 15641-15641/com.anshon.myapplication E/MainActivity: consumer onNext: 400
```

2.onErrorResumeNext  拦截错误重新发射新

```java
        Observable.create((ObservableOnSubscribe<Integer>) emitter -> {
            emitter.onNext(1);
            emitter.onNext(2);
            emitter.onError(new IOException("access denied!"));
            emitter.onNext(5);
            emitter.onComplete();
        }).onErrorResumeNext(throwable -> Observable.create(emitter -> {
            emitter.onNext(3);
            emitter.onNext(4);
            emitter.onComplete();
        })).subscribe(new Observer<Integer>() {
                    @Override
                    public void onSubscribe(@NonNull Disposable d) {

                    }

                    @Override
                    public void onNext(@NonNull Integer integer) {
                        Log.e(TAG, "consumer onNext: " + integer);
                    }

                    @Override
                    public void onError(@NonNull Throwable e) {
                        Log.e(TAG, "consumer onError: " + e.getMessage());
                    }

                    @Override
                    public void onComplete() {

                    }
                });

2020-12-30 00:56:34.595 16775-16775/com.anshon.myapplication E/MainActivity: consumer onNext: 1
2020-12-30 00:56:34.595 16775-16775/com.anshon.myapplication E/MainActivity: consumer onNext: 2
2020-12-30 00:56:34.595 16775-16775/com.anshon.myapplication E/MainActivity: consumer onNext: 3
2020-12-30 00:56:34.595 16775-16775/com.anshon.myapplication E/MainActivity: consumer onNext: 4
```

3.retry  

```java
        Observable.create((ObservableOnSubscribe<Integer>) emitter -> {
            emitter.onNext(1);
            emitter.onNext(2);
            emitter.onError(new IOException("access denied!"));
            emitter.onNext(3);
            emitter.onComplete();
        }).retry(3, throwable -> true).subscribe(new Observer<Integer>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onNext(@NonNull Integer integer) {
                Log.e(TAG, "consumer onNext: " + integer);
            }

            @Override
            public void onError(@NonNull Throwable e) {
                Log.e(TAG, "consumer onError: " + e.getMessage());
            }

            @Override
            public void onComplete() {
                Log.e(TAG, "consumer onComplete: ");
            }
        });

2020-12-30 01:23:52.070 20081-20081/com.anshon.myapplication E/MainActivity: consumer onNext: 1
2020-12-30 01:23:52.070 20081-20081/com.anshon.myapplication E/MainActivity: consumer onNext: 2
2020-12-30 01:23:52.070 20081-20081/com.anshon.myapplication E/MainActivity: consumer onNext: 1
2020-12-30 01:23:52.070 20081-20081/com.anshon.myapplication E/MainActivity: consumer onNext: 2
2020-12-30 01:23:52.070 20081-20081/com.anshon.myapplication E/MainActivity: consumer onNext: 1
2020-12-30 01:23:52.070 20081-20081/com.anshon.myapplication E/MainActivity: consumer onNext: 2
2020-12-30 01:23:52.071 20081-20081/com.anshon.myapplication E/MainActivity: consumer onNext: 1
2020-12-30 01:23:52.071 20081-20081/com.anshon.myapplication E/MainActivity: consumer onNext: 2
2020-12-30 01:23:52.071 20081-20081/com.anshon.myapplication E/MainActivity: consumer onError: access denied!
```



## 其他

doOnNext  不订阅事件（非下游）的情况下，获取数据

```java
        Observable.just("1", "2", "3", "4")
                .doOnNext(s -> {
                    Log.e(TAG, "doOnNext: " + s);
                }).subscribe(s -> {
            Log.e(TAG, "subscribe: " + s);
        });

2021-01-11 23:17:53.647 15794-15794/com.anshon.myapplication E/MainActivity: doOnNext: 1
2021-01-11 23:17:53.647 15794-15794/com.anshon.myapplication E/MainActivity: subscribe: 1
2021-01-11 23:17:53.647 15794-15794/com.anshon.myapplication E/MainActivity: doOnNext: 2
2021-01-11 23:17:53.647 15794-15794/com.anshon.myapplication E/MainActivity: subscribe: 2
2021-01-11 23:17:53.647 15794-15794/com.anshon.myapplication E/MainActivity: doOnNext: 3
2021-01-11 23:17:53.647 15794-15794/com.anshon.myapplication E/MainActivity: subscribe: 3
2021-01-11 23:17:53.647 15794-15794/com.anshon.myapplication E/MainActivity: doOnNext: 4
2021-01-11 23:17:53.647 15794-15794/com.anshon.myapplication E/MainActivity: subscribe: 4
```

