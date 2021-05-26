Cuando no necesitamos un elemento del Stream de respuesta podemos usar los métodos:
- forEach para ejecutar un Consumer sobre una colección de elementos.
- allMatch, anyMatch y noneMatch para comprobar si todos, alguno o ningún elemento cumple con una condición
- removeIf para eliminar los elementos de una `Collection<E>` que cumplen con un predicado haciendo uso del método remove() de su `Iterator<E>`. Devuelve true si se borró algún elemento.

Existen los siguientes métodos que devuelven 0 elementos definidos en Stream:

| Interfaz        | Método                                                         |
|-----------------|----------------------------------------------------------------|
| `Stream<T>`     | `void forEach(Consumer<? super T> action)`                     |
| `Stream<T>`     | `void forEachOrdered(Consumer<? super T> action)`              |
| `Stream<T>`     | `boolean allMatch(Predicate<? super T> predicate)`             |
| `Stream<T>`     | `boolean anyMatch(Predicate<? super T> predicate)`             |
| `Stream<T>`     | `boolean noneMatch(Predicate<? super T> predicate)`            |

El método peek es una operación intermedia equivalente al método forEach. Se suele usar para debuguear pasos intermedios de un Stream.

| Interfaz        | Método                                                         |
|-----------------|----------------------------------------------------------------|
| `Stream<T>`     | `Stream<T> peek(Consumer<? super T> action)`                   |

A mayores existen los siguientes métodos forEach y removeIf definidos en otras clases de Java:

| Interfaz        | Método                                                         |
|-----------------|----------------------------------------------------------------|
| `Iterable<T>`   | `default void forEach(Consumer<? super T> action)`             |
| `Map<K,V>`      | `default void forEach(BiConsumer<? super K,? super V> action)` |
| `Collection<E>` | `default boolean removeIf(Predicate<? super E> filter)`        |

Ejemplo de uso de los métodos anteriores:

```java
package es.prueba;

import java.util.*;
import java.util.function.*;
import java.util.stream.*;

public class Main {
    public static void main(String[] args) throws Exception {
        System.out.println("1. forEach de Iterable de Integer");

        List<Integer> integerList = Arrays.asList(1,2,3,4,5);
        Consumer<Integer> integerConsumer = System.out::println;
        integerList.forEach(integerConsumer);
        System.out.println();

        System.out.println("2. forEach de Iterable de String");

        List<String> stringList = Arrays.asList("one","two","three","four");
        Consumer<String> stringConsumer = System.out::println;
        stringList.forEach(stringConsumer);
        System.out.println();

        System.out.println("3. forEach de Map");

        Map<String, String> map = new HashMap<String, String>();
        map.put("A", "Alex");
        map.put("B", "Brian");
        map.put("C", "Charles");
        map.forEach((k, v) -> System.out.println("Key = " + k + ", Value = " + v));
        System.out.println();

        System.out.println("4. peek de Stream sin una operación final");

        Stream<String> stringStream = Stream.of("one", "two", null, "three", "four")
                .peek(e -> System.out.println("Initial value: " + e))
                .filter(Objects::nonNull)
                .peek(e -> System.out.println("Filtered nonNull value: " + e))
                .filter(e -> e.length() > 3)
                .peek(e -> System.out.println("Filtered length>3 value: " + e))
                .map(String::toUpperCase)
                .peek(e -> System.out.println("Mapped value: " + e));
        System.out.println();

        System.out.println("5. peek y forEach de Stream");

        Stream.of("one", "two", null, "three", "four")
                .peek(e -> System.out.println("Initial value: " + e))
                .filter(Objects::nonNull)
                .peek(e -> System.out.println("Filtered nonNull value: " + e))
                .filter(e -> e.length() > 3)
                .peek(e -> System.out.println("Filtered length>3 value: " + e))
                .map(String::toUpperCase)
                .peek(e -> System.out.println("Mapped value: " + e))
                .forEach(e -> System.out.println("ForEach value: " + e));
        System.out.println();

        System.out.println("6. peek y forEachOrdered de Stream");

        Stream.of("one", "two", null, "three", "four")
                .peek(e -> System.out.println("Initial value: " + e))
                .filter(Objects::nonNull)
                .peek(e -> System.out.println("Filtered nonNull value: " + e))
                .filter(e -> e.length() > 3)
                .peek(e -> System.out.println("Filtered length>3 value: " + e))
                .map(String::toUpperCase)
                .peek(e -> System.out.println("Mapped value: " + e))
                .parallel()
                .peek(e -> System.out.println("Parallel value: " + e))
                .forEachOrdered(e -> System.out.println("ForEachOrdered value: " + e));
        System.out.println();

        System.out.println("7. allMatch, anyMatch y noneMatch de Stream");

        List<Integer> allNullList = new ArrayList<>(Arrays.asList(null,null,null,null,null));
        List<Integer> oneNullList = new ArrayList<>(Arrays.asList(1,2,null,4,5));
        List<Integer> noneNullList = new ArrayList<>(Arrays.asList(1,2,3,4,5));
        Predicate<Integer> isNullIntegerPredicate = x -> (x == null);
        System.out.println("allMatch sobre allNullList: " + allNullList.stream().allMatch(isNullIntegerPredicate));
        System.out.println("anyMatch sobre allNullList: " + allNullList.stream().anyMatch(isNullIntegerPredicate));
        System.out.println("noneMatch sobre allNullList: " + allNullList.stream().noneMatch(isNullIntegerPredicate));
        System.out.println();
        System.out.println("allMatch sobre oneNullList: " + oneNullList.stream().allMatch(isNullIntegerPredicate));
        System.out.println("anyMatch sobre oneNullList: " + oneNullList.stream().anyMatch(isNullIntegerPredicate));
        System.out.println("noneMatch sobre oneNullList: " + oneNullList.stream().noneMatch(isNullIntegerPredicate));
        System.out.println();
        System.out.println("allMatch sobre noneNullList: " + noneNullList.stream().allMatch(isNullIntegerPredicate));
        System.out.println("anyMatch sobre noneNullList: " + noneNullList.stream().anyMatch(isNullIntegerPredicate));
        System.out.println("noneMatch sobre noneNullList: " + noneNullList.stream().noneMatch(isNullIntegerPredicate));
        System.out.println();

        System.out.println("8. removeIf de Collection de Integer");

        List<Integer> integerList2 = new ArrayList<>(Arrays.asList(1,2,3,4,5));
        Predicate<Integer> integerPredicate = x -> (x > 2 && x < 5);
        integerList2.removeIf(integerPredicate);
        Consumer<Integer> integerConsumer2 = System.out::println;
        integerList2.forEach(integerConsumer2);
        System.out.println();
    }
}
```

