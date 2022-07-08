### 值对象

#### 区分值对象和实体的依据

1、业务参与者对它的相等判断是依据值还是依据身份标识
在办理还书手续的场景中，图书管理员并不关心图书的信息，而是判断归还的图书ID是否包含在借阅记录中。
如果图书丢失了，读者即使自行购买了一本相同的图书来尝试归还，也不能正常办理还书手续。因此，图书Book在借阅管理场景中应定义为实体类型。

2、确定对象的属性值是否会发生变化，如果变化了，究竟是产生一个完全不同的对象，还是维持相同的身份标识
在员工的出勤记录的场景中，依据相等性进行判断时，可以认为出勤记录的值相等就是同一条出勤记录，这意味着我们可以将其定义为值对象。
然而，出勤记录的状态值是可以更改的，假定根据打卡的结果判断该员工为矿工，在员工提出申请并证明其忘记打卡时，就需要修改出勤记录的状态。
修改后的出勤记录还是同一条出勤记录，其同一性只能通过唯一的身份标识进行判断，这意味着应将出勤记录定义为实体。

3、生命周期的管理
值对象没有身份标识，意味着无须管理其生命周期。

#### 不变性
考虑到值对象只需要关注值的特点，领域驱动设计建议**尽量将值对象设计为不变类**。
若能保证值对象的不变性，就可以减少并发控制的成本，因为一个不变的类是线程安全的。

不变类定义需要满足的几个条件：
1、对象创建以后其状态就不能修改
2、对象的所有字段都是final类型
3、对象是正确创建的（创建期间没有this引用溢出）

```java
public final class Money {

    private final Double faceValue;
    private final Currency currency;

    public Money() {
        this(0d, Currency.RMB);
    }

    public Currency getCurrency() {
        return currency;
    }

    public Double getFaceValue() {
        return faceValue;
    }

    public Money(Doucle value, Currency currency) {
        this.faceValue = value;
        this.currency = currency;
    }

    public Money add(Money toAdd) {
        if (!currency.equals(toAdd.getCurrency())) {
            throw new NonMatchingCurrencyException("You cannot add money with different currencies");
        }

        return new Money(faceValue + toAdd.getFaceValue(), currency);
    }
}
```
Money类的faceValue与currency字段均被声明为final字段，由构造函数初始化。
faceValue字段的类型为不变的double类型，currency字段为不变的枚举类型。
add()方法并没有直接修改当前对象的值，而是返回了一个新的Money对象。

### 领域行为
值对象拥有的往往是”自给自足的领域行为“。这些领域行为能够让值对象的表现能力变得更加丰富，更加智能。它们通常为值对象提供如下能力：
1、自我验证
2、自我组合
3、自我运算

### 值对象的优势
在进行领域设计建模时，要善于运用值对象而非内建类型去表达那些细粒度的领域概念（仅就静态语言而言）。相较于内建类型，值对象的优势更加明显。
内建类型：编程语言提供的内置类型。
- 内建类型无法展现领域概念，值对象则不然。例如String类型与Name类型表达name属性、int类型与Age类型表达age属性。
显然使用后者更加直观地体现了业务含义。

- 内建类型无法封装显而易见的领域逻辑，值对象则不然。除了少数语言提供了为已有类型扩展方法的机制，内建类型都是封闭的。
如果属性定义为内建类型，就无法封装领域行为，只能将其交给拥有属性的主对象，导致作为主对象的实体变得臃肿。

- 内建类型缺乏验证能力，值对象则不然。对强类型语言而言，类型的验证包括两方面：对类型的自身验证和对值的验证。
如前所述，值对象具有自我验证的能力，其定义的类型自身也是一种隐含的验证。
例如，分别定义书名与书号为Title与ISBN值对象后，如果调用者将书的编号误传给书名，编译器会检查到类型不匹配错误。