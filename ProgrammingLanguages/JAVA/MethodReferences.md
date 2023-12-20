# JAVA 的方法也可以被引用!

## :: 存在目的

`::` 方法引用操作符主要是為了簡化 Lambda 表達式的書寫而存在的。它提供了一種更簡潔、更清晰地表示使用現有方法的方式，並將其轉換為函數式介面的實例。這使得程式碼更易讀、更簡潔，特別是當你只需要使用現有方法而不需要在 Lambda 表達式中進行額外的操作時。



### 靜態方法引用

假設有一個簡單的類 `MathUtils`，其中包含一個靜態方法 `double square(double x)`：

```java
class MathUtils { 
    public static double square(double x) 
    { return x * x;     
    } 
}
```

現在，我們可以使用方法引用 `MathUtils::square`：

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
        Function<Double, Double> squareFunc = MathUtils::square;
        double result = squareFunc.apply(5.0);
        System.out.println("Square of 5.0: " + result);
    }
}
```

### 實例方法引用

假設有一個包含實例方法 `printMessage` 的類 `MessagePrinter`：

```java
class MessagePrinter {
    public void printMessage(String message) {
        System.out.println(message);
    }
}
```

現在，我們可以使用方法引用 `messagePrinterInstance::printMessage`：

```java
import java.util.function.Consumer;

public class Main {
    public static void main(String[] args) {
        MessagePrinter messagePrinterInstance = new MessagePrinter();

        Consumer<String> printMessageFunc = messagePrinterInstance::printMessage;
        printMessageFunc.accept("Hello, world!");
    }
}
```

### 建構函數引用

與前面不同，這次我們將使用建構函數引用創建字符串：

```java
import java.util.function.Supplier;

public class Main {
    public static void main(String[] args) {
        Supplier<String> stringSupplier = String::new;
        String emptyString = stringSupplier.get();
        System.out.println("Empty string: " + emptyString);
    }
}
```

這些例子展示了不同類型的方法引用以及它們的用法，你可以根據需要使用適合的方法引用類型。

## 比較方法?  GPT亂說!

```java
class MyClass {
    public void myMethod() {
        System.out.println("This is my method.");
    }
}

public class Main {
    public static void main(String[] args) {
        MyClass obj1 = new MyClass();
        MyClass obj2 = new MyClass();

        // 將方法引用賦值給變數
        Runnable methodRef1 = obj1::myMethod;
        Runnable methodRef2 = obj2::myMethod;

        // 檢查方法引用是否相等
        System.out.println(methodRef1 == methodRef2); 
        // 不能比較方法  ! 因為ref 無法指向方法

    }
}
```

### GPT

> 沒法取得特定物件的方法 只能取得類別的方法 這跟JS不同

對的，Java 的反射機制主要用於操作類別層面的資訊，因此在 Java 中，通常只能取得類別層面的方法，而不是特定物件實例的方法。這意味著你可以透過反射獲取類別中的方法，但無法直接取得特定物件實例的方法。

相對地，在 JavaScript 中，你可以直接獲取物件實例的方法，這允許你動態操作物件層面的資訊。這是 JavaScript 與 Java 反射機制在這方面的差異之一。
