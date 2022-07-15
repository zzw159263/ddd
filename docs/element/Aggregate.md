### 聚合

#### 类的关系
在理解聚合的概念之前，需要先理清面向对象设计中类之间的关系。

对象建模需要表达的类关系包括：
- 泛化（generalization）
- 关联（association）
- 依赖（dependency）

1、泛化关系

泛化关系体现了通用父类和特定子类之间的关系。在编程语言中往往表示为子类继承自父类或子类派生自父类。
泛化关系在UML类图中以空心三角形加实线的形式表现。

泛化关系会导致子类与父类之间的耦合，父类发生的任何变更都会传递给子类，形成所谓的”脆弱的父类“。
在面向对象设计要素中，我们往往使用继承这一术语来表示泛化关系。

2、关联关系
关联关系代表了类之间的一种结构关系，用以指定一个类的对象与另一个类的对象之间存在连接关系。
关联关系包括一对一、一对多、多对多关系，在UML类图中分别用连线和数字标记关联关系和关系的数量。
如果两个类之间的关联关系存在方向，则需要使用箭头表示关联的导航方向。如果没有箭头，就表示双向关联。

组合关系：
存在一种特殊的关联关系：关联双方分别体现整体与部分的特征，代表整体的对象包含了代表部分的对象。
依据关系的强弱，组合关系又分为合成（composition）关系与聚合（aggregation）关系。

- 合成关系
合成关系不仅代表了整体与部分的关系，还体现了强烈的”所有权“特征。
这种所有权使得二者的生命周期存在一种啮合关系，即组成合成关系的两个对象属于同一个生命周期。
当代表整体概念的主体对象被销毁时，代表部分概念的从对象也将随之而被销毁。
- 聚合关系
聚合关系同样代表了整体和部分的包含关系，却没有所有权特征，不会约束它们的生命周期，故而关联强度要弱于合成关系。

显然，满足组合关系的两个类不应该存在多对多关系，因为两个类不可能互为整体和部分。

3、依赖关系
依赖关系代表一个类使用了另一个类的信息或服务。
依赖双方耦合较弱。
依赖关系产生于：
- 类的方法接收了另一个类的参数；
- 类的方法返回了另一个类的对象；
- 类的方法内部创建了另一个类的示例；
- 类的方法内部使用了另一个类的成员。

#### 模型的设计约束
领域对象模型表达了领域概念映射的类以及类之间的关系，类的关系导致了对象之间的耦合。
如果不对类的关系加以控制，耦合就会蔓延。一旦需要考虑数据持久化、一致性、对象之间的通信机制以及加载数据的性能等设计约束，
网状的耦合关系就会成为致命毒药，直接影响领域驱动设计模型的质量。

1、控制类的关系

- 去除不必要的关系：如果确定类之间的关系没有必要存在，就要斩断它；
- 降低耦合的强度：引入泛化提取通用特征，形成更弱的依赖或关联关系；正确识别合成还是聚合关系，也能降低耦合强度；
```java

```
- 避免双向耦合：

2、引入边界

在一个复杂的软件系统中，即使通过正确地甄别和控制关系来改进模型，但由于规模的原因，由对象建立的模型最终还是会形成下图所示的一张彼此互联互通的对象网。
对关系的控制可以让对象模型中类之间的关系变得更简单。同时还需要**引入边界来降低和限制领域类之间的关系**。

如何控制边界：
- 依据”高内聚低耦合“的原则，将高内聚的类放在一个边界内，形成各自的边界（这种边界不是限界上下文的边界而是类的边界）
- 每个边界定义一个主对象，边界外的对象只能访问边界内的主对象
根据以上方法划分的边界称为**聚合**，边界内的主对象称为**聚合根**

#### 聚合的定义与特征
定义：

将实体和值对象划分为聚合并围绕着聚合定义边界。选择一个实体作为每个聚合的根，并允许外部对象仅能持有聚合根的引用。
作为一个整体来定义聚合的属性和不变量，并将执行职责赋予聚合根或指定的框架机制。

