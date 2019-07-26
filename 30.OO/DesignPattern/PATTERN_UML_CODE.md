PATTERN_UML_CODE
====================
`PATTERN_UML_CODE` 记录了设计模式章节的 UML 模型图相关代码。

## 11.单例模式

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

## 12.简单工厂模式
<div align="center"> <img src="images/12.simpleFactory.png" width="360px"> </div><br>

```text
@startuml

interface Product {
    + show() : void
}

class ConcreteProductA {
  + show() : void
}
Product <|.. ConcreteProductA
class ConcreteProductB {
  + show() : void
}
Product <|.. ConcreteProductB

class SimpleFactory{
    + createProduct() : Product
}
ConcreteProductA <-- SimpleFactory
ConcreteProductB <-- SimpleFactory

note bottom  of SimpleFactory
  <b>public Product createProduct(int type) {  </b>
  <b>     if (type == 1) { </b>
  <b>           return new ConcreteProductA();  </b>
  <b>     } else if (type == 2) { </b>
  <b>           return new ConcreteProductB(); </b>
  <b>     } </b>
  <b>} </b>
end note

@enduml
```

## 13.工厂方法模式

<div align="center"> <img src="images/13.factoryMethod.png" width="280px"> </div><br>

```text
@startuml

interface Product {
    + show() : void
}

interface FactoryMethod {
    + createProduct() : Product
}

class ConcreteProduct {
  + show() : void
}
Product <|.. ConcreteProduct

class ConcreteFactory {
   + createProduct() : Product
}
FactoryMethod <|.. ConcreteFactory

ConcreteProduct <-- ConcreteFactory

note bottom  of ConcreteFactory
  <b>public Product createProduct() {  </b>
  
  <b>    return new ConcreteProduct(); </b>
  <b>} </b>
end note

@enduml
```

## 14.抽象工厂模式

<div align="center"> <img src="images/14.abstractFactory.png" width="600px"> </div><br>

```text
@startuml
skinparam classAttributeIconSize 0
interface AbstractFactory {
    + createProductA() : ProductA
    + createProductB() : ProductB
}
class ConcreteFactory1{
    + createProductA() : ProductA
    + createProductB() : ProductB
}
AbstractFactory <|.. ConcreteFactory1
class ConcreteFactory2{
    + createProductA() : ProductA
    + createProductB() : ProductB
}
AbstractFactory <|.. ConcreteFactory2

interface ProductA {
    + show() : void
}
class ConcreteProductA1 {
  + show() : void
}
class ConcreteProductA2 {
  + show() : void
}
ProductA <|.. ConcreteProductA1
ProductA <|.. ConcreteProductA2

interface ProductB {
    + show() : void
}
class ConcreteProductB1 {
  + show() : void
}
class ConcreteProductB2 {
  + show() : void
}

ProductB <|.. ConcreteProductB1
ProductB <|.. ConcreteProductB2

ConcreteProductA1 <-- ConcreteFactory1
ConcreteProductB1 <-- ConcreteFactory1

ConcreteProductA2 <-- ConcreteFactory2
ConcreteProductB2 <-- ConcreteFactory2

class TestClient {

}
AbstractFactory <-- TestClient
ProductA <-- TestClient
ProductB <-- TestClient

@enduml
```

## 15.原型模式

<div align="center"> <img src="images/15.prototype.png" width="5200px"> </div><br>

```text

@startuml
skinparam classAttributeIconSize 0
interface Cloneable {

}

class ConcretePrototype{
    + clone() : Object
}
Cloneable <|.. ConcretePrototype

class TestPrototype{
    + main() : void
}
ConcretePrototype<-- TestPrototype

note bottom  of TestPrototype
  <b>public static void main(String[] args) throws CloneNotSupportedException {  </b>
  
  <b>    ConcretePrototype prototype = new ConcretePrototype(); </b>
  <b>    ConcretePrototype clone = (ConcretePrototype) prototype.clone(); </b>
  <b>} </b>
end note

@enduml
```

## 16.建造者模式

