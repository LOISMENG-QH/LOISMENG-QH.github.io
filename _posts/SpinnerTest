---
layout:       post  
title:        Spinner详解
subtitle:     spinner的用法、自定义spinner和spinner里面字体加粗的做法
date:         2021-09-11
auther:       Lois
header-img:   img/dingshiwu.jpg
catalog:      true  
tags:  
    - Quanta
    - Android

---

> "吃好再拉"

# Spinner详解

​	在app做选择的时候经常会看到下拉框的选择，想要学明白！就产出这篇博客啦啦啦

​	下拉框通常有两种呈现形式，一个是下拉式dropdown，一个是对话框时dialog，实际上两种方式的转换只需要改变spinnerMode这一属性即可，dialog式会多一个小标题。

​	因为我懒，就把要展示的功能都做到一个板块里了（现在先忽略下面的张子枫吧嘎嘎

<img src="D:\程序媛\安卓\博客\spinner\dropdown.gif" alt="dropdown" style="zoom:67%;" /><img src="D:\程序媛\安卓\博客\spinner\dialog.gif" alt="dialog" style="zoom:67%;" />

## spinner的常用属性	

| 属性名                           | 作用                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| android:dropDownHorizontalOffset | 设置列表框的水平偏移距离                                     |
| android:dropDownVerticalOffset   | 设置列表框的水平竖直距离                                     |
| android:dropDownSelector         | 列表框被选中时的背景                                         |
| android:dropDownWidth            | 设置下拉列表框的宽度                                         |
| android:gravity                  | 设置里面组件的对其方式                                       |
| android:popupBackground          | 设置列表框的背景                                             |
| android:prompt                   | 设置对话框模式的列表框的提示信息(标题)，只能够引用string.xml 中的资源id,而不能直接写字符串 |
| android:spinnerMode              | 列表框的模式，有两个可选值： dialog：对话框风格的窗口 dropdown：下拉菜单风格的窗口(默认) |
| 可选属性：android:entries        | 使用数组资源设置下拉列表框的列表项目                         |

## Spinner的用法

### 在布局里添加控件spinner，并设置好显示样式

最主要的是要设置spinnerMode样式和dropDownVerticalOffset（列表中每一项之间的垂直距离）

```java
<Spinner
            android:id="@+id/spinner_drop"
            android:layout_width="0dp"
            android:layout_weight="2"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:spinnerMode="dialog"
            android:dropDownVerticalOffset = "50dp"
            android:background="@null"/>
```

### 设置好数据源

spinner设置数据源有两种方式，一种是在xml文件里设置，一般适用于已经明确知道所需要数据的时候，一种是在java文件里面动态设置，比较灵活。

####  利用arrays.xml文件静态设置数据



先说说静态设置数据，与大部分控件相同。

先在values文件夹下新建arrays.xml文件，存放一个数据数组。

```java
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="hero">
        <item>易烊千玺</item>
        <item>迪丽热巴</item>
        <item>杨洋</item>
        <item>宋亚轩</item>
        <item>刘耀文</item>
        <item>乔晶晶</item>
        <item>于途</item>
        <item>虞姬</item>
    </string-array>
</resources>

```

在编写spinner布局的时候利用android:entries属性设置下拉列表的列表项目。

```java
<Spinner
            android:id="@+id/spinner_hero"
            android:layout_width="0dp"
            android:layout_weight="2"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:spinnerMode="dropdown"
            android:dropDownVerticalOffset = "50dp"
            android:background="@null"
            android:entries="@array/hero"/>
```

这样列表直接就可以获取到数据啦

<img src="D:\程序媛\安卓\博客\spinner\array.gif" alt="array" style="zoom:67%;" />

再按照设定的步骤进行点击事件的设置就很容易实现自己想要的功能啦

#### 动态设置数据源

##### 新建一个类来动态设置数据

```java
package com.example.spinnertest;

public class HeroBean {
    private int icon;
    private String name;

    public HeroBean() {
    }

    public HeroBean(int icon, String name) {
        this.icon = icon;
        this.name = name;
    }

    public int getIcon() {
        return icon;
    }

    public String getName() {
        return name;
    }

    public void setIcon(int icon) {
        this.icon = icon;
    }

    public void seName(String name) {
        this.name = name;
    }
}

```

##### 新建一个列表项目的布局，展示列表中的item，命名为spinner_item

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="5dp">


    <ImageView
        android:id="@+id/icon"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:src="@drawable/zf1"/>

    <TextView
        android:id="@+id/name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:text="张子枫"
        android:textSize="16sp" />
</LinearLayout>

```

![image-20210911195256999](C:\Users\蒙秋华\AppData\Roaming\Typora\typora-user-images\image-20210911195256999.png)

​										<!--类似于这样子 甚至可以做其他的自定义样式-->

##### 编写一个适配器，命名为MyAdapter（其实应该以这个adapter的作用明明会更好，比如HeroAdapter）

这个adapter的作用是返回选择的视图

Spinner 继承自 AdapterView，也就是说它也通过 Adapter 来提供数据支持

Spinner 默认会选中第一个值，就是默认调用 spinner.setSection(0)，我们也通过这个设置默认的选中值

```java
public class MyAdapter extends BaseAdapter {
    private List<HeroBean> mHeroBeans;
    private Context mContext;

    public MyAdapter(List<HeroBean> heroBeans, Context context) {
        this.mHeroBeans = heroBeans;
        this.mContext = context;
    }

    @Override
    public int getCount() {
        return mHeroBeans.size();
    }

    @Override
    public Object getItem(int i) {
        return mHeroBeans.get(i);
    }

