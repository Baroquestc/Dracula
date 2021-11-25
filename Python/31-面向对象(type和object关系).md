# 31-面向对象(type和object关系)

## 1 概括

- 父子关系，通过父类与子类来描述，通过 object 描述
- 类型实例关系，通过类和实例来描述，通过 type 描述

## 2  object (继承关系/`__bases__`)

- 本数据类型同样也是继承自object（bool继承于`int`类型）

## 3 type (实例化关系/`__class__`)

- 基本数据类对象的类型都是 type 类型（bool也是`int`）

## 4 type 与 object 关系

1. object 的类型是 type
2. type 继承自 object