<div align="center"> <img src="images/16.builder.png" width="360px"> </div><br>


```text
@startuml
skinparam classAttributeIconSize 0

class Product {
    - partA : String
    - partB : String
    - partC : String
    + setPartA(String partA) : void
    + setPartB(String partB) : void
    + setPartC(String partC) : void
    + show() : void
}

abstract class Builder {
    # product : Product
    + {abstract} buildPartA() : void
    + {abstract} buildPartB() : void
    + {abstract} buildPartC() : void
    + getResult() : Product
}
Builder o-- Product

class ConcreteBuilder {
    + buildPartA() : void
    + buildPartB() : void
    + buildPartC() : void
}
Builder <|-- ConcreteBuilder

class Director {
    - builder : Builder
    + Director(Builder builder)
    + buildProduct() : Product
}
Director o-- Builder

@enduml
```



## 21.代理模式

<div align="center"> <img src="images/21.proxy.png" width="360px"> </div><br>


```text

@startuml
skinparam classAttributeIconSize 0
interface Subject {
    + request() : void
}

class RealSubject{
     + request() : void
}
Subject <|.. RealSubject

class Proxy{
     - realSubject : RealSubject
     + request() : void
     + preRequest() : void
     + postRequest() : void
}
Subject <|.. Proxy
RealSubject <-- Proxy

@enduml
```

## 22.适配器模式

<div align="center"> <img src="images/18.classAdapter.png" width="520px"> </div><br>

```text

@startuml
skinparam classAttributeIconSize 0
interface Target {
    + request() : void
}

class Adaptee{
     + concreteRequest() : void
}

class ClassAdapter{
     + request() : void
}
Target <|.. ClassAdapter
Adaptee <|-- ClassAdapter
note bottom  of ClassAdapter
  <b>public void request() { </b>
  
  <b>    concreteRequest(); </b>
  <b>} </b>
end note

class TestClassAdapter{
     + main() : void
}
Target <.. TestClassAdapter
note bottom  of TestClassAdapter
  <b>public static void main(String[] args) { </b>
  
  <b>    Target target = new ClassAdapter(); </b>
  <b>    target.request(); </b>
  <b>} </b>
end note
@enduml
```

<div align="center"> <img src="images/22.objectAdapter.png" width="520px"> </div><br>

```text

@startuml
skinparam classAttributeIconSize 0
interface Target {
    + request() : void
}

class Adaptee{
     + concreteRequest() : void
}

class ObjectAdapter{
     - adaptee : Adaptee
     + ObjectAdapter(Adaptee adaptee) 
     + request() : void
}
Target <|.. ObjectAdapter
Adaptee <--  ObjectAdapter
note bottom  of ObjectAdapter
  <b>public void request() { </b>
  
  <b>    adaptee.concreteRequest(); </b>
  <b>} </b>
end note

class TestObjectAdapter{
     + main() : void
}
Target <.. TestObjectAdapter
note bottom  of TestObjectAdapter
  <b>public static void main(String[] args) { </b>
  
  <b>    Target target = new ObjectAdapter(new Adaptee()); </b>
  <b>    target.request(); </b>
  <b>} </b>
end note
@enduml
```

## 23.外观模式

<div align="center"> <img src="images/23.facade.png" width="520px"> </div><br>

```text

@startuml
skinparam classAttributeIconSize 0

class SubSystemA{
    + oprationA() : void
}
class SubSystemB{
    + oprationB() : void
}
class SubSystemC{
    + oprationC() : void
}

class Facade{
    - subA : SubSystemA
    - subB : SubSystemB
    - subC : SubSystemC
     + wrapOpration() : void
}
note bottom  of Facade
  <b>public void wrapOpration() { </b>
  
  <b>    subA.oprationA();</b>
  <b>    subA.oprationB();</b>
  <b>    subA.oprationC();</b>
  <b>} </b>
end note

SubSystemA <--  Facade
SubSystemB <--  Facade
SubSystemC <--  Facade

class TestClient{

}
Facade <.. TestClient

@enduml
```
