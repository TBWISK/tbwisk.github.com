---
layout: post
category : read
tagline: "小技巧"
tags : [读书笔记,android]
date: 2015-02-10 15:01:45
title: Android Hacks 9 避免Edittext的时间验证
---


> ###为什么要避免时间验证  
首先，我们确定的是时间的格式通常是如何的？当我们使用Edittext输入时间的时候，是不是需要 - 来区别呢？系统通常都是用ms来表示时间，然后转换为显示的时间。所以，我们应该尽量的避免时间的验证，那么问题就有了。如何去避免呢？就是今天的内容。    

#### 涉及的知识点  
DialogFragment  DatePicker  观察者模式
 
> 1.DialogFragment的使用  

> 2.DatePicker的使用和理解  

> 3.如何使用回调  

#### interface  
下面那个String 是为了传递数据，int 可以判断传递的类型。   
当知道多种类型后，那么复用这个问题就来了。  
```java
public interface Settime {
    public void onclick(String name,int item);
}
```
#### 首先明确我们拥有的布局文件
> 下面的布局是点击后的弹窗布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <DatePicker
        android:id="@+id/datepicker"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:calendarViewShown="false" />

    <Button
        android:id="@+id/button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="提交" />

</LinearLayout>
```

上面的布局是不是很简单呢？是的，因为Fragment是一个可以碎片，通常用于一个Activity用于展示多种页面。可以让app的页面更丰富和精彩。

 
```java
public class Test5_1 extends DialogFragment {

    private String dateString;
    private Settime onitem;

    @Override
    public void onAttach(Activity activity) {
      
        try {
            onitem = (Settime) activity;
        } catch (Exception e) {
           
        }

        super.onAttach(activity);
    }

    @Override
    public View onCreateView(LayoutInflater inflater,
            @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View v = inflater.inflate(R.layout.test5_1, null);
        DatePicker datePicker = (DatePicker) v.findViewById(R.id.datepicker);
        // datePicker.setc
        Calendar calendar = Calendar.getInstance();

        int year = calendar.get(Calendar.YEAR);
        int monthOfYear = calendar.get(Calendar.MONTH);
        int dayOfMonth = calendar.get(Calendar.DAY_OF_MONTH);

        String monthOfYear__;
        String dayOfMonth__;
        if (monthOfYear <= 8) {
            monthOfYear__ = "0" + (monthOfYear + 1);
        } else {
            monthOfYear__ = "" + (monthOfYear + 1);
        }
        if (dayOfMonth <= 8) {
            dayOfMonth__ = "0" + dayOfMonth;
        } else {
            dayOfMonth__ = "" + dayOfMonth;
        }
        dateString = "" + year + "-" + monthOfYear__ + "-" + dayOfMonth__;
        // 上面的是初始化时间

        datePicker.setMaxDate(calendar.getTimeInMillis());

        datePicker.init(year, monthOfYear, dayOfMonth,
                new OnDateChangedListener() {

                    public void onDateChanged(DatePicker view, int year,
                            int monthOfYear, int dayOfMonth) {
                        // 对下面对时间格式处理
                        String monthOfYear_;
                        if (monthOfYear <= 8) {
                            monthOfYear_ = "0" + (monthOfYear + 1);
                        } else {
                            monthOfYear_ = "" + (monthOfYear + 1);
                        }

                        String dayOfMonth_;

                        if (dayOfMonth <= 8) {
                            dayOfMonth_ = "0" + (dayOfMonth);
                        } else {
                            dayOfMonth_ = "" + (dayOfMonth);
                        }
                        // 对上面时间格式处理
                        dateString = "" + year + "-" + monthOfYear_ + "-"
                                + dayOfMonth_;
                    }

                });
        Button button = (Button) v.findViewById(R.id.button);
        button.setOnClickListener(new OnClickListener() {

            @Override
            public void onClick(View v) {
                // TODO Auto-generated method stub
                onitem.onclick(dateString, 0);
                dismiss();
            }
        });

        return v;
    }
}

```

#### 知识点    
上面利用 Calendar 实现了DatePicker 数据的初始化。  
然后为什么这么复杂？  
因为如果点击进去，时间没有 改变，那么会没有返回时间，当然也可以在一开始的Activity中设定好时间。这里就需要注意在button的点击事件中判断 datepicker是不是有改变过了。  

public void onAttach(Activity activity) 这是不是很熟悉呢？为了绑定 activtiy中的回调函数而使用，从而达到在DialogFragment中调用Activity的回调函数，从DialogFragment的数据传递到Activity中。  
预览：  
<img src="/assets/picture/20150209205652.png">



 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <Button
        android:id="@+id/button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/edit_text"
        android:text="dddsdfdsfdsd" />

</LinearLayout>
```

```java
public class Test5 extends FragmentActivity implements Settime{

    @Override
    protected void onCreate(Bundle savedInstanceState) {
       
        super.onCreate(savedInstanceState);

        
        setContentView(R.layout.test5);
        Button button = (Button)findViewById(R.id.button);
        button.setOnClickListener(onClickListener );
            

    }
    private OnClickListener onClickListener = new  OnClickListener() {
        
        @Override
        public void onClick(View v) {
            getSupportFragmentManager().beginTransaction().add(new Test5_1(), "").commit();
        }
    };

    @Override
    public void onclick(String name, int item) {
        Button button = (Button)findViewById(R.id.button);
        button.setText(name);
    }
}
```
#### 知识点  
其实这里没多大知识点，唯一注意的是getSupportFragmentManager() 和getFragmentManger() 的兼容性问题。  

预览：  
<img src="/assets/picture/20150209205635.png">

### Tag  
学会简单的思想方式避免问题的复杂化。减少复杂代码，那么以后的问题和繁琐的过程就会少很多。

  