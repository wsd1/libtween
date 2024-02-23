# 参考代码说明

最近在搞LED点阵屏显示项目：chooPanel。希望能做些动态的效果。easing function是个不错的解决方案。在github上找到了这个。
看完代码，觉得相当不错。能够超额的完成自己对动态变化的需求。
大致说明一下：

Tween_Engine 挂了一串 Tween_Node，每一个node都有个tween

Tween 结构也有一个 Tween_Node 成员，可以成链。该链挂载所有该tween完成后需要被执行的tween。

大致的使用流程如下：

```
      //创建engine是所有当前正在运行tween的根节点，初期该engine没有任何tween。
      Tween_Engine* engine = Tween_CreateEngine(); 

      //设置起点终点prop，目前C语言下能够用到的 prop 也就是位置 长宽和角度什么的了
      Tween_Props props = Tween_MakeProps(x1, y1, w1, h1, rot1);  
      Tween_Props toProps = Tween_MakeProps(x2, y2, w2, h2, rot2);   

      //创建tween，但是还未开始奥。 注意其中有个 update 的回调，这个就是给你更新UI的
      Tween* tween = Tween_CreateTween(engine, &props, &toProps, duration, TWEEN_EASING_ELASTIC_IN_OUT, update, NULL);

      //如果变化之后，还有变化接力tween2，tween3，那么可以
      Tween_ChainTweens(tween, tween2);
      Tween_ChainTweens(tween2, tween3);

      //或者 如果变化之后，还有一堆变化tween2和tween3一起发生，那么可以
      Tween_ChainTweens(tween, tween2);
      Tween_ChainTweens(tween, tween3);

      
      //要手动启动第一个tween，其中 tween 会被挂到 engine上，之后就使用 engine 来更新了
      Tween_StartTween(tween, SDL_GetTicks()); 


      //engine 上挂着的全都是已经start的tween，动起来～～～  
      while(!terminate) {
         Tween_UpdateEngine(engine, SDL_GetTicks());
         .
         .
      }

```

可见，这个lib简直就是霹雳小娇娃，简单清晰，功能全面。让人希罕的很。dy 20240224

---

libtween 0.2
------------

*** What is libtween?

libtween is a tweening engine written in C. 

It is a port of "Javascript Tweening Engine" by @sole and others at:

   https://github.com/sole/tween.js

*** How do I build the source?

   ./configure
   make
   sudo make install

*** Usage

To use libtween

   1) create callback function that updates properties of an object
   2) create a Tween_Engine
   3) create Tween_Props structures that represents the start and to properties of an object
   4) create a Tween with Tween_Engine, Tween_Props, duration, easing, and callback parameters 
   5) start the Tween
   6) update the Tween_Engine in an animation loop
   7) destroy Tween
   8) destroy Tween_Engine
 
   RECT gRect;

   void update(Tween* tween) {
     gRect.x = tween->props->x;
     gRect.y = tween->props->y;
   }
   
   .
   .
   .

      Tween_Engine* engine = Tween_CreateEngine();
      Tween_Props props = Tween_MakeProps(x1, y1, w1, h1, rot1);
      Tween_Props toProps = Tween_MakeProps(x2, y2, w2, h2, rot2);
      Tween* tween = Tween_CreateTween(engine, &props, &toProps, duration,
         TWEEN_EASING_ELASTIC_IN_OUT, update, NULL);
         Tween_Start(engine, SDL_GetTicks());   //注意，原作者这里可能搞错了 应该是 Tween_StartTween(tween, SDL_GetTicks()); 

      while(!terminate) {
         Tween_UpdateEngine(engine, SDL_GetTicks());
      
         .
         .
      }
   
      Tween_Destroy(tween);
      Tween_Engine(engine);

*** Examples

Currently libtween comes with two examples, hello_world and bars. 

These examples closely match the tween.js examples at:

   http://sole.github.io/tween.js/examples/00_hello_world.html
   http://sole.github.io/tween.js/examples/01_bars.html

To run the examples, you must have SDL2 installed on your system

*** Differences from tween.js

libtween is a thorough port of tween.js with a few differences. Tween_Props structures are used simulate javascript properties and contains the following variables:

    x, y, width, height, rotation, r, g, b, a

Tween_Props currently has no concept of arrays. Because of this, the interpolation features of tween.js have not been ported.
