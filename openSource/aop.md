# 面向切面编程AOP

## 1.动态代理

```java
public interface DbOperation {
    void insert(String key, String value);
    void delete(String key);
    void save();
}
```

```java
	public class MainActivity extends AppCompatActivity implements DbOperation {
    private static final String TAG = "MainActivity";
    private ImageView imageView;
    private DbOperation db;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        imageView = findViewById(R.id.image_view);
        db = (DbOperation) Proxy.newProxyInstance(getClassLoader(), new Class[]{DbOperation.class}, new DbHelper(this));
    }
    
    class DbHelper implements InvocationHandler {

        DbOperation dbOperation;

        public DbHelper(DbOperation operation) {
            dbOperation = operation;
        }

        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            dbOperation.save();
            return method.invoke(dbOperation, args);
        }
    }


    public void testAop(View view) {
        db.insert("hello", "there");
    }

    @Override
    public void insert(String key, String value) {
        Log.e(TAG, "insert: ");
    }

    @Override
    public void delete(String key) {
        Log.e(TAG, "delete: ");
    }

    @Override
    public void save() {
        Log.e(TAG, "save: ");
    }
}

2021-01-19 00:25:10.238 19951-19951/com.anshon.myapplication E/MainActivity: save: 
2021-01-19 00:25:10.239 19951-19951/com.anshon.myapplication E/MainActivity: insert: 
```



## 2.AspectJ预编译织入

使用AspectJ 在 java文件编译成class文件时织入代码

1.切入点   pointcut，需要切面的点在哪里

2.通知   advice，向切入点注入代码的实现方法，有  before、after、around，切入点代码运行（之前，之后，前后），运行织入代码

3.连接点 joint point，所有的目标方法都是连接点



工程配置：

1.工程目录build.gradle

```groovy
buildscript {
    repositories {
        google()
        jcenter()
    }
    dependencies {
        ...
        classpath "org.aspectj:aspectjtools:1.9.6"
        classpath "org.aspectj:aspectjweaver:1.9.6"
    }
}
```

2.app目录build.gradle

```groovy
apply plugin: 'com.android.application'

buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.aspectj:aspectjtools:1.9.6"
        classpath "org.aspectj:aspectjweaver:1.9.6"
    }
}
...
dependencies {
  	...
    implementation group: 'org.aspectj', name: 'aspectjrt', version: '1.9.6'
}
```

3.注解类

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ClickBehavior {
    String value();
}
```

4.切面类

```java
@Aspect
public class ClickBehaviorAspect {
    private static final String TAG = "ClickBehaviorAspect";

    //1. 找到需要处理的切入点，
    // excution,以方法执行时作为切入点，触发aspect类
    // * *(..) 可以处理ClickBehavior这个类的所有方法
    @Pointcut("execution(@com.anshon.myapplication.annotation.ClickBehavior * *(..))")
    public void methodPointCut() {
    }//函数名随便定义

    //2.对切入点如何处理
    @Around("methodPointCut()")
    public Object jointPoint(ProceedingJoinPoint joinPoint) throws Throwable {
        //获取签名方法
        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();

        //获取方法所属类名
        String className = methodSignature.getDeclaringType().getSimpleName();

        //获取方法名
        String methodName = methodSignature.getName();

        //获取方法注解值
        String annotValue = methodSignature.getMethod().getAnnotation(ClickBehavior.class).value();

        Context context = (Context) joinPoint.getThis();

        boolean spIsLogin = false;
        if (spIsLogin) {
            Log.d(TAG, "is login, continue");
            return joinPoint.proceed();
        } else {
            Log.e(TAG, "is not login, start login activity");
            context.startActivity(new Intent(context, LoginActivity.class));
            return null;
        }
    }
}
```



## 3.asm

https://juejin.cn/post/6888555395074555918