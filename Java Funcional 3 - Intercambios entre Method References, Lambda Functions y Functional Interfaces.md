```java
import java.util.function.*;

public class Main {
    public static void main(String[] args) throws Exception {
        Main main = new Main();

        //1. Functional interfaces predefinidas en Java - Method references
        BiFunction<Integer,Integer,String> biFunction1 = MyStaticFunctionClass::addition;
        Function<Integer,String> function1 = MyStaticFunctionClass::addition;

        BiFunction<Integer,Integer,String> biFunction2 = (new MyInstanceFunctionClass())::addition;
        Function<Integer,String> function2 = (new MyInstanceFunctionClass())::addition;

        main.testFunctions(biFunction1, function1, biFunction2, function2);

        //2. Functional interfaces predefinidas en Java - Lambda expressions
        BiFunction<Integer,Integer,String> biFunction3 = (a, b) -> "lambda function - " + MyStaticFunctionClass.addition(a, b);
        Function<Integer,String> function3 = (a) -> "lambda function - " + MyStaticFunctionClass.addition(a);

        BiFunction<Integer,Integer,String> biFunction4 = (a, b) -> "lambda function - " + (new MyInstanceFunctionClass()).addition(a, b);
        Function<Integer,String> function4 = (a) -> "lambda function - " + (new MyInstanceFunctionClass()).addition(a);

        main.testFunctions(biFunction3, function3, biFunction4, function4);

        //3. Functional interfaces creadas ad-hoc - Method references
        MyBiFunctionInterface<Integer,Integer,String> biFunction5 = MyStaticFunctionClass::addition;
        MyFunctionInterface<Integer,String> function5 = MyStaticFunctionClass::addition;

        MyBiFunctionInterface<Integer,Integer,String> biFunction6 = (new MyInstanceFunctionClass())::addition;
        MyFunctionInterface<Integer,String> function6 = (new MyInstanceFunctionClass())::addition;

        main.testFunctionalInterfaces(biFunction5, function5, biFunction6, function6);

        //4. Functional interfaces creadas ad-hoc - Lambda expressions
        MyBiFunctionInterface<Integer,Integer,String> biFunction7 = (a, b) -> "lambda functional interface - " + MyStaticFunctionClass.addition(a, b);
        MyFunctionInterface<Integer,String> function7 = (a) -> "lambda functional interface - " + MyStaticFunctionClass.addition(a);

        MyBiFunctionInterface<Integer,Integer,String> biFunction8 = (a, b) -> "lambda functional interface - " + (new MyInstanceFunctionClass()).addition(a, b);
        MyFunctionInterface<Integer,String> function8 = (a) -> "lambda functional interface - " + (new MyInstanceFunctionClass()).addition(a);

        main.testFunctionalInterfaces(biFunction7, function7, biFunction8, function8);

        //5. Constructor references
        PersonFactoryWithoutArguments<Person> personFactoryWithoutArguments = Person::new;
        PersonFactoryWithArguments<Person> personFactoryWithArguments = Person::new;

        Person person1 = personFactoryWithoutArguments.create();
        System.out.println(person1.getFirstName() + " " + person1.getLastName());

        Person person2 = personFactoryWithArguments.create("Bruce", "Wayne");
        System.out.println(person2.getFirstName() + " " + person2.getLastName());
    }

    public void testFunctions(BiFunction<Integer,Integer,String> biFunction1, Function<Integer,String> function1, BiFunction<Integer,Integer,String> biFunction2, Function<Integer,String> function2) {
        Integer a = 1;
        Integer b = 2;

        String result1 = this.useBiFunctionMethod(biFunction1, a, b);
        System.out.println(result1);

        String result2 = this.useFunctionMethod(function1, a);
        System.out.println(result2);

        String result3 = this.useBiFunctionMethod(biFunction2, a, b);
        System.out.println(result3);

        String result4 = this.useFunctionMethod(function2, a);
        System.out.println(result4);

        System.out.println();
    }

    public void testFunctionalInterfaces(MyBiFunctionInterface<Integer,Integer,String> biFunction1, MyFunctionInterface<Integer,String> function1, MyBiFunctionInterface<Integer,Integer,String> biFunction2, MyFunctionInterface<Integer,String> function2) {
        Integer a = 1;
        Integer b = 2;

        String result1 = this.useBiFunctionInterfaceMethod(biFunction1, a, b);
        System.out.println(result1);

        String result2 = this.useFunctionInterfaceMethod(function1, a);
        System.out.println(result2);

        String result3 = this.useBiFunctionInterfaceMethod(biFunction2, a, b);
        System.out.println(result3);

        String result4 = this.useFunctionInterfaceMethod(function2, a);
        System.out.println(result4);

        System.out.println();
    }

    public <T,U,R> R useBiFunctionMethod(BiFunction<T,U,R> biFunction, T param1, U param2) {
        R result = biFunction.apply(param1, param2);
        return result;
    }

    public <T,R> R useFunctionMethod(Function<T,R> function, T param) {
        R result = function.apply(param);
        return result;
    }

    public <T,U,R> R useBiFunctionInterfaceMethod(MyBiFunctionInterface<T,U,R> biFunction, T param1, U param2) {
        R result = biFunction.addition(param1, param2);
        return result;
    }

    public <T,R> R useFunctionInterfaceMethod(MyFunctionInterface<T,R> function, T param) {
        R result = function.addition(param);
        return result;
    }
}

class MyStaticFunctionClass {
    private static Integer value = 0;

    public static String addition(Integer i1, Integer i2) {
        value = (i1 + i2);
        return "static bifunction addition: " + value.toString();
    }

    public static String addition(Integer i) {
        value = (value + i);
        return "static function addition: " + value.toString();
    }
}

class MyInstanceFunctionClass {
    private Integer value = 0;

    public String addition(Integer i1, Integer i2) {
        value = (i1 + i2);
        return "instance bifunction addition: " + value.toString();
    }

    public String addition(Integer i) {
        value = (value + i);
        return "instance function addition: " + value.toString();
    }
}

@FunctionalInterface
interface MyBiFunctionInterface<T,U,R> {
    public abstract R addition(T i1, U i2);
}

@FunctionalInterface
interface MyFunctionInterface<T,R> {
    public abstract R addition(T i);
}

class Person {
    String firstName;
    String lastName;

    Person() {}

    Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public String getFirstName() {
        return this.firstName;
    }

    public String getLastName() {
        return this.lastName;
    }
}

@FunctionalInterface
interface PersonFactoryWithoutArguments<P extends Person> {
    P create();
}

@FunctionalInterface
interface PersonFactoryWithArguments<P extends Person> {
    P create(String firstName, String lastName);
}
```

```txt
static bifunction addition: 3
static function addition: 4
instance bifunction addition: 3
instance function addition: 1

lambda function - static bifunction addition: 3
lambda function - static function addition: 4
lambda function - instance bifunction addition: 3
lambda function - instance function addition: 1

static bifunction addition: 3
static function addition: 4
instance bifunction addition: 3
instance function addition: 1

lambda functional interface - static bifunction addition: 3
lambda functional interface - static function addition: 4
lambda functional interface - instance bifunction addition: 3
lambda functional interface - instance function addition: 1

null null
Bruce Wayne
```