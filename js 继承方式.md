## 1. 原型链继承
  将构造函数的原型设置为另一个构造函数的实例对象，这样就可以继承另一个原型对象的所有属性和方法，可以继续往上，最终形成原型链。

  第一个问题是，当实现继承后，另一个原型的实例属性，变成了现在这个原型的原型属性，然后该原型的引用类型属性会被所有的实例共享，这样继承原型引用类型属性的实例之间不再具有自己的独特性了。

  第二个问题是，在创建子类型的实例时，没有办法在不影响所有对象实例的情况下给超类型的构造函数中传递参数。

## 2. 借用构造函数继承
  为了解决原型中包含引用类型值的问题，开始使用借用构造函数，也叫伪造对象或经典继承

    function SuperType() {
      this.colors = ["red", "blue", "green"];
    }

    function SubType() {
      //继承SuperType
      SuperType.call(this);
    }

    var instance1 = new SubType();
    instance1.colors.push("black");
    alert(instance1.colors); //"red,blue,green,black"
    var instance2 = new SubType();
    alert(instance2.colors); //"red,blue,green" 
  将SuperType函数在SubType构造函数中调用，在每个实例中执行，这样每个实例中都会有一份SuperType中的属性方法的副本，也就实现了继承SuperType。

  这种模式的优势就是可以在子类型构造函数中向超类型构造函数传递参数。

  存在的问题就是，所有的类型都只能使用构造函数模式（因为超类型的原型中定义的方法对于子类型不可见），因此方法都在构造函数中定义，函数复用就无从谈起了。

## 3. 组合继承
  也叫伪经典继承，将原型链和借用构造函数的技术组合到一块。使用原型链实现对原型属性和方法的继承，而通过构造函数来实现对实例属性的继承。

    function SuperType(name) {
      this.name = name;
      this.colors = ["red", "blue", "green"];
    }

    SuperType.prototype.sayName = function() {
      alert(this.name);
    }

    function SubType(name, age) {
      // 继承属性
      SuperType.call(this, name);
      this.age = age;
    }

    // 继承方法
    SubType.prototype = new SuperType();
    SubType.prototype.constructor = SubType;
    SubType.prototype.sayAge = function() {
      alert(this.age);
    };

    var instance1 = new SubType("Nicholas", 29);
    instance1.colors.push("black");
    alert(instance1.colors); //"red,blue,green,black"
    instance1.sayName(); //"Nicholas";
    instance1.sayAge(); //29
    var instance2 = new SubType("Greg", 27);
    alert(instance2.colors); //"red,blue,green"
    instance2.sayName(); //"Greg";
    instance2.sayAge(); //27 
  将SubType的原型指定为SuperType的一个实例，大致步骤和原型链继承类似，只是多了在SubType中借调SuperType的过程。

  实例属性定义在构造函数中，而方法则定义在构造函数的新原型中，同时将新原型的constructor指向构造函数。

  可以通过instanceof和isPrototypeOf()来识别基于组合继承创建的对象。

  避免了原型链和借用构造函数的缺陷，融合了它们的优点，成为JS中最常用的继承模式。

  实际上是借用了构造函数，以覆盖的方式，解决了在原型链继承中原型的引用类型属性共享在所有实例中的问题。

  因为在子类型中借调构造函数(SuperType.call(this))时，会在自己的所有实例中执行一遍SuperType中的代码，由于每个实例this都是不同的，因此SuperType中定义的属性会在每个实例中有一份副本，也就避免了原型链继承中，原型属性共享的问题（覆盖了原型属性）。

## 4. 原型式继承
  不自定义类型的情况下，临时创建一个构造函数，借助已有的对象作为临时构造函数的原型，然后在此基础实例化对象，并返回。

    function object(o){
    function F(){}
    F.prototype = o;
    return new F();
    } 
本质上是object()对传入其中的对象执行了一次浅复制

    var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
    };

    var anotherPerson = object(person);
    anotherPerson.name = "Greg";
    anotherPerson.friends.push("Rob");

    var yetAnotherPerson = object(person);
    yetAnotherPerson.name = "Linda";
    yetAnotherPerson.friends.push("Barbie");

    alert(person.friends); //"Shelby,Court,Van,Rob,Barbie" 
  原型的引用类型属性会在各实例之间共享。

  当只想单纯地让一个对象与另一个对象保持类似的情况下，原型式继承是完全可以胜任的。

  注意:

  ES5 通过新增 Object.create()方法规范化了原型式继承。这个方法接收两个参数：一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。在传入一个参数的情况下，Object.create()与这里的object()方法的行为相同。第二个参数与Object.defineProperties()方法的第二个参数格式相同：每个属性都是通过自己的描述符定义的。

## 5. 寄生式继承
  其实就是在原型式继承得到对象的基础上，在内部再以某种方式来增强对象，然后返回。  

    function createAnother(original) {
      var clone = object(original);
      clone.sayHi = function() {
        alert("hi");
      };
      return clone;
    }
  思路与寄生构造函数和工厂模式类似。

  新的对象中不仅具有original的所有属性和方法，而且还有自己的sayHi()方法。

  寄生式继承在主要考虑对象而不是自定义类型和构造函数的情况下非常有用。

  由于寄生式继承为对象添加函数不能做到函数复用，因此效率降低。

## 6. 寄生组合式继承
  组合继承是JS中最常用的继承模式，但其实它也有不足，组合继承无论什么情况下都会调用两次超类型的构造函数，并且创建的每个实例中都要屏蔽超类型对象的所有实例属性。

  寄生组合式继承就解决了上述问题，被认为是最理想的继承范式。

    function object(o) {
      function F(){}
      F.prototype = o;
      return new F();
    }

    function inheritPrototype(superType, subType) {
      var prototype = object(superType.prototype);
      prototype.constructor = subType;
      subType.prototype = prototype;
    }

    function SuperType(name) {
      this.name = name;
      this.colors = ["red", "blue", "green"];
    }

    SuperType.prototype.sayName = function() {
      alert(this.name);
    };

    function SubType(name, age) {
      SuperType.call(this, name);
      this.age = age;
    }

    inheritPrototype(SuperType, SubType);	// 这一句，替代了组合继承中的SubType.prototype = new SuperType()

    SubType.prototype.sayAge = function() {
      alert(this.age);
    };
  既然在组合模式中我们通过借调构造函数来为每个实例定义实例属性，从而覆盖原型属性，影响了效率，那么是否可以把原型改变一下呢，不让它作为SuperType的实例，这样就不会有一些无用的原型属性了。

  不必为了指定子类型的原型而调用超类型的构造函数，我们需要的只不过是超类型原型的一个副本。

  在inheritPrototype()函数中所做的事:

  在inheritPrototype函数中用到了原型式继承中的object()方法，将超类型的原型指定为一个临时的空构造函数的原型，并返回构造函数的实例。
  此时由于构造函数内部为空（不像SuperType里面有实例属性），所以返回的实例也不会自带实例属性，这很重要！因为后面用它作为SubType的原型时，就不会产生无用的原型属性了，借调构造函数也就不用进行所谓的“重写”了。
  然后为这个对象重新指定constructor为SubType，并将其赋值给SubType的原型。这样，就达到了将超类型构造函数的实例作为子类型原型的目的，同时没有一些从SuperType继承过来的无用原型属性。