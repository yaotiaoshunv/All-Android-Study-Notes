

一、前言

首先来看一个示例，会输出什么呢？

```java
public class StartActivity extends AppCompatActivity {
    Button btnStartMain;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_start);
        btnStartMain = findViewById(R.id.btn_start_main);

        Log.e("TAG", "height1 - " + btnStartMain.getMeasuredHeight());

        btnStartMain.postDelayed(new Runnable() {
            @Override
            public void run() {
                Log.e("TAG", "height2 - " + btnStartMain.getMeasuredHeight());
            }
        }, 0L);
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.e("TAG", "height3 - " + btnStartMain.getMeasuredHeight());
    }
}
```

结果：

![image-20210308104015222](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210308104015222.png)



二、



