Los siguientes elementos son intercambiables:
- interfaces funcionales predefinidas en Java
- interfaces funcionales creadas ad-hoc
- funciones lambda
- referencias a métodos

No se consideran interfaces funcionales (y, por tanto, no son intercambiables con todo lo anterior):
- clases abstractas con un único método abstract predefinidas en Java
- clases abstractas con un único método abstract creadas ad-hoc

Tipos de referencias a métodos:

| Tipo referencia                                   | Lambda equivalente                      | Ejemplo referencia y interfaz funcional                                | Ejemplo lambda                          |
|---------------------------------------------------|-----------------------------------------|------------------------------------------------------------------------|-----------------------------------------|
| Ref a método estático: ClassName::staticMethod    | (args) -> ClassName.staticMethod(args)  | `Supplier<Double> classSupplier = Math::random;`                       | () -> Math.random()                     |
| Ref a método de instancia: object::instanceMethod | (args) -> object.instanceMethod(args)   | `Function<String,String> instanceFunction = s1::concat;`               | (String s2) -> s1.concat(s2)            |
| Ref a constructor: ClassName::new                 | (args) -> new ClassName(args)           | `Function<String, Integer> classFunction1 = Integer::new;`             | (String s) -> new Integer(s)            |
| Ref a método de clase: ClassName::instanceMethod  | (obj, args) -> obj.instanceMethod(args) | `BiFunction<String, String, String> classBiFunction = String::concat;` | (String s1, String s2) -> s1.concat(s2) |

Ejemplo de referencias a métodos de tipo Consumer, Predicate, Supplier y Function:

```java
import java.util.*;
import java.util.function.*;
import java.io.*;

public class Main {
    public static void main(String[] args) throws Exception {
        Main main = new Main();
        main.process();
    }

    public void process() throws Exception {
        //Consumer sobre método de instancia
        PrintStream referenceObject1 = System.out;
        String referenceMethodArgument1_1 = "aaa ";

        Consumer<String> instanceConsumer1_1 = referenceObject1::print;
        Consumer<String> instanceConsumer1_2 = referenceObject1::println;
        Consumer<String> instanceConsumer1_3 = instanceConsumer1_1.andThen(instanceConsumer1_2);

        instanceConsumer1_3.accept(referenceMethodArgument1_1);
        System.out.println();

        //----------------------------------------------------------------------------------------

        //Predicate sobre método de instancia
        String referenceObject2 = "abcd";
        String referenceMethodArgument2_1 = "ab";
        String referenceMethodArgument2_2 = "cd";
        String referenceMethodArgument2_3 = "ba";

        Predicate<String> instancePredicate2_1 = referenceObject2::startsWith;
        Predicate<String> instancePredicate2_2 = referenceObject2::endsWith;
        Predicate<String> instancePredicate2_3 = instancePredicate2_1.or(instancePredicate2_2);

        System.out.println(instancePredicate2_3.test(referenceMethodArgument2_1));
        System.out.println(instancePredicate2_3.test(referenceMethodArgument2_2));
        System.out.println(instancePredicate2_3.test(referenceMethodArgument2_3));
        System.out.println();

        //----------------------------------------------------------------------------------------

        //Supplier sobre método de instancia
        Integer referenceObject3 = 1;

        Supplier<String> instanceSupplier3_1 = referenceObject3::toString;

        System.out.println(instanceSupplier3_1.get());
        System.out.println();

        //----------------------------------------------------------------------------------------

        //Consumer sobre método static
        String[] referenceMethodArgument4_1 = new String[]{"ccc","aaa","bbb"};

        Consumer<String[]> classConsumer4_1 = Arrays::sort;

        classConsumer4_1.accept(referenceMethodArgument4_1);
        System.out.println(referenceMethodArgument4_1[0]);
        System.out.println(referenceMethodArgument4_1[1]);
        System.out.println(referenceMethodArgument4_1[2]);
        System.out.println();

        //----------------------------------------------------------------------------------------

        //Predicate sobre método static
        String referenceMethodArgument5_1 = null;
        String referenceMethodArgument5_2 = "ab";

        Predicate<String> classPredicate5_1 = Objects::isNull;

        System.out.println(classPredicate5_1.test(referenceMethodArgument5_1));
        System.out.println(classPredicate5_1.test(referenceMethodArgument5_2));
        System.out.println();

        //----------------------------------------------------------------------------------------

        //Supplier sobre método static
        Supplier<Double> classSupplier6_1 = Math::random;

        System.out.println(classSupplier6_1.get());
        System.out.println();

        //----------------------------------------------------------------------------------------

        //Function sobre método static
        String referenceMethodArgument7_1 = "5";

        Function<String, Integer> classFunction7_1 = Integer::new;
        Function<Integer, Double> classFunction7_2 = Double::new;
        Function<String, Double> classFunction7_3 = classFunction7_1.andThen(classFunction7_2);

        System.out.println(classFunction7_3.apply(referenceMethodArgument7_1));
        System.out.println();

        //----------------------------------------------------------------------------------------

        //Convertir Function a Consumer
        MutableString referenceObject8 = new MutableString("ddd");
        String referenceMethodArgument8_1 = "eee";

        Consumer<String> classConsumer8_1 = referenceObject8::concatFunction;

        classConsumer8_1.accept(referenceMethodArgument8_1);
        System.out.println(referenceObject8.getValue());
        System.out.println();
    }

    class MutableString {
        private String value;

        public MutableString(String value) {
            this.value = value;
        }

        private String concatFunction(String concatArgument) {
            value = value + " " + concatArgument;
            return value;
        }

        private String getValue() {
            return value;
        }
    }
}
```

```txt
aaa aaa 

true
true
false

1

aaa
bbb
ccc

true
false

0.844389664993695

5.0

ddd eee

```