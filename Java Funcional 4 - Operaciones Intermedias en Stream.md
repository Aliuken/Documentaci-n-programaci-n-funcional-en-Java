Existen los siguientes métodos intermedios en Stream:

| Clase/Interfaz | Método                                                                                    |
|----------------|-------------------------------------------------------------------------------------------|
| `Stream<T>`    | `public Stream<T> filter(Predicate<? super T> predicate)`                                 |
| `Stream<T>`    | `public <R> Stream<R> map(Function<? super T,? extends R> mapper)`                        |
| `Stream<T>`    | `public IntStream mapToInt(ToIntFunction<? super T> mapper)`                              |
| `Stream<T>`    | `public LongStream mapToLong(ToLongFunction<? super T> mapper)`                           |
| `Stream<T>`    | `public DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper)`                     |
| `Stream<T>`    | `public <R> Stream<R> flatMap(Function<? super T,? extends Stream<? extends R>> mapper)`  |
| `Stream<T>`    | `public IntStream flatMapToInt(Function<? super T,? extends IntStream> mapper)`           |
| `Stream<T>`    | `public LongStream flatMapToLong(Function<? super T,? extends LongStream> mapper)`        |
| `Stream<T>`    | `public DoubleStream flatMapToDouble(Function<? super T,? extends DoubleStream> mapper)`  |
| `Stream<T>`    | `Stream<T> sorted()`                                                                      |
| `Stream<T>`    | `Stream<T> sorted(Comparator<? super T> comparator)`                                      |

A mayores existen estos otros métodos de ordenación en otras clases de Java:

| Clase/Interfaz | Método                                                                                    |
|----------------|-------------------------------------------------------------------------------------------|
| `Arrays`       | `public static void sort(primitive[] a)`                                                  |
| `Arrays`       | `public static void sort(primitive[] a, int fromIndex, int toIndex)`                      |
| `Arrays`       | `public static void sort(Object[] a)`                                                     |
| `Arrays`       | `public static void sort(Object[] a, int fromIndex, int toIndex)`                         |
| `Arrays`       | `public static <T> void sort(T[] a, Comparator<? super T> c)`                             |
| `Arrays`       | `public static <T> void sort(T[] a, int fromIndex, int toIndex, Comparator<? super T> c)` |
| `List<E>`      | `default void sort(Comparator<? super E> c)`                                              |

La clase `Arrays` contiene a mayores métodos equivalentes `parallelSort`.

Ejemplo de uso de filter, map, flatMap y sorted en Stream:

