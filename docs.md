# Design Patterns JS

**[Behavioral](#behavioral)**
* [Chain Of Resp](#chain-of-resp)
* [Command](#command)
* [Interpreter](#interpreter)
* [Iterator](#iterator)
* [Mediator](#mediator)
* [Memento](#memento)
* [Observer](#observer)
* [State](#state)
* [Strategy](#strategy)
* [Template](#template)
* [Visitor](#visitor)

**[Creational](#creational)**
* [Abstract Factory](#abstract-factory)
* [Builder](#builder)
* [Factory](#factory)
* [Prototype](#prototype)
* [Singleton](#singleton)

**[Structural](#structural)**
* [Adapter](#adapter)
* [Bridge](#bridge)
* [Composite](#composite)
* [Decorator](#decorator)
* [Facade](#facade)
* [Flyweight](#flyweight)
* [Proxy](#proxy)



## behavioral
### Chain Of Resp

> 职责链模式：为解除请求的发送者和接收者之间耦合，而使多个对象都有机会处理这个请求。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它。

##### chain-of-resp-es6.js
```Javascript
class ShoppingCart {

  constructor() {
    this.products = [];
  }

  addProduct(p) {
    this.products.push(p);
  };
}

class Discount {

  calc(products) {
    let ndiscount = new NumberDiscount();
    let pdiscount = new PriceDiscount();
    let none = new NoneDiscount();
    ndiscount.setNext(pdiscount);
    pdiscount.setNext(none);
    return ndiscount.exec(products);
  };
}

class NumberDiscount {

  constructor() {
    this.next = null;
  }

  setNext(fn) {
    this.next = fn;
  };

  exec(products) {
    let result = 0;
    if (products.length > 3)
      result = 0.05;

    return result + this.next.exec(products);
  };
}

class PriceDiscount {

  constructor() {
    this.next = null;
  }

  setNext(fn) {
    this.next = fn;
  };

  exec(products) {
    let result = 0;
    let total = products.reduce((a, b) => a + b);

    if (total >= 500)
      result = 0.1;

    return result + this.next.exec(products);
  };
}

class NoneDiscount {
  exec() {
    return 0;
  };
}

export {
  ShoppingCart,
  Discount
};

```
##### chain-of-resp.js
```Javascript
function ShoppingCart() {
  this.products = [];

  this.addProduct = function(p) {
    this.products.push(p);
  };
}

function Discount() {
  this.calc = function(products) {
    var ndiscount = new NumberDiscount();
    var pdiscount = new PriceDiscount();
    var none = new NoneDiscount();

    ndiscount.setNext(pdiscount);
    pdiscount.setNext(none);

    return ndiscount.exec(products);
  };
}

function NumberDiscount() {
  this.next = null;
  this.setNext = function(fn) {
    this.next = fn;
  };

  this.exec = function(products) {
    var result = 0;
    if (products.length > 3)
      result = 0.05;

    return result + this.next.exec(products);
  };
}

function PriceDiscount() {
  this.next = null;

  this.setNext = function(fn) {
    this.next = fn;
  };

  this.exec = function(products) {
    var result = 0;
    var total = products.reduce(function(a, b) {
      return a + b;
    });

    if (total >= 500)
      result = 0.1;

    return result + this.next.exec(products);
  };
}

function NoneDiscount() {
  this.exec = function() {
    return 0;
  };
}

module.exports = [ShoppingCart, Discount];

```

### Command

> 命令模式：将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可取消的操作。

##### command.js
```Javascript
function Cockpit(command) {
  this.command = command;
}

Cockpit.prototype.execute = function() {
  this.command.execute();
};

function Turbine() {
  this.speed = 0;
  this.state = false;
}

Turbine.prototype.on = function() {
  this.state = true;
  this.speed = 100;
};

Turbine.prototype.off = function() {
  this.speed = 0;
  this.state = false;
};

Turbine.prototype.speedDown = function() {
  if (!this.state) return;

  this.speed -= 100;
};

Turbine.prototype.speedUp = function() {
  if (!this.state) return;

  this.speed += 100;
};

function OnCommand(turbine) {
  this.turbine = turbine;
}

OnCommand.prototype.execute = function() {
  this.turbine.on();
};

function OffCommand(turbine) {
  this.turbine = turbine;
}

OffCommand.prototype.execute = function() {
  this.turbine.off();
};

function SpeedUpCommand(turbine) {
  this.turbine = turbine;
}

SpeedUpCommand.prototype.execute = function() {
  this.turbine.speedUp();
};

function SpeedDownCommand(turbine) {
  this.turbine = turbine;
}

SpeedDownCommand.prototype.execute = function() {
  this.turbine.speedDown();
};

module.exports = [Cockpit, Turbine, OnCommand, OffCommand, SpeedUpCommand, SpeedDownCommand];

```
##### command_es6.js
```Javascript
class Cockpit {

  constructor(command) {
    this.command = command;
  }

  execute() {
    this.command.execute();
  }
}

class Turbine {

  constructor() {
    this.state = false;
  }

  on() {
    this.state = true;
  }

  off() {
    this.state = false;
  }
}

class OnCommand {

  constructor(turbine) {
    this.turbine = turbine;
  }

  execute() {
    this.turbine.on();
  }
}

class OffCommand {

  constructor(turbine) {
    this.turbine = turbine;
  }

  execute() {
    this.turbine.off();
  }
}

export {
  Cockpit,
  Turbine,
  OnCommand,
  OffCommand
};

```

### Interpreter

> 解释器模式：给定一个语言, 定义它的文法的一种表示，并定义一个解释器, 该解释器使用该表示来解释语言中的句子。

##### interpreter.js
```Javascript
function Sum(left, right) {
  this.left = left;
  this.right = right;
}

Sum.prototype.interpret = function() {
  return this.left.interpret() + this.right.interpret();
};

function Min(left, right) {
  this.left = left;
  this.right = right;
}

Min.prototype.interpret = function() {
  return this.left.interpret() - this.right.interpret();
};

function Num(val) {
  this.val = val;
}

Num.prototype.interpret = function() {
  return this.val;
};

module.exports = [Num, Min, Sum];

```
##### interpreter_es6.js
```Javascript
class Sum {

  constructor(left, right) {
    this.left = left;
    this.right = right;
  }

  interpret() {
    return this.left.interpret() + this.right.interpret();
  }
}

class Min {

  constructor(left, right) {
    this.left = left;
    this.right = right;
  }

  interpret() {
    return this.left.interpret() - this.right.interpret();
  }
}

class Num {

  constructor(val) {
    this.val = val;
  }

  interpret() {
    return this.val;
  }
}

export {
  Num,
  Min,
  Sum
};

```

### Iterator

> 迭代器模式：提供一种方法顺序访问一个聚合对象中各个元素, 而又不需暴露该对象的内部表示。

##### iterator.js
```Javascript
function Iterator(el) {
  this.index = 0;
  this.elements = el;
}

Iterator.prototype = {
  next: function() {
    return this.elements[this.index++];
  },
  hasNext: function() {
    return this.index < this.elements.length;
  }
};

module.exports = Iterator;

```
##### iterator_es6.js
```Javascript
class Iterator {

  constructor(el) {
    this.index = 0;
    this.elements = el;
  }

  next() {
    return this.elements[this.index++];
  }

  hasNext() {
    return this.index < this.elements.length;
  }
}

export default Iterator;

```

### Mediator

> 中介者模式：用一个中介对象来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。

##### mediator.js
```Javascript
function TrafficTower() {
  this.airplanes = [];
}

TrafficTower.prototype.requestPositions = function() {
  return this.airplanes.map(function(airplane) {
    return airplane.position;
  });
};

function Airplane(position, trafficTower) {
  this.position = position;
  this.trafficTower = trafficTower;
  this.trafficTower.airplanes.push(this);
}

Airplane.prototype.requestPositions = function() {
  return this.trafficTower.requestPositions();
};

module.exports = [TrafficTower, Airplane];

```
##### mediator_es6.js
```Javascript
class TrafficTower {

  constructor() {
    this.airplanes = [];
  }

  requestPositions() {
    return this.airplanes.map(airplane => {
      return airplane.position;
    });
  }
}

class Airplane {

  constructor(position, trafficTower) {
    this.position = position;
    this.trafficTower = trafficTower;
    this.trafficTower.airplanes.push(this);
  }

  requestPositions() {
    return this.trafficTower.requestPositions();
  }
}

export {
  TrafficTower,
  Airplane
};

```

### Memento

> 备忘录模式：在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到保存的状态。

##### memento.js
```Javascript
function Memento(value) {
  this.value = value;
}

var originator = {
  store: function(val) {
    return new Memento(val);
  },
  restore: function(memento) {
    return memento.value;
  }
};

function Caretaker() {
  this.values = [];
}

Caretaker.prototype.addMemento = function(memento) {
  this.values.push(memento);
};

Caretaker.prototype.getMemento = function(index) {
  return this.values[index];
};

module.exports = [originator, Caretaker];

```
##### memento_es6.js
```Javascript
class Memento {
  constructor(value) {
    this.value = value;
  }
}

const originator = {
  store: function(val) {
    return new Memento(val);
  },
  restore: function(memento) {
    return memento.value;
  }
};

class Caretaker {

  constructor() {
    this.values = [];
  }

  addMemento(memento) {
    this.values.push(memento);
  }

  getMemento(index) {
    return this.values[index];
  }
}

export {
  originator,
  Caretaker
};

```

### Observer

> 观察者模式：定义对象间的一种一对多的依赖关系,以便当一个对象的状态发生改变时,所有依赖于它的对象都得到通知并自动刷新。

##### observer.js
```Javascript
function Product() {
  this.price = 0;
  this.actions = [];
}

Product.prototype.setBasePrice = function(val) {
  this.price = val;
  this.notifyAll();
};

Product.prototype.register = function(observer) {
  this.actions.push(observer);
};

Product.prototype.unregister = function(observer) {
  this.actions = this.actions.filter(function(el) {
    return el != observer;
  });
};

Product.prototype.notifyAll = function() {
  return this.actions.forEach(function(el) {
    el.update(this);
  }.bind(this));
};

var fees = {
  update: function(product) {
    product.price = product.price * 1.2;
  }
};

var proft = {
  update: function(product) {
    product.price = product.price * 2;
  }
};

module.exports = [Product, fees, proft];

```
##### observer_es6.js
```Javascript
class Product {
  constructor() {
    this.price = 0;
    this.actions = [];
  }

  setBasePrice(val) {
    this.price = val;
    this.notifyAll();
  }

  register(observer) {
    this.actions.push(observer);
  }

  unregister(observer) {
    this.actions = this.actions.filter(el => !(el instanceof observer));
  }

  notifyAll() {
    return this.actions.forEach(el => el.update(this));
  }
}

class Fees {
  update(product) {
    product.price = product.price * 1.2;
  }
}

class Proft {
  update(product) {
    product.price = product.price * 2;
  }
}

export {
  Product,
  Fees,
  Proft
};

```

### State

> 状态模式：允许一个对象在其内部状态改变时改变它的行为。对象看起来似乎修改了它所属的类。

##### state.js
```Javascript
function Order() {
  this.state = new WaitingForPayment();

  this.nextState = function() {
    this.state = this.state.next();
  };
}

function WaitingForPayment() {
  this.name = 'waitingForPayment';
  this.next = function() {
    return new Shipping();
  };
}

function Shipping() {
  this.name = 'shipping';
  this.next = function() {
    return new Delivered();
  };
}

function Delivered() {
  this.name = 'delivered';
  this.next = function() {
    return this;
  };
}

module.exports = Order;

```
##### state_es6.js
```Javascript
class OrderStatus {
  constructor(name, nextStatus) {
    this.name = name;
    this.nextStatus = nextStatus;
  }

  next() {
    return new this.nextStatus();
  }
}

class WaitingForPayment extends OrderStatus {
  constructor() {
    super('waitingForPayment', Shipping);
  }
}

class Shipping extends OrderStatus {
  constructor() {
    super('shipping', Delivered);
  }
}

class Delivered extends OrderStatus {
  constructor() {
    super('delivered', Delivered);
  }
}

class Order {
  constructor() {
    this.state = new WaitingForPayment();
  }

  nextState() {
    this.state = this.state.next();
  };
}

export default Order;

```

### Strategy

> 策略模式：定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。本模式使得算法的变化可独立于使用它的客户。

##### strategy.js
```Javascript
function ShoppingCart(discount) {
  this.discount = discount;
  this.amount = 0;
}

ShoppingCart.prototype.setAmount = function(amount) {
  this.amount = amount;
};

ShoppingCart.prototype.checkout = function() {
  return this.discount(this.amount);
};

function guestStrategy(amount) {
  return amount;
}

function regularStrategy(amount) {
  return amount * 0.9;
}

function premiumStrategy(amount) {
  return amount * 0.8;
}

module.exports = [ShoppingCart, guestStrategy, regularStrategy, premiumStrategy];

```
##### strategy_es6.js
```Javascript
class ShoppingCart {

  constructor(discount) {
    this.discount = discount;
    this.amount = 0;
  }

  checkout() {
    return this.discount(this.amount);
  }

  setAmount(amount) {
    this.amount = amount;
  }
}

function guestStrategy(amount) {
  return amount;
}

function regularStrategy(amount) {
  return amount * 0.9;
}

function premiumStrategy(amount) {
  return amount * 0.8;
}

export {
  ShoppingCart,
  guestStrategy,
  regularStrategy,
  premiumStrategy
};

```

### Template

> 模板方法模式：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。Template Method使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

##### template.js
```Javascript
function Tax() {}

Tax.prototype.calc = function(value) {
  if (value >= 1000)
    value = this.overThousand(value);

  return this.complementaryFee(value);
};

Tax.prototype.complementaryFee = function(value) {
  return value + 10;
};

function Tax1() {}
Tax1.prototype = Object.create(Tax.prototype);

Tax1.prototype.overThousand = function(value) {
  return value * 1.1;
};

function Tax2() {}
Tax2.prototype = Object.create(Tax.prototype);

Tax2.prototype.overThousand = function(value) {
  return value * 1.2;
};

module.exports = [Tax1, Tax2];

```
##### template_es6.js
```Javascript
class Tax {

  calc(value) {
    if (value >= 1000)
      value = this.overThousand(value);

    return this.complementaryFee(value);
  }

  complementaryFee(value) {
    return value + 10;
  }

}

class Tax1 extends Tax {

  constructor() {
    super();
  }

  overThousand(value) {
    return value * 1.1;
  }
}

class Tax2 extends Tax {

  constructor() {
    super();
  }

  overThousand(value) {
    return value * 1.2;
  }
}

export {
  Tax1,
  Tax2
};

```

### Visitor

> 访问者模式：表示一个作用于某对象结构中的各元素的操作。它使你可以在不改变各元素的类的前提下定义作用于这些元素的新操作。

##### visitor.js
```Javascript
function bonusVisitor(employee) {
  if (employee instanceof Manager)
    employee.bonus = employee.salary * 2;
  if (employee instanceof Developer)
    employee.bonus = employee.salary;
}

function Employee() {
  this.bonus = 0;
}

Employee.prototype.accept = function(visitor) {
  visitor(this);
};

function Manager(salary) {
  this.salary = salary;
}

Manager.prototype = Object.create(Employee.prototype);

function Developer(salary) {
  this.salary = salary;
}

Developer.prototype = Object.create(Employee.prototype);

module.exports = [Developer, Manager, bonusVisitor];

```
##### visitor_es6.js
```Javascript
function bonusVisitor(employee) {
  if (employee instanceof Manager)
    employee.bonus = employee.salary * 2;
  if (employee instanceof Developer)
    employee.bonus = employee.salary;
}

class Employee {

  constructor(salary) {
    this.bonus = 0;
    this.salary = salary;
  }

  accept(visitor) {
    visitor(this);
  }
}

class Manager extends Employee {
  constructor(salary) {
    super(salary);
  }
}

class Developer extends Employee {
  constructor(salary) {
    super(salary);
  }
}

export {
  Developer,
  Manager,
  bonusVisitor
};

```


## creational
### Abstract Factory

> 抽象工厂模式：提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。

##### abstract-factory.js
```Javascript
function droidProducer(kind) {
  if (kind === 'battle') return battleDroidFactory;
  return pilotDroidFactory;
}

function battleDroidFactory() {
  return new B1();
}

function pilotDroidFactory() {
  return new Rx24();
}

function B1() {}
B1.prototype.info = function() {
  return "B1, Battle Droid";
};

function Rx24() {}
Rx24.prototype.info = function() {
  return "Rx24, Pilot Droid";
};

module.exports = droidProducer;

```
##### abstract-factory_es6.js
```Javascript
function droidProducer(kind) {
  if (kind === 'battle') return battleDroidFactory;
  return pilotDroidFactory;
}

function battleDroidFactory() {
  return new B1();
}

function pilotDroidFactory() {
  return new Rx24();
}

class B1 {
  info() {
    return "B1, Battle Droid";
  }
}

class Rx24 {
  info() {
    return "Rx24, Pilot Droid";
  }
}

export default droidProducer;

```

### Builder

> 生成器模式：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

##### builder.js
```Javascript
function Request() {
  this.url = '';
  this.method = '';
  this.payload = {};
}

function RequestBuilder() {

  this.request = new Request();

  this.forUrl = function(url) {
    this.request.url = url;
    return this;
  };

  this.useMethod = function(method) {
    this.request.method = method;
    return this;
  };

  this.payload = function(payload) {
    this.request.payload = payload;
    return this;
  };

  this.build = function() {
    return this.request;
  };

}

module.exports = RequestBuilder;

```
##### builder_es6.js
```Javascript
class Request {
  constructor() {
    this.url = '';
    this.method = '';
    this.payload = {};
  }
}

class RequestBuilder {
  constructor() {
    this.request = new Request();
  }

  forUrl(url) {
    this.request.url = url;
    return this;
  }

  useMethod(method) {
    this.request.method = method;
    return this;
  }

  payload(payload) {
    this.request.payload = payload;
    return this;
  }

  build() {
    return this.request;
  }

}

export default RequestBuilder;

```

### Factory

> 工厂方法模式：定义一个用于创建对象的接口，让子类决定将哪一个类实例化。Factory Method使一个类的实例化延迟到其子类。

##### factory.js
```Javascript
function bmwFactory(type) {
  if (type === 'X5')
    return new Bmw(type, 108000, 300);
  if (type === 'X6')
    return new Bmw(type, 111000, 320);
}

function Bmw(model, price, maxSpeed) {
  this.model = model;
  this.price = price;
  this.maxSpeed = maxSpeed;
}

module.exports = bmwFactory;

```
##### factory_es6.js
```Javascript
class BmwFactory {

  static create(type) {
    if (type === 'X5')
      return new Bmw(type, 108000, 300);
    if (type === 'X6')
      return new Bmw(type, 111000, 320);
  }
}

class Bmw {
  constructor(model, price, maxSpeed) {
    this.model = model;
    this.price = price;
    this.maxSpeed = maxSpeed;
  }
}

export default BmwFactory;

```

### Prototype

> 原型模式：定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。本模式使得算法的变化可独立于使用它的客户。

##### prototype.js
```Javascript
function Sheep(name, weight) {
  this.name = name;
  this.weight = weight;
}

Sheep.prototype.clone = function() {
  return new Sheep(this.name, this.weight);
};

module.exports = Sheep;

```
##### prototype_es6.js
```Javascript
class Sheep {

  constructor(name, weight) {
    this.name = name;
    this.weight = weight;
  }

  clone() {
    return new Sheep(this.name, this.weight);
  }
}

export default Sheep;

```

### Singleton

> 单例模式：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

##### singleton.js
```Javascript
function Person() {

  if (typeof Person.instance === 'object')
    return Person.instance;

  Person.instance = this;

  return this;
}

module.exports = Person;

```
##### singleton_es6.js
```Javascript
class Person {
  constructor() {
    if (typeof Person.instance === 'object') {
      return Person.instance;
    }
    Person.instance = this;
    return this;
  }
}

export default Person;

```


## structural
### Adapter

> 适配器模式：将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

##### adapter.js
```Javascript
function Soldier(lvl) {
  this.lvl = lvl;
}

Soldier.prototype.attack = function() {
  return this.lvl * 1;
};

function Jedi(lvl) {
  this.lvl = lvl;
}

Jedi.prototype.attackWithSaber = function() {
  return this.lvl * 100;
};

function JediAdapter(jedi) {
  this.jedi = jedi;
}

JediAdapter.prototype.attack = function() {
  return this.jedi.attackWithSaber();
};

module.exports = [Soldier, Jedi, JediAdapter];

```
##### adapter_es6.js
```Javascript
class Soldier {
  constructor(level) {
    this.level = level;
  }

  attack() {
    return this.level * 1;
  }
}

class Jedi {
  constructor(level) {
    this.level = level;
  }

  attackWithSaber() {
    return this.level * 100;
  }
}

class JediAdapter {
  constructor(jedi) {
    this.jedi = jedi;
  }

  attack() {
    return this.jedi.attackWithSaber();
  }
}

export {
  Soldier,
  Jedi,
  JediAdapter
};

```

### Bridge

> 桥接模式：将抽象部分与它的实现部分分离，使它们都可以独立地变化。

##### bridge.js
```Javascript
function EpsonPrinter(ink) {
  this.ink = ink();
}
EpsonPrinter.prototype.print = function() {
  return "Printer: Epson, Ink: " + this.ink;
};

function HPprinter(ink) {
  this.ink = ink();
}
HPprinter.prototype.print = function() {
  return "Printer: HP, Ink: " + this.ink;
};

function acrylicInk() {
  return "acrylic-based";
}

function alcoholInk() {
  return "alcohol-based";
}

module.exports = [EpsonPrinter, HPprinter, acrylicInk, alcoholInk];

```
##### bridge_es6.js
```Javascript
class Printer {
  constructor(ink) {
    this.ink = ink;
  }
}

class EpsonPrinter extends Printer {
  constructor(ink) {
    super(ink);
  }

  print() {
    return "Printer: Epson, Ink: " + this.ink.get();
  }
}

class HPprinter extends Printer {
  constructor(ink) {
    super(ink);
  }

  print() {
    return "Printer: HP, Ink: " + this.ink.get();
  }
}

class Ink {
  constructor(type) {
    this.type = type;
  }
  get() {
    return this.type;
  }
}

class AcrylicInk extends Ink {
  constructor() {
    super("acrylic-based");
  }
}

class AlcoholInk extends Ink {
  constructor() {
    super("alcohol-based");
  }
}

export {
  EpsonPrinter,
  HPprinter,
  AcrylicInk,
  AlcoholInk
};

```

### Composite

> 组合模式：将对象组合成树形结构以表示“部分-整体”的层次结构。Composite使得客户对单个对象和复合对象的使用具有一致性。

##### composite.js
```Javascript
// composition
function EquipmentComposition(name) {
  this.equipments = [];
  this.name = name;
}

EquipmentComposition.prototype.add = function(equipment) {
  this.equipments.push(equipment);
};

EquipmentComposition.prototype.getPrice = function() {
  return this.equipments.map(function(equipment) {
    return equipment.getPrice();
  }).reduce(function(a, b) {
    return a + b;
  });
};

function Equipment() {}

Equipment.prototype.getPrice = function() {
  return this.price;
};

// -- leafs
function FloppyDisk() {
  this.name = "Floppy Disk";
  this.price = 70;
}
FloppyDisk.prototype = Object.create(Equipment.prototype);

function HardDrive() {
  this.name = "Hard Drive";
  this.price = 250;
}
HardDrive.prototype = Object.create(Equipment.prototype);

function Memory() {
  this.name = "8gb memomry";
  this.price = 280;
}
Memory.prototype = Object.create(Equipment.prototype);

module.exports = [EquipmentComposition, FloppyDisk, HardDrive, Memory];

```
##### composite_es6.js
```Javascript
//Equipment
class Equipment {

  getPrice() {
    return this.price || 0;
  }

  getName() {
    return this.name;
  }

  setName(name) {
    this.name = name;
  }
}

// --- composite ---
class Composite extends Equipment {

  constructor() {
    super();
    this.equipments = [];
  }

  add(equipment) {
    this.equipments.push(equipment);
  }

  getPrice() {
    return this.equipments.map(equipment => {
      return equipment.getPrice();
    }).reduce((a, b) => {
      return a + b;
    });
  }
}

class Cabinet extends Composite {
  constructor() {
    super();
    this.setName('cabinet');
  }
}

// --- leafs ---
class FloppyDisk extends Equipment {
  constructor() {
    super();
    this.setName('Floppy Disk');
    this.price = 70;
  }
}

class HardDrive extends Equipment {
  constructor() {
    super();
    this.setName('Hard Drive');
    this.price = 250;
  }
}

class Memory extends Equipment {
  constructor() {
    super();
    this.setName('Memory');
    this.price = 280;
  }
}

export {
  Cabinet,
  FloppyDisk,
  HardDrive,
  Memory
};

```

### Decorator
> 装饰模式：动态地给一个对象添加一些额外的职责。就扩展功能而言，Decorator模式比生成子类方式更为灵活。

```Javascript
function Pasta() {
  this.price = 0;
}
Pasta.prototype.getPrice = function() {
  return this.price;
};

function Penne() {
  this.price = 8;
}
Penne.prototype = Object.create(Pasta.prototype);

function SauceDecorator(pasta) {
  this.pasta = pasta;
}

SauceDecorator.prototype.getPrice = function() {
  return this.pasta.getPrice() + 5;
};

function CheeseDecorator(pasta) {
  this.pasta = pasta;
}

CheeseDecorator.prototype.getPrice = function() {
  return this.pasta.getPrice() + 3;
};

module.exports = [Penne, SauceDecorator, CheeseDecorator];

```
##### decorator_es6.js
```Javascript
class Pasta {
  constructor() {
    this.price = 0;
  }
  getPrice() {
    return this.price;
  }
}

class Penne extends Pasta {
  constructor() {
    super();
    this.price = 8;
  }
}

class PastaDecorator extends Pasta {
  constructor(pasta) {
    super();
    this.pasta = pasta;
  }

  getPrice() {
    return this.pasta.getPrice();
  }
}

class SauceDecorator extends PastaDecorator {
  constructor(pasta) {
    super(pasta);
  }

  getPrice() {
    return super.getPrice() + 5;
  }
}

class CheeseDecorator extends PastaDecorator {
  constructor(pasta) {
    super(pasta);
  }

  getPrice() {
    return super.getPrice() + 3;
  }
}

export {
  Penne,
  SauceDecorator,
  CheeseDecorator
};

```

### Facade

> 外观模式：为子系统中的一组接口提供一个一致的界面， Facade模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

##### facade.js
```Javascript
var shopFacade = {
  calc: function(price) {
    price = discount(price);
    price = fees(price);
    price += shipping();
    return price;
  }
};

function discount(value) {
  return value * 0.9;
}

function shipping() {
  return 5;
}

function fees(value) {
  return value * 1.05;
}

module.exports = shopFacade;

```
##### facade_es6.js
```Javascript
class ShopFacade {
  constructor() {
    this.discount = new Discount();
    this.shipping = new Shipping();
    this.fees = new Fees();
  }

  calc(price) {
    price = this.discount.calc(price);
    price = this.fees.calc(price);
    price += this.shipping.calc();
    return price;
  }
}

class Discount {

  calc(value) {
    return value * 0.9;
  }
}

class Shipping {
  calc() {
    return 5;
  }
}

class Fees {

  calc(value) {
    return value * 1.05;
  }
}

export default ShopFacade;

```

### Flyweight

> 享元模式：运用共享技术有效地支持大量细粒度的对象。

##### flyweight.js
```Javascript
function Color(name) {
  this.name = name;
}

var colorFactory = {
  colors: {},
  create: function(name) {
    var color = this.colors[name];
    if (color) return color;

    this.colors[name] = new Color(name);
    return this.colors[name];
  }
};

module.exports = colorFactory;

```
##### flyweight_es6.js
```Javascript
class Color {
  constructor(name) {
    this.name = name
  }
}

class colorFactory {
  constructor(name) {
    this.colors = {};
  }
  create(name) {
    let color = this.colors[name];
    if (color) return color;
    this.colors[name] = new Color(name);
    return this.colors[name];
  }
};

export {
  colorFactory
};

```

### Proxy

> 代理模式：为其他对象提供一个代理以控制对这个对象的访问。

##### proxy.js
```Javascript
function Car() {
  this.drive = function() {
    return "driving";
  };
}

function CarProxy(driver) {
  this.driver = driver;
  this.drive = function() {
    if (driver.age < 18)
      return "too young to drive";
    return new Car().drive();
  };
}

function Driver(age) {
  this.age = age;
}

module.exports = [Car, CarProxy, Driver];

```
##### proxy_es6.js
```Javascript
class Car {
  drive() {
    return "driving";
  };
}

class CarProxy {
  constructor(driver) {
    this.driver = driver;
  }
  drive() {
    return (this.driver.age < 18) ? "too young to drive" : new Car().drive();
  };
}

class Driver {
  constructor(age) {
    this.age = age;
  }
}

export {
  Car,
  CarProxy,
  Driver
};

```



