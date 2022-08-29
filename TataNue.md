## 1. _Can we nest the Scaffold widget? Why or Why not?_

> We can use Nested Scaffolds, when we make two separate screens, each one with its own Scaffold.

In the give nexample, We can see there are two distinct pages/Routes/screens, and each one has its own Scaffold. We are navigating back and forth by using Navigator, and so our pages are being added to the stack, one Scaffold on top of the other. That is fine.



```sh
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Navigation Basics',
    home: FirstRoute(),
  ));
}

class FirstRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Route'),
      ),
      body: Scaffold(
        body: Center(
          child: RaisedButton(
            child: Text('Open route'),
            onPressed: () {
              Navigator.push(
              context,
              MaterialPageRoute(builder: (context) => SecondRoute()),
            );
            },
          ),
        ),
      ),
    );
  }
}

class SecondRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Second Route"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            Navigator.pop(context);
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
```
> When we are working on an app where we have to deal with only one screen we can use Single Scaffold for the handling.

For example we have seen tabbed application where we are show one screen at a time so we can use single scaffold.

```sh
import 'package:flutter/material.dart';

void main() {
  runApp(TabBarDemo());
}

class TabBarDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: DefaultTabController(
        length: 3,
        child: Scaffold(
          appBar: AppBar(
            bottom: TabBar(
              tabs: [
                Tab(
                  icon: Icon(
                    Icons.favorite,
                    color: Colors.pink,
                    size: 24.0,
                  ),
                ),
                Tab(
                  icon: Icon(
                    Icons.audiotrack,
                    color: Colors.green,
                    size: 24.0,
                  ),
                ),
                Tab(
                  icon: Icon(
                    Icons.beach_access,
                    color: Colors.yellow,
                    size: 24.0,
                  ),
                ),
              ],
            ),
            title: Text('Single Scaffold'),
          ),
          body: TabBarView(
            children: [
              Icon(Icons.favorite),
              Icon(Icons.audiotrack),
              Icon(Icons.beach_access),
            ],
          ),
        ),
      ),
    );
  }
}
```

So the use of scaffold really depends upon the requirement of application.


# 2. _What are the different ways we can create a custom widget_
- We can make a custom widget by extacting code from Other widget 

```sh
class BodyWidget extends StatelessWidget {
  const BodyWidget({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: <Widget>[
        // ...
      ],
    );
  }
}
```
> Put your cursor on Stack and right-click to show the context menu. Then choose Refactor ▸ Extract ▸ Extract Flutter Widget….

We can name it as per our convenience.

- We can create custom widget using `CustomPainter` when we want to design a complex UI or want to make some animations and stuff.
- We can make custom text widgets using `Flutter Text Rendering`.

# 3. _How can I access platform(iOS or Android) specific code from Flutter?_

For most of the platform specific UI we can use [Platform Widget]

Lets take an example of opening web browser on android app.

```sh
import 'package:flutter/material.dart'; 
import 'dart:async'; 
import 'package:flutter/services.dart'; 

void main() => runApp(MyApp()); 
class MyApp extends StatelessWidget {
   @override 
   Widget build(BuildContext context) {
      return MaterialApp(
         title: 'Flutter Demo', 
         theme: ThemeData( 
            primarySwatch: Colors.blue, 
         ), 
         home: MyHomePage(
            title: 'Flutter Demo Home Page'
         ), 
      ); 
   }
}
class MyHomePage extends StatelessWidget {
   MyHomePage({Key key, this.title}) : super(key: key); 
   final String title; 
   static const platform = const MethodChannel('flutterapp.tutorialspoint.com/browser'); 
   Future<void> _openBrowser() async {
      try {
         final int result = await platform.invokeMethod('openBrowser', <String, String>{ 
            'url': "https://flutter.dev" 
         });
      }
      on PlatformException catch (e) { 
         // Unable to open the browser print(e); 
      } 
   }
   @override 
   Widget build(BuildContext context) {
      return Scaffold( 
         appBar: AppBar( 
            title: Text(this.title), 
         ), 
         body: Center(
            child: RaisedButton( 
               child: Text('Open Browser'), 
               onPressed: _openBrowser, 
            ), 
         ),
      );
   }
}

```
From flutter we can use `MethodChannel` to Notify Android or iOS app to do some platform specific task. 

And it uses `platform.invokeMethod` to read back the response from either of application.

Application side code we can writr following code in `MainActivity.java` to do the desired.

```sh
package com.tutorialspoint.flutterapp.flutter_browser_app; 

import android.app.Activity; 
import android.content.Intent; 
import android.net.Uri; 
import android.os.Bundle; 
import io.flutter.app.FlutterActivity; 
import io.flutter.plugin.common.MethodCall; 
import io.flutter.plugin.common.MethodChannel.Result; 
import io.flutter.plugins.GeneratedPluginRegistrant; 

public class MainActivity extends FlutterActivity { 
   private static final String CHANNEL = "flutterapp.tutorialspoint.com/browser"; 
   @Override 
   protected void onCreate(Bundle savedInstanceState) { 
      super.onCreate(savedInstanceState); 
      GeneratedPluginRegistrant.registerWith(this); 
      new MethodChannel(getFlutterView(), CHANNEL).setMethodCallHandler(
         new MethodCallHandler() {
            @Override 
            public void onMethodCall(MethodCall call, Result result) {
               String url = call.argument("url"); 
               if (call.method.equals("openBrowser")) { 
                  openBrowser(call, result, url); 
               } else { 
                  result.notImplemented(); 
               }
            }
         }
      ); 
   }
   private void openBrowser(MethodCall call, Result result, String url) {
      Activity activity = this; if (activity == null) {
         result.error(
            "ACTIVITY_NOT_AVAILABLE", "Browser cannot be opened without foreground activity", null
         ); 
         return; 
      } 
      Intent intent = new Intent(Intent.ACTION_VIEW); 
      intent.setData(Uri.parse(url)); 
      activity.startActivity(intent); 
      result.success((Object) true); 
   }
}
```

# 4. _What is BuildContext? What is its importance?_

> `BuildContext` is, like it's name is implying, the context in which a specific widget is built.

BuildContext is really useful when you want to pass data downward without having to manually assign it to every widgets' configurations for example ; you'll want to access them everywhere. But you don't want to pass it on every single constructor.

You could potentially make a global or a singleton ; but then when confs change your widgets won't automatically rebuild.

BuildContext is a locator that is used to track each widget in a tree and locate them and their position in the tree. The BuildContext of each widget is passed to their build method. Remember that the build method returns the widget tree a widget renders. Each BuildContext is unique to a widget.

# 5. _Refactor the code below so that the children will wrap to the next line when the display width is small for them to fit._

We can add Flexible to the code to wrap line when adding long texts.

```sh
new Container(
       child: Row(
         children: <Widget>[
            Flexible(
               child: new Text("I love flutter and i want to be a flutter developer"))
                ],
        ));
```
 
# 6. _3. In the below code, list1 declared with var, list2 with final and list3 with const. What is the difference between these lists? Will the last two lines compile?_ 

```sh
var list = ["i", "Love", "Flutter"];

final list2 = list;
list2[2] = "Dart"; //Will compile, we can't change the type of final list2

const list3 = list; // Won't compile
```


   [Platform Widget]: <https://pub.dev/packages/flutter_platform_widgets>

