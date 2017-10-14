1、两种启动QT Quick App的方式：
a）QQmlApplicationEngine搭配Window：QML文档拥有窗口的完整控制权

```js
//cpp文件
#include <QGuiApplication>
#include <QQmlApplicationEngine>

int main(int argc, char *argv[])
{
    QGuiApplication app(argc, argv);

    QQmlApplicationEngine engine;
    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));

    return app.exec();
}



//qml文件
import QtQuick 2.7
import QtQuick.Window 2.2

Window {
  children:[
    visible: true
    width: 640
    height: 480
    title: qsTr("Hello World")
]
    MainForm {
        anchors.fill: parent
        mouseArea.onClicked: {
            console.log(qsTr('Clicked on background. Text: "' + textEdit.text + '"'))
        }
    }
}
```

b）QQuickView搭配Item：显示QML文档，对窗口的控制权在C++，需要用qmlscene工具加载文档。

```js
//cpp文件
#include <QGuiApplication>
#include <QQuickView>

int main(int argc, char *argv[])
{
    QGuiApplication app(argc, argv);

    QQuickView viewer;
    viewer.setResizeMode(QQuickView::SizeRootObjectToView);
    viewer.setSource(QUrl("qrc:/main.qml"));
    viewer.show();

    return app.exec();
}



//qml文件
import QtQuick 2.7
import QtQuick.Window 2.2

Item {
    visible: true
    width: 640
    height: 480
    title: qsTr("Hello World")

    MainForm {
        anchors.fill: parent
        mouseArea.onClicked: {
            console.log(qsTr('Clicked on background. Text: "' + textEdit.text + '"'))
        }
    }
}
```