特征：
- 聚合是包含了实体和值对象的一个边界（聚合内部只能包含实体和值对象）；
- 聚合内包含的实体和值对象形成一棵树，只有实体才能作为这棵树的根。这个根称为聚合根，这个实体称为根实体；
- 外部对象只允许持有聚合根的引用，以起到边界的控制作用；
- 聚合作为一个完整的领域概念整体，其内部会维护这个领域概念的完整性，体现业务上的不变量约束；
- 由聚合根统一对外提供履行该领域概念职责的行为方法，实现内部各个对象之间的行为协作。

面向对象的聚合（OO聚合）与领域驱动设计聚合（DDD聚合）的区别：
OO聚合：Account（账户）与Transaction（交易）之间存在整体与部分的关系，Account包含多个Transaction记录
DDD聚合：一个Account对象可以聚合0~n个Transaction对象，但他们却分别属于两个不同的DDD聚合。

#### 聚合的设计原则
1、完整性

聚合作为一个受到边界控制的领域共同体，对外由聚合根体现为一个统一的概念，对内则管理和维护着高内聚的对象关系。对内对外具有一致的生命周期。
例如：订单聚合由Order聚合根实体体现订单的领域概念，其中的OrderItem（订单商品）和Address（配送地址）不能脱离订单单独存在。
这些对象在创建订单时需要一并创建且拥有共同的生命周期。如果聚合中对象的生命周期不一致就违背了一致性。

完整性除了可以通过聚合来保证，也可以通过聚合之间的关系来保证，二者无非是约束机制不同。

2、独立性

考虑独立性时，可以针对聚合内的非聚合根实体（除聚合根实体外的其他实体）询问：
- 目标聚合是否已经足够完整
- 带合并实体是否会被调用者单独使用

例如：在线试题领域中Question（问题）与Answer（答案）之间，Answer无法独立与Question单独存在，因此二者在同一个聚合中。
在线问答平台领域中，对一个Question用户可以有不同的Answer并且可以针对Answer进行点赞、评论、分享、收藏等操作。
这些操作赋予了Answer独立的行为，此时Question和Answer就可以独立成两个聚合，通过引用来维持Question聚合的完整性。

不同于实体，值对象不存在这种独立性，因为值对象不能脱离实体单独称为一个聚合。

3、不变量

在数据发生变化时必须保持的一致性规则，涉及聚合成员之间的内部关系。
不变量代表了领域逻辑中的业务规则或验证条件，有时也可将不变量理解为“不变条件”或“固定规则”。

例如：在航班计划限界上下文中，编写“修改航班计划起飞时间与计划到达时间”这一业务服务规则时，有如下标准：
若该航班有共享航班，在修改航班计划起飞时间与到达时间时，关联的所有共享航班的计划起飞时间与计划到达时间也要随之修改，以保持与主航班的一致性。

这一验收标准可以视为航班与共享航班之间的不变量。针对这一业务场景，需要将Flight与SharedFlight两个实体放入同一个聚合且以Flight为聚合根。

例如：报表系统中“报表类别名称不可少于8个字符且不能重复”

这一规则是验证条件，对报表聚合内部报表类别值对象的Name属性进行单独验证，没有聚合对象之间的关系进行约束，非不变量。

4、一致性

5、最高原则

只有聚合根才是访问聚合边界的唯一入口。
例如：订单聚合外的对象要修改订单项的商品数量，就需要通过获得Order聚合根实体，然后通过Order操作OrderItem对象进行修改。
```java
    Order order = orderRepo.orderOf(orderId).get();
    order.changeItemQuantity(orderItemId, quantitiy);
    orderRepo.save(order);
```
changeItemQuantity()方法的封装符合信息专家模式的要求，会促使聚合与外部对象的协作尽量以行为协作方式进行。
同时也避免了作为聚合隐私的内部对象暴露到聚合之外，促进了聚合边界的保护作用。

#### 聚合的协作

聚合之间的引用形式有两种：
- 聚合根对象的引用
- 集合根身份表示的引用

