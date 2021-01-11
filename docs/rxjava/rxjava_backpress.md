# 背压模式



当发射事件太多导致订阅者来不及消费而堵塞时，引入背压概念，rxjava2引入。背压式被订阅者Flowable与Observable使用方式上一致，只是使用场景不同。

创建背压生产者需指定策略：

BackpressureStrategy.MISSING   不带缓存硬钢，直到累死，且不在下游报错

BackpressureStrategy.ERROR  缓存池满报错   （次常用）

BackpressureStrategy.BUFFER 缓存等待处理  （常用）

BackpressureStrategy.DROP 缓存池满了丢弃

BackpressureStrategy.LATEST  缓存池只缓存最新值

```java
        Flowable.create((FlowableOnSubscribe<String>) emitter -> {
            for (int i = 0; i < Integer.MAX_VALUE; i++) {
                emitter.onNext("emitter:" + i);
            }
            emitter.onComplete();
        }, BackpressureStrategy.BUFFER)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Subscriber<String>() {
                    @Override
                    public void onSubscribe(Subscription s) {
                        Log.e(TAG, "onSubscribe: " + s);
                        s.request(Integer.MAX_VALUE); //接收所有事件
                    }

                    @Override
                    public void onNext(String s) {
                        Log.e(TAG, "onNext: " + s);
                        try {
                            Thread.sleep(5);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }

                    @Override
                    public void onError(Throwable t) {
                        Log.e(TAG, "onError: " + t.getMessage());
                    }

                    @Override
                    public void onComplete() {
                        Log.e(TAG, "onComplete: ");
                    }
                });
    }
```

