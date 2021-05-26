Existen los siguientes métodos finales en Optional:

| Clase         | Método                                                                                                           |
|---------------|------------------------------------------------------------------------------------------------------------------|
| `Optional<T>` | `public void ifPresent(Consumer<? super T> consumer)`                                                            |
| `Optional<T>` | `public T get()`                                                                                                 |
| `Optional<T>` | `public T orElse(T other)`                                                                                       |
| `Optional<T>` | `public T orElseGet(Supplier<? extends T> other)`                                                                |
| `Optional<T>` | `public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X extends Throwable` |

A mayores la clase Objects contiene métodos similares que pueden usarse como filtros en Streams o en argumentos y valores retornados de métodos:

| Clase     | Método                                                                             |
|-----------|------------------------------------------------------------------------------------|
| `Objects` | `public static boolean isNull(Object obj)`                                         |
| `Objects` | `public static boolean nonNull(Object obj)`                                        |
| `Objects` | `public static boolean equals(Object a, Object b)`                                 |
| `Objects` | `public static boolean deepEquals(Object a, Object b)`                             |
| `Objects` | `public static <T> T requireNonNull(T obj)`                                        |
| `Objects` | `public static <T> T requireNonNull(T obj, String message)`                        |
| `Objects` | `public static <T> T requireNonNull(T obj, Supplier<String> messageSupplier)`      |
| `Objects` | `public static <T> T requireNonNullElse(T obj, T defaultObj)`                      |
| `Objects` | `public static <T> T requireNonNullElseGet(T obj, Supplier<? extends T> supplier)` |

No existe una clase "Strings" equivalente a Objects. No obstante, hay una implementación de dicha clase (basada en la clase Objects de Java 16) en: https://github.com/Aliuken/Java-Strings/blob/main/Strings.java

Ejemplo de uso de ifPresent, filter, map y flatMap en Optional:

```java
import java.util.*;
import java.util.function.*;

public class Main {
    public static void main(String[] args) throws Exception {
        Main main = new Main();
        main.process();
    }

    public void process() {
        Optional<Integer> param1 = null;
        Integer param2 = new Integer(111);
        Optional<String> param3 = Optional.of("aaa");
        Optional<Integer> param4 = Optional.empty();
        Optional<Integer> param5 = Optional.of(new Integer(111));

        System.out.println("param1: " + param1);
        System.out.println("param2: " + param2);
        System.out.println("param3: " + param3);
        System.out.println("param4: " + param4);
        System.out.println("param5: " + param5);

        System.out.println();

        System.out.println("this::consumer <=> x -> ()");
        Consumer<Integer> consumer = this::consumer;
        testIfPresent(param1, consumer, "consumer");
        testIfPresent(param2, consumer, "consumer");
        testIfPresent(param3, consumer, "consumer");
        testIfPresent(param4, consumer, "consumer");
        testIfPresent(param5, consumer, "consumer");

        System.out.println();

        testGet(param1);
        testGet(param2);
        testGet(param3);
        testGet(param4);
        testGet(param5);

        System.out.println();

        System.out.println("this::supplierNull <=> () -> (T) null");
        Supplier<Integer> supplierNull = this::supplierNull;
        testOrElse(param1, supplierNull, "supplierNull");
        testOrElse(param2, supplierNull, "supplierNull");
        testOrElse(param3, supplierNull, "supplierNull");
        testOrElse(param4, supplierNull, "supplierNull");
        testOrElse(param5, supplierNull, "supplierNull");

        System.out.println();

        System.out.println("this::supplierNull <=> () -> (T) null");
        supplierNull = this::supplierNull;
        testOrElseGet(param1, supplierNull, "supplierNull");
        testOrElseGet(param2, supplierNull, "supplierNull");
        testOrElseGet(param3, supplierNull, "supplierNull");
        testOrElseGet(param4, supplierNull, "supplierNull");
        testOrElseGet(param5, supplierNull, "supplierNull");

        System.out.println();

        System.out.println("indexOutOfBoundsExceptionSupplier <=> IndexOutOfBoundsException::new");
        Supplier<IndexOutOfBoundsException> indexOutOfBoundsExceptionSupplier = IndexOutOfBoundsException::new;
        testOrElseThrow(param1, indexOutOfBoundsExceptionSupplier, "indexOutOfBoundsExceptionSupplier");
        testOrElseThrow(param2, indexOutOfBoundsExceptionSupplier, "indexOutOfBoundsExceptionSupplier");
        testOrElseThrow(param3, indexOutOfBoundsExceptionSupplier, "indexOutOfBoundsExceptionSupplier");
        testOrElseThrow(param4, indexOutOfBoundsExceptionSupplier, "indexOutOfBoundsExceptionSupplier");
        testOrElseThrow(param5, indexOutOfBoundsExceptionSupplier, "indexOutOfBoundsExceptionSupplier");

        System.out.println();
    }

    private void testIfPresent(Object param, Consumer<Integer> consumer, String consumerName) {
        if(param == null) {
            System.out.println("  * " + param + ".ifPresent(" + consumerName + ") <=> NullPointerException, param es null");
            return;
        }

        Optional<Integer> optionalParam;
        try {
            optionalParam = (Optional<Integer>) param;
            if(optionalParam.isPresent()) {
                Integer i = optionalParam.get();
            }
        } catch(ClassCastException e) {
            System.out.println("  * " + param + ".ifPresent(" + consumerName + ") <=> Error de compilación, param no es Optional<Integer>");
            return;
        }

        System.out.print("  * " + optionalParam + ".ifPresent(" + consumerName + ") <=> ");
        optionalParam.ifPresent(consumer);
        if(!optionalParam.isPresent()) {
            System.out.println();
        }
    }

    private void testGet(Object param) {
        if(param == null) {
            System.out.println("  * " + param + ".get() <=> NullPointerException, param es null");
            return;
        }

        Optional<Integer> optionalParam;
        try {
            optionalParam = (Optional<Integer>) param;
            if(optionalParam.isPresent()) {
                Integer i = optionalParam.get();
            }
        } catch(ClassCastException e) {
            System.out.println("  * " + param + ".get() <=> Error de compilación, param no es Optional<Integer>");
            return;
        }

        System.out.print("  * " + optionalParam + ".get() <=> ");
        try {
            Integer result = optionalParam.get();
            System.out.println(result);
        } catch(NoSuchElementException e) {
            System.out.println("NoSuchElementException");
        }
    }

    private void testOrElse(Object param, Supplier<Integer> supplier, String supplierName) {
        if(param == null) {
            System.out.println("  * " + param + ".orElse(" + supplierName +".get()) <=> NullPointerException, param es null");
            return;
        }

        Optional<Integer> optionalParam;
        try {
            optionalParam = (Optional<Integer>) param;
            if(optionalParam.isPresent()) {
                Integer i = optionalParam.get();
            }
        } catch(ClassCastException e) {
            System.out.println("  * " + param + ".orElse(" + supplierName +".get()) <=> Error de compilación, param no es Optional<Integer>");
            return;
        }

        Integer result = optionalParam.orElse(supplier.get());
        System.out.println("  * " + optionalParam + ".orElse(" + supplierName +".get()) <=> " + result);
    }

    private void testOrElseGet(Object param, Supplier<Integer> supplier, String supplierName) {
        if(param == null) {
            System.out.println("  * " + param + ".orElseGet(" + supplierName +") <=> NullPointerException, param es null");
            return;
        }

        Optional<Integer> optionalParam;
        try {
            optionalParam = (Optional<Integer>) param;
            if(optionalParam.isPresent()) {
                Integer i = optionalParam.get();
            }
        } catch(ClassCastException e) {
            System.out.println("  * " + param + ".orElseGet(" + supplierName +") <=> Error de compilación, param no es Optional<Integer>");
            return;
        }

        Integer result = optionalParam.orElseGet(supplier);
        System.out.println("  * " + optionalParam + ".orElseGet(" + supplierName +") <=> " + result);
    }

    private void testOrElseThrow(Object param, Supplier<IndexOutOfBoundsException> supplier, String supplierName) {
        if(param == null) {
            System.out.println("  * " + param + ".orElseThrow(" + supplierName +") <=> NullPointerException, param es null");
            return;
        }

        Optional<Integer> optionalParam;
        try {
            optionalParam = (Optional<Integer>) param;
            if(optionalParam.isPresent()) {
                Integer i = optionalParam.get();
            }
        } catch(ClassCastException e) {
            System.out.println("  * " + param + ".orElseThrow(" + supplierName +") <=> Error de compilación, param no es Optional<Integer>");
            return;
        }

        System.out.print("  * " + optionalParam + ".orElseThrow(" + supplierName +") <=> ");
        try {
            Integer result = optionalParam.orElseThrow(supplier);
            System.out.println(result);
        } catch(IndexOutOfBoundsException e) {
            System.out.println("IndexOutOfBoundsException");
        }
    }

    private void consumer(Integer param) {
        System.out.println("Consumer " + param);
    }

    private Integer supplierNull() {
        System.out.println("    Ejecutando supplierNull para operación siguiente");
        return null;
    }
}
```

