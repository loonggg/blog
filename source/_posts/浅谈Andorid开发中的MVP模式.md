---
title: 浅谈Andorid开发中的MVP模式
date: 2016-01-26 12:39:04
tags:
- MVP
- MVC
categories: 
- 技术博客
---
![](http://7xsgef.com1.z0.glb.clouddn.com/loonggg640.jpg)

>导语：最近公众号后台经常收到一些消息，说能不能讲一些开发模式，经过思考后，我决定讲一讲MVP模式。希望对大家能够有所帮助。并写了一个简单的小demo。
    
#### 背景
看到MVP，大家肯定会想什么是MVP呢？这个我可以肯定的告诉大家MVP（Most Valuable Player）是最有价值球员的意思，这当然是开玩笑了。之所以会出现MVP这种架构模式，是因为我相信大家在开发App时，肯定会发现，Activity的负担非常重，既要初始化控件，又要写一些逻辑操作的展示等等，有时候很多Activity中的代码都充当了Controller和Model的角色，所以你会发现Activity违背单一职责原则，负担过重。所以，就出现了这么一种架构模式，叫MVP，并不是最有价值球员哦。
<!--more-->
**什么是MVP架构**
MVP就是Model-View-Presenter，MVP是从经典的模式MVC演变而来，它们的基本思想有相通的地方：Controller/Presenter负责逻辑的处理，Model提供数据，View负责显示。作为一种新的模式，MVP与MVC有着一个重大的区别：在MVP中View并不直接使用Model，它们之间的通信是通过Presenter (MVC中的Controller)来进行的，所有的交互都发生在Presenter内部，而在MVC中View会直接从Model中读取数据而不是通过 Controller。

在MVC里，View是可以直接访问Model的！从而，View里会包含Model信息，不可避免的还要包括一些业务逻辑。 在MVC模型里，更关注的Model的不变，而同时有多个对Model的不同显示，及View。所以，在MVC模型里，Model不依赖于View，但是View是依赖于Model的。不仅如此，因为有一些业务逻辑在View里实现了，导致要更改View也是比较困难的，至少那些业务逻辑是无法重用的。

用流程图的方式解释就更清楚了：
![](http://7xsgef.com1.z0.glb.clouddn.com/loonggg6401.png)

**MVP和MVC的区别，及MVP是如何解决MVC的问题？**

MVP架构：

    View： 对应于Activity，负责View的绘制以及与用户交互
    Model： 依然是业务逻辑和实体模型
    Presenter： 负责完成View于Model间的交互
![](http://7xsgef.com1.z0.glb.clouddn.com/loonggg6402.png)


* View不直接与Model交互，而是通过与Presenter交互来与Model间接交互。
* Presenter与View的交互是通过接口来进行的。
* 通常View与Presenter是一对一的，但复杂的View可能绑定多个Presenter来处理逻辑。


MVC架构：

    View：对应于布局文件

    Model：业务逻辑和实体模型

    Controllor：对应于Activity
![](http://7xsgef.com1.z0.glb.clouddn.com/loonggg6403.png)

- View可以与Model直接交互。
- Controller是基于行为的，并且可以被多个View共享。
- 可以负责决定显示哪个View。

总结解释一下就是说：从MVC到MVP的一个转变，就是减少了Activity的职责，减轻了它的负担，简化了Activity中的代码和一些操作，将逻辑代码提取到了Presenter中进行处理，降低了其耦合度。

进一步的解释：

>在MVP里，Presenter完全把Model和View进行了分离，主要的程序逻辑在Presenter里实现。而且，Presenter与具体的View是没有直接关联的，而是通过定义好的接口进行交互，从而使得在变更View时候可以保持Presenter的不变，即重用！ 不仅如此，我们还可以编写测试用的View，模拟用户的各种操作，从而实现对Presenter的测试--而不需要使用自动化的测试工具。 我们甚至可以在Model和View都没有完成时候，就可以通过编写Mock Object（即实现了Model和View的接口，但没有具体的内容的）来测试Presenter的逻辑。 在MVP里，应用程序的逻辑主要在Presenter来实现，其中的View是很薄的一层。因此就有人提出了Presenter First的设计模式，就是根据User Story来首先设计和开发Presenter。在这个过程中，View是很简单的，能够把信息显示清楚就可以了。在后面，根据需要再随便更改View，而对Presenter没有任何的影响了。 如果要实现的UI比较复杂，而且相关的显示逻辑还跟Model有关系，就可以在View和Presenter之间放置一个Adapter。由这个 Adapter来访问Model和View，避免两者之间的关联。而同时，因为Adapter实现了View的接口，从而可以保证与Presenter之间接口的不变。这样就可以保证View和Presenter之间接口的简洁，又不失去UI的灵活性。 在MVP模式里，View只应该有简单的Set/Get的方法，用户输入和设置界面显示的内容，除此就不应该有更多的内容，绝不容许直接访问Model--这就是与MVC很大的不同之处。


**MVP的优点**

    1.降低耦合度，隐藏数据，Activity中代码更简洁

    2.模块职责划分明显
    3.方便测试驱动开发
    4.代码复用度较高
    5.代码灵活性


**MVP架构模式实例**

这个实例是根据用户id获取用户信息并展示的一个过程，其中获取用户信息用了一个线程进行了模拟获取。希望大家能够看懂，并对大家有所帮助。

我们先看一下MVP目录结构图
![](http://7xsgef.com1.z0.glb.clouddn.com/loonggg6404.png)

1、Model层
首先是一个javabean User实体类
```java
package net.loonggg.mvpdemo.bean;
public class User {    
    private String name;    
    private String id;    
    private String sex;    
    private String age;    
    public String getName() {        
        return name;
    }    
    
    public void setName(String name) {        
        this.name = name;
    }    
    public String getId() {        
        return id;
    }    
    public void setId(String id) {        
        this.id = id;
    }    
    public String getSex() {        
        return sex;
    }    
    public void setSex(String sex) {        
        this.sex = sex;
    }    
    public String getAge() {        
        return age;
    }    
    public void setAge(String age) {        
        this.age = age;
    }

}
```
Model层抽象接口实现：
```java
package net.loonggg.mvpdemo.model;
import net.loonggg.mvpdemo.bean.User;

public class GetUserInfo implements IGetUser {    
    @Override
    public void getUserInfo(final int id, final OnUserInfoListener listener) {        
        // 模拟子线程耗时操作
        new Thread() {            
            @Override
            public void run() {                
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }                // 模拟信息获取成功
                if (id == 1) {
                    User user = new User();
                    user.setName("非著名程序员");
                    user.setAge("26");
                    user.setSex("男");
                    user.setId("1");
                    listener.getUserInfoSuccess(user);
                } else {
                    listener.getUserInfoFailed();
                }
            }
        }.start();
    }

}
```
Model层抽象接口
```java
package net.loonggg.mvpdemo.model;
public interface IGetUser {    
    public void getUserInfo(int id, OnUserInfoListener listener);
}

package net.loonggg.mvpdemo.model;
import net.loonggg.mvpdemo.bean.User;

public interface OnUserInfoListener {    
    void getUserInfoSuccess(User user);   
     
    void getUserInfoFailed();
}
```
2、View层
我们都知道Presenter与View交互是通过接口，所以我们需要定义一个IShowUserView的接口，这个接口封装的方法基本上都跟视图展示有关。
```java
package net.loonggg.mvpdemo.view;
import net.loonggg.mvpdemo.bean.User;

public interface IShowUserView {    
    void showLoading();    
    void hideLoading();    
    void toMainActivity(User user);    
    void showFailedError();
}
```
3、Presenter层
Presenter是Model和View之间交互的桥梁，里面有一些业务逻辑的操作。
```java
public class UserInfoPresenter {    
    private IGetUser iGetUser;    
    private IShowUserView iShowUserView;    
    private Handler mHandler = new Handler();    
    public UserInfoPresenter(IShowUserView iShowUserView) {        
        this.iShowUserView = iShowUserView;        
        this.iGetUser = new GetUserInfo();
    }    
    
    public void getUserInfoToShow(int id) {
        iShowUserView.showLoading();
        iGetUser.getUserInfo(id, new OnUserInfoListener() {            @Override
            public void getUserInfoSuccess(final User user) {                // 需要在UI线程执行
                mHandler.post(new Runnable() {                    @Override
                    public void run() {
                        iShowUserView.toMainActivity(user);
                        iShowUserView.hideLoading();
                    }
                });
            }            @Override
            public void getUserInfoFailed() {
                mHandler.post(new Runnable() {                    @Override
                    public void run() {
                        iShowUserView.showFailedError();
                        iShowUserView.hideLoading();
                    }
                });
            }
        });
    }

}
```
4、Activity中的调用
```java
public class MainActivity extends Activity implements IShowUserView {    
    private Button btn;    
    private TextView name_tv, age_tv, sex_tv;    
    private ProgressDialog pd = null;    
    
    private UserInfoPresenter userInfoPresenter;    
    @Override
    protected void onCreate(Bundle savedInstanceState) {        
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        userInfoPresenter = new UserInfoPresenter(this);
        btn = (Button) findViewById(R.id.btn);
        name_tv = (TextView) findViewById(R.id.name_tv);
        age_tv = (TextView) findViewById(R.id.age_tv);
        sex_tv = (TextView) findViewById(R.id.sex_tv);
        pd = new ProgressDialog(this);
        pd.setMessage("正在加载……");

        btn.setOnClickListener(new View.OnClickListener() {            @Override
            public void onClick(View v) {
                userInfoPresenter.getUserInfoToShow(1);
            }
        });
    }    
    
    @Override
    public void showLoading() {
        pd.show();
    }    
    
    @Override
    public void hideLoading() {
        pd.cancel();
    }   
    
    @Override
    public void toMainActivity(User user) {
        name_tv.setText(user.getName());
        age_tv.setText(user.getAge());
        sex_tv.setText(user.getSex());
    }    
    
    @Override
    public void showFailedError() {
        Toast.makeText(this, "获取信息有误", Toast.LENGTH_SHORT).show();
    }

}
```
    结语：看完实例代码，有点感觉了吧？俗话说好记性不如烂笔头，看不如写，试着自己去写一个，领会一下其中的精神，相信你会豁然开朗。当然有人说这么做，是不是又多了一层，感觉又麻烦了，是吗？降低了耦合度，提取了代码，并增加了复用，代码更简洁，其实好处还是很多的。


获取demo的方法和以前一样，只需要在公众号内回复关键字“MVP”即可获得。

