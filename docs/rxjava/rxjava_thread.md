## 线程切换

1. subscribeOn 上游不论切换多少次线程，只执行第一次

   observeOn 下游线程切换多次，每次都会执行，效果相当于执行最后一次

```java
        Observable.create((ObservableOnSubscribe<Integer>) emitter -> {
            Log.e(TAG, "creator: " + Thread.currentThread().getName());
            emitter.onNext(1);
        })
        .subscribeOn(AndroidSchedulers.mainThread())
        .subscribeOn(AndroidSchedulers.mainThread())//忽略
        .subscribeOn(Schedulers.io())//忽略
        .observeOn(AndroidSchedulers.mainThread())//执行
        .observeOn(AndroidSchedulers.mainThread())//执行
        .observeOn(Schedulers.io())//执行
        .subscribe(integer -> Log.e(TAG, "consumer accept: " + Thread.currentThread().getName()));

2020-12-30 08:00:19.590 31257-31257/com.anshon.myapplication E/MainActivity: creator: main
2020-12-30 08:00:19.591 31257-31310/com.anshon.myapplication E/MainActivity: consumer accept: RxCachedThreadScheduler-2
```

2.设置异步生产时，生产端并发执行全部完成后，消费端才能接收到

```java
Observable.create((ObservableOnSubscribe<Integer>) emitter -> {
            Log.e(TAG, "onNext: 1");
            emitter.onNext(1);
            Log.e(TAG, "onNext: 2");
            emitter.onNext(2);
            Log.e(TAG, "onNext: 3");
            emitter.onNext(3);
            emitter.onComplete();
        })
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(integer -> Log.e(TAG, "consumer accept: "+integer));

2020-12-30 08:06:45.244 32634-32751/com.anshon.myapplication E/MainActivity: onNext: 1
2020-12-30 08:06:45.245 32634-32751/com.anshon.myapplication E/MainActivity: onNext: 2
2020-12-30 08:06:45.245 32634-32751/com.anshon.myapplication E/MainActivity: onNext: 3
2020-12-30 08:06:45.249 32634-32634/com.anshon.myapplication E/MainActivity: consumer accept: 1
2020-12-30 08:06:45.249 32634-32634/com.anshon.myapplication E/MainActivity: consumer accept: 2
2020-12-30 08:06:45.249 32634-32634/com.anshon.myapplication E/MainActivity: consumer accept: 3
```



## 应用：加载图片

```java
        Observable.just(path).map(s -> {
            Log.e(TAG, "testRXJava: "+Thread.currentThread().getName());
            try {
                URL url = new URL(s);
                HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                connection.setConnectTimeout(5000);
                if (connection.getResponseCode() == HttpURLConnection.HTTP_OK) {
                    Bitmap bitmap = BitmapFactory.decodeStream(connection.getInputStream());
                    return bitmap;
                }

            } catch (Exception e) {
                e.printStackTrace();
            }
            return null;
        }).subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(bitmap -> {
                    imageView.setImageBitmap(bitmap);
        });
```

