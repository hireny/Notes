# 如何消除繁琐的if else语句

if判断语句是很多编程语言的重要组成部分。但是，若我们最终编写了大量嵌套的if语句，这将使得我们的代码更加复杂和难以维护。

让我们看看能否使用别的方式来做呢。

设计模式是为了更好的代码重用性，可读性，可靠性，可维护性，它有六大原则

```
1)单一职责原则（Single Responsibility Principle，简称SRP）:该原则是针对类来说的，即一个类应该只负责一项职责.
　　　　　　2)开放--封闭原则（The Open-Closed Principle简称OCP）:是说软件实体（类、模块、函数等等）应该可以扩展，但是不可以修改。
　　　　　　3)依赖倒转原则（Dependence Inversion Principle :针对接口编程，不要对实现编程
　　　　　　4)里氏代换原则（Liskov Substitution Principle，简称LSP）:里氏代换原则，子类型必须能够替换掉他们的父类型
　　　　　　5)迪米特法则（Law of Demeter）:如果两个类不必彼此直接通信，那么这两个类就不应当发生直接的相互作用
　　　　　　6)合成/聚合复用原则（Composition/Aggregation Principle]，简称CARP）:尽量使用合成/聚合，尽量不使用类继承。合成聚合是“has  a”的关系，而继承是“is  a”的关系。
```

示例

if...else
```java
public int calculate(int a, int b, String operator) {
        int result = Integer.MIN_VALUE;

        if ("add".equals(operator)) {
            result = a + b;
        } else if ("multiply".equals(operator)) {
            result = a * b;
        } else if ("divide".equals(operator)) {
            result = a / b;
        } else if ("subtract".equals(operator)) {
            result = a - b;
        } else if ("modulo".equals(operator)) {
            result = a % b;
        }
        return result;
    }
```

case-switch
```java
public int calculateUsingSwitch(int a, int b, String operator) {
        int result = 0;
        switch (operator) {
            case "add":
                result = a + b;
                break;
            case "multiply":
                result = a * b;
                break;
            case "divide":
                result = a / b;
                break;
            case "subtract":
                result = a - b;
                break;
            case "modulo":
                result = a % b;
                break;
            default:
                result = Integer.MIN_VALUE;
        }
        return result;
    }
```

重构
工厂模式加策略模式
抽象层
```java
public interface Operation {
    int apply(int a, int b);
}
```

加法实现Addition.java
```java
public class Addition implements Operation {
    @Override
    public int apply(int a, int b) {
        return a + b;
    }
}
```

减法实现Subtraction.java
```java
public class Subtraction implements Operation {
        @Override public int apply(int a, int b) {
                return a - b;
        }
}
```

乘法实现Multiplication.java
```java
public class Multiplication implements Operation {
        @Override public int apply(int a, int b) {
                return a*b;
        }
}
```

除法实现Division.java
```java
public class Division implements Operation {
        @Override public int apply(int a, int b) {
                return a / b;
        }
}
```
求余实现Modulo.java
```java
public class Modulo implements Operation {
        @Override public int apply(int a, int b) {
                return a % b;
        }
}
```

工厂类OperatorFactory.java
```java
import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

public class OperatorFactory {

    static Map<String, Operation> operationMap = new HashMap<>();
    static {
        operationMap.put("add", new Addition());
        operationMap.put("divide", new Division());
        operationMap.put("multiply", new Multiplication());
        operationMap.put("subtract", new Subtraction());
        operationMap.put("modulo", new Modulo());
    }

    public static Optional<Operation> getOperation(String operation) {
        return Optional.ofNullable(operationMap.get(operation));
    }
}
```
使用示例

public int calculateUsingFactory(int a, int b, String operator) {
    Operation targetOperation = OperatorFactory
      .getOperation(operator)
      .orElseThrow(() -> new IllegalArgumentException("Invalid Operator"));
    return targetOperation.apply(a, b);
}
 

3.2 枚举方式重构

枚举实现Operator.java

复制代码
public enum Operator {

    ADD {
        @Override
        public int apply(int a, int b) {
            return a + b;
        }
    },

    MULTIPLY {
        @Override
        public int apply(int a, int b) {
            return a * b;
        }
    },

    SUBTRACT {
        @Override
        public int apply(int a, int b) {
            return a - b;
        }
    },

    DIVIDE {
        @Override
        public int apply(int a, int b) {
            return a / b;
        }
    },

    MODULO {
        @Override
        public int apply(int a, int b) {
            return a % b;
        }
    };

    public abstract int apply(int a, int b);
}
复制代码
封装Operator到Calculator.java

    public int calculate(int a, int b, Operator operator) {
        return operator.apply(a, b);
    }
使用示例

@Test
public void whenCalculateUsingEnumOperator_thenReturnCorrectResult() {
    Calculator calculator = new Calculator();
    int result = calculator.calculate(3, 4, Operator.valueOf("ADD"));
    assertEquals(7, result);
}
 

3.3 命令模式

抽象的接口

public interface Command {
    Integer execute();
}
实现类

复制代码
package com.baeldung.reducingIfElse;

public class AddCommand implements Command {

    private int a;
    private int b;

    public AddCommand(int a, int b) {
        this.a = a;
        this.b = b;
    }

    @Override
    public Integer execute() {
        return a + b;
    }
}
复制代码
其它略

包装

    public int calculate(Command command) {
        return command.execute();
    }
测试demo

@Test
public void whenCalculateUsingCommand_thenReturnCorrectResult() {
    Calculator calculator = new Calculator();
    int result = calculator.calculate(new AddCommand(3, 7));
    assertEquals(10, result);
}
 

4.规则引擎重构

抽象规则

public interface Rule {

    boolean evaluate(Expression expression);

    Result getResult();
}
实现规则AddRule.java 其它略

复制代码
public class AddRule implements Rule {

    private int result;

    @Override
    public boolean evaluate(Expression expression) {
        boolean evalResult = false;
        if (expression.getOperator() == Operator.ADD) {
            this.result = expression.getX() + expression.getY();
            evalResult = true;
        }
        return evalResult;
    }

    @Override
    public Result getResult() {
        return new Result(result);
    }
}
复制代码
 

其中：返回结果

复制代码
public class Result {
    int value;

    public Result(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
复制代码
表达式

复制代码
public class Expression {

        private Integer x;
        private Integer y;
        private Operator operator;

        public Expression(Integer x, Integer y, Operator operator) {
                this.x = x;
                this.y = y;
                this.operator = operator;
        }

        public Integer getX() {
                return x;
        }

        public Integer getY() {
                return y;
        }

        public Operator getOperator() {
                return operator;
        }
}
复制代码
规则引擎RuleEngine.java

复制代码
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.stream.Collectors;

public class RuleEngine {

    private static List<Rule> rules = new ArrayList<>();

    static {
        rules.add(new AddRule());
    }

    public Result process(Expression expression) {

        Rule rule = rules.stream()
            .filter(r -> r.evaluate(expression))
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("Expression does not matches any Rule"));
        return rule.getResult();
    }
}
复制代码
测试demo

复制代码
@Test
public void whenNumbersGivenToRuleEngine_thenReturnCorrectResult() {
    Expression expression = new Expression(5, 5, Operator.ADD);
    RuleEngine engine = new RuleEngine();
    Result result = engine.process(expression);
 
    assertNotNull(result);
    assertEquals(10, result.getValue());
}
