
# Golang 工厂模式

## 简单工厂模式

**简单工厂模式（Simple Factory Pattern）** ：定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。因为在简单工厂模式中用于创建实例的方法是静态（static）方法，因此简单工厂模式又被称为静态工厂方法（Static Factory Method）模式，它属于类创建型模式。

### 简单工厂需要:

- 工厂结构体
- 产品接口
- 产品结构体

### 示例说明

创建一个饺子店工厂结构体，和饺子类的接口。该工厂的其中一个方法用来生产不同口味的饺子，如韭菜的猪肉馅的。

    type DumplingsShop struct{
        Generate(t string) *Dumplings
    }


    type Dumplingsinterface interface {
        create()
    }

创建肉馅和韭菜馅的饺子结构体，并且实现对应接口的方法。

      type DumplingsMeat struct{}

      func (* DumplingsMeat)create(){
          fmt.Println("DumplingsMeat create")
      }

      type DumplingsChives struct{}

      func (* DumplingsChives)create(){
          fmt.Println("DumplingsMeat create")
      }

    func(* DumplingsShop)Create(type string)*Dumplings{
        switch type {
            case "meat":
            return new(DumplingsMeat)
            case "chives":
            return new(DumplingsChives)
            default:
            return nil
        }
    }

工厂实例化调用

    var type string
    dumplingFactory := DumplingsShop{}
    type = "meat"
    meat := dumplingFactory.Create(type)//返回肉馅饺子对象
    meat.create()
    type = "chives"
    chives := dumplingFactory.Create(type) //返回韭菜馅饺子对象
    chives.create()

## 简单工厂模式优缺点

- **优点:** 工厂类是整个工厂模式的核心，我们只需要传入给定的信息，就可以创建所需实例，在多人协作的时候，无需知道对象之间的内部依赖，可以直接创建，有利于整个软件体系结构的优化。

- **缺点:** 工厂类中包含了所有实例的创建逻辑，一旦这个工厂类出现问题，所有实例都会受到影响，并且，工厂类中生产的产品都基于一个共同的接口，一旦要添加不同种类的产品，这就会增加工厂类的复杂度，将不同种类的产品混合在一起，违背了单一职责，系统的灵活性和可维护性都会降低，并且当新增产品的时候，必须要修改工厂类，违背了『系统对扩展开放，对修改关闭』的原则。

## 工厂方法模式

**工厂方法模式（英语：Factory method pattern）** 是一种实现了“工厂”概念的面向对象设计模式。就像其他创建型模式一样，它也是处理在不指定对象具体类型的情况下创建对象的问题。工厂方法模式的实质是“定义一个创建对象的接口，但让实现这个接口的类来决定实例化哪个类。工厂方法让类的实例化推迟到子类中进行。”，实际应用中工厂方法模式针对的是一个产品等级结构。




### 工厂方法需要:

- 工厂接口
- 工厂结构体
- 产品接口
- 产品结构体

### 示例说明

创建一个饺子店工厂接口，和饺子类的接口。该工厂用来生产不同口味的饺子，如韭菜的猪肉馅的。

    type DumplingsShopinterface interface{
        Generate(t string) *Dumplings
    }

    type Dumplingsinterface interface {
        create()
    }

创建北京和西安对应馅的饺子结构体，并且实现对应接口的方法。

      type BeijingDumplingsMeat struct{}

      func (* BeijingDumplingsMeat)create(){
          fmt.Println("BeijingDumplingsMeat create")
      }

      type BeijingDumplingsChives struct{}
      ...
      type XianDumplingsMeat struct{}
      ...
      type XianDumplingsChives struct{}
      ...

创建北京和西安工厂

    type BeijingDumplings struct{}
    type XianDumplings struct{}

    func(* BeijingDumplings)Generate(t string) *Dumplings{
        switch t { 
        case "chives" :
        return new(BeijingDumplingsChives) 
        case "meat" :
        return new(BeijingDumplingsMeat) 
        default:
        return nil
        }
    }

    func(* XianDumplings)Generate(t string) *Dumplings{
        switch t{}
        case "chives" :
        return new(XianDumplingsChives) 
        case "meat" :
        return new(XianDumplingsMeat) 
        default:
        return nil
    }