根据聚合的最高原则，聚合外部的对象不能引用除根实体之外的任何内部对象，但同时允许聚合内部的对象保持对其他聚合根的引用。

聚合的协作由于都通过聚合根实体这唯一的入口，就等同于根实体的协作，也就体现为根实体之间的关联关系和依赖关系。

1、关联关系
在代码模型中，当你将一个聚合或聚合的集合定义为另一个聚合的字段时，就意味着主聚合需要承担其字段的生命周期管理工作。
这一做法违背了聚合的设计原则。
例如：博客Blog和博文Post分属两个聚合，定义在同一个限界上下文中。他们之间存在组合关系，如下实现仍然不合理：

```java
public class Blog extends AggregateRoot<Blog> {
    private List<Post> posts;

    public List<Post> getPosts() {
        return this.posts;
    }
}
```
Blog聚合和Post聚合的生命周期应由各自的资源库分别管理。
当BlogRepository在加载Blog聚合时，并不需要加载其下的所有Post，即使采用延迟加载的方式，也不妥当。
如果我们将发出导航的聚合称为**主聚合**，将导航指向的聚合称为**从聚合**，则正确的设计应使得：
- 主聚合不考虑从聚合的生命周期，完全不知从聚合；
- 从聚合通过主聚合根实体的ID建立与主聚合的隐含联系。
Blog聚合指向Post聚合，Blog为主聚合，Post为从聚合，则设计应调整为：
```java
/**
 * 主聚合Blog感知不到从聚合Post的信息
 */
public class Blog extends AggregateRoot<Blog> {
    private BlogId blogId;
    
}

public class Post extends AggregateRoot<Post> {
    private PostId postId;
    
    //通过主聚合的blogId建立关联
    private String blogId;
}
```

既然不允许聚合根之间以对象引用方式建立关联，那么聚合内部的对象就更不能关联外部的聚合根了，这在一定程度上会影响编码的实现。
考虑Order聚合内OrderItem实体与Product聚合根之间的关系。毫无疑问，采用对象引用更加简单直接：

```java
public class OrderItem extends Entity<OrderItemId> {
    // Product为商品聚合的根实体
    private Product product;
    private Quantitiy quantitiy;

    public Product getProduct() {
        return this.product;
    }
}
```
直接通过OrderItem引用的Product聚合根实例即可遍历商品信息：
```java
    List<OrderItem> orderItems = order.getOrderItems();
    orderItems.forEach(oi -> oi.getProduct() );
```
问题在于，Order聚合的资源库无法管理Product聚合的生命周期，也就是说，OrderRepository在获得订单时，无法获得对应的Product对象。
既然如此，就应该在OrderItem内部引用Product聚合的身份标识：

```java
public class OrderItem extends Entity<OrderItemId> {
    // Product聚合的身份标识
    private String productId;

    public String getProductId() {
        return this.productId;
    }
}
```
通过身份标识引用外部的聚合根，就能解除彼此之间强生命周期的依赖，也避免了加载引用的聚合对象。
在菱形对称架构中，只要OrderItem持有了Product的身份标识，假设订单和商品分处不同的限界上下文，应用服务想要获得客户订单中的商品信息。
可以通过OrderResponse响应消息的装配器OrderResponseAssembler调用ProductClient获得商品信息，并将其组装为OrderResponse消息：
```java
public class OrderAppService {
    
    @Service
    private OrderService orderService;
    @Service 
    private OrderResponseAssembler assembler;
    
    public OrdersResponse customerOrders(String customerId) {
        List<Order> orders = orderService.allOrdersBy(customerId);
        List<OrderResponse> orderResponses = orders.stream
                                                   .map(order -> assembler.assemble(order))
                                                   .collect(Collectors.toList());
        return new OrdersResponse(orderResponses);
            
    }
}

public class OrderResponseAssembler {
    
    @Service
    private ProductClient productClient;
    
    public OrderResponse assemble (Order order) {
        OrderResponse orderResponse = transformFrom(order);
        List<OrderItemResponse> orderItemResponses = order.getOrderItems.stream()
                                                          .map(oi -> transformFrom(oi))
                                                          .collect(Collectors.toList());
        orderResponse.addAll(orderItemResponses);
        return orderResponse;
    }
    
    private OrderResponse transformFrom(Order order) {
        // do something
    }
    
    private OrderItemResponse transformFrom(OrderItem orderItem) {
        OrderItemResponse orderItemResponse = new OrderItemResponse();
        // do something
        ProductResponse product = productClient.productBy(orderItem.getProductId());
        orderItemResponse.setProductId(product.getId());
        orderItemResponse.setProductName(product.getName());
        orderItemResponse.setProductPrice(product.getPrice());
        
        return orderItemResponse; 
    }
}
```