```txt
param1: null
param2: 111
param3: Optional[aaa]
param4: Optional.empty
param5: Optional[111]

this::consumer <=> x -> ()
  * null.ifPresent(consumer) <=> NullPointerException, param es null
  * 111.ifPresent(consumer) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].ifPresent(consumer) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.ifPresent(consumer) <=> 
  * Optional[111].ifPresent(consumer) <=> Consumer 111

  * null.get() <=> NullPointerException, param es null
  * 111.get() <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].get() <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.get() <=> NoSuchElementException
  * Optional[111].get() <=> 111

this::supplierNull <=> () -> (T) null
  * null.orElse(supplierNull.get()) <=> NullPointerException, param es null
  * 111.orElse(supplierNull.get()) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].orElse(supplierNull.get()) <=> Error de compilación, param no es Optional<Integer>
    Ejecutando supplierNull para operación siguiente
  * Optional.empty.orElse(supplierNull.get()) <=> null
    Ejecutando supplierNull para operación siguiente
  * Optional[111].orElse(supplierNull.get()) <=> 111

this::supplierNull <=> () -> (T) null
  * null.orElseGet(supplierNull) <=> NullPointerException, param es null
  * 111.orElseGet(supplierNull) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].orElseGet(supplierNull) <=> Error de compilación, param no es Optional<Integer>
    Ejecutando supplierNull para operación siguiente
  * Optional.empty.orElseGet(supplierNull) <=> null
  * Optional[111].orElseGet(supplierNull) <=> 111

indexOutOfBoundsExceptionSupplier <=> IndexOutOfBoundsException::new
  * null.orElseThrow(indexOutOfBoundsExceptionSupplier) <=> NullPointerException, param es null
  * 111.orElseThrow(indexOutOfBoundsExceptionSupplier) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].orElseThrow(indexOutOfBoundsExceptionSupplier) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.orElseThrow(indexOutOfBoundsExceptionSupplier) <=> IndexOutOfBoundsException
  * Optional[111].orElseThrow(indexOutOfBoundsExceptionSupplier) <=> 111
```
