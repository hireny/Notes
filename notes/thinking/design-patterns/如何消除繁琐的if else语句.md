# 如何消除繁琐的if else语句

## 一、简介

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


`if...else`语句实现的计算器的加减乘除与取模功能：
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

`switch`语句实现的加减乘除与取模功能：
```java
public int calculate(int a, int b, String operator) {
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

## 二、工厂 + 策略模式

重构
使用工 模式+策略模式来消除 `if/else` 语句

抽象层

```java
public interface Operation {
    int apply(int a, int b);
}
```

加法的实现类：
```java
public class Addition implements Operation {
    @Override
    public int apply(int a, int b) {
        return a + b;
    }
}
```

减法的实现类：
```java
public class Subtraction implements Operation {
        @Override public int apply(int a, int b) {
                return a - b;
        }
}
```

乘法的实现类：
```java
public class Multiplication implements Operation {
        @Override public int apply(int a, int b) {
                return a*b;
        }
}
```

除法的实现类：
```java
public class Division implements Operation {
        @Override public int apply(int a, int b) {
                return a / b;
        }
}
```

求余的实现类：
```java
public class Modulo implements Operation {
        @Override public int apply(int a, int b) {
                return a % b;
        }
}
```

封装策略模式的方法：
```java
public int calculate(int a, int b, String operator) {
	Operation operation = null;
    if ("add".equals(operator)) {
		operation = new Addition();
	} else if ("multiply".equals(operator)) {
		operation = new Multiplication();
	} else if ("divide".equals(operator)) {
		operation = new Division();
	} else if ("subtract".equals(operator)) {
		operation = new Subtraction();
	} else if ("modulo".equals(operator)) {
		operation = new Modulo();
	}
	return operation.apply(a, b);
}
```

使用示例：

```java
public class Test {
    public static void main(String[] args) {
        Calculates calculates = new Calculates();
        // 使用 策略模式 实现的简单的计算器功能
        System.out.println("使用 策略模式 实现的简单的计算器功能：" );
        System.out.println("1 + 24 = " + calculates.calculateUsingStrategys(1, 24, "add"));	// 24
        System.out.println("24 - 1 = " + calculates.calculateUsingStrategys(24, 1, "subtract"));	// 23
        System.out.println("2 * 23 = " + calculates.calculateUsingStrategys(2, 23, "multiply"));	// 46
        System.out.println("23 / 2 = " + calculates.calculateUsingStrategys(23, 2, "divide"));		// 11
        System.out.println("23 % 2 = " + calculates.calculateUsingStrategys(23, 2, "modulo"));		// 1
    }
}
```

在封装策略模式的方法中也没有消除`if/else`语句，只是将加减乘除的方法封装在策略模式中了，因此，我们要使用工厂模式将`if/else`语句消除，以简化语句

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

public class OperatorFactory {

    static Map<String, Operation> operationMap = new HashMap<>();
    static {
    	// 将具体的策略对象放入一个Map中，
        operationMap.put("add", new Addition());
        operationMap.put("divide", new Division());
        operationMap.put("multiply", new Multiplication());
        operationMap.put("subtract", new Subtraction());
        operationMap.put("modulo", new Modulo());
    }

    public static Optional<Operation> getOperation(String operation) {
    	// 根据传进来的字符串从Map中查找对应的策略
        return Optional.ofNullable(operationMap.get(operation));
    }
}
```

封装工厂模式的方法：
```java
public int calculateUsingFactory(int a, int b, String operator) {
	// 获取到目标策略
    Operation targetOperation = OperatorFactory
      .getOperation(operator)
      .orElseThrow(() -> new IllegalArgumentException("Invalid Operator"));
    // 调用目标策略具体的实现
    return targetOperation.apply(a, b);
}
```

使用示例：

```java
public class Test {
    public static void main(String[] args) {
        Calculates calculates = new Calculates();
        // 使用 工厂 + 策略模式 实现的简单的计算器功能
        System.out.println("使用 工厂 + 策略模式 实现的简单的计算器功能：" );
        System.out.println("1 + 24 = " + calculates.calculateUsingFactory(1, 24, "add"));
        System.out.println("24 - 1 = " + calculates.calculateUsingFactory(24, 1, "subtract"));
        System.out.println("2 * 23 = " + calculates.calculateUsingFactory(2, 23, "multiply"));
        System.out.println("23 / 2 = " + calculates.calculateUsingFactory(23, 2, "divide"));
        System.out.println("23 % 2 = " + calculates.calculateUsingFactory(23, 2, "modulo"));
    }
}
```

上面这种优化方案有一个弊端，为了能够快速拿到对应的策略实现，需要map对象来保存策略，当添加一个新策略的时候，还需要手动添加到map中，容易被忽略。

## 三、使用枚举

枚举实现Operator.java

```java
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
```

通过枚举优化之后的代码封装如下：

```java
public int calculateUsingEnumeration(int a, int b, String operator) {
	Operator operatorEnum = Operator.valueOf(operator.toUpperCase());
    return operatorEnum.apply(a, b);
}
```

枚举类型消除 if/else 语句的使用示例：

```java
public class Test {
    public static void main(String[] args) {
        Calculates calculates = new Calculates();
        // 使用 枚举类型 实现的简单的计算器功能
        System.out.println("使用 枚举类型 实现的简单的计算器功能：" );
        System.out.println("1 + 24 = " + calculates.calculateUsingEnumerations(1, 24, "add"));
        System.out.println("24 - 1 = " + calculates.calculateUsingEnumerations(24, 1, "subtract"));
        System.out.println("2 * 23 = " + calculates.calculateUsingEnumerations(2, 23, "multiply"));
        System.out.println("23 / 2 = " + calculates.calculateUsingEnumerations(23, 2, "divide"));
        System.out.println("23 % 2 = " + calculates.calculateUsingEnumerations(23, 2, "modulo"));
    }
}
```