2、依赖关系

#### 聚合生命周期的管理
对象不可能永远存在内存中管理，结合持久化，将对象的生命周期与数据操作结合，聚合的生命周期包含以下几个阶段：

1、工厂

对象创建的原则：
- 禁止外部对象绕开聚合根直接创建其内部对象；
在Java语言中，可以为每一个聚合建立一个包，除聚合根之外，聚合内的其他实体和值对象的构造函数皆定义为默认访问修饰符。
```java
// questioncontext为问题上下文
// question为Question聚合的包名
package com.dddexplained.dddclub.questioncontext.domain.question;

public class Question extends Entity<QuestionId> implements AggregateRoot<Question> {
    
    public Question(String title, String description){
        // do something
    }
}

// Question聚合内的Answer与聚合根位于同一个包
package com.dddexplained.dddclub.questioncontext.domain.question;

public class Answer{
    
    // 定义为默认访问修饰符，只允许同一个包的类访问
    Answer(String... results) {
        // do something
    }
}
```
- 使用工厂创建对象
如果使用构造函数创建对象，其他聚合需要了解该聚合的创建逻辑，就违背了“最小知识法则”，并且在面对复杂的构造逻辑时，显得有些力不从心。
基于以上因素考虑，有必要对创建逻辑进行封装，引入工厂（factory）承担这一职责。

1）**由被依赖聚合担任工厂**

领域驱动设计虽然建议引入工厂创建聚合，但并不要求必须引入专门的工厂类，而是可由一个聚合担任另一个“聚合的工厂”。
担任工厂角色的聚合称为“聚合工厂”，被创建的聚合称为“聚合产品”。

使用场景：
- 聚合工厂往往由被引用的聚合来承担，如此就可以**将自己拥有的信息传给被创建的聚合产品**。

```java
public class Blog extends Entity<BlogId> implements AggregateRoot<Blog> {
    // 工厂方法时一个实例方法，无需再传入BlogId
    public Post createPost(String title, String content) {
        // 这里的id是Blog的id
        // 通过调用value()方法将id的值传递给Post，建立它与Blog的隐含关联
        return new Post(this.id.value(), title, content, this.authorId);
    }
}
```
PostService领域服务作为调用者，可通过Blog聚合创建文章；
```java
public class PoseService{
    
    private BlogRepository blogRepository;
    private PostRepository postRepository;
    
    public void writePost(String blogId, String title, String content) {
        Blog blog = blogRepository.blogOf(BlogId.of(blogId));
        Post post = blog.createPost(title, content);
        postRepository.add(post);
    }
}
```
- 当聚合产品的创建需要用到聚合工厂的方法时，可以考虑这一设计方式。
例如，培训上下文定义了Training和Course聚合，而创建Training聚合时需要判断Course的日程信息：
```java
public class Course extends Entity<CourseId> implements AggregateRoot<Course> {
    
    private List<Calendar> calendars = new arrayList<>();
    
    public Training createFrom (CalendarId calendarId) {
        if (notContains(calendarId)) {
            throw new TrainingException("Selected calendar is not scheduled for current course.");
        }
        
        return new Training(this.id, calendarId);
    } 
    
    // calendars是Course拥有的知识，要通过它确定培训的Calendar属于课程日常计划
    private boolean notContains(CalendarId calendarId) {
        return calendars.stream().allMatch(c -> c.id().equles(calendarId));
    }
}
```
由于创建方法会产生聚合工厂与聚合产品之间的依赖，若二者位于不同限界上下文，遵循菱形对称架构的要求，应避免这一设计。

