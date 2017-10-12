---
layout: post
category : read
tagline: "单纯的翻译"
tags : [Tools,android]
date: 2015-02-26 15:01:45
title: butterknife 的使用
---


butterknife 出处 ：  
官方网址：http://jakewharton.github.io/butterknife/    
github地址：https://github.com/JakeWharton/butterknife   

> ### 这篇文章的目的  
首先，不是很多人都会看得懂英文，总需要有翻译的人。那么我就翻译一下吧。
     
转载请注明出处：http://tbwisk.github.io/    

> #### 介绍    

用 Butter Knife 注射字段 @InjectView 和一个视图 ID，并且自动地在适配你的视图在你的布局文件中layout 

```java
class ExampleActivity extends Activity {
  @InjectView(R.id.title) TextView title;
  @InjectView(R.id.subtitle) TextView subtitle;
  @InjectView(R.id.footer) TextView footer;

  @Override public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.simple_activity);
    ButterKnife.inject(this);
    // TODO Use "injected" views...
  }
}
```

这种方法替代了缓慢的映射和生成代码来执行寻找视图。调用注入代表到这个合成的代码是你可以看见和调试的。    
  
上面实例的生成代码大致相当如下：  

```java
public void inject(ExampleActivity activity) {
  activity.subtitle = (android.widget.TextView) activity.findViewById(2130968578);
  activity.footer = (android.widget.TextView) activity.findViewById(2130968579);
  activity.title = (android.widget.TextView) activity.findViewById(2130968577);
}
```


##### 没有 Activity 的时候的注入    

你可以通过提供的根视图，执行任何注射在任意的目标在根视图中。  

```java
public class FancyFragment extends Fragment {
  @InjectView(R.id.button1) Button button1;
  @InjectView(R.id.button2) Button button2;

  @Override public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.fancy_fragment, container, false);
    ButterKnife.inject(this, view);
    // TODO Use "injected" views...
    return view;
  }
}
```

另外一个是在适配器列表中简化 视图 holder模式    

```java
public class MyAdapter extends BaseAdapter {
  @Override public View getView(int position, View view, ViewGroup parent) {
    ViewHolder holder;
    if (view != null) {
      holder = (ViewHolder) view.getTag();
    } else {
      view = inflater.inflate(R.layout.whatever, parent, false);
      holder = new ViewHolder(view);
      view.setTag(holder);
    }

    holder.name.setText("John Doe");
    // etc...

    return view;
  }

  static class ViewHolder {
    @InjectView(R.id.title) TextView name;
    @InjectView(R.id.job_title) TextView jobTitle;

    public ViewHolder(View view) {
      ButterKnife.inject(this, view);
    }
  }
}
```

你可以看到这个实现的行动提供样品。    

呼叫 ButterKnife.inject 你可以在任何地方把findViewById调用。  

注射提供的其他api:   

*  注入任意对象使用一个活动作为视图根。如果你使用像MVC模式，你可以用ButterKnife注入控制器使用其Activity 如 ButterKnife.inject(this, activity).    
*  字段注射一个视图的孩子使用 ButterKnife.inject(this) 。如果你使用 merge 标签在一个布局文件中和inflate  构造一个定制视图，你可以在之后马上调用。作为一种选择，定制视图类型 通常从  Xml中inflate，可以使用它在onFinishInflate()回调。   


##### View Lists  

你可以把多个视图打入列表或数组。List and Array    

```java
@InjectViews({ R.id.first_name, R.id.middle_name, R.id.last_name })
List<EditText> nameViews;
The apply method allows you to act on all the views in a list at once.

ButterKnife.apply(nameViews, DISABLE);
ButterKnife.apply(nameViews, ENABLED, false);
Action and Setter interfaces allow specifying simple behavior.

static final Action<View> DISABLE = new Action<>() {
  @Override public void apply(View view, int index) {
    view.setEnabled(false);
  }
}
static final Setter<View, Boolean> ENABLED = new Setter<>() {
  @Override public void set(View view, Boolean value, int index) {
    view.setEnabled(value);
  }
}

```

一个Android属性也可以使用与 apply 方法。

```java
ButterKnife.apply(nameViews, View.ALPHA, 0);
```

##### LISTENER INJECTION  监听注入  

监听器还可以自动配置方法。   

```java
@OnClick(R.id.submit)
public void submit(View view) {
  // TODO submit data to server...
}
```

所有侦听器方法的参数是可选的。   

```java
@OnClick(R.id.submit)
public void submit() {
  // TODO submit data to server...
}
```


定义一个特定的类型,它将自动计算执行。  

```java
@OnClick(R.id.submit)
public void sayHi(Button button) {
  button.setText("Hello!");
}
```

指定多个id为共同在一个绑定事件处理。   

```java
@OnClick({ R.id.door1, R.id.door2, R.id.door3 })
public void pickDoor(DoorView door) {
  if (door.hasPrizeBehind()) {
    Toast.makeText(this, "You win!", LENGTH_SHORT).show();
  } else {
    Toast.makeText(this, "Try again", LENGTH_SHORT).show();
  }
}
```

自定义视图可以绑定自己的听众不指定一个ID。   

```java
public class FancyButton extends Button {
  @OnClick
  public void onClick() {
    // TODO do something!
  }
}
```


##### INJECTION RESET  注射清零   

Fragments 比Activity 生命周期有不同的历程。当在Fragments 中的 onCreateView注入时,在Fragment 的onDestroyView 中视图设置为null。Butter Knife 有一个自动复位 rest 方法来做到这一点。

```java
public class FancyFragment extends Fragment {
  @InjectView(R.id.button1) Button button1;
  @InjectView(R.id.button2) Button button2;

  @Override public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.fancy_fragment, container, false);
    ButterKnife.inject(this, view);
    // TODO Use "injected" views...
    return view;
  }

  @Override public void onDestroyView() {
    super.onDestroyView();
    ButterKnife.reset(this);
  }
}
```


##### OPTIONAL INJECTIONS 可选择注射   

默认情况下,@InjectView和侦听器注射都是必需的。如果无法找到目标的观点将抛出一个异常。  

抑制这种行为并创建一个可选的注入,@Optional注释添加到字段或方法。

```java
@Optional @InjectView(R.id.might_not_be_there) TextView mightNotBeThere;

@Optional @OnClick(R.id.maybe_missing) void onMaybeMissingClicked() {
  // TODO ...
}
```

##### MULTI-METHOD LISTENERS 多个方法的监听器    

方法注释相应的监听器有多个回调可以绑定到任何其中之一。每一个注释它都绑定有一个默认的回调。指定一个替代使用回调参数。  

```java
@OnItemSelected(R.id.list_view)
void onItemSelected(int position) {
  // TODO ...
}

@OnItemSelected(value = R.id.maybe_missing, callback = NOTHING_SELECTED)
void onNothingSelected() {
  // TODO ...
}
```


##### BONUS   

还包括findById方法简化代码,还需要找到视图在 View , Activity 或 Dialog。它使用泛型来推断返回类型,并自动执行。    

```java
View view = LayoutInflater.from(context).inflate(R.layout.thing, null);
TextView firstName = ButterKnife.findById(view, R.id.first_name);
TextView lastName = ButterKnife.findById(view, R.id.last_name);
ImageView photo = ButterKnife.findById(view, R.id.photo);
```

添加一个静态导入ButterKnife.findById 和 享受更多的乐趣。

