---
layout:       post  
title:        startActivityForResult() 被弃用，来试试Activity Result API
subtitle:     Activity Result API的简单使用
date:         2022-08-29
auther:       Lois
header-img:   img/a8d26a405d26e771984e1a232a22357.jpg
catalog:      true  
tags:  
    - Quanta
    - Android

---

> "正式开学第一天![img](https://raw.githubusercontent.com/LOISMENG-QH/PicsStore/main/markdown/01C3533E.png)"

# startActivityForResult() 被弃用，来试试Activity Result API

​	

startActivityForResult()用于两个活动之间交换信息，今天用到的时候发现这个方法已经被弃用了。

![image-20220829115100882](https://raw.githubusercontent.com/LOISMENG-QH/PicsStore/main/markdown/image-20220829115100882.png)

​	效果图是这样子滴

<img src="https://raw.githubusercontent.com/LOISMENG-QH/PicsStore/main/markdown/8.29.gif" alt="8.29" style="zoom:50%;" />

​	了解到大家原来都已经用起了Activity Result API，今天把它拿下！

​	先来回顾一下startActivityForResult的使用

## startActivityForResult()的使用

### 1. 通过startActivityForResult()在MainAcitivity向SecondActivity请求数据

```java
Intent intent1 = new Intent(MainActivity.this, SecondActivity.class);
intent1.putExtra("come","来自活动一的数据");//给SecondActivity传送数据
startActivityForResult(intent1, 1);//向SecondActivity请求数据，并且进行跳转
```

### 2. 在SecondActivity中响应请求发送数据

通过getIntent（）和getStringExtra获得来自MainActivity的数据，实现两个活动的交流。

```java
Intent intent = getIntent();
String s = intent.getStringExtra("come");
```

用setResult（）方法设置要返回给MainActivity的数据。

```java
Intent intent2 = new Intent(SecondActivity.this, MainActivity.class);
intent2.putExtra("return", "活动二返回的数据");
setResult(RESULT_OK, intent2);
finish();
```

### 3. 在MainActivity的在onActivityResult()方法中去解析SecondActivity返回的结果

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    switch (requestCode) {
        case 1:
            if (resultCode == RESULT_OK) {
                String returnedDate = data.getStringExtra("return");
                Log.e(TAG, returnedDate);

            }
            break;
        default:
    }
    super.onActivityResult(requestCode, resultCode, data);
}
```

​	看得出来确实，一旦活动和数据多一些，onActivityResult就会变得臃肿耦合，resultcode也要不断进行扩充定义。

​	现在官方推荐使用Activity Result API代替startActivityForResult()方法

​	那这个要怎么用呢

## 使用Activity Result API

### 1.**在app下的build.gradle中加入依赖:**

```java
implementation 'androidx.activity:activity:1.2.0-beta01'
implementation 'androidx.fragment:fragment:1.3.0-beta01'
```

### 2.**定义协议**

在MainAcitivity中新建一个 ResultContract类继承于ActivityResultContract<I，O>，I是输入的数据类型，O是输出的数据类型。在下面的例子里两个活动的数据都定义为String。

我们在createIntent的方法里放入MainActivity传送给SecondActivity的数据，在parseResult里取出SecondActivity传回来的数据。

```java
class ResultContract extends ActivityResultContract<String, String> {
        @NonNull
        @Override
        public Intent createIntent(@NonNull Context context, String input) {
            Intent intent = new Intent(MainActivity.this, SecondActivity.class);
            intent.putExtra("come", input);
            return intent;
        }

        @Override
        public String parseResult(int resultCode, @Nullable Intent intent) {
            return intent.getStringExtra("return");
        }
    }