2）**引入专门的聚合工厂**

当创建的聚合属于一个多态的继承体系时，构造函数就无法封装这一创建逻辑了，我们又不能将创建逻辑的判断职责“转嫁”给调用者，就有必要引入专门的工厂类。

例如：
以下案例中，由于航班Flight聚合本身形成了一个继承体系，如下图。创建什么样的聚合需要某些条件控制，因此创建了专门的聚合工厂。
```java
public class FlightFactory {
    public static Flight createFlight(String flightId, String ioFlag, String airportCode, String airlineIATACode) {
        if (ioFlag.equalsIngnoreCase("A")) {
            return new ArrivalFlight(flightId, airportCode, airlineIATACode);
        }
        
        return new DepartualFlight(flightId, airportCode, airlineIATACode);
    }
}
```
当然，为了满足聚合创建的未来变化，亦可考虑引入工厂方法模式或抽象工厂模式，甚至通过获得类型元数据后利用反射来创建。

由于**不建议聚合依赖于访问外部资源的端口**，引入专门工厂类的另一个好处是可以通过它依赖端口获得创建聚合时必要的值。
例如，在创建跨境电商平台的商品聚合时，海外商品的价格采用了不同的汇率，在创建商品时，需要将不同的汇率按照当前的汇率牌价统一换算为人民币。
汇率换算器ExchangeRateConverter需要调用第三方的汇率换算服务，实际上属于商品上下文南向网关的客户端端口。
工厂类ProductFactory会调用它：
```java
public class ProductFactory {
    
    @Autowired
    private ExchangeRateConverter converter;
    
    public Product createProduct(String name, String description, String price) {
        Money valueOfPrice = converter.convert(price.getValue());
        return new Product(name, description, new Price(valueOfPrice));
    }    
}
```
由于需要通过依赖注入将适配器实现注入工厂类，故而该工厂类定义的工厂方法为实例方法。
为防止调用者绕开工厂直接实例化聚合，可考虑将聚合根实体的构造函数声明为包范围内限制，并将聚合工厂与聚合产品放在同一个包。

3）**聚合自身担任工厂**

如果创建聚合产品的过程不需要用到聚合工厂的知识也不需要外部控制，可以让聚合自身担任工厂
这是一种典型的简单工厂模式，例如由Order类定义静态方法 ，封装创建自身实例的逻辑。
```java
package com.dddexpained.ecommerce.ordercontext.domain.order;

public class Order {
    // 定义私有构造函数
    private Order (CustomerId customerId, ShippingAddress address, Contact contact, Basket basket) {
        // do something
    }
    
    public static Order createOrder(CustomerId customerId, ShippingAddress address, Contact contact, Basket basket) {
        if (customerId == null || customerId.isEmpty()) {
            throw new OrderException("Null or empty customerId.");
        }
        if (address == null || address.isInvalid()) {
            throw new OrderException("Null or invalid address.");
        }
        if (contact == null || contact.isInvalid()) {
            throw new OrderException("Null or invalid contact.");
        }
        if (basket == null || basket.isInvalid()) {
            throw new OrderException("Null or invalid basket.");
        }
        
        return new Order(customerId, address, contact, basket);
    }
}
```
这一设计方式无须多余的工厂类，创建聚合对象的逻辑也更严格。
不止聚合的工厂，对于领域模型中的实体与值对象（包括ID类），都可以考虑定义这样具有业务含义或提供自然接口的静态工厂方法，使得创建逻辑变得更加合理而贴切。

4）**消息契约模型或装配器担任工厂**

如果远程服务或应用服务接收到的消息是用于创建的命令请求，则消息契约与领域模型之间的转换操作，实则是聚合的工厂方法。

5）**使用建造者组装聚合**

