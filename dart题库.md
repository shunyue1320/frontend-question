1. 下面关于Dart语言说法正确的是？

- [x] A、Dart是少数同时支持JIT和AOT的语言之一。

- [ ] B、Dart中的Isolate共享内容，属于多线程模型。

- [ ] C、Dart中在创建对象的时候，需要先查找可用内存。

- [ ] D、Dart语言跟JavaScript一样，是一门解释型的语言。


25. 下面关于泛型描述正确的有？

- [ ] A、适当指定泛型可以生成更好的代码。

- [ ] B、使用泛型可以减少代码重复

- [ ] C、在实现泛型类型时，可以使用extends限制其参数类型。

- [ ] D、泛型参数可以在函数的返回类型、参数类型、局部变量类型中使用。


26. 下面关于库的描述，正确的有？

- [ ] A、使用import来导入库

B、import只能导入整个库。

C、Dart支持延迟加载库。

D、import可以导入库的一部分。


27. Dart语言中，关于类型定义typedef的描述正确的有？

A、typedef目前只能用于函数。

B、当函数类型赋值给变量时，typedef会保留类型信息。

C、typedef是类型的别名，不能用于函数类型检查。

D、typedef只能保留函数返回值类型，不能保留函数参数类型。


28. 下面关于元数据注解描述正确的是？

A、元数据注解以@开头。

B、元数据注解@后边跟编译时常量的引用和常量构造函数的调用。

C、Dart里边只定义了deprecated和override两个元数据常量。

D、我们可自定义元数据，使用的时候调用其普通构造函数实例化即可。


29. 关于Dart语言的注释，描述正确的有？

A、单行注释以// 开头。

B、多行注释以/*开头，*/结尾。

C、Dart编译器会忽略所有注释文本。

D、文档注释中[]会在词法范围内解析。


30. 下面关于枚举类型描述正确的有？

A、枚举类型是一种特殊的类型，定义后可以动态为其添加成员。

B、枚举类型的长度是可变的。

C、在switch语句中使用enum，如果没有处理enum所有值，将会得到一个警告。

D、枚举类型表示固定数量的常量值。


31. 使用dynamic声明的变量，可以将其他类型的值赋值给该变量。

A、正确     B、错误


32. Dart语言中，一切皆Object，所以一个变量使用Object声明后，可以赋值任意类型的值。

A、正确     B、错误


33. var声明的变量，一旦赋值后，其类型就不能再次更改。

A、正确   B、错误


34. 在类中，定义的实例变量，都会隐式的添加getter和setter方法。

A、正确    B、错误


35. 类的工厂构造函数中，可以访问当前实例this。


A、正确     B、错误



36. Dart语言中，所有的类都隐式定义了一个接口。

A、正确    B、错误


37. 想让类A支持类B的API，而不继承B的实现，那么需要实现B接口中所有的属性和方法。

A、正确   B、错误


38. 子类中可以覆盖父类的实例方法、getter和setter，使用@override注解来重写某个成员。

A、正确


B、错误


39. 在类中，还可以重写某一些操作符，使用operator关键字修饰操作符。

A、正确


B、错误


40. mixin是在多个类层次结构中重用类代码的一种方式。

答案选项


A、正确


B、错误


41. 计算下面表达式的结果：
5 ~/ 2 = （）


42. 在switch语句中，每个非空的case子句都以（）结束。


43. 将下面表达式的转换成int类型的值（）
var str = '10010';


44. 在使用类成员时，为了避免左侧操作数为空时异常，可以使用（）代替（.）。


45. 定义一个可调用类，只需要实现该类的（）方法即可。


46. 要使用mixin，需要在（）关键字后边加上一个或多个mixin名称。


47. 如果导入两个具有冲突标识符的库，可以为一个或者两个库指定一个前缀。指定库前缀使用（）关键字。


48. Dart属于（）线程模型，通过（）实现高并发。


49. Dart事件循环包含两个队列，分别是（）、（）。


50. 如果要确保在抛出异常时运行某些业务代码，可以使用（）子句。


51. 采用同步生成器的方式，生成0-50的序列。

```js
Iterable<int> syncGenerator([int n = 50]) sync* { 
  int i = 0; 
  while(i <= n) yield i++; 
}

main(List<String> args) {
  print(syncGenerator());   //同步生成器
}
```


52. 采用异步生成器的方式生成100以内的偶数序列。

```js
Stream <int> asyncGenerator([int n = 100]) async* {
  int i = 0;
  while(i < n) yield i += 2; 
}

main(List<String> args) {
  asyncGenerator().listen(print);  //异步生成器
}
```


53. 采用 同步/异步 生成器生成12项斐波那契额数列。

```js
//同步生成器
Iterable<int> syncGenerator([int n = 12]) sync* { 
  int i = 1; 
  while(i <= n) yield Feb(i++); 
}

//异步生成器
Stream <int> asyncGenerator([int n = 12]) async* {
  int i = 1;
  while(i <= n) yield Feb(i++); 
}
 
int Feb(int n) {
  if (n < 2) return n; 
  return Feb(n - 1) + Feb(n - 2); 
} 

main(List<String> args) {
  print(syncGenerator());          //同步生成器
  asyncGenerator().listen(print);  //异步生成器
}
```
