### 实体
实体应该包含如下三个要素

1、身份标识
是实体对象的必要标识，用于区分不同的实体对象。

    1）通用类型（雪花算法id、数据库自增id等）
    2）领域类型（代表了业务含义的字段，如pin）

2、属性

用来说明主体的静态特征，并持有数据与状态。

    1）原子属性
    2）组合属性

```java
public class Product extends BaseEntity {
    /**
     * 名称
     */
    private String name;
    /**
     * 数量
     */
    private Integer quantity;
    /**
     * 类别
     */
    private Category category;
    /**
     * 重量
     */
    private Weight weight;
    /**
     * 体积
     */
    private Volume volume;
    /**
     * 价格
     */
    private Price price;
}
```
Product实体的name、quantity属性属于原子属性，category、weight属于组合属性。

如何判断应该建立原子属性还是组合属性？
该属性是否存在约束规则、组合因子和领域行为。

约束规则：
以产品类别（category）属性为例，对比产品名称，产品名称可能只有长度、特殊字符等限制。
而产品类别可能有类别是否存在、层级是否正确等约束条件，无法通过注解来校验。

组合因子：
判断属性是否不可再分，如重量与体积属性有着明显的特征： 需要值与计算单位共同组合。
如果只有值而无单位，就会因为单位不同导致计算错误、概念混乱。

领域行为：
例如Product实体的价格（price）属性需要提供针对该领域概念的运算行为，若不定义为Price组合属性，就无法封装这些领域行为。

3、领域行为

    1）变更状态的领域行为
    2）自给自足的领域行为
    3）互为协作的领域行为

变更状态的领域行为：
实体的状态由属性持有。变更状态领域行为即改变属性值的领域行为。与值对象不同，实体对象允许调用者更改其状态。
许多语言都支持通过get和set访问器访问对象属性（状态），这实际上是**技术因素干扰着领域模型的设计**。
如果不考虑一些框架对实体get/set访问器的限制，应该让变更状态的方法名满足业务含义。
```java
public class Product extends BaseEntity {
    /**
     * 价格
     */
    private Price price;
    
    public void changePriceTo(Price newPrice) {
        if (!this.price.sameCurrency(newPrice)) {
            throw new CurrencyException("Cannot change the price of this product to a different currency");
        }
        
        this.price = newPrice;
    }
}
```

自给自足的领域行为：
自给自足意味着实体对象只操作了自己的属性，不外求于别的对象。主要是对实体已有的属性值包括调用该实体组合属性定义的方法返回的值进行计算或判断，返回调用者希望获得的结果。
不同于变更状态的领域行为，自给自足的领域行为不会改变状态而产生副作用。
```java
public class Rights extends BaseEntity{
    private Long beginTimeStamp;
    private Long endTimeStamp;
    
    public boolean isValid(){
        return System.System.currentTimeMillis() > beginTimeStamp &&
                System.currentTimeMillis() <= endTimeStamp;
    }
}
```

互为协作的领域行为