当需要多个参数进行组合创建，需要定义各种接收不同参数的方法响应各种组合方式时，可以使用建造者组装聚合。
建造者的构造方法可以对参数施加约束条件，避免非法值传入。定义构建方法时，要结合自然语音风格与领域逻辑为方法命名。

建造者模式有两种实现风格。
- 一种风格是单独定义Builder类，由它对外提供组合构建聚合对象的API。
例如：
```java
public class Flight extends Entity<FlightId> implements AggregateRoot<Flight> {
    private String flightNo;
    private Carrier carrier;
    private AirPort departureAirport;
    private Airport arrivalAirport;
    private Gate boardingGate;
    private localDate flightDate;
    
    private Flight(String flightNo) {
        this.flightNo = flightNo;
    }
    
    public static class Builder {
        // required fields
        private final String flightNo;
        
        // optional fields
        private Carrier carrier;
        private Airport departureAirport;
        private Airport arrivalAirport;
        private Gate boardingGate;
        private LocalDate flightDate;
        
        public Builder(String flightNo) {
            this.flightNo = flightNo;
        }
        
        public Builder beCarriedBy(String airlineCode){
            carrier = new Carrier(airlineCode);
            return this;
        }
        
        public Builder departFrom(String airportCode) {
            departureAirport = new Airport(airportCode);
            return this;
        }
        
        public Builder arriveAt(String airportCode) {
            arrivalAirport = new Airport(airportCode);
            return this;
        }
        
        public Builder boardingOn(String gateNo){
            boardingGate = new Gate(gateNo);
            return this;
        }
        
        public Builder flyingIn(LocalDate flyingInDate) {
            flightDate = flyingInDate;
            return this;
        }
        
        public Flight build() {
            return new Flight(this);
        }
    }
    
    private Flight(Builder builder) {
        flightNo = builder.flightNo;
        carrier = builder.carrier;
        departureAirport = builder.departureAirport;
        arrivalAirport = builder.arrivalAirport;
        boardingGate = builder.boardingGate;
        flightDate = builder.flightDate;
    }
}
```
客户端可以使用如下的流畅接口创建Flight聚合：
```java
Flight flight = Flight.PrepareBuilder("CA4116")
                      .beCarrieBy("CA")
                      .departFrom("PEK")
                      .arriveAt("CTU")
                      .boardingOn("C29")
                      .flyingIn(LocalDate.of(2019, 8, 8))
                      .build();
```

- 另一种风格是由被构建的聚合对象担任近乎Builder的角色，然后将可选的构造参数定义到每个单独的构建方法中，并返回聚合对象自身以形成流畅接口。
```java
public class Flight extends Entity<FlightId> implements AggregateRoot<Flight> {
    
    private String flightNo;
    private Carrier carrier;
    private AirportCode departureAirport;
    private AirportCode arrivalAirport;
    private Gate boardingGate;
    private LocalDate flightDate;
    
    // 聚合必备的字段要在构造函数的参数中给出
    private Flight(String flightNo) {
        this.flightNo = flightNo;
    }

    public Builder beCarriedBy(String airlineCode){
        carrier = new Carrier(airlineCode);
        return this;
    }

    public Builder departFrom(String airportCode) {
        departureAirport = new Airport(airportCode);
        return this;
    }

    public Builder arriveAt(String airportCode) {
        arrivalAirport = new Airport(airportCode);
        return this;
    }

    public Builder boardingOn(String gateNo){
        boardingGate = new Gate(gateNo);
        return this;
    }

    public Builder flyingIn(LocalDate flyingInDate) {
        flightDate = flyingInDate;
        return this;
    }
}
```
相较于第一种风格，它的构建方式更为流畅。从调用者角度看，它没有显式的建造者类，也没有强制要求在最后调用Builder()方法。
```java
Flight flight = Flight.withFlightNo("CA4116")
                      .beCarrieBy("CA")
                      .departFrom("PEK")
                      .arriveAt("CTU")
                      .boardingOn("C29")
                      .flyingIn(LocalDate.of(2019, 8, 8));
```
