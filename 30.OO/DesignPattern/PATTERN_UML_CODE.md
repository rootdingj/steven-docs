PATTERN_UML_CODE
====================
`PATTERN_UML_CODE` 记录了设计模式章节的 UML 模型图相关代码。

## 1.单例模式

<div align="center"> <img src="images/11.singleton.png" width="280px"> </div><br>

```text
@startuml

skinparam classAttributeIconSize 0
class Singleton {
 - {static} singleton : Singleton
 - Singleton()
 + getSingleton() : Singleton
}

Singleton  -->  Singleton

note bottom  of Singleton
  <b>public static Singleton getInstance() {  </b>
  <b>     // 第一次检查，用来避免 singleton 已经被实例化之后的加锁操作</b>
  <b>     if (singleton != null) { </b>
  <b>           // 不创建  </b>
  <b>     }else { </b>
  <b>           synchronized (Singleton.class) { </b>
  <b>           // 第二次检查，加锁确保只有一个线程进行实例化操作 </b>
  <b>           if(singleton == null) { </b>
  <b>               singleton = new Singleton(); </b>
  <b>           }</b>
  <b>     }</b>
  <b>} </b>
end note
```