```java
import java.util.*;
import java.util.function.*;
import java.util.stream.*;

public class Main {
    List<Integer> listParam = Arrays.asList(3, null, 1, 2);

    Predicate<Integer> predicate1 = (Integer e) -> true;
    Predicate<Integer> predicate2 = (Integer e) -> e != null;

    Function<Integer, Stream<Stream<String>>> function1 = (Integer integerParam) -> {
        String stringParam = (integerParam != null) ? integerParam.toString() : null;
        Stream<Stream<String>> result = Stream.of(Stream.of(stringParam, "a"), Stream.of("b"));
        return result;
    };

    Function<Integer, String> function2 = (Integer integerParam) -> {
        String result = (integerParam != null) ? integerParam.toString() : null;
        return result;
    };

    Comparator<Integer> comparator = (Integer integer1, Integer integer2) -> {
        if(integer1 == null) {
            if(integer2 == null) {
                return 0;
            } else {
                return -1;
            }
        } else if(integer2 == null) {
            return 1;
        } else {
            return integer1.compareTo(integer2);
        }
    };

    public static void main(String[] args) {
        Main main = new Main();
        main.process();
    }

    public void process() {
        this.processStream(true, true, true);
        this.processStream(false, true, true);
        this.processStream(true, false, true);
        this.processStream(false, false, true);
        this.processStream(true, true, false);
        this.processStream(false, true, false);
        this.processStream(true, false, false);
        this.processStream(false, false, false);
    }

    private void processStream(boolean isPredicate1, boolean isMap, boolean isFunction1) {
        Predicate<Integer> filterPredicate;
        if(isPredicate1) {
            System.out.println("- Filter: ALL");
            filterPredicate = predicate1;
        } else {
            System.out.println("- Filter: NOT NULL");
            filterPredicate = predicate2;
        }

        if(isMap) {
            System.out.println("- Map Type: MAP");
            if(isFunction1) {
                System.out.println("- Function: FUNCTION1");

                List<Stream<Stream<String>>> streamResult = listParam.stream()
                        .filter(filterPredicate)
                        .sorted(comparator)
                        .map(function1)
                        .collect(Collectors.toList());

                this.printResultListFunction1Map(streamResult);
                System.out.println();
            } else {
                System.out.println("- Function: FUNCTION2");

                List<String> streamResult = listParam.stream()
                        .filter(filterPredicate)
                        .sorted(comparator)
                        .map(function2)
                        .collect(Collectors.toList());

                System.out.println("result: " +streamResult);
                System.out.println();
            }
        } else {
            System.out.println("- Map Type: FLATMAP");
            if(isFunction1) {
                System.out.println("- Function: FUNCTION1");

                List<Stream<String>> streamResult = listParam.stream()
                        .filter(filterPredicate)
                        .sorted(comparator)
                        .flatMap(function1)
                        .collect( Collectors.toList());

                this.printResultListFunction1FlatMap(streamResult);
                System.out.println();
            } else {
                System.out.println("- Function: FUNCTION2");
                System.out.println("result: No compila porque FUNCTION2 no devuelve un Stream");
                System.out.println();
            }
        }
    }

    private void printResultListFunction1Map(List<Stream<Stream<String>>> list) {
        int elementCollectedIndex = 0;
        for(Stream<Stream<String>> element : list) {
            List<Stream<String>> elementCollected = element
                    .collect(Collectors.toList());
            printResultElementFunction1Map(elementCollected, elementCollectedIndex);
            elementCollectedIndex++;
        }
    }

    private void printResultListFunction1FlatMap(List<Stream<String>> list) {
        int elementCollectedIndex = 0;
        for(Stream<String> element : list) {
            List<String> elementCollected = element
                    .collect(Collectors.toList());
            printResultElementFunction1FlatMap(elementCollected, elementCollectedIndex);
            elementCollectedIndex++;
        }
    }

    private void printResultElementFunction1Map(List<Stream<String>> elementCollected, int elementCollectedIndex) {
        if(elementCollected != null && !elementCollected.isEmpty()) {
            String subElementValues = "";
            for (Stream<String> subElement : elementCollected) {
                List<String> subElementValue = subElement.collect(Collectors.toList());
                subElementValues = subElementValues.concat(subElementValue + ", ");
            }
            subElementValues = subElementValues.substring(0, subElementValues.length() - 2);
            System.out.println("result[" + elementCollectedIndex + "]: " + subElementValues);
        }
    }

    private void printResultElementFunction1FlatMap(List<String> elementCollected, int elementCollectedIndex) {
        if(elementCollected != null && !elementCollected.isEmpty()) {
            String subElementValues = "";
            for (String subElement : elementCollected) {
                subElementValues = subElementValues.concat(subElement + ", ");
            }
            subElementValues = subElementValues.substring(0, subElementValues.length() - 2);
            System.out.println("result[" + elementCollectedIndex + "]: " + elementCollected);
        }
    }
}
```

```txt
- Filter: ALL
- Map Type: MAP
- Function: FUNCTION1
result[0]: [null, a], [b]
result[1]: [1, a], [b]
result[2]: [2, a], [b]
result[3]: [3, a], [b]

- Filter: NOT NULL
- Map Type: MAP
- Function: FUNCTION1
result[0]: [1, a], [b]
result[1]: [2, a], [b]
result[2]: [3, a], [b]

- Filter: ALL
- Map Type: FLATMAP
- Function: FUNCTION1
result[0]: [null, a]
result[1]: [b]
result[2]: [1, a]
result[3]: [b]
result[4]: [2, a]
result[5]: [b]
result[6]: [3, a]
result[7]: [b]

- Filter: NOT NULL
- Map Type: FLATMAP
- Function: FUNCTION1
result[0]: [1, a]
result[1]: [b]
result[2]: [2, a]
result[3]: [b]
result[4]: [3, a]
result[5]: [b]

- Filter: ALL
- Map Type: MAP
- Function: FUNCTION2
result: [null, 1, 2, 3]

- Filter: NOT NULL
- Map Type: MAP
- Function: FUNCTION2
result: [1, 2, 3]

- Filter: ALL
- Map Type: FLATMAP
- Function: FUNCTION2
result: No compila porque FUNCTION2 no devuelve un Stream

- Filter: NOT NULL
- Map Type: FLATMAP
- Function: FUNCTION2
result: No compila porque FUNCTION2 no devuelve un Stream

```
