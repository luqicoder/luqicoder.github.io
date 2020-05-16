---
title: 利用QSignalMapper为无参数的信号函数增加参数传递
date: 2020-03-18 08:34:57
categories: 
- C++
- Qt
tags:
- 信号与槽
---

## 前言：

最近需要完成一个课程设计，碰到了一个问题。

![image-20200316175938563](C:\Users\卢奇\AppData\Roaming\Typora\typora-user-images\image-20200316175938563.png)

如上图，我想要点击不同按钮在窗体中绘制不同图形。一般思路就是通过信号与槽，连接按钮与界面的update()信号进行图形界面更新。

 <!-- more -->

```C++
QObject::connect(Btn,  SIGNAL(clicked()), drawWidget, SLOT(update()));

QObject::connect(Btn1,  SIGNAL(clicked()), drawWidget, SLOT(update()));

QObject::connect(Btn2，SIGNAL(clicked()), drawWidget, SLOT(update()));
```

当窗体接受到update()信号后，利用事件过滤器，调用drawing()函数进行绘图操作

```c++
bool Widget::eventFilter(QObject *watched, QEvent *event)
{
    if(watched == rightWidget && event->type() == QEvent::Paint)
    {
        drawing(shapeType);
    }
    return QWidget::eventFilter(watched,event);
}
bool Widget::drawing(QString type)
{
    QPainter painter;
    painter.begin(drawWidget);
    painter.drawLine(QPoint(0,0), QPoint(100, 100));
    painter.end();
}
```

如果按照上面代码思路，很明显有一个问题，不能根据选择按钮的不同进行绘制不同的图形。

后面想了想是否需要通过重写clicked()函数，能够让信号函数接收参数。但是这样是否也需要重写update()函数，一起接受clicked信号发送的参数。(感觉这样实现会很麻烦)，后面换了一个思路，重新构造一个槽函数，同时在类中定义一个变量shapeType。

注意：**信号与槽有个重要机制，信号函数参数一定大于等于槽函数参数数量，且类型必须一致。如果信号函数没得参数发送，槽函数是接受不了参数的。**

## QSignalMapper 

通过百度：发现可以利用QSignalMapper 这个类。**利用该类可以接收无参数的signal，然后重新发射出有参数的signal，这些参数的类型可以是int、QString、QWidget *或QObject ***

下面是一个QSignalMapper样例：

```C++
QSignalMapper *signalMapper = new QSignalMapper(this);

connect(button1, SIGNAL(triggered()), signalMapper, SLOT(map()));
connect(button2, SIGNAL(triggered()), signalMapper, SLOT(map()));
connect(button3, SIGNAL(triggered()), signalMapper, SLOT(map()));
connect(button4, SIGNAL(triggered()), signalMapper, SLOT(map()));
connect(button5, SIGNAL(triggered()), signalMapper, SLOT(map()));

signalMapper->setMapping(button1, 1);
signalMapper->setMapping(button2, 2);
signalMapper->setMapping(button3, 3);
signalMapper->setMapping(button4, 4);
signalMapper->setMapping(button5, 5);

connect(signalMapper, SIGNAL(mapped(int)), this, SLOT(printMsg(int)));
```

根据样例思路，可以在按钮点击的同时发送我们需要的信息。这样，我们可以定义一个自己的槽函数，设置一个类变量shapeType，当按钮点击的时候，槽函数更改shapeType变量，在drawing绘图函数中根据shapeType变量绘制不同图形。



```C++
connect(Btn,  SIGNAL(clicked()), signalMapper, SLOT(map()));
connect(Btn1, SIGNAL(clicked()), signalMapper, SLOT(map()));
connect(Btn2, SIGNAL(clicked()), signalMapper, SLOT(map()));
connect(Btn3, SIGNAL(clicked()), signalMapper, SLOT(map()));
signalMapper->setMapping(Btn, "round");
signalMapper->setMapping(Btn1, "rect");
signalMapper->setMapping(Btn2, "button");
signalMapper->setMapping(Btn3, "zuhe");
connect(signalMapper, SIGNAL(mapped(QString)), this, SLOT(drawShape(QString)));

//绘图函数
bool Widget::drawing(QString type)
{
    QPainter painter;
    painter.begin(rightWidget);
    if(type == "line")
        painter.drawLine(QPoint(0,0), QPoint(100, 100));
    else if(type == "round")
        painter.drawEllipse(QPoint(100, 100), 50, 50);
    else if(type == "rect")
        painter.drawRect(QRect(0,0, 100, 100));
    else
        painter.drawLine(QPoint(0,0), QPoint(100, 100));
    painter.end();
}

//事件过滤器
bool Widget::eventFilter(QObject *watched, QEvent *event)
{
    if(watched == rightWidget && event->type() == QEvent::Paint)
    {
        drawing(shapeType);
    }
    return QWidget::eventFilter(watched,event);
}

//槽函数
bool Widget::drawShape(QString type)
{
    shapeType = type;
    rightWidget->update();

}
```

## 参考：

[Qt使用connect函数时向slot传递参数]("https://blog.csdn.net/imred/article/details/72940365?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task")