    @Override
    public long getItemId(int i) {
        return i;
    }
    //设定下拉之前的布局
    @Override
    public View getView(int position, View view, ViewGroup viewGroup) {
        //绑定列表中item的布局
        LayoutInflater _LayoutInflater=LayoutInflater.from(mContext);
        view=_LayoutInflater.inflate(R.layout.spinner_hero, null);
        if(view!=null)
        {
            TextView textView=(TextView)view.findViewById(R.id.name);
            ImageView imageView=view.findViewById(R.id.icon);
            textView.setText(mHeroBeans.get(position).getName());
            imageView.setImageResource(mHeroBeans.get(position).getIcon());
        }
        return view;
    }
}



```

getView（）和getDropDownView（）的区别

##### 编写MainActivity

```java
private void initSpinnerHero() {
        //初始化绑定布局
        Spinner spinner_hero = findViewById(R.id.spinner_hero);
        final TextView hero_show=findViewById(R.id.hero_show);
        //新建一个ArrayList<HeroBean>存放数据
        mData=new ArrayList<HeroBean>();
        //因为自定义的HeroBean有一张图片和名字，两个数据都要传进去
        mData.add(new HeroBean(R.drawable.zf1,"张子枫"));
        mData.add(new HeroBean(R.drawable.zf1,"子枫妹妹"));
        mData.add(new HeroBean(R.drawable.zf1,"子枫妹"));
        mData.add(new HeroBean(R.drawable.zf1,"小张张"));
        mData.add(new HeroBean(R.drawable.zf1,"妹妹"));
        mData.add(new HeroBean(R.drawable.zf1,"疯子张"));
        //将这个ArrayList放进适配器中
        myAdapter = new MyAdapter(mData, this);
        //给我们的spinner设置适配器
        spinner_hero.setAdapter(myAdapter);

//        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN) {
//            spinner_hero.setPopupBackgroundResource(R.drawable.bg_spinner);
//            spinner_hero.setDropDownVerticalOffset(dip2px(20));
//        }
        spinner_hero.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override //选中的时候执行的方法
            public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
                Toast.makeText(MainActivity.this, mData.get(i).getName(), Toast.LENGTH_SHORT).show();
                hero_show.setText(mData.get(i).getName());
                myAdapter.setSelectedPostion(i);
            }

            @Override
            public void onNothingSelected(AdapterView<?> parent) {

            }
        });}
```

到这里为止，在OnCreate（）执行这个初始化放方法private void initSpinnerHero() ，我们的spinner就会有数据啦！

但是有数据，还没有完哦！

通常光弄出一个框框是满足不了我们的需求的，我们会用被选择的数据做些什么对啵，所以点击事件，也是很有必要的。

### spinner的点击事件

这个也很简单，使用spinner实例对象的setOnItemSelectedListener匿名内部方法。

```java
pinner_hero.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override //选中的时候执行的方法
            public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
                //出现一个Toast显示所选中的信息
                Toast.makeText(MainActivity.this, mData.get(i).getName(), Toast.LENGTH_SHORT).show();
                //在布局设定一个文本框显示所选中的信息，因为我的Data是HeroBean类，里面包含了一个图片和文本框，用类里面封装的方法获取文本内容
                hero_show.setText(mData.get(i).getName());
               
            }
```

或者

在MainActivity直接新建一个类MySelectedListener，接口是AdapterView.OnItemSelectedListener，override方法onItemSelected（）进行点击事件的响应。

```java
class MySelectedListener implements AdapterView.OnItemSelectedListener{

        @Override
        public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
            Toast.makeText(MainActivity.this, "你选择的是：" + talkArray[position],Toast.LENGTH_LONG).show();
        }

        @Override
        public void onNothingSelected(AdapterView<?> parent) {

        }
    }
```

### spinner效果

怎么实现spinner中被选中的选项字体加粗呢，例如这样![bold](D:\程序媛\安卓\博客\spinner\bold.gif)

我们在刚才自定义的adapter里面添加多一个变量

```java
private int selectedPostion;
```

然后在自定义的adapter里面添加这两个方法

```java
//实现被选中的文字加粗
    @Override
    public View getDropDownView(int position, View convertView, ViewGroup parent) {
        //获取下列列表的布局
        View view = super.getDropDownView(position, convertView, parent);
        TextView textView = (TextView)view.findViewById(R.id.name);
//      被选中的一项进行文本加粗
        if(selectedPostion == position){
            textView.setTextColor(0xff373741);
            textView.getPaint().setFakeBoldText(true);
        }
        else{
            textView.setTextColor(0xff6d6d6d);
            textView.getPaint().setFakeBoldText(false);
        }
        return view;
    }

    public void setSelectedPostion(int selectedPostion) {
        this.selectedPostion = selectedPostion;
    }
```

就可以啦！

# 小结

- Spinner会默认选中第一个值，就是默认调用spinner.setSection(0), 你可以通过这个设置默认的选中值
- 会在开头就触发一次OnItemSelectedListener 事件，暂时没找到解决方法，下面折衷的处理是：添加一个boolean值，然后设置 为false，在onItemSelected时进行判断，false说明是默认触发的，不做任何操作 将boolean值设置为true；true的话则正常触发事件！
- **android:prompt 属性使用常见问题**
  设置之后不起作用:prompt属性只有在dialog状态才有用，所以要在xml中，将style设置为Widget.Spinner
  prompt属性要用string下资源，不支持字符直接输入，否则会报错误

# 疑问

关于getView（）方法和   getDropDownView() 方法的区别

有待补充...



部分源码：

https://github.com/LOISMENG-QH/SpinnerTest.git

参考：

https://blog.csdn.net/Mq_sir/article/details/117928511

https://blog.csdn.net/chzphoenix/article/details/79414564?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-16.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-16.no_search_link





