Las interfaces funcionales tienen que tener:
- La anotación @FunctionalInterface opcionalmente
- Un único método public abstract sin contar los métodos public de la clase Object
- Los métodos métodos public abstract definidos en la clase Object que se quieran (por ejemplo: equals, hashCode y toString)
- Los métodos public default no abstractos que se quieran
- Los métodos public static no abstractos que se quieran
- Los métodos private no abstractos que se quieran
- Los métodos private static no abstractos que se quieran

Las siguientes combinaciones de modificadores son incompatibles en interfaces:
- abstract y default
- (abstract o default) y static
- (abstract o default) y private

No se pueden implementar/sobreescribir (con @Override) métodos de interfaces con los modificadores:
- public static
- private
- private static

Ejemplo de interfaz funcional, interfaz hija e implementación:

```java
@FunctionalInterface
public interface BaseInterface {
    public abstract void publicAbstractMethod();

    public default void publicDefaultMethod() {
        System.out.println("publicDefaultMethod");
        this.privateMethod();
        BaseInterface.privateStaticMethod();
    }

    public static void publicStaticMethod() {
        System.out.println("publicStaticMethod");
        BaseInterface.privateStaticMethod();
    }

    private void privateMethod() {
        System.out.println("privateMethod");
        BaseInterface.privateStaticMethod();
    }

    private static void privateStaticMethod() {
        System.out.println("privateStaticMethod");
    }
}

public interface ChildInterface extends BaseInterface {
    @Override
    default void publicAbstractMethod() {
        System.out.println("Implements publicAbstractMethod 1");
        BaseInterface.publicStaticMethod();
    }

    @Override
    default void publicDefaultMethod() {
        System.out.println("Overrides publicDefaultMethod 1");
        BaseInterface.publicStaticMethod();
    }

    public default void anotherMethod() {
        this.publicAbstractMethod(); //No tiene por qué estar implementado para ser llamado
        this.publicDefaultMethod(); //No tiene por qué estar sobreescrito para ser llamado
        BaseInterface.publicStaticMethod();
    }
}

public class ChildImplementation implements ChildInterface {
    @Override
    public void publicAbstractMethod() {
        System.out.println("Overrides publicAbstractMethod 2");
        BaseInterface.publicStaticMethod();
    }

    @Override
    public void publicDefaultMethod() {
        System.out.println("Overrides publicDefaultMethod 2");
        BaseInterface.publicStaticMethod();
    }

    @Override
    public void anotherMethod() {
        System.out.println("Overrides anotherMethod 1");
        BaseInterface.publicStaticMethod();
    }
}
```

Existen las siguientes interfaces funcionales predefinidas en Java:

1) En el paquete java.util.function

| Tipo interfaz       | Método interfaz funcional   | Otros métodos disponibles                                                         |
|---------------------|-----------------------------|-----------------------------------------------------------------------------------|
| `Supplier<T>`       | `T get()`                   |                                                                                   |
| `Consumer<T>`       | `void accept(T t)`          | `default Consumer<T> andThen(Consumer<? super T> after)`                          |
| `BiConsumer<T,U>`   | `void accept(T t, U u)`     | `default BiConsumer<T,U> andThen(BiConsumer<? super T,? super U> after)`          |
| `Predicate<T>`      | `boolean test(T t)`         | `default Predicate<T> negate()` <br/> `default Predicate<T> and(Predicate<? super T> other)` <br/> `default Predicate<T> or(Predicate<? super T> other)` <br/> `static <T> Predicate<T> isEqual(Object targetRef)`                                  |
| `BiPredicate<T,U>`  | `boolean test(T t, U u)`    | `default BiPredicate<T,U> negate()` <br/> `default BiPredicate<T,U> and(BiPredicate<? super T,? super U> other)` <br/> `default BiPredicate<T,U> or(BiPredicate<? super T,? super U> other)`                                                     |
| `Function<T,R>`     | `R apply(T t)`              | `default <V> Function<T,V> andThen(Function<? super R,? extends V> after)` <br/> `default <V> Function<V,R> compose(Function<? super V,? extends T> before)` <br/> `static <T> Function<T,T> identity()`                                            |
| `BiFunction<T,U,R>` | `R apply(T t, U u)`         | `default <V> BiFunction<T,U,V> andThen(Function<? super R,? extends V> after)`    |
| `UnaryOperator<T>`  | `T apply(T t)`              | `default <V> Function<T,V> andThen(Function<? super T,? extends V> after)` <br/> `default <V> Function<V,T> compose(Function<? super V,? extends T> before)` <br/> `static <T> UnaryOperator<T> identity()`                                         |
| `BinaryOperator<T>` | `T apply(T t, T u)`         | `default <V> BiFunction<T,T,V> andThen(Function<? super T,? extends V> after)` <br/> `static <T> BinaryOperator<T> minBy(Comparator<? super T> comparator)` <br/> `static <T> BinaryOperator<T> maxBy(Comparator<? super T> comparator)` |

NOTAS:
- Los métodos default definidos (por ejemplo compose y andThen) no ejecutan nada, sólo combinan dos interfaces funcionales en una nueva
- Las clases `Consumer<T>` y `BiConsumer<T,U>` utilizan como parámetros Consumer y BiConsumer en vez de un Function
- Las clases `Consumer<T>` y `BiConsumer<T,U>` no tienen compose debido a que sería idéntico al andThen al no tener salida los parámetros
- Las clases `BiFunction<T,U,R>` y `BinaryOperator<T>` no tienen compose debido a que Java no permite múltiples parámetros de salida

2) En otros paquetes:

| Tipo interfaz   | Método interfaz funcional   | Otros métodos abstract de Object | Otros métodos disponibles |
|-----------------|-----------------------------|----------------------------------|---------------------------|
| `Comparator<T>` | `int compare(T o1, T o2)`   | `boolean equals(Object obj)`     | Muchos otros métodos      |
| `Runnable`      | `void run()`                |                                  |                           |
| `Callable<V>`   | `V call() throws Exception` |                                  |                           |

Para ver la lista completa entrar en:
https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/class-use/FunctionalInterface.html