```

### 3.**注册协议，获取启动器-ActivityResultLauncher**

registerForActivityResult有两个参数第一个参数就是我们定义的Contract协议，第二个参数是一个回调ActivityResultCallback<O>,其中O就是前面Contract的输出类型。

在启动器中我们获取了返回的数据并且进行了展示。

```java
ActivityResultLauncher launcher = registerForActivityResult(new ResultContract(), new ActivityResultCallback<String>() {
    @Override
    public void onActivityResult(String result) {
        Toast.makeText(MainActivity.this, result, Toast.LENGTH_SHORT).show();
    }
});
```

### 4.**调用启动器的launch方法开启界面跳转**

最后在MainActivity中启动这个启动器

```java
launcher.launch("来自活动一的数据");
```



SecondActivity中的代码不变，在这里就不贴出来了。



这样就可以实现两个活动的数据传递。

## Contract协议说明

在新的ResultAPI中，虽然只是短短三步就可以实现数据传递，但是代码量并没有减少，使用起来每次都要定义Contract协议。好在！Google为开发者们预定义了很多Contract，可以供我们直接使用。



> StartActivityForResult() 
> RequestMultiplePermissions()
> RequestPermission()
> TakePicturePreview()
> TakePicture()
> TakeVideo()
> PickContact()
> CreateDocument()
> OpenDocumentTree()
> OpenMultipleDocuments()
> OpenDocument()
> GetMultipleContents()
> GetContent()

- **StartActivityForResult**: 通用的Contract,不做任何转换，Intent作为输入，ActivityResult作为输出，这也是最常用的一个协定。
- **RequestMultiplePermissions**：用于请求一组权限
- **RequestPermission**: 用于请求单个权限
- **TakePicturePreview**: 调用MediaStore.ACTION_IMAGE_CAPTURE拍照，返回值为Bitmap图片
- **TakePicture**: 调用MediaStore.ACTION_IMAGE_CAPTURE拍照，并将图片保存到给定的Uri地址，返回true表示保存成功。
- **TakeVideo**: 调用MediaStore.ACTION_VIDEO_CAPTURE 拍摄视频，保存到给定的Uri地址，返回一张缩略图。
- **PickContact**: 从通讯录APP获取联系人
- **GetContent**: 提示用选择一条内容，返回一个通过ContentResolver#openInputStream(Uri)访问原生数据的Uri地址（content://形式） 。默认情况下，它增加了Intent#CATEGORY_OPENABLE, 返回可以表示流的内容。
- **CreateDocument**: 提示用户选择一个文档，返回一个(file:/http:/content:)开头的Uri。
- **OpenMultipleDocuments**: 提示用户选择文档（可以选择多个），分别返回它们的Uri，以List的形式。
- **OpenDocumentTree**: 提示用户选择一个目录，并返回用户选择的作为一个Uri返回，应用程序可以完全管理返回目录中的文档。

有了这些预定义的Contract，使用起来就方便多啦，不仅可以轻松实现数据传递，还可以进行拍照，选择图片，选择联系人，打开文档等等返回数据的场景。



## 简化一下预定义的Contract使用吧

### 1.在MainActivity中定义启动器

在启动器中处理返回的数据，如果在SecondAcitivity中设置的result不为空并且resultcode为ok，则对数据进行处理。

```java
ActivityResultLauncher<Intent> intentActivityResultLauncher = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
    @Override
    public void onActivityResult(ActivityResult result) {
        //此处是跳转的result回调方法
        //如果在SecondAcitivity中设置的result不为空并且resultcode为ok，则对数据进行处理
        if (result.getData() != null && result.getResultCode() == Activity.RESULT_OK) {
            String msg = result.getData().getStringExtra("return");
            Toast.makeText(getApplicationContext(), msg, Toast.LENGTH_LONG).show();
        } else {
            Toast.makeText(getApplicationContext(), "获取的数据为空", Toast.LENGTH_LONG).show();
        }
    }
});
```

### 2.运行启动器

```java
button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent intent = new Intent(MainActivity.this, SecondActivity.class);
                intent.putExtra("come","来自活动一的数据");
                intentActivityResultLauncher.launch(intent);
            }
        });

```

就可以啦！！！

吼吼吼！这样看来真的很方便，但还是摸索了一个下午QAQ，网上大多数资料都是kotlin版本的，惭愧残惭愧，还没学习kotlin。

总的来说，有了官方预定义的Contract确实对于很多数据回调的场景提供了便利，也可以自己自定义一个新的contract，具有一定的灵活性，真不戳！

期待下一次更深层次的挖掘。先这样吧，俺累了。