```txt
1. forEach de Iterable de Integer
1
2
3
4
5

2. forEach de Iterable de String
one
two
three
four

3. forEach de Map
Key = A, Value = Alex
Key = B, Value = Brian
Key = C, Value = Charles

4. peek de Stream sin una operación final

5. peek y forEach de Stream
Initial value: one
Filtered nonNull value: one
Initial value: two
Filtered nonNull value: two
Initial value: null
Initial value: three
Filtered nonNull value: three
Filtered length>3 value: three
Mapped value: THREE
ForEach value: THREE
Initial value: four
Filtered nonNull value: four
Filtered length>3 value: four
Mapped value: FOUR
ForEach value: FOUR

6. peek y forEachOrdered de Stream
Initial value: one
Filtered nonNull value: one
Initial value: two
Initial value: four
Initial value: null
Initial value: three
Filtered nonNull value: three
Filtered nonNull value: four
Filtered nonNull value: two
Filtered length>3 value: three
Filtered length>3 value: four
Mapped value: FOUR
Mapped value: THREE
Parallel value: FOUR
Parallel value: THREE
ForEachOrdered value: THREE
ForEachOrdered value: FOUR

7. allMatch, anyMatch y noneMatch de Stream
allMatch sobre allNullList: true
anyMatch sobre allNullList: true
noneMatch sobre allNullList: false

allMatch sobre oneNullList: false
anyMatch sobre oneNullList: true
noneMatch sobre oneNullList: false

allMatch sobre noneNullList: false
anyMatch sobre noneNullList: false
noneMatch sobre noneNullList: true

8. removeIf de Collection de Integer
1
2
5

```

Alternativas a removeIf de Collection:

```java
//Usando Iterator

public static <T> void remove(List<T> list, Predicate<T> predicate) {
    Iterator<T> itr = list.iterator();
    while (itr.hasNext()) {
        T t = itr.next();
        if(predicate.test(t)) {
            itr.remove();
        }
    }
}

//Usando filter y Collectors.toList() de Stream

public static <T> List<T> remove(List<T> list, Predicate<T> predicate) {
    return list.stream()
                .filter(x -> !predicate.test(x))
                .collect(Collectors.toList());
}

//Usando filter y Collectors.toCollection(...) de Stream

public static <T> List<T> remove(List<T> list, Predicate<T> predicate) {
    return list.stream()
                .filter(x -> !predicate.test(x))
                .collect(Collectors.toCollection(ArrayList::new));
}

```

Ejemplo de unión de Lists en una nueva List:

```java
private static <T> List<T> mergeLists(List<T>... lists){
    List<T> result = new ArrayList<>();
    Stream.of(lists).forEach(result::addAll);
    return result;
}
```