工厂实例化调用

      var DumplingsShopFactory DumplingsShopinterface
      DumplingsShopFactory := new(BeijingDumplings)
      b = DumplingsShopFactory.Generate("meat")  // 传入肉馅的参数，会返回北京市的肉馅饺子
      b.create()

      DumplingsShopFactory := new(XianDumplings)
      b = DumplingsShopFactory.Generate("meat") // 同样传入肉馅的参数，会返回北京市的肉馅饺子
      b.create()


### 工厂方法模式的优缺点

- **优点:** 符合“开闭”原则，具有很强的的扩展性、弹性和可维护性。修改时只需要添加对应的工厂类即可使用了依赖倒置原则，依赖抽象而不是具体，使用（客户）和实现（具体类）松耦合。客户只需要知道所需产品的具体工厂，而无须知道具体工厂的创建产品的过程，甚至不需要知道具体产品的类名。

- **缺点:** 每增加一个产品时，都需要一个具体类和一个具体创建者，使得类的个数成倍增加，导致系统类数目过多，复杂性增加对简单工厂，增加功能修改的是工厂类；对工厂方法，增加功能修改的是产品类。

## 抽象工厂模式

**抽象工厂模式（英语：Abstract factory pattern）** 是一种软件开发设计模式。抽象工厂模式提供了一种方式，可以将一组具有同一主题的单独的工厂封装起来。在正常使用中，客户端程序需要创建抽象工厂的具体实现，然后使用抽象工厂作为接口来创建这一主题的具体对象。客户端程序不需要知道（或关心）它从这些内部的工厂方法中获得对象的具体类型，因为客户端程序仅使用这些对象的通用接口。抽象工厂模式将一组对象的实现细节与他们的一般使用分离开来。实际应用中针对的多个产品等级结构。

## 示例说明

创建一个肉馅饺子店工厂接口和韭菜馅饺子店工厂接口，以及饺子类的接口。

    type DumplingsShopinterface interface{
        GenerateMeatDumpling() *Dumplingsinterface
        GenerateChivesDumpling() *Dumplingsinterface
    }

    type Dumplingsinterface interface {
        create()
    }

实现北京肉馅饺子、北京韭菜饺子、西安肉馅饺子、西安韭菜饺子4中实例对象。

      type BeijingDumplingsMeat struct{}

      func (* BeijingDumplingsMeat)create(){
          fmt.Println("BeijingDumplingsMeat create")
      }

      type BeijingDumplingsChives struct{}
      ...
      type XianDumplingsMeat struct{}
      ...
      type XianDumplingsChives struct{}
      ...

创建北京和西安工厂

    type BeijingDumplingsFactory struct{}
    type XianDumplingsFactory struct{}

    func(* BeijingDumplingsFactory)GenerateMeatDumpling() *Dumplings{
        return new(BeijingDumplingsMeat) 
        }
    }
    func(* BeijingDumplingsFactory)GenerateChivesDumpling() *Dumplings{
        return new(BeijingDumplingsChives) 
        }
    }

    func(* XianDumplingsFactory)GenerateMeatDumpling() *Dumplings{
        return new(XianDumplingsMeat) 
        }
    }
    func(* XianDumplingsFactory)GenerateChivesDumpling() *Dumplings{
        return new(XianDumplingsChives) 
        }
    }

工厂示例调用

      var DumplingsShopFactory DumplingsShopinterface
      DumplingsShopFactory := new(BeijingDumplingsFactory)
      b = DumplingsShopFactory.GenerateMeatDumpling()  // 传入肉馅的参数，会返回北京市的肉馅饺子
      b.create()
      ...


### 抽象工厂模式的优缺点

- **优点:** 抽象工厂模式除了具有工厂方法模式的优点外，最主要的优点就是可以在类的内部对产品族进行约束。所谓的产品族，一般或多或少的都存在一定的关联，抽象工厂模式就可以在类内部对产品族的关联关系进行定义和描述，而不必专门引入一个新的类来进行管理。

- **缺点:** 产品族的扩展将是一件十分费力的事情，假如产品族中需要增加一个新的产品，则几乎所有的工厂类都需要进行修改。所以使用抽象工厂模式时，对产品等级结构的划分是非